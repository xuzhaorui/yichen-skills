# yichen-macos-vpn-coexist

Help Shadowrocket and Tailscale provide proxy and tailnet access at the same time on macOS.

## What it covers

- Distinguishes macOS VPN tunnel contention from Shadowrocket route exclusions
- Checks whether `100.64.0.0/10` is incorrectly included in `tun-excluded-routes`
- Offers the advanced Homebrew `tailscaled` backend when the GUI Network Extensions keep displacing each other
- Plans a user-visible handoff before any VPN interruption and verifies the two connections separately

## Install

```bash
npx skills add xuzhaorui/yichen-skills --skill yichen-macos-vpn-coexist
```

The skill is also usable by copying this folder into a loaded skills directory.

## Important limits

The Tailscale CLI daemon is an advanced, less-tested macOS option. The example command uses `--accept-routes=false --accept-dns=false`, which avoids accepting subnet routes and Tailscale DNS; MagicDNS and subnet-router access may therefore need separate configuration. The skill does not promise that every Shadowrocket profile or macOS release will work.

This skill was informed by a successful macOS setup where the Shadowrocket main VPN remained connected while Tailscale's Homebrew daemon registered as an online tailnet device. Registration and an active VPN indicator do not by themselves prove every tailnet route or service is reachable; the skill asks for a target-level test when needed.

## Upstream references

- [Tailscale FAQ: other VPNs](https://tailscale.com/docs/reference/faq/other-vpns)
- [Tailscaled on macOS](https://github.com/tailscale/tailscale/wiki/Tailscaled-on-macOS)
- [Tailscale issue #21371](https://github.com/tailscale/tailscale/issues/21371)
- [Shadowrocket issue #20](https://github.com/LOWERTOP/Shadowrocket/issues/20)
