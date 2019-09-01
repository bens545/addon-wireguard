# Community Hass.io Add-ons: WireGuard

[![GitHub Release][releases-shield]][releases]
![Project Stage][project-stage-shield]
[![License][license-shield]](LICENSE.md)

![Supports armhf Architecture][armhf-shield]
![Supports armv7 Architecture][armv7-shield]
![Supports aarch64 Architecture][aarch64-shield]
![Supports amd64 Architecture][amd64-shield]
![Supports i386 Architecture][i386-shield]

[![GitLab CI][gitlabci-shield]][gitlabci]
![Project Maintenance][maintenance-shield]
[![GitHub Activity][commits-shield]][commits]

[![Discord][discord-shield]][discord]
[![Community Forum][forum-shield]][forum]

[![Buy me a coffee][buymeacoffee-shield]][buymeacoffee]

[![Support my work on Patreon][patreon-shield]][patreon]

WireGuard: fast, modern, secure VPN tunnel.

## About

[WireGuard®][wireguard] is an extremely simple yet fast and modern VPN that
utilizes state-of-the-art cryptography. It aims to be faster, simpler, leaner,
and more useful than IPsec, while avoiding the massive headache.

It intends to be considerably more performant than OpenVPN. WireGuard is
designed as a general-purpose VPN for running on embedded interfaces and
supercomputers alike, fit for many different circumstances.

Initially released for the Linux kernel, it is now cross-platform (Windows,
macOS, BSD, iOS, Android) and widely deployable,
including via an Hass.io add-on!

WireGuard is currently under heavy development, but already it might be
regarded as the most secure, easiest to use, and the simplest VPN solution
in the industry.

## Installation

WireGuard is pretty simple, however, can be quite complex for user that isn't
familiar with all terminology used. The add-on takes care of a lot of things
for you (if you want).

Follow the following steps for installation & a quick start:

1. [Add our Hass.io add-ons repository][repository] to your Hass.io instance.
1. Install the "WireGuard" add-on.
1. Set the `host` configuration option to your Hass.io (external) address,
    e.g., `myautomatedhome.duckdns.org`.
1. Change the name of the peer to something useful, e.g., `myphone`.
1. Save the configuration.
1. Start the "WireGuard" add-on
1. Check the logs of the "WireGuard" add-on to see if everything went well.
1. Forward port `51820` (UDP!) in your router to your Hass.io IP.
1. Download/Open the file `/ssl/wireguard/myphone/qrcode.png` stored on your
   Hass.io machine, e.g., using Samba, Visual Studio Code or the Configurator
   add-on.
1. Install the WireGuard app on your phone.
1. Add a new WireGuard connection to your phone, by scanning the QR code.
1. Connect!

**NOTE**: Do not add this repository to Hass.io, please use:
`https://github.com/hassio-addons/repository`.

## Configuration

Now, for starters, don't get scared by the number of options and difficult
terms this add-on provides. WireGuard can be a complex piece of software,
but the add-on only has a few, simple, required settings. All the rest is
handled by the add-on. However, If you would like to set up a more complex
configuration, the add-on would allow that to.

If you are familiar with WireGuard, please note the following:
The configuration of WireGuard looks very similar to all terms used in the
WireGuard configuration. There is, however, one big difference: The
add-on is able to generate configurations for the add-on, but also for the
peers (clients).

**Note**: _Remember to restart the add-on when the configuration is changed._

A little more extensive example add-on configuration:

```json
{
  "log_level": "info",
  "server": {
    "host": "myautomatedhome.duckdns.org",
    "addresses": [
      "10.10.10.1"
    ],
    "dns": [
      "1.1.1.1",
      "1.0.0.1"
    ]
  },
  "peers": [
      {
        "name": "frenck",
        "addresses": [
          "10.10.10.2"
        ],
        "allowed_ips": [],
        "client_allowed_ips": []
      },
      {
        "name": "ninja",
        "public_key": "QNLXV8lrsPnKOd011DO8g5DWyad6iHJDSVOD6yOqjiE=",
        "addresses": [
          "10.10.10.3"
        ],
        "allowed_ips": [],
        "client_allowed_ips": [
          "10.10.10.0/24",
          "192.168.1.0/24"
        ]
      }
  ]
}
```

