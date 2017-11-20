# A minimal development environment

This project represents a virtual bare-bones development environment, ready to
be raped with new software. Have all the dependencies installed, then do
`vagrant up`.

One or many machines may easily be set up thru editing configuration variables
at the top in `./Vagrantfile`. See the [`machine_names` configuration][1] for
more info.

Batteries included:

- Latest Ubuntu (`17.10`) and CentOS (`7.4.1708`)
- Admin user/password: vagrant/vagrant
- Static IPs on a private network: `192.168.60.10` .. `.11` ..
- Ansible plumbing to facilitate machine provisioning
- **That's it!**

Used as a template by a Windows-addicted developer as a quick way to bring about
new development environments whether they be single- or multi-machine.

[1]: #machine_names

## Dependencies

[Vagrant][2] is used to run the `Vagrantfile` which uses a box- and
configuration specific to the [VirtualBox][3] provider. So both of these
software packages must be installed on your host machine. I think you might also
wanna put your hands on VirtualBox's [extension pack][3].

Ansible is **not** required to be installed on your host machine. See
[Machine provisioning with Ansible][4].

[2]: https://www.vagrantup.com/downloads.html
[3]: https://www.virtualbox.org/wiki/Downloads
[4]: #Machine-provisioning-with-Ansible

## Configuration

Default configuration values are replaced by editing the `CONFIGURATION` hash
declared at the top of `./Vagrantfile`.

### `machine_names`

Defines machine names and thus implicitly, how many machines to create - in the
form of an array of strings. Defaults to `['my-ubuntu']`, i.e., 1 machine.

A machine name should not contain any whitespace.

The name is used in a lot of places..

1. hostname (you may use the name instead of an IP address when communicating
   between guest machines)
1. Vagrant's machine name (what you see when you do `vagrant status`)
1. VirtualBox's machine name together with the node's- IP address and creation date

By default, only one machine will be created and the first machine defined
is referred to as a **master**. If more than just one machine is defined, then
these other machines are referred to as **slaves**.

Unless configured differently, the master node is given a full desktop and more
computing power versus slaves who are headless with less computing power.

This example will set up one master and two slaves:

    machine_names: ['master', 'slave1', 'slave2']

The available configurations reflect the intent of this project which is to
provide one chief VM the developer uses as his development machine loaded with
GUI-based applications such as IDEs and any configurable number of headless
machines acting as a "remote" data center.

It is possible to make all nodes headless, see the [`master_acts_as_slave`][5]
configuration.

[5]: #master_acts_as_slave

### `ip_start`

Defines the first three bytes of the private IP addresses used. Defaults to
`'192.168.60.'`.

All machines will use the same private network. This configuration value
dictates the start of the IP range used. The full range is, of course, dependent
on how many machines are defined.

The configuration value affects only the first three bytes of the IP addresses
used. The last byte is always `1` for the host (i.e. by default, `192.168.60.1`)
and `10` for the master (`192.168.60.10`). If slaves are defined, their last
byte will be `11`, `12` and so on.

With this information in mind, I would never define more slave machines than 245
(255-10) lololol. If you do, let me know what happened.

The full IP address will be appended to the machine's VirtualBox name and is
thus visible directly in the machine list located in the VirtualBox GUI.

### `master_acts_as_slave`

Defines whether or not to configure the master machine just like a slave
machine. By default, this is `false`.

With all default configuration values applied, the master node will get a
full-blown desktop and a generous amount of computing resources (CPU and memory)
unlike slave machines which have no desktop and try to use as little computing
resources as possible.

However, set `master_acts_as_slave` to `true` and all of the master-specific
configuration will stop to apply. Instead, the first machine will be configured
just like a slave.

As an example, maybe your host machine already have a bunch of IDEs and build
tools installed in order for your app to be developed and all you want is a
bunch of fictitious machines to act as a Docker Swarm for deployment:

    machine_names: ["mgr1", "mgr2", "wrk1", "wrk2", "wrk3"],
    master_acts_as_slave: true

### `master_box`

Determines which box the master node uses. By default, this is
[`'fso/artful64-desktop'`][6].

