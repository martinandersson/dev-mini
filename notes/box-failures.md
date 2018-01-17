# Boxes' hall of shame

Briefly documents failures of Vagrant boxes.

In all honesty, it's my impression that only a minority of all Vagrant boxes
actually work.

## 2018-01-17

Using `Windows 10`, `Vagrant 2.0.1`, `VirtualBox 5.2.4` with `Extensions Pack
5.2.4` and `vagrant-vbguest 0.15.0`.

- `archlinux/archlinux` (2018.01.07)  
  ..fails cuz Ansible - or rather, its modules - requires Python, and using
  Ansible to install Python on a target machine without Python is like .. a
  nightmare. Seems like any OS without Python natively installed is simply out
  of the question for Ansible. Great! Lovely! They shud put that on their
  website.

  Error message:

  > `fatal: [target1]: FAILED! => {"changed": false,
  > "module_stderr": "Shared connection to target1 closed.\r\n", "module_stdout":
  > "/bin/sh: /usr/bin/python: No such file or directory\r\n", "msg": "MODULE
  > FAILURE", "rc": 0}`.

- `generic/alpine36` (1.3.30)  
  Has no guest additions and vagrant-vbguest plugin fails to install it (invalid
  ISO-mount command).

- `generic/rhel7` (1.3.30)  
  vagrant-vbguest plugin fails to update guest additions; the removal step is
  all of a sudden interactive; asking to confirm the removal of the old version
  and then the installation aborts when prompted with a yes/no question.

- `fedora/27-atomic-host` (27.20180104.5)  
  vagrant-vbguest plugin fails to install guest additions; "Read-only file
  system". lol @ myself.

- `fedora/27-cloud-base` (20171105)  
   vagrant-vbguest plugin fails to install guest additions; "Failed to
   synchronize cache for repo 'updates'".

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