**Note**: _This is just an example, don't copy and paste it! Create your own!_

### Option: `server.host`

This configuration option is the hostname that your clients will use to connect
to your WireGuard add-on. The `host` is mainly used to generate client
configurations and SHOULD NOT contain a port. If you want to change the port,
use the "Network" section of the add-on configuration.

Example: `myautomatedhome.duckdns.org`, for local testing `hassio.local`
will actually work.

### Option: `server.addresses`

A list of IP (IPv4 or IPv6) addresses (optionally with CIDR masks) to be
assigned to the server/add-on interface.

It is strongly advised to create/use a separate IP address space from your
home network, e.g., if your home network uses `192.168.1.x` then DO NOT use
that for the add-on.

### Option: `server.dns` _(optional)_

A list of DNS servers used by the add-on and the configuration generated for
the clients. This configuration option is optional, and if no DNS servers are
set, it will use the built-in DNS server from Hass.io.

**If you are running the [AdGuard][adguard] or [Pi-hole][pihole] add-on,
you can add  `172.30.32.1` as a DNS IP address in the list.** This will cause your
clients to use those. What this does, it effectively making your clients
to have ad-filtering (e.g., your mobile phone), while not at home.

### Option: `server.private_key` _(optional)_

Allows you to provide your own base64 private key generated by `wg genkey`.
This option supports the use of `!secret`. If you don't supply one,
the add-on will generate one for you and store it in:
`/ssl/wireguard/private_key`.

### Option: `server.public_key` _(optional)_

Allows you to provide your own a base64 public key calculated by `wg pubkey`
from a private key. This option supports the use of `!secret`.

If you don't supply one, the add-on will calculate one based on the private
key that was supplied via the `server.private_key` or, in case no private key
was supplied, calculate it from the generated private key.

### Option: `server.fwmark` _(optional)_

A 32-bit fwmark for outgoing packets. May be specified in hexadecimal by
prepending "0x". If you don't know what this is, then you probably don't
need it.

### Option: `server.table` _(optional)_

Controls the routing table to which routes are added. Setting it to `off`
disables the creation of routes altogether. When not provided, the add-on
adds routes to the default table and enables special handling of default routes.

### Option: `server.pre_up` _(optional)_

Allows you to run commands before WireGuard is started.

### Option: `server.pre_down` _(optional)_

Allows you to run commands before WireGuard is stopped.

### Option: `server.post_up` _(optional)_

Allows you to run commands after WireGuard has been started. This is useful
for modifying things like routing. If not provided, the add-on will by default
route all traffic coming in from the VPN through your home network.

If you like to disable that, setting this option to `"off"`,
will disable that behavior.

By default it executes the following:

`iptables -A FORWARD -i %i -j ACCEPT;
iptables -A FORWARD -o %i -j ACCEPT;
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE`

### Option: `server.post_down` _(optional)_

Allows you to run commands after WireGuard has been stopped. This is useful
for modifying things like routing. If not provided, the add-on will by default
remove the default rules created by the `post_up` defaults.

If you like to disable that, setting this option to `"off"`,
will disable that behavior.

By default it executes the following:

`iptables -D FORWARD -i %i -j ACCEPT;
iptables -D FORWARD -o %i -j ACCEPT;
iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE`

### Option: `server.mtu` _(optional)_

The MTU is automatically determined from the endpoint addresses or the
system default route, which is usually a sane choice.

However, to manually specify an MTU to override this automatic discovery,
this value may be specified explicitly.

### Option: `peers.name`

This is an identifier for you. It helps you to know what this peer is, e.g.,
`myphone`, `mylaptop`, `ninja`.

This name is also used for creating the directory in `/ssl/wireguard` where
the generated client configuration and QR codes are stored. Therefore, a name
has a maximum of 32 characters, can only contain A-Z (or a-z) and 0-9.
Names contain a hyphen (-) but must not start or end with one.

### Option: `peers.addresses`

