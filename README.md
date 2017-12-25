# Community Hass.io Add-ons: FTP

[![GitHub Release][releases-shield]][releases]
![Project Stage][project-stage-shield]
![Project Maintenance][maintenance-shield]
[![GitHub Activity][commits-shield]][commits]
[![License][license-shield]](LICENSE.md)

[![CircleCI][circleci-shield]][circleci]
[![Code Climate][codeclimate-shield]][codeclimate]
[![Bountysource][bountysource-shield]][bountysource]
[![Discord][discord-shield]][discord]
[![Community Forum][forum-shield]][forum]

[![Patreon][patreon-shield]][patreon]
[![PayPal][paypal-shield]][paypal]
[![Bitcoin][bitcoin-shield]][bitcoin]

A secure and fast FTP server for Hass.io

## About

The FTP protocol might be come in handy sometimes. While old,
it still has its use. For example, most IP Cameras still support the upload
of images or videos via FTP.

This add-on provides an FTP Server for Hass.io in a reasonably secure manner.
While FTP is not entirely secure by its (unencrypted) nature, this add-on
supports FTP over SSL (FTPS) and jails (chroot) the virtual users in their
home directories.

Of course, if you'd really want to, you could also use this add-on to again
access to your Home Assistant configuration via FTP.

## Installation

The installation of this add-on is pretty straightforward and not different in
comparison to installing any other Hass.io add-on.

1. [Add our Hass.io add-ons repository][repository] to your Hass.io instance.
1. Install the "FTP" add-on.
1. Start the "FTP" add-on
1. Check the logs of the "FTP" add-on to see if everything went well.

**NOTE**: Do not add this repository to Hass.io, please use:
`https://github.com/hassio-addons/repository`.

## Docker status

[![Docker Architecture][armhf-arch-shield]][armhf-dockerhub]
[![Docker Version][armhf-version-shield]][armhf-microbadger]
[![Docker Layers][armhf-layers-shield]][armhf-microbadger]
[![Docker Pulls][armhf-pulls-shield]][armhf-dockerhub]

[![Docker Architecture][aarch64-arch-shield]][aarch64-dockerhub]
[![Docker Version][aarch64-version-shield]][aarch64-microbadger]
[![Docker Layers][aarch64-layers-shield]][aarch64-microbadger]
[![Docker Pulls][aarch64-pulls-shield]][aarch64-dockerhub]

[![Docker Architecture][amd64-arch-shield]][amd64-dockerhub]
[![Docker Version][amd64-version-shield]][amd64-microbadger]
[![Docker Layers][amd64-layers-shield]][amd64-microbadger]
[![Docker Pulls][amd64-pulls-shield]][amd64-dockerhub]

[![Docker Architecture][i386-arch-shield]][i386-dockerhub]
[![Docker Version][i386-version-shield]][i386-microbadger]
[![Docker Layers][i386-layers-shield]][i386-microbadger]
[![Docker Pulls][i386-pulls-shield]][i386-dockerhub]

## Configuration

**Note**: _Remember to restart the add-on when the configuration is changed._

Example add-on configuration:

```json
{
  "log_level": "info",
  "port": 21,
  "data_port": 20,
  "banner": "Welcome to the Hass.io FTP service.",
  "pasv": true,
  "pasv_min_port": 30000,
  "pasv_max_port": 30010,
  "pasv_address": "",
  "ssl": false,
  "certfile": "fullchain.pem",
  "keyfile": "privkey.pem",
  "implicit_ssl": false,
  "max_clients": 5,
  "users": [
    {
      "username": "hassio",
      "password": "changeme",
      "allow_chmod": true,
      "allow_download": true,
      "allow_upload": true,
      "allow_dirlist": true,
      "addons": false,
      "backup": true,
      "config": true,
      "share": true,
      "ssl": false
    },
    {
      "username": "camera",
      "password": "changeme",
      "allow_chmod": false,
      "allow_download": false,
      "allow_upload": true,
      "allow_dirlist": true,
      "addons": false,
      "backup": false,
      "config": false,
      "share": true,
      "ssl": false
    }
  ]
}
```

**Note**: _This is just an example, don't copy and past it! Create your own!_

### Option: `log_level`

The `log_level` option controls the level of log output by the addon and can
be changed to be more or less verbose, which might be useful when you are
dealing with an unknown issue. Possible values are:

- `trace`: Show every detail, like all called internal functions.
- `debug`: Shows detailed debug information.
- `info`: Normal (usually) interesting events.
- `warning`: Exceptional occurrences that are not errors.
- `error`:  Runtime errors that do not require immediate action.
- `fatal`: Something went terribly wrong. Add-on becomes unusable.

Please note that each level automatically includes log messages from a
more severe level, e.g., `debug` also shows `info` messages. By default,
the `log_level` is set to `info`, which is the recommended setting unless
you are troubleshooting.

