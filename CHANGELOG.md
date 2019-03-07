# Changelog

All noteworthy changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/)
and this project **tries to** adhere to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

## [Unreleased]

- Nothing, yet!

### Changed

- Upgraded Ansible from version `2.7.2` to version `2.7.8`.
- Dropped support for provider `vmware_workstation` and `vmware_fusion` (only
  using `vmware_desktop` - see [this][2.2.2-1]).
- Switched out the default/instructional playbook into one that performs a
  system upgrade and installs a text editor (gedit).

[2.2.2-1]: https://www.hashicorp.com/blog/introducing-the-vagrant-vmware-desktop-plugin#what-s-new

## [2.2.1] - 2018-11-21

### Changed

- Upgraded Ansible from version `2.6.1` to version `2.7.2`.

## [2.2.0] - 2018-07-30

### Changed

- Upgraded Ansible from version `2.5.1` to version `2.6.1`.
- Updated all documented examples.

### Fixed

- Fixed a bug where private keys for VMware-VMs wouldn't be copied to the
  Ansible controller.

## [2.1.0] - 2018-04-22

### Added

- Support for the VMware provider.

### Changed

- Primary box to [`pristine/ubuntu-budgie-18-x64`][2.1.0-1]. 
- Upgraded Ansible from version `2.4.3.0` to version `2.5.1`.

[2.1.0-1]: https://app.vagrantup.com/pristine/boxes/ubuntu-budgie-18-x64

## [2.0.2] - 2018-04-06

### Changed

- Upgraded Ansible from version `2.4.2.0` to version `2.4.3.0`.
- Folder ignored by git renamed from `.ignored/` to `git-ignored/`.
- Changed project name from "mini-dev" to "dev-mini". This will hopefully make
  forks easier to identify in enumerations and other listings.

## [2.0.1] - 2018-02-24

### Fixed

- In the VirtualBox machine name, swap IP enclosing `[]` characters with `()`
  ([issue #18][2.0.1-1]).
- Do not append creation date stamp to the VirtualBox machine name
  ([issue #19][2.0.1-2]).

[2.0.1-1]: https://github.com/martinanderssondotcom/dev-mini/issues/18
[2.0.1-2]: https://github.com/martinanderssondotcom/dev-mini/issues/19

## [2.0.0] - 2018-01-17

### Added

- `notes/examples.md`

### Changed

- Upgraded Ansible from version `2.4.1.0` to version `2.4.2.0`
  ([issue #17][2.0.0-7]).
- Completely new configuration model, as described in README.md. Most
  importantly, the concept of "master" and "slave" is gone. We simply work with
  configuration profiles, of which any number of machines can be instantiated.
  Also, see [issue #3][2.0.0-6].
- Removed the `install_ansible_roles` flag ([issue #9][2.0.0-1]).
- Changed the master box from the undocumented and treacherous
  [`fso/artful64-desktop`][2.0.0-2] to the well-documented and stable
  [`pristine/ubuntu-budgie-17-x64`][2.0.0-3]. The reasons for this move is
  performance and stability. The Budgie desktop is snappy and responsive, as
  well as beautiful to look at. One doesn't have to be ashamed of using Linux
  when using Budgie. Plus, the pristine boxes are handcrafted high quality with
  all necessary hacks implemented for a smooth experience. The switch has two
  consequences:
  1. The document `notes/unattended-upgrades` is deleted. The new box
     [documents this issue][2.0.0-4].
  1. The `keyboard_layout` configuration goes away. I tried about five million
     different command-line Voodoo tricks to set a keyboard layout as part of
     the provisioning without the GUI and I also tried the only [Ansible Role
     available][2.0.0-5], but nothing worked. I even compared two disk
     snapshots and repeated exactly the effects of the GUI-solution - i.e., I
     modded tons of files all over the place which for whatever reason had
     duplicated the keyboard layout value(s), but even this didn't work. So,
     fuck it. It takes about 10 seconds for the user to change the layout using
     the "Region and language" GUI app anyways.
- Moved notes to its own directory `notes/`.

[2.0.0-7]: https://github.com/martinanderssondotcom/dev-mini/issues/17
[2.0.0-6]: https://github.com/martinanderssondotcom/dev-mini/issues/3
[2.0.0-1]: https://github.com/martinanderssondotcom/dev-mini/issues/9
[2.0.0-2]: https://app.vagrantup.com/fso/boxes/artful64-desktop
[2.0.0-3]: https://app.vagrantup.com/pristine/boxes/ubuntu-budgie-17-x64
[2.0.0-4]: https://github.com/martinanderssondotcom/box-ubuntu-budgie-17-x64/issues/3
[2.0.0-5]: https://galaxy.ansible.com/gantsign/keyboard/

## [1.0.1] - 2017-11-19

### Fixed

- Fixed a bad variable reference which would crash the auto-installation of
  Vagrant plugins' dependencies.

## 1.0.0 - 2017-11-19 [YANKED]

Initial release.

### Added

- `README.md`
- `Vagrantfile`
- `provisioning/playbook.yml`
- `provisioning/requirements.yml`
- `provisioning/changelog.md`
- `provisioning/box-failures.md`
- `provisioning/unattended-upgrades.md`

[Unreleased]: https://github.com/martinanderssondotcom/dev-mini/compare/v2.2.1...HEAD
[2.2.1]: https://github.com/martinanderssondotcom/dev-mini/compare/v2.2.0...v2.2.1
[2.2.0]: https://github.com/martinanderssondotcom/dev-mini/compare/v2.1.0...v2.2.0
[2.1.0]: https://github.com/martinanderssondotcom/dev-mini/compare/v2.0.2...v2.1.0
[2.0.2]: https://github.com/martinanderssondotcom/dev-mini/compare/v2.0.1...v2.0.2
[2.0.1]: https://github.com/martinanderssondotcom/dev-mini/compare/v2.0.0...v2.0.1
[2.0.0]: https://github.com/martinanderssondotcom/dev-mini/compare/v1.0.1...v2.0.0
[1.0.1]: https://github.com/martinanderssondotcom/dev-mini/compare/v1.0.0...v1.0.1