A list of IP (IPv4 or IPv6) addresses (optionally with CIDR masks) to be
assigned to the peer.

This is used in the client configuration, but also for used by the add-on to
set the allowed IPs (unless overriden by the `peers.allowed_ips` option.)

### Option: `peers.private_key` _(optional)_

Allows you to provide your own base64 private key generated by `wg genkey` for
the peer. This option supports the use of `!secret`.

Technically, the add-on does not need this, however, since the add-on can
generate client configurations, it can be helpful.

If no private key and no public key is provided, the add-on will generate one
for you and store it in: `/ssl/wireguard/<peer.name>/`.

**Private keys, in general, should only be known by client, while this add-on
supports setting or generating one for your client is helpful and easy, it
isn't the best security practice. The best practice is to provide just the
`peers.public_key` option below, the add-on will honor that.**

### Option: `peers.public_key` _(optional, but recommended!)_

Allows you to provide your own a base64 public key calculated by `wg pubkey`
from a private key. This option supports the use of `!secret`.

If you don't supply one, the add-on will calculate one based on the private
key that was supplied via the `peer.private_key` or, in case no private key
was supplied, calculated it from the generated private key for this peer.

**While this add-on can generate public/private keypairs, from best security
practice perspective, it is strongly advised to manually provide a public key
for each of your peers. In that case, the add-on will not generate or configure
a private key by itself.**

### Option: `peers.allowed_ips` _(optional)_

**This configuration only valid for the add-on/server end and does not
affect client configurations.!**

A list of IPs (IPv4 or IPv6) addresses (optionally with CIDR masks) from which
incoming traffic for this peer is allowed and to which outgoing traffic for
this peer is directed.

If there are no IP addresses configured, the add-on will use the addresses
listed in `peers.addresses`.

The catch-all `0.0.0.0/0` may be specified for matching all IPv4 addresses,
and `::/0` may be specified for matching all IPv6 addresses.

### Option: `peers.client_allowed_ips` _(optional)_

**This configuration only valid for the peer end/client configuration and does
not affect the server/add-on!**

A list of IPs (IPv4 or IPv6) addresses (optionally with CIDR masks) from which
incoming traffic from the server is allowed and to which outgoing traffic for
this peer is directed.

The catch-all `0.0.0.0/0` may be specified for matching all IPv4 addresses,
and `::/0` may be specified for matching all IPv6 addresses.

If not configured, the add-on will use `0.0.0.0/0` in the generated client
configuration, routing all traffic on your client through the VPN tunnel.

### Option: `peers.persistent_keep_alive` _(optional)_

A seconds interval, between 1 and 65535 inclusive, of how often to send an
authenticated empty packet to the peer for the purpose of keeping a stateful
firewall or NAT mapping valid persistently.

For example, if the interface very rarely sends traffic, but it might at
anytime receive traffic from a peer, and it is behind NAT, the interface might
benefit from having a persistent keepalive interval of 25 seconds.

By default or when unspecified, this option is set to 25 seconds. This is
different from the WireGuard default, since the use case for this add-on is
most likely to be installed in home setups behind a NAT.

If set to "off", this option is disabled.

### Option: `peers.endpoint` _(optional)_

An endpoint IP or hostname, followed by a colon, and then a port number. This
is used by the add-on/server to connect to its peer.

This is completely optional as the endpoint will be updated automatically
to the most recent source IP address and port of correctly authenticated
packets from the peer/client.

### Option: `peers.pre_shared_key` _(optional)_

A base64 preshared key generated by `wg genpsk`. This option adds an additional
layer of symmetric-key cryptography to be mixed into the already existing
public-key cryptography, for post-quantum resistance.

### Option: `peers.fwmark` _(optional)_

**This configuration only valid for the peer end/client configuration and does
not affect the server/add-on!**

A 32-bit fwmark for outgoing packets. May be specified in hexadecimal by
prepending "0x". If you don't know what this is, then you probably don't
need it.

### Option: `log_level` _(optional)_

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

## Finding generated client configurations

All generated files are stored in `/ssl/wireguard`. This includes the
client configurations generated by this add-on.

