# A minimal development environment

This project represents a virtual bare-bones development environment, ready to
be raped with new software.

How to get going:

1. Make a fork or copy the relevant files (`Vagrantfile`, `provisioning/*`).
1. Have all the [dependencies][intro-1] installed.
1. `vagrant up`.

By default, only one [Ubuntu Budgie 18][intro-2] VM is defined. But, many
machines using any number of configuration profiles (Vagrant box, static IP, et
cetera..) may easily be set up thru editing configuration values at the top in
the provided `Vagrantfile`. Furthermore, Ansible plumbing has been added to
facilitate machine provisioning. All of this is described in subsequent
sections.

This project was conceived by a Windows-addicted developer who got tired of
hacking together new Vagrantfiles for each unique software project. I like to
see it as a template replacement for "vagrant init".

Examples can be found in [notes/examples.md][intro-3]. Also see
[dev-java-9][intro-4].

[intro-1]: #dependencies
[intro-2]: https://ubuntubudgie.org/
[intro-3]: https://github.com/martinanderssondotcom/dev-mini/blob/master/notes/examples.md
[intro-4]: https://github.com/martinanderssondotcom/dev-java-9

## Dependencies

[Vagrant][dep-1] is used to run the `Vagrantfile` which uses a box- and
configuration specific to the [VirtualBox][dep-2] provider. So both of these
software packages must be installed on your host machine. I think you might also
wanna put your hands on VirtualBox's [extension pack][dep-2].

Ansible is **not** required to be installed on your host machine. See
[Machine provisioning with Ansible][dep-3].

[dep-1]: https://www.vagrantup.com/downloads.html
[dep-2]: https://www.virtualbox.org/wiki/Downloads
[dep-3]: #machine-provisioning-with-ansible

## Configuration

Default configuration values are replaced by editing the value of the
`CONFIGURATION` variable declared at the top of `Vagrantfile`.

By default, the Vagrantfile defines **one configuration profile and one
machine**: <sup>[[source][conf-1]]</sup>

> CONFIGURATION = {  
> &nbsp;&nbsp;machines: 'my-ubuntu',  
> &nbsp;&nbsp;box: 'pristine/ubuntu-budgie-18-x64',  
> &nbsp;&nbsp;first_ip: '192.168.60.10',  
> &nbsp;&nbsp;cpus: Etc.nprocessors,  
> &nbsp;&nbsp;memory_mb: 4096  
> }

This is a very simple and straightforward case. One Ruby hash describes one
profile and one String describes one machine using that profile. But, any number
of profiles and any number of machines can be combined wrapping said entities in
a Ruby Array. Here is an example that defines **two profiles and three
machines**:

> CONFIGURATION = {  
> &nbsp;&nbsp;machines: 'desktop',  
> &nbsp;&nbsp;box: 'pristine/ubuntu-budgie-18-x64',  
> &nbsp;&nbsp;first_ip: '192.168.60.10',  
> &nbsp;&nbsp;cpus: Etc.nprocessors,  
> &nbsp;&nbsp;memory_mb: 4096  
> }, {  
> &nbsp;&nbsp;machines: ['headless1', 'headless2'],  
> &nbsp;&nbsp;box: 'mhubbard/centos7',  
> &nbsp;&nbsp;first_ip: '192.168.60.11',  
> &nbsp;&nbsp;cpus: 2,  
> &nbsp;&nbsp;memory_mb: 512  
> }

Next, we go into details of the available configuration values that can be used.
Almost all values are required. The only optional ones are [`gui`][conf-2] and
[`ansible_group`][conf-3].

[conf-1]: https://github.com/martinanderssondotcom/dev-mini/blob/master/Vagrantfile#L4-L11
[conf-2]: #gui
[conf-3]: #ansible_group

### `machines`

Defines machine names and thus implicitly, how many machines to create.

Can either be a String - one machine, or an Array of strings if the profile
should be used to create multiple machines.

A machine name should not contain any whitespace.

The name is used as..

1. hostname (you may use the name instead of an IP address when communicating
   between guest machines)
1. Vagrant's machine name (what you see when you do `vagrant status`)
1. VirtualBox's machine name (suffixed with the node's IP address in parenthesis)

### `box`

Defines which Vagrant box to use.

