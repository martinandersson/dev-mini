# Boxes' hall of shame

## 2017-12-13

Using `Windows 10`, `Vagrant 2.0.1`, `VirtualBox 5.2.2` with `Extensions Pack
5.2.2` and `vagrant-vbguest 0.15.0`.

- `trueability/elementaryos-0.4`: Fail to mount synced dir, plus the box is badly setup.

## 2017-11-19

Using `Windows 10`, `Vagrant 2.0.1`, `VirtualBox 5.2.0` with `Extensions Pack
5.2.0` and `vagrant-vbguest 0.15.0`.

### Ubuntu 17.10

- `fso/artful64`: Vagrant can't configure network ([github issue][1]).
- `bento/ubuntu-17.10`: Vagrant can't configure network ([github issue][1]).
- `generic/ubuntu1710`: Vagrant can't configure network ([github issue][1]).
- `ubuntu/artful64`: Vagrant can't configure network ([github issue][1]).
- `averri/ubuntu-17.10-mini`: Box fails to download (omg!!).

### Ubuntu 17.04

- `ubuntu/zesty64`: Ansible/SSH crash, vagrant user not setup.
- `fso/zesty64`: Update of guest additions crash first, then folder sync.
- `generic/ubuntu1704`: Update of guest additions crash first, then folder sync.
- `bento/ubuntu-17.04`: Update of guest additions crash first, then folder sync.

### Ubuntu 16.04

- `ubuntu/xenial64`: Ansible/SSH crash, vagrant user not setup.
- `bento/ubuntu-16.04`: Update of guest additions crash first, then folder sync.
- `generic/ubuntu1604`:  Update of guest additions crash first, then folder sync.
- `geerlingguy/ubuntu1604`: Fail to mount synced dir.

### Alpine Linux

- `maier/alpine-3.6-x86_64`: Fail to mount synced dir.
- `generic/alpine36`: Fail to mount synced dir.
- `alpine/alpine64`: Fail to mount synced dir.

### CentOS

- `centos/7`: Everything except `/vagrant/.vagrant/` syncs, and then SSH crash because of it.
- `bento/centos-7.4`:  Fail to mount synced dir.
- `geerlingguy/centos7`: Fail to mount synced dir.

[1]: https://github.com/hashicorp/vagrant/issues/9134