Each peer/client will have its own folder, by the name specified in the
add-on configuration. The add-on additionally generates an image for each
client containing a QR code, to allow a quick an easy set up on, e.g., your
mobile phone.

## Using on a Generic Linux/Debian/Ubuntu-based Hass.io system

The HassOS operating system for Hass.io by default has installed WireGuard
support in its Linux kernel. However, if you run Hass.io on a generic Linux
installation (e.g., based on Ubuntu or Debian), WireGuard support is not
available by default.

This will cause the add-on to throw a large warning during the start up.
However, the add-on will work as advertised!

When this happens, the add-on falls back on a standalone instance of WireGuard
running inside the add-on itself. This method has drawbacks in terms of
performance.

In order to run WireGuard optimal, you should install WireGuard on your
host system. The add-on will pick that up automatically on the next start.

### Ubuntu

```bash
sudo add-apt-repository ppa:wireguard/wireguard
sudo apt-get update
sudo apt-get install wireguard
```

### Debian

```bash
sudo echo "deb http://deb.debian.org/debian/ unstable main" > /etc/apt/sources.list.d/unstable.list
sudo printf 'Package: *\nPin: release a=unstable\nPin-Priority: 90\n' > /etc/apt/preferences.d/limit-unstable
sudo apt update
sudo apt install wireguard
```

### Fedora

```bash
sudo dnf copr enable jdoss/wireguard
sudo dnf install wireguard-dkms wireguard-tools
```

### Other

If you have a different operating system on which you run Hass.io on, please
consult [WireGuard installation manual][wireguard-install]

## _"Missing WireGuard kernel module. Falling back to slow userspace implementation."_

If you've seen this warning in the add-on logs files, please check the chapter
above for more information.

## _"IP forwarding is disabled on the host system!"_

IP forwarding is the ability for an operating system to accept incoming
network packets on one interface, recognize that it is not meant for the
system itself, but that it should be passed on to another network,
and then forwards it accordingly.

Basically, it allows for data coming in from your VPN client will be routed to
other places like your home network, or the internet.

To enable IP forwarding, run the following commands directly on your **host**
system (e.g., Ubuntu, Debian).

```bash
sudo sysctl -w net.ipv4.ip_forward=1
sudo echo "net.ipv4.ip_forward = 1" > /etc/sysctl.conf
sudo sysctl -p /etc/sysctl.conf
```

## Snapshots

The WireGuard add-on can be backed up using Hass.io snapshots. There is one
caveat to take into account: A partial snapshot of the add-on DOES NOT contain
the generated client configurations, including their public/private keys
(if those keys are generated by the add-on).

Client configurations are stored in the `/ssl/wireguard` folder. If you
use partial snapshots, please be sure to backup both the `ssl` folder and the
add-on.

## WireGuard status API

This add-on provides a simple WireGuard status API. This API is not an
official API, darn simple, and experimental, but does allow you to pull
in data from the add-on into Home Assistant.

With the use of the [Home Assistant RESTful][ha-rest] integration, one should
be able to grab some interesting data from this add-on.

Example:

```yaml
sensor:
  - platform: rest
    resource: http://a0d7b954-wireguard
```

At this moment, we do not have template or examples on how this could be
used effectively with Home Assistant.
If you have, sharing would be appreciated!

## Troubleshooting

- You can test if the tunnel works (when not using custom DNS servers), by
  visiting <http://homeassistant:8123>. If a Home Assistant login page appears,
  it is working!
- Changes to peer/client configuration of this add-on, are not automatically
  passed to your (already) configured clients. You have to change those
  manually on your client device OR remove the WireGuard profile on the client
  device and load the new client configuration (e.g., by scanning the QR
  code).
- If you are running the [AdGuard][adguard] or [Pi-hole][pihole] add-on,
  you can add  `172.30.32.1` as a DNS IP address the list to use it.
- If you run a protection service like CloudFlare on your `server.host`
  address, please remember, that WireGuard will try to connect to CloudFlare
  in that case (and not your Home). Please consider using your IP
  address in the `server.host` field, or get an addition DNS record (e.g.,
  using DuckDNS) that does point directly to your IP address.