The value must be either the name of a locally installed box or the shorthand
name of a box in [Vagrant's cloud][box-1].

[box-1]: https://app.vagrantup.com/boxes/search

### `first_ip`

Defines the first static IP in a generated range of IPs used by the VMs on a
private network.

If only one machine is defined, then only one IP will be used. Subsequent
machines will get an IP similar to the previous machine's IP except the last
byte will have been incremented by 1. For example, if multiple machines are
defined and the default value is not changed, then the *second* machine will be
assigned IP "192.168.60.11". The *third* machine will be assigned IP
"192.168.60.12", and so on.

The IP will be appended to the machine's VirtualBox name and is thus clearly
visible in the VirtualBox GUI's machine list.

For the VirtualBox provider, what Vagrant calls a "private network" is
translated into NAT + host-only (VirtualBox lingo, see [VirtualBox docs][ip-1]).
The NAT part enables Internet access from the VMs. The host-only part puts the
host and VMs together on an internal network such that all machines can see each
other. But, the outside world can not initiate a connection with anyone of the
VMs.

The host IP on the internal network defaults to the network address + ".1".
I.e., by default, unless the first_ip configuration is changed, this will be
`192.168.60.1`. <sup>[[source][ip-2]]</sup>

[ip-1]: https://www.virtualbox.org/manual/ch06.html#network_hostonly
[ip-2]: https://www.virtualbox.org/manual/ch06.html#network_nat_service

### `cpus`

Defines the number of virtual CPUs for the VM and is currently only applied for
the VirtualBox provider.

By default, the value will be the number of _available_ processors on the host
machine (including _logical_ cores).

There is a raging debate online as to how many virtual CPUs VirtualBox should
assign to the VM. VirtualBox warns against using more CPUs than physical cores..
<sup>[[source][cpu-1]]</sup>

> You should not, however, configure virtual machines to use more CPU cores than
> you have available physically (real cores, no hyperthreads).

..but does not care to provide an explanation.

Gorilla research uniformly points to a performance improvement using _all_
available cores including logical <sup>[[source1][cpu-2], [source2][cpu-3],
[source3][cpu-4]]</sup>. It appears that VirtualBox got spooked over reports
that heavy CPU loads could erm.. crash VirtualBox <sup>[[source1][cpu-5],
[source2][cpu-6]]</sup>. So the "fix" was to simply discourage users from using
all of their CPUs? Ultra lol!

[cpu-1]: https://www.virtualbox.org/manual/ch03.html
[cpu-2]: https://forums.virtualbox.org/viewtopic.php?f=1&t=59259&start=15
[cpu-3]: http://envobi.com/post/virtualbox-hyper-threading-benchmark-surprise/
[cpu-4]: https://unix.stackexchange.com/a/325959
[cpu-5]: https://www.virtualbox.org/ticket/14944
[cpu-6]: https://superuser.com/a/1179451

### `memory_mb`

Amount of RAM, in MiB, that the VM should allocate for itself from the host.
This configuration is currently only applied to the VirtualBox provider.

Please note what VirtualBox has to say about this: <sup>[[source][mem-1]]</sup>

> The memory you give to the VM will not be available to your host OS while the
> VM is running, so do not specify more than you can spare.

[mem-1]: https://www.virtualbox.org/manual/ch01.html#gui-createvm

### `gui`

If provided, then it must be either `true` or `false` depending on whether or
not you want a UI attached to the VM.

This value is specific to VirtualBox and should not "normally" have to be
provided because the box sort of knows already if it comes with a desktop or
not. But a great majority of all Vagrant boxes out there are a complete joke, to
be honest, and they do not configure the VirtualBox provider accurately.

The default box used by this project [does configure its provider
properly][gui-1] and no action is needed.

[gui-1]: https://github.com/martinanderssondotcom/box-ubuntu-budgie-17-x64/blob/b03d62419c7bcb7319712c010b6505a1b341b549/Vagrantfile#L8

### `ansible_group`

This configuration value is optional and if provided, defines one Ansible
inventory group which comprises all the [`machines`][grp-1] in the profile.

[grp-1]: #machines

## Machine provisioning with Ansible

This project has some built-in Ansible plumbing to facilitate machine
provisioning.

Ansible will be installed on the last machine defined, hereafter referred to as
the "controller". This controller will then be used to provision all machines,
including itself. Therefore, the host machine does not need to have Ansible
installed.

Write Ansible plays in `provisioning/playbook.yml`.

Valid Ansible [host patterns][ans-1] that can be used to target machines
include - but are not limited to - [machine names][ans-2], [IP][ans-3] and
[Ansible group][ans-4].

Ansible roles can be automagically installed on the controller by putting them
in `provisioning/requirements.yml`.

Vagrant will run the ansible provisioner only once during the first "vagrant
up" call. Vagrant will by default not - for better or worse - run provisioning
again. But, if the initial provisioning crashed or you just feel like running
the playbook a second time, do this:

    vagrant provision --provision-with ansible

[ans-1]: http://docs.ansible.com/ansible/latest/intro_patterns.html#patterns
[ans-2]: #machines
[ans-3]: #first_ip
[ans-4]: #ansible_group

## Change log

See [CHANGELOG.md][27].

[27]: https://github.com/martinanderssondotcom/dev-mini/blob/master/CHANGELOG.md

## License

Copy paste whatever you want.

## Contact

[martinandersson.com/contact][28]

[28]: http://www.martinandersson.com/contact/