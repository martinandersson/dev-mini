# Changelog

All noteworthy changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/)
and this project **tries to** adhere to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Changed

- Moved notes to its own directory `./notes`.

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