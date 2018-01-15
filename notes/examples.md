# Configuration examples

This document provides a few examples on how to configure `Vagrantfile` and
related files in `provisioning/`.

## Playing with Ansible

Ansible - in full compliance with modern and sad software principles - has
insufficient documentation. As an example, all but the most simple playbooks
use [host facts][ans-1]. But which host facts exist, possible values and what
these values mean is completely nondocumented. It's like going to a restaurant
and not even get a food menu. [Recent work][ans-2] has added just a tiny bit of
docs to cover for this, but I doubt we will ever see proper documentation which
in a perfect and sane world would go so far as to describe not only which host
facts exists, but even *how* they were gathered from the target machine.

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
> &nbsp;&nbsp;box: 'mhubbard/centos7',  
> &nbsp;&nbsp;first_ip: '192.168.60.12',  
> &nbsp;&nbsp;cpus: 2,  
> &nbsp;&nbsp;memory_mb: 512  
> }, {  
> &nbsp;&nbsp;machines: 'controller',  
> &nbsp;&nbsp;box: 'pristine/ubuntu-budgie-17-x64',  
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

All you need to do in order to run this playbook is `vagrant up` or `vagrant
provision --provision-with ansible`. Behind the scenes, Vagrant executes
`ansible-playbook` on the controller machine with a bunch of arguments, two of
which are relevant for us:

- An auto-generated [inventory directory][ans-4]: `/tmp/vagrant-ansible/inventory`
- The playbook: `provision/playbook.yml`

A piece of the output produced by Ansible version 2.4.2.0:

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
Vagrant constructed for us:

    printf "[defaults]\ninventory = /tmp/vagrant-ansible/inventory/" >> ~/.ansible.cfg

Now, this outta work:

    ansible -m ping targets

[ans-1]: http://docs.ansible.com/ansible/latest/playbooks_variables.html#information-discovered-from-systems-facts
[ans-2]: https://github.com/ansible/ansible/pull/34263
[ans-3]: https://github.com/martinanderssondotcom/mini-dev/blob/master/Vagrantfile#L147
[ans-4]: http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html#using-inventory-directories-and-multiple-inventory-sources

## Playing with Docker

Work in progress.