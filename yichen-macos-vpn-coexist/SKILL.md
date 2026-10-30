---
name: yichen-macos-vpn-coexist
description: Diagnose and configure macOS Shadowrocket and Tailscale to coexist, including route conflicts and the advanced Tailscale CLI backend. Use when both clients must stay available or one tunnel displaces the other; do not use for unrelated VPN setup.
---

# macOS Shadowrocket and Tailscale Coexistence

Help the user keep Shadowrocket for proxy traffic while Tailscale provides tailnet access. Treat coexistence as a network change: preserve the user's current connection, warn before any action that may reload or disconnect a tunnel, and verify both clients independently.

## Workflow

1. **Establish the target and current state.** Confirm macOS, client variants and versions, which VPN is connected, whether the user needs tailnet node access only or also subnet routes, exit nodes, and MagicDNS. Inspect the current Shadowrocket route settings before editing them. Never infer that two green app indicators mean traffic routes correctly.
2. **Separate tunnel contention from route conflicts.** Tailscale's macOS app uses Apple's Network Extension. A current open Tailscale report describes it repeatedly reasserting its Packet Tunnel and displacing Shadowrocket: [tailscale/tailscale#21371](https://github.com/tailscale/tailscale/issues/21371). Check the issue and vendor guidance for current status before relying on a workaround.
3. **Check the specific `100.64.0.0/10` route case.** Tailscale uses this range for node IPs. A Shadowrocket issue documents new tailnet connections failing when the range appears in `tun-excluded-routes`; remove it only if that exact conflicting setting is present and the user approves the profile edit: [LOWERTOP/Shadowrocket#20](https://github.com/LOWERTOP/Shadowrocket/issues/20). Do not confuse this setting with a `DIRECT` rule or another similarly named bypass list. If it is absent, leave the profile unchanged and investigate tunnel ownership instead.
4. **If the app-based tunnels keep fighting, offer the advanced CLI backend.** The official Tailscale macOS wiki says `tailscaled` uses `/dev/utun` rather than Apple's Network Extension and is less tested: [Tailscaled on macOS](https://github.com/tailscale/tailscale/wiki/Tailscaled-on-macOS). Explain that this is a fallback with a different support and DNS tradeoff, not a guaranteed vendor-supported fix.
5. **Prepare before changing network state.** Explain the exact toggle or command, likely interruption, how to restore the previous VPN, and what the user should do if the assistant loses connectivity. If the user's work depends on the current tunnel, ask them to perform the handoff step and wait for them before proceeding. Never silently turn off Shadowrocket.
6. **Configure only the chosen path.** For the Homebrew service, the documented shape is:

   ```bash
   brew install --formula tailscale
   sudo brew services start tailscale
   sudo tailscale up --accept-routes=false --accept-dns=false
   ```

   Use the flags only when the user wants tailnet node access without accepting subnet routes or Tailscale DNS. If they need subnet routes or MagicDNS, discuss those route/DNS changes first. Let the user enter their Mac administrator password and complete browser authentication themselves; never request that they paste passwords, auth URLs containing tokens, or Auth Keys into chat. Keep the native Tailscale GUI off when testing this daemon backend, and disable Shadowrocket's stuck built-in Tailscale module so it does not start a second Tailscale implementation. Leave Shadowrocket's main VPN on when possible.
7. **Verify both sides and the actual path.** Check `tailscale status` and the tailnet machine list for the new Mac, then check macOS VPN settings for Shadowrocket still connected. Where the user has a known reachable tailnet target, use a bounded `tailscale ping` or application-level connection test. Report separately whether registration, tunnel coexistence, and target traffic each succeeded. Do not claim full routing success from an admin-console device entry alone.
8. **Stop cleanly if a check fails.** Do not repeat identity resets or create more Auth Keys as blind retries. Report the exact layer that failed, keep logs off unless the user authorizes a bounded capture, and give the recovery command or UI steps before stopping.

## Safety and privacy

- Do not publish or log device names, tailnet IPs, login URLs, Auth Keys, account identifiers, screenshots, VPN logs, or private profile contents.
- Prefer the browser sign-in flow for CLI enrollment. Do not reuse a key saved for a failed integration unless the user specifically wants to.
- Do not change exit nodes, subnet-route acceptance, DNS, or `tun-excluded-routes` without tying the change to the user's stated connectivity need.
- Before any log capture, explain what it may contain, limit the capture window, and turn it off after the requested diagnosis.
- Before any tunnel interruption, give a concrete recovery path and wait for the user's handoff when losing the active connection could strand them.

## References

- [Tailscale FAQ: using Tailscale alongside other VPNs](https://tailscale.com/docs/reference/faq/other-vpns)
- [Tailscale macOS CLI daemon guide](https://github.com/tailscale/tailscale/wiki/Tailscaled-on-macOS)
- [Open Shadowrocket/Tailscale tunnel-ownership issue](https://github.com/tailscale/tailscale/issues/21371)
- [Shadowrocket `100.64.0.0/10` route-conflict report](https://github.com/LOWERTOP/Shadowrocket/issues/20)