Value is either the name of an installed box or the shorthand name of a box in
[Vagrant's cloud][7].

"artful64" is a reference to 64-bit Ubuntu 17.10. For a translation between
weird-ass ninja-encrypted "code names" and something so simple as a no-nonsense
version number, see [this link][8].

This configuration does not apply if [`master_acts_as_slave`][5] is set to
`true` in which case the first machine will get whatever [`slave_box`][9]
defines.

[6]: https://app.vagrantup.com/fso/boxes/artful64-desktop
[7]: https://app.vagrantup.com/boxes/search
[8]: https://wiki.ubuntu.com/Releases
[9]: #slave_box

### `master_cpus`

Number of virtual CPUs on the master node. By default, this is the number of
_available_ processors on the host machine (including _logical_ cores).

This configuration does not apply if [`master_acts_as_slave`][5] is set to
`true` in which case the first machine will get whatever [`slave_cpus`][10]
defines.

There is a raging debate online as to how many virtual CPUs VirtualBox should
assign to the VM. VirtualBox puts forth a warning against using more CPUs than
physical cores.. ([source][11]):

> You should not, however, configure virtual machines to use more CPU cores than
> you have available physically (real cores, no hyperthreads).

..but does not care to provide an explanation.

Gorilla research uniformly points to a performance improvement using _all_
available cores including logical (see [this][12], [that][13] and the
[third][14]). It appears that VirtualBox simply got spooked over reports that
heavy CPU loads could erm.. crash VirtualBox (see [this][15] and [that][16]). So
the "fix" was to simply discourage users from using all their CPUs? L-O-L.

[10]: #slave_cpus
[11]: https://www.virtualbox.org/manual/ch03.html
[12]: https://forums.virtualbox.org/viewtopic.php?f=1&t=59259&start=15
[13]: http://envobi.com/post/virtualbox-hyper-threading-benchmark-surprise/
[14]: https://unix.stackexchange.com/a/325959
[15]: https://www.virtualbox.org/ticket/14944
[16]: https://superuser.com/a/1179451

### `master_memory_mb`

Amount of RAM, in MB, that the master node should allocate for itself from the
host. By default, this value is `4096`.

Please note what VirtualBox has to say about this ([source][17]):

> The memory you give to the VM will not be available to your host OS while the
> VM is running, so do not specify more than you can spare.

This configuration does not apply if [`master_acts_as_slave`][5] is set to
`true` in which case the first machine will get whatever amount of RAM that
[`slave_memory_mb`][18] defines.

[17]: https://www.virtualbox.org/manual/ch01.html#gui-createvm
[18]: #slave_memory_mb

### `keyboard_layout`

Keyboard layout. Defaults to `nil` (nothing will be done about it).

If not `nil`, then it must be a 2-letter country code that specifies a keyboard
layout. For example, set to "se" for Swedish or "de" for German. For more
information, click [here][19].

If `nil`, then our provisioning applies not sorcery and keyboard layout will be
whatever the OS wants him to be; "for most platforms" this is "us"
([source][20]).

This configuration value applies to the master node only. It is expected that
you will SSH into slave nodes using a properly configured terminal and keyboard
on the host machine ;)

**Please note** that a custom layout only bites after an immediate reboot
following machine provisioning and only if prior to this reboot you did not use
the OS such as opening up a terminal! Don't ask me why. Maybe it's a feature.

[19]: https://wiki.archlinux.org/index.php/Keyboard_configuration_in_Xorg
[20]: http://manpages.ubuntu.com/manpages/xenial/man5/keyboard.5.html#contenttoc2

### `install_ansible_roles`

If `true`, will automagically install Ansible roles declared in
`./provisioning/requirements.yml` on the last machine before having the same
machine run all plays declared in `./provisioning/playbook.yml`. By default, the
value is `false`.

This flag is currently needed to avoid passing the `ansible-galaxy` command an
empty requirements file (this file is empty until you put roles in it). Cuz if
we do, Ansible Galaxy crash. In the future, this flag will be replaced with
in-script logic that figures out whether or not to call Ansible Galaxy without
human intervention.

### `slave_box`

Same as [`master_box`][21] except this configuration value applies to slaves
only. The default value is [`'mhubbard/centos7'`][22].

[21]: #master_box
[22]: https://app.vagrantup.com/mhubbard/boxes/centos7

### `slave_cpus`

Same as [`master_cpus`][23] except this configuration value applies to slaves
only. The default value is `2`.

[23]: #master_cpus

### `slave_memory_mb`

Same as [`master_memory_mb`][24] except this configuration value applies to
slaves only. The default value is `512`.

[24]: #master_memory_mb

## Machine provisioning with Ansible

The project has some built-in Ansible plumbing to help facilitate machine
provisioning. The last machine defined will become a provisioning controller by
installing Ansible inside the guest machine and then provision itself as well as
others.

Write all your plays in `./provisioning/playbook.yml`. You can reference machine
names in the playbook as well as two dynamically defined host groups; `master`
and `slaves`.

If you use [Ansible roles][25], then these can be automagically installed on the
controller but you must do two things! First, put 'em in
`./provisioning/requirements.yml` and then set [`install_ansible_roles`][26] to
`true`.

[25]: https://galaxy.ansible.com/list
[26]: #install_ansible_roles

## Change log

See [`./provisioning/changelog.md`][27].

[27]: https://github.com/martinanderssondotcom/mini-dev/blob/master/provisioning/changelog.md

## License

Copy paste whatever you want.

## Contact

martinandersson.com/contact