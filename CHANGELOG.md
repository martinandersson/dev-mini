# Changelog

All noteworthy changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/)
and this project **tries to** adhere to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- Changed the master box from the undocumented and treacherous
  [`fso/artful64-desktop`][unreleased-1] to the well-documented and stable
  [`pristine/ubuntu-budgie-17-x64`][unreleased-2]. The reasons for this move is
  performance and stability. The Budgie desktop is snappy and responsive, as
  well as beautiful to look at. One doesn't have to be ashamed of using Linux
  when using Budgie. Plus, the pristine boxes are handcrafted high quality with
  all necessary hacks implemented for a smooth experience. The switch has two
  consequences:
  1. The document `./notes/unattended-upgrades` is deleted. The new box
     [documents this issue][unreleased-4].
  1. The `keyboard_layout` configuration goes away. I tried about five million
     different command-line Voodoo tricks to set a keyboard layout as part of
     the provisioning without the GUI and I also tried the only [Ansible Role
     available][unreleased-3], but nothing worked. I even compared two disk
     snapshots and repeated exactly the effects of the GUI-solution - i.e., I
     modded tons of files all over the place which for whatever reason had
     duplicated the keyboard layout value(s), but even this didn't work. So,
     fuck it. It takes about 10 seconds for the user to change the layout using
     the "Region and language" GUI app anyways.
- Moved notes to its own directory `./notes`.

[unreleased-1]: https://app.vagrantup.com/fso/boxes/artful64-desktop
[unreleased-2]: https://app.vagrantup.com/pristine/boxes/ubuntu-budgie-17-x64
[unreleased-3]: https://galaxy.ansible.com/gantsign/keyboard/
[unreleased-4]: https://github.com/martinanderssondotcom/box-ubuntu-budgie-17-x64/issues/3

## [1.0.1] - 2017-11-19

### Changed

- Fixed a bad variable reference which would crash the auto-installation of
  Vagrant plugins' dependencies.

## 1.0.0 - 2017-11-19 [YANKED]

Initial release.

### Added

- `./README.md`
- `./Vagrantfile`
- `./provisioning/playbook.yml`
- `./provisioning/requirements.yml`
- `./provisioning/changelog.md`
- `./provisioning/box-failures.md`
- `./provisioning/unattended-upgrades.md`

[Unreleased]: https://github.com/martinanderssondotcom/mini-dev/compare/v1.0.1...HEAD
[1.0.1]: https://github.com/martinanderssondotcom/mini-dev/compare/v1.0.0...v1.0.1