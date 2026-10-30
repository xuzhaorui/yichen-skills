# yichen-macos-vpn-coexist

帮助 macOS 上的 Shadowrocket 负责代理流量，同时让 Tailscale 访问 tailnet。

## 能处理的问题

- 区分 macOS VPN 隧道争用与 Shadowrocket 路由排除冲突
- 检查 `100.64.0.0/10` 是否误列在 `tun-excluded-routes`
- 当图形客户端的 Network Extension 反复互相抢占时，评估较高级的 Homebrew `tailscaled` 方案
- 在改 VPN 前安排用户可见的交接，并分别核验两个连接

## 安装

```bash
npx skills add xuzhaorui/yichen-skills --skill yichen-macos-vpn-coexist
```

也可以把本目录复制到 Agent 实际加载的 skills 目录。

## 限制

Tailscale 命令行守护进程属于 macOS 高级用法，测试较少。示例命令使用 `--accept-routes=false --accept-dns=false`，因此不会接收子网路由或 Tailscale DNS；如需 MagicDNS 或子网路由，应单独讨论配置。此 Skill 不保证所有 Shadowrocket 配置或 macOS 版本均可共存。

本 Skill 参考了一次成功的 macOS 配置：Shadowrocket 主 VPN 保持连接，Homebrew Tailscale 后台服务加入 tailnet 并显示在线。设备注册成功和 VPN 指示灯在线，并不能证明每条 tailnet 路由或服务都可访问；需要时还应针对目标设备做连通性测试。

## 上游资料

- [Tailscale FAQ：与其他 VPN 共用](https://tailscale.com/docs/reference/faq/other-vpns)
- [Tailscale macOS 命令行守护进程说明](https://github.com/tailscale/tailscale/wiki/Tailscaled-on-macOS)
- [Tailscale issue #21371](https://github.com/tailscale/tailscale/issues/21371)
- [Shadowrocket issue #20](https://github.com/LOWERTOP/Shadowrocket/issues/20)