These log level also affects the log levels of the FTP server.

### Option: `port`

The port the FTP will listen on for incoming FTP connections.

### Option: `data_port`

The port from which PORT style connections originate.

### Option: `banner`

This string option allows you to provide the greeting banner displayed by
the FTP server when a connection first comes in.

### Option: `pasv`

Set to `false` if you want to disallow the PASV method of obtaining a data
connection. For more information about passive versus active FTP, see
[this excellent Stack Overflow][passive-vs-active] answer.

### Option: `pasv_min_port`

The minimum port to allocate for PASV style data connections. Can be used to
specify a narrow port range to assist firewalling.

### Option: `pasv_max_port`

The maximum port to allocate for PASV style data connections. Can be used to
specify a narrow port range to assist firewalling.

### Option: `pasv_address`

Use this option to override the IP address that the FTP server will advertise
in response to the PASV command. Provide a numeric IP address, or provide a
hostname which will be DNS resolved for you at startup. When left empty, the
address is taken from the incoming connected socket.

### Option: `ssl`

Enables/Disables SSL on the FTP Server. Set it `true` to enable it,
`false` otherwise.

### Option: `certfile`

The certificate file to use for SSL.

**Note**: _The file MUST be stored in `/ssl/`, which is default for Hass.io_

### Option: `keyfile`

The private key file to use for SSL.

**Note**: _The file MUST be stored in `/ssl/`, which is default for Hass.io_

### Option: `implicit_ssl`

If set to `true`, an SSL handshake is the first thing expect on all connections
(the FTPS protocol).

### Option: `max_clients`

This is the maximum number of clients which may be connected at the same time.
Any additional clients connecting will get an error message.

### Option: `users`

This option allows you to specify a list of one or more users. Each user can
have its own permissions like defined in the sub-options below.

#### Sub-option: `username`

The username the user needs to use to login to the FTP server. A valid username
has a maximum of 32 characters, contains only `A-Z` and `0-9`.
Usernames may contain a hyphen (`-`) but must not start or end with one.

#### Sub-option: `password`

The password the user logs in with.

#### Sub-option: `allow_chmod`

Setting this option to `true` will allow the use of the `SITE CHMOD` command for
that user.

#### Sub-option: `allow_download`

Setting this option to `true` will allow the user to download files from the
FTP server.

#### Sub-option: `allow_upload`

This controls whether any FTP commands which change the filesystem are
allowed or not.

These commands are `STOR`, `DELE`, `RNFR`, `RNTO`, `MKD`, `RMD`,
`APPE`, and `SITE`.

#### Sub-option: `allow_dirlist`

Setting this option to `true`, allows to user to browse all directories
the user was given access to by using the list commands.

#### Sub-option: `addons`

Allow the user to access the `/addons` directory.

#### Sub-option: `backup`

Allow the user to access the `/backup` directory.

#### Sub-option: `config`

Allow the user to access the `/config` directory.

#### Sub-option: `share`

Allow the user to access the `/share` directory.

#### Sub-option: `ssl`

Allow the user to access the `/ssl` directory.

## Changelog & Releases

This repository keeps a [change log](CHANGELOG.md). The format of the log
is based on [Keep a Changelog][keepchangelog].

Releases are based on [Semantic Versioning][semver], and use the format
of ``MAJOR.MINOR.PATCH``. In a nutshell, the version will be incremented
based on the following:

- ``MAJOR``: Incompatible or major changes.
- ``MINOR``: Backwards-compatible new features and enhancements.
- ``PATCH``: Backwards-compatible bugfixes and package updates.

## Support

Got questions?

You have several options to get them answered:

- The Home Assistant [Community Forum][forum], we have a
  [dedicated topic][forum] on that forum regarding this repository.
- The Home Assistant [Discord Chat Server][discord] for general Home Assistant
  discussions and questions.
- Join the [Reddit subreddit][reddit] in [/r/homeassistant][reddit]

You could also [open an issue here][issue] GitHub.

## Contributing

This is an active open-source project. We are always open to people who want to
use the code or contribute to it.

We have set up a separate document containing our
[contribution guidelines](CONTRIBUTING.md).

Thank you for being involved! :heart_eyes:

## Authors & contributors

The original setup of this repository is by [Franck Nijhof][frenck].