- If the initial connection from you client fails, this can be caused by
  resolving issues of `server.host` on the client device. You can consider
  solving this by editing the "Endpoint" setting on the client on the device
  in the connection profile.
- We had reports of people with connection issues on the client side, being
  resolved by configuring/setting the "Listen port" to `51820` on the client
  device in the connection profile.

## Changelog & Releases

This repository keeps a change log using [GitHub's releases][releases]
functionality. The format of the log is based on
[Keep a Changelog][keepchangelog].

Releases are based on [Semantic Versioning][semver], and use the format
of ``MAJOR.MINOR.PATCH``. In a nutshell, the version will be incremented
based on the following:

- ``MAJOR``: Incompatible or major changes.
- ``MINOR``: Backwards-compatible new features and enhancements.
- ``PATCH``: Backwards-compatible bugfixes and package updates.

## Support

Got questions?

You have several options to get them answered:

- The [Community Hass.io Add-ons Discord chat server][discord] for add-on
  support and feature requests.
- The [Home Assistant Discord chat server][discord-ha] for general Home
  Assistant discussions and questions.
- The Home Assistant [Community Forum][forum].
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

Copyright (c) 2019 Franck Nijhof

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

[aarch64-shield]: https://img.shields.io/badge/aarch64-yes-green.svg
[adguard]: https://github.com/hassio-addons/addon-adguard-home
[amd64-shield]: https://img.shields.io/badge/amd64-yes-green.svg
[armhf-shield]: https://img.shields.io/badge/armhf-yes-green.svg
[armv7-shield]: https://img.shields.io/badge/armv7-yes-green.svg
[buymeacoffee-shield]: https://www.buymeacoffee.com/assets/img/guidelines/download-assets-sm-2.svg
[buymeacoffee]: https://www.buymeacoffee.com/frenck
[commits-shield]: https://img.shields.io/github/commit-activity/y/hassio-addons/addon-wireguard.svg
[commits]: https://github.com/hassio-addons/addon-wireguard/commits/master
[contributors]: https://github.com/hassio-addons/addon-wireguard/graphs/contributors
[discord-ha]: https://discord.gg/c5DvZ4e
[discord-shield]: https://img.shields.io/discord/478094546522079232.svg
[discord]: https://discord.me/hassioaddons
[dockerhub]: https://hub.docker.com/r/hassioaddons/wireguard
[forum-shield]: https://img.shields.io/badge/community-forum-brightgreen.svg
[forum]: https://community.home-assistant.io/t/community-hass-io-add-on-wireguard/134662?u=frenck
[frenck]: https://github.com/frenck
[gitlabci-shield]: https://gitlab.com/hassio-addons/addon-wireguard/badges/master/pipeline.svg
[gitlabci]: https://gitlab.com/hassio-addons/addon-wireguard/pipelines
[ha-rest]: https://www.home-assistant.io/components/rest/
[home-assistant]: https://home-assistant.io
[i386-shield]: https://img.shields.io/badge/i386-yes-green.svg
[issue]: https://github.com/hassio-addons/addon-wireguard/issues
[keepchangelog]: http://keepachangelog.com/en/1.0.0/
[license-shield]: https://img.shields.io/github/license/hassio-addons/addon-wireguard.svg
[maintenance-shield]: https://img.shields.io/maintenance/yes/2019.svg
[patreon-shield]: https://www.frenck.nl/images/patreon.png
[patreon]: https://www.patreon.com/
[pihole]: https://github.com/hassio-addons/addon-pi-hole
[project-stage-shield]: https://img.shields.io/badge/project%20stage-experimental-yellow.svg
[reddit]: https://reddit.com/r/homeassistant
[releases-shield]: https://img.shields.io/github/release/hassio-addons/addon-wireguard.svg
[releases]: https://github.com/hassio-addons/addon-wireguard/releases
[repository]: https://github.com/hassio-addons/repository
[semver]: http://semver.org/spec/v2.0.0.htm
[wireguard-install]: https://www.wireguard.com/install/
[wireguard]: https://www.wireguard.com
