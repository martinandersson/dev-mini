# Configuration examples

This document provides a few examples on how to configure `Vagrantfile` and
related files in `provisioning/`.

1. [Playing with Ansible](#playing-with-ansible)
1. [Playing with Docker](#playing-with-docker)

## Playing with Ansible

Ansible - in full compliance with modern and sad software principles - suffers
from insufficient documentation. As an example, all but the most simple
playbooks use [host facts][ans-1]. But which host facts exist, possible values
and what these values mean is completely nondocumented. It's like going to a
restaurant and not even get a food menu. [Recent work][ans-2] has added just a
tiny bit of docs to cover for this, but I doubt we will ever see proper
documentation which in a perfect and sane world would go so far as to describe
not only which host facts exists, but even *how* they were gathered from the
target machine.

Having that said, this example will investigate what values we might see for two
such nondocumented host facts; `ansible_os_family` and `ansible_distribution`.

The last machine defined will get Ansible automagically installed (which
specific version to install is [editable][ans-3]). So there is no other
requirement to get going with Ansible other than to define a multi-machine
environment. Actually, a single-machine environment would suffice since Ansible
can manage the same machine it is installed on. But that is not the purpose of
neither Ansible nor this example.

Put this configuration in the `Vagrantfile`:

> CONFIGURATION = {  
> &nbsp;&nbsp;machines: 'target1',  
> &nbsp;&nbsp;ansible_group: 'targets',  
> &nbsp;&nbsp;box: 'debian/stretch64',  
> &nbsp;&nbsp;first_ip: '192.168.60.11',  
> &nbsp;&nbsp;cpus: 2,  
> &nbsp;&nbsp;memory_mb: 512  
> }, {  
> &nbsp;&nbsp;machines: 'target2',  
> &nbsp;&nbsp;ansible_group: 'targets',  
> &nbsp;&nbsp;box: 'centos/7',  
> &nbsp;&nbsp;first_ip: '192.168.60.12',  
> &nbsp;&nbsp;cpus: 2,  
> &nbsp;&nbsp;memory_mb: 512  
> }, {  
> &nbsp;&nbsp;machines: 'controller',  
> &nbsp;&nbsp;box: 'pristine/ubuntu-budgie-18-x64',  
> &nbsp;&nbsp;first_ip: '192.168.60.10',  
> &nbsp;&nbsp;cpus: Etc.nprocessors,  
> &nbsp;&nbsp;memory_mb: 4096  
> }

Make this the contents of `provisioning/playbook.yml`:

> \---  
> \- hosts: targets  
> &nbsp;&nbsp;tasks:  
> &nbsp;&nbsp;- name: Dump some facts  
> &nbsp;&nbsp;&nbsp;&nbsp;debug: "msg='OS family/distribution: {{ansible_os_family}}/{{ansible_distribution}}'"

All you need to do in order to run this playbook is `vagrant up` *or* `vagrant
provision --provision-with ansible` in case the environment is running already.
Behind the scenes, Vagrant executes `ansible-playbook` on the controller machine
with a bunch of arguments, two of which are relevant for us:

- An auto-generated [inventory directory][ans-4]: `/tmp/vagrant-ansible/inventory`
- The playbook: `provision/playbook.yml`

A piece of the output produced by Ansible version 2.6.1:

```
TASK [Dump some facts] *********************************************************
ok: [target1] => {
    "msg": "OS family/distribution: Debian/Debian"
}
ok: [target2] => {
    "msg": "OS family/distribution: RedHat/CentOS"
}
```

If we wanna fool around with Ansible manually outside the scope of the
aforementioned playbook and Vagrant, then we need to describe our target
inventory to Ansible. It can either be done explicitly per command invoked with
the `-i` option, or we can configure Ansible to always use the inventory that
Vagrant constructed for us (run this on the controller):

    printf "[defaults]\ninventory = /tmp/vagrant-ansible/inventory/" >> ~/.ansible.cfg

Now, this outta work:

    ansible -m ping targets

[ans-1]: http://docs.ansible.com/ansible/latest/playbooks_variables.html#information-discovered-from-systems-facts
[ans-2]: https://github.com/ansible/ansible/pull/34263
[ans-3]: https://github.com/martinanderssondotcom/dev-mini/blob/master/Vagrantfile#L145
[ans-4]: http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html#using-inventory-directories-and-multiple-inventory-sources

## Playing with Docker

In this example we will setup a very simple Docker Swarm, consisting of one
manager and one worker node.

Put this configuration in the `Vagrantfile`:

> CONFIGURATION = {  
> &nbsp;&nbsp;machines: 'worker',  
> &nbsp;&nbsp;box: 'centos/7',  
> &nbsp;&nbsp;first_ip: '192.168.60.11',  
> &nbsp;&nbsp;cpus: 2,  
> &nbsp;&nbsp;memory_mb: 512  
> }, {  
> &nbsp;&nbsp;machines: 'manager',  
> &nbsp;&nbsp;box: 'pristine/ubuntu-budgie-18-x64',  
> &nbsp;&nbsp;first_ip: '192.168.60.10',  
> &nbsp;&nbsp;cpus: Etc.nprocessors,  
> &nbsp;&nbsp;memory_mb: 4096  
> }

It is important that the manager is defined last such that he becomes the
Ansible controller. As opposed to letting Vagrant decide what synchronization
type works best, the worker box (`centos/7`) has defined in its included
Vagrantfile; _rsync_ to be the folder synchronization primitive and for this
particular synchronization type, Vagrant excludes the `.vagrant/` folder.

However, access to this folder is needed by the Ansible controller because
that's how he gets his hands on private keys to use for SSH access into the
other machines he wants to provision (see [these lines of code][docker-1]). Had
the controller been running the `centos/7` box, then he wouldn't have found the
`.vagrant/` folder and crashed.

Of course - adhering to modern best practices - why Vagrant excludes this folder
is completely [undocumented][docker-2] nor does it seem to be reversible. One
can only add _more_ folders to exclude.

Either way, problem solved by making the manager to also be the controller.

Make this the contents of `provisioning/playbook.yml`:

> \---  
> \- name: Install Docker  
> &nbsp;&nbsp;hosts: all  
> &nbsp;&nbsp;become: yes  
> &nbsp;&nbsp;vars:  
> &nbsp;&nbsp;&nbsp;&nbsp;Ubuntu: docker-ce=18.06.0\~ce\~3-0~ubuntu  
> &nbsp;&nbsp;&nbsp;&nbsp;CentOS: docker-ce-18.06.0.ce-3.el7  
> &nbsp;&nbsp;pre_tasks:  
> &nbsp;&nbsp;- name: Ensure the docker group is present  
> &nbsp;&nbsp;&nbsp;&nbsp;group: name=docker  
> &nbsp;&nbsp;- name: Ensure vagrant belongs to the docker group  
> &nbsp;&nbsp;&nbsp;&nbsp;user: name=vagrant groups=docker append=yes  
> &nbsp;&nbsp;roles:  
> &nbsp;&nbsp;- role: geerlingguy.docker  
> &nbsp;&nbsp;&nbsp;&nbsp;docker_package: "{{ vars[ansible_distribution] }}"  
> &nbsp;&nbsp;&nbsp;&nbsp;docker_apt_repository: "deb [arch=amd64] ht<span>tps://download</span>.docker.com/linux/ubuntu bionic stable"  
> &nbsp;&nbsp;&nbsp;&nbsp;docker_compose_version: 1.22.0

We needed to specify weird-ass package names to be installed. Yes, despite using
an Ansible role which one would think ought to sort that out by himself. Well,
turns out - not too surprisingly - that Docker package naming is a science on
its own and there exists no AI smart enough to handle this drama.

In theory, hacking together which Docker CE package name to use is a two-step
process. First, go to [this page][docker-3] and click on the target OS. That
will bring you to a new page listing an OS-specific package syntax to use. Then,
because this documentation is almost guaranteed to be outdated, replace the
version part with the latest and greatest version found on
[this page][docker-4]. Easy peasy!

But in practice, this doesn't hold. The package names in the repositories are
not consistent and Docker's own website doesn't even list the latest releases.
So the safest bet is to probe the repositories manually for each OS ([Ubuntu][docker-5], [CentOS][docker-6]).

The latest and greatest version of Docker Compose is officially found on
[this page][docker-7] and that is what I have used. I have not looked into the
repository manually. Maybe I should, lol.

The manager machine uses an Ubuntu 18.10 box and for the time being
(2018-07-29), this particular version ("cosmic") does not have a dedicated
Docker repository. Hence, we specifically point the Ansible role to the
repository for 18.04 ("bionic") by redefining the `docker_apt_repository`
variable. In the future, you should be able to remove this hack.

The `pre_tasks` added is basically a [Docker-sponsored hack][docker-8] so we
don't have to prefix every `docker` command with `sudo`.

The role needs to be installed before running the playbook. So put him in
`provisioning/requirements.yml`:

> \---  
> \- src: geerlingguy.docker  
> &nbsp;&nbsp;version: 2.5.1

Do a `vagrant up` and everything should work just fine (lol I can't stop
laughing).

Next, do this (i.e., first SSH into manager and then type the command):

    $ vagrant ssh manager
    vagrant@manager:~$ docker swarm init --advertise-addr 192.168.60.10:2377 --listen-addr 192.168.60.10:2377
    To add a worker to this swarm, run the following command:
    
        docker swarm join --token SWMTKN-1-0mpfo3mdv1prhqgre5esahwoedpbq3ctxu60xbhjaq0kfxknru-0oarib6er7fh1bctbzbbotsza 192.168.60.10:2377
    
    To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

Run the command Docker gave us on the worker node:

    $ vagrant ssh worker
    [vagrant@worker ~]$ docker swarm join --token SWMTKN-1-0mpfo3mdv1prhqgre5esahwoedpbq3ctxu60xbhjaq0kfxknru-0oarib6er7fh1bctbzbbotsza 192.168.60.10:2377
    This node joined a swarm as a worker.

[docker-1]: https://github.com/martinanderssondotcom/dev-mini/blob/master/Vagrantfile#L121-L123
[docker-2]: https://www.vagrantup.com/docs/synced-folders/rsync.html#rsync__exclude
[docker-3]: https://docs.docker.com/install/#server
[docker-4]: https://docs.docker.com/release-notes/docker-ce
[docker-5]: https://download.docker.com/linux/ubuntu/dists/bionic/pool/stable/amd64/
[docker-6]: https://download.docker.com/linux/centos/7/x86_64/stable/Packages/
[docker-7]: https://github.com/docker/compose/releases
[docker-8]: https://docs.docker.com/engine/installation/linux/linux-postinstall/#manage-docker-as-a-non-root-user