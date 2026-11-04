# OpenWrt telephony feed

[简体中文](README.md) | **English**

This private source feed is used by the customized
[hnt510-great/openwrt](https://github.com/hnt510-great/openwrt) main tree.
The project targets x86_64/glibc, APK, native systemd and GNOME. Package
selection follows the main tree's saved configuration.

| Item | Value |
| --- | --- |
| Feed name | `telephony` |
| Repository branch | `master` |
| Original upstream | [https://github.com/openwrt/telephony](https://github.com/openwrt/telephony) (`master`) |

Based on OpenWrt telephony, this feed preserves recipes for voice services,
protocol libraries and kernel modules, including local commits and upstream
merge history.

| Directory | Contents |
| --- | --- |
| `libs/` | PJPROJECT, libsrtp, sofia-sip, spandsp, DAHDI/OSLEC and related components |
| `net/` | Asterisk, FreeSWITCH, Kamailio, baresip, coturn, rtpengine and diagnostic tools |

## Project role

The feed is maintained with the main tree's glibc/APK, native systemd and
mainline kernel configuration. Its history includes adaptations such as
rtpengine builds on newer kernels. Preserve those feed-local changes when
bringing in original upstream source.

Kernel-related recipes must use the current main-tree configuration and
matching kernel generation. A new image requires modules and development
headers with the same ABI; complete builds use the main tree's generation
reservation entry point.

The project docs do not provide dedicated SIP-account, PBX, RTP-call or physical
telephone acceptance procedures. Router smoke tests do not establish those
services' acceptance. Selection and enablement follow `.config` and device
configuration.

## Get the source and build

Authenticate on the build machine with an account that can read every private
repository, then obtain the complete main tree:

```sh
gh auth login --hostname github.com --git-protocol https --web
gh auth setup-git
git clone https://github.com/hnt510-great/openwrt.git
cd openwrt
./scripts/feeds update -a
./scripts/feeds install -a
```

The main tree's `feeds.conf.default` already contains:

```text
src-git telephony https://github.com/hnt510-great/openwrt-telephony.git;master
```

To update only this feed in an existing main tree:

```sh
./scripts/feeds update telephony
./scripts/feeds install -a -p telephony
```

Select the desired package in the main configuration and run `make defconfig`.
Example individual package build:

```sh
make package/feeds/telephony/rtpengine/compile V=s
```

Run these commands from the main-tree root. Preserve an existing `.config`;
see [local build](https://github.com/hnt510-great/openwrt/blob/main/docs/local-build.md) for setup in a new checkout.
`install` creates build-recipe links rather than installing software on a
device. The full `update -a` also checks and can modify main-tree custom recipes.

## Maintenance and validation

`origin` points to this private repository. To merge original upstream changes
in a new clone, add `upstream` once:

```sh
git -C feeds/telephony remote add upstream https://github.com/openwrt/telephony.git
git -C feeds/telephony fetch upstream
git -C feeds/telephony merge upstream/master
# Review, resolve conflicts and validate before pushing
git -C feeds/telephony push origin master
```

Skip the add command if `upstream` already exists. `scripts/feeds update` pulls
the private branch; it does not merge original upstream automatically. Feed
integration and main-tree commits are separate. Preserve existing commits,
stashes and uncommitted work. Main-tree `patches/feeds/` files and feature
manifests are historical backups; do not reapply changes already present.

Changes to published APK contents require a new package version, preserving
immutable package identities. Record source checks, package builds, VM tests
and physical-device validation separately. This page summarizes existing
design and evidence without claiming new build or device results. See
[private GitHub hosting](https://github.com/hnt510-great/openwrt/blob/main/docs/github-source-hosting.md).

## Design and validation documents

- [Mainline kernel maintenance](https://github.com/hnt510-great/openwrt/blob/main/docs/mainline-kernel.md)
- [Kernel packages and upgrades](https://github.com/hnt510-great/openwrt/blob/main/docs/kernel-upgrade.md)
- [Native service supervision](https://github.com/hnt510-great/openwrt/blob/main/docs/systemd-native-supervision.md)
- [Source and configuration reconstruction](https://github.com/hnt510-great/openwrt/blob/main/docs/local-build.md)

## License and upstream

Retain upstream copyrights and licenses. Each package's source follows its
recipe's `PKG_LICENSE` and source declarations.