For a full list of all authors and contributors,
check [the contributor's page][contributors].

## We have got some Hass.io add-ons for you

Want some more functionality to your Hass.io Home Assistant instance?

We have created multiple add-ons for Hass.io. For a full list, check out
our [GitHub Repository][repository].

## License

MIT License

Copyright (c) 2017 Franck Nijhof

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

[aarch64-arch-shield]: https://img.shields.io/badge/architecture-aarch64-blue.svg
[aarch64-dockerhub]: https://hub.docker.com/r/hassioaddons/ftp-aarch64
[aarch64-layers-shield]: https://images.microbadger.com/badges/image/hassioaddons/ftp-aarch64.svg
[aarch64-microbadger]: https://microbadger.com/images/hassioaddons/ftp-aarch64
[aarch64-pulls-shield]: https://img.shields.io/docker/pulls/hassioaddons/ftp-aarch64.svg
[aarch64-version-shield]: https://images.microbadger.com/badges/version/hassioaddons/ftp-aarch64.svg
[amd64-arch-shield]: https://img.shields.io/badge/architecture-amd64-blue.svg
[amd64-dockerhub]: https://hub.docker.com/r/hassioaddons/ftp-amd64
[amd64-layers-shield]: https://images.microbadger.com/badges/image/hassioaddons/ftp-amd64.svg
[amd64-microbadger]: https://microbadger.com/images/hassioaddons/ftp-amd64
[amd64-pulls-shield]: https://img.shields.io/docker/pulls/hassioaddons/ftp-amd64.svg
[amd64-version-shield]: https://images.microbadger.com/badges/version/hassioaddons/ftp-amd64.svg
[armhf-arch-shield]: https://img.shields.io/badge/architecture-armhf-blue.svg
[armhf-dockerhub]: https://hub.docker.com/r/hassioaddons/ftp-armhf
[armhf-layers-shield]: https://images.microbadger.com/badges/image/hassioaddons/ftp-armhf.svg
[armhf-microbadger]: https://microbadger.com/images/hassioaddons/ftp-armhf
[armhf-pulls-shield]: https://img.shields.io/docker/pulls/hassioaddons/ftp-armhf.svg
[armhf-version-shield]: https://images.microbadger.com/badges/version/hassioaddons/ftp-armhf.svg
[bitcoin-shield]: https://img.shields.io/badge/donate-bitcoin-blue.svg
[bitcoin]: https://blockchain.info/payment_request?address=3GVzgN6NpVtfXnyg5dQnaujtqVTEDBCtAH
[bountysource-shield]: https://img.shields.io/bountysource/team/hassio-addons/activity.svg
[bountysource]: https://www.bountysource.com/teams/hassio-addons/issues
[circleci-shield]: https://img.shields.io/circleci/project/github/hassio-addons/addon-ftp.svg
[circleci]: https://circleci.com/gh/hassio-addons/addon-ftp
[codeclimate-shield]: https://img.shields.io/badge/code%20climate-protected-brightgreen.svg
[codeclimate]: https://codeclimate.com/github/hassio-addons/addon-ftp
[commits-shield]: https://img.shields.io/github/commit-activity/y/hassio-addons/addon-ftp.svg
[commits]: https://github.com/hassio-addons/addon-ftp/commits/master
[contributors]: https://github.com/hassio-addons/addon-ftp/graphs/contributors
[discord-shield]: https://img.shields.io/discord/330944238910963714.svg
[discord]: https://discord.gg/c5DvZ4e
[forum-shield]: https://img.shields.io/badge/community-forum-brightgreen.svg
[forum]: https://community.home-assistant.io/t/community-hass-io-add-on-ftp/36799?u=frenck
[frenck]: https://github.com/frenck
[home-assistant]: https://home-assistant.io
[i386-arch-shield]: https://img.shields.io/badge/architecture-i386-blue.svg
[i386-dockerhub]: https://hub.docker.com/r/hassioaddons/ftp-i386
[i386-layers-shield]: https://images.microbadger.com/badges/image/hassioaddons/ftp-i386.svg
[i386-microbadger]: https://microbadger.com/images/hassioaddons/ftp-i386
[i386-pulls-shield]: https://img.shields.io/docker/pulls/hassioaddons/ftp-i386.svg
[i386-version-shield]: https://images.microbadger.com/badges/version/hassioaddons/ftp-i386.svg
[issue]: https://github.com/hassio-addons/addon-ftp/issues
[keepchangelog]: http://keepachangelog.com/en/1.0.0/
[license-shield]: https://img.shields.io/github/license/hassio-addons/addon-ftp.svg
[maintenance-shield]: https://img.shields.io/maintenance/yes/2017.svg
[patreon-shield]: https://img.shields.io/badge/donate-patreon-blue.svg
[patreon]: https://www.patreon.com/frenck
[paypal-shield]: https://img.shields.io/badge/donate-paypal-blue.svg
[paypal]: https://www.paypal.me/FranckNijhof
[project-stage-shield]: https://img.shields.io/badge/project%20stage-experimental-yellow.svg
[reddit]: https://reddit.com/r/homeassistant
[releases-shield]: https://img.shields.io/github/release/hassio-addons/addon-ftp.svg
[releases]: https://github.com/hassio-addons/addon-ftp/releases
[repository]: https://github.com/hassio-addons/repository
[semver]: http://semver.org/spec/v2.0.0.htm
[passive-vs-active]: https://stackoverflow.com/a/1699163/299699
