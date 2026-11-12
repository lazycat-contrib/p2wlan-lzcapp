# P2WLAN Server 懒猫微服打包

上游：https://github.com/yhan-sun/p2wlan （MIT）

P2WLAN：跨平台 P2P 虚拟局域网工具（房间组网、NAT 穿透、IPv6 直连、Relay 回退）。本包部署其自托管 **Control 服务端**（控制面）。

## 打包说明

- 镜像：`ghcr.io/yhan-sun/p2wlan/server`，tag 形如 `server-v0.1.173`，映射为 SemVer `0.1.173`，由 [ca-x/lazycat-github-action](https://github.com/ca-x/lazycat-github-action) 镜像模式自动同步（amd64/arm64）
- 容器以非 root（10001）运行，`/data` 持久化到 `/lzcapp/var/data`（SQLite 数据库、支持日志）
- 健康检查：`/health`

## 设计说明：仅包含 Control，不含 Relay

Relay 中继需要**客户端可达的公网 TLS 端口和受信任的域名证书**（客户端会校验 `tls://relay:port` 证书链），LazyCat 微服环境无法为容器提供独立公网端口 + 有效证书，因此本打包只运行 Control：

- 账号注册登录、房间管理、设备信令、管理台（`/admin/`，Bearer Token 认证）经懒猫可信 HTTPS 反代正常工作
- 客户端在支持直连的网络环境（IPv6、NAT 可穿透）下可正常组网联机
- 双侧均为硬 NAT 且无 IPv6 的场景缺少 Relay 回退；需要完整 Relay 的用户请按上游自托管指南部署

## 设置向导

- `admin_token`（secret）：管理台 `/admin/` 的 Bearer Token（≥32 字符），随机默认值
- `JWT_SECRET`：由 `stable_secret` 自动生成，无需配置

## 使用

部署后在 P2WLAN 客户端选择自托管服务，Control 地址填 `https://p2wlan.<你的懒猫域名>`。
