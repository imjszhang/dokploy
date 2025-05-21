
# Cloudflare Tunnel 集成

这个目录包含用于将 Dokploy 项目通过 Cloudflare Tunnel 映射到公网的 Docker Compose 配置。

## 概述

Cloudflare Tunnel 提供了一种安全的方式，无需公网 IP 或开放防火墙端口，即可将内部服务暴露到互联网。通过创建从您的基础设施到 Cloudflare 网络的出站连接，实现安全、可靠的公网访问。

## 主要功能

- **安全连接**：创建从内部网络到 Cloudflare 的安全出站连接
- **零信任安全**：无需开放入站端口，降低攻击面
- **自动 TLS**：通过 Cloudflare 自动为您的服务提供 HTTPS
- **DDoS 防护**：利用 Cloudflare 的网络保护您的应用免受攻击
- **简化管理**：集中管理访问控制和证书

## 快速开始

### 前提条件

1. Cloudflare 账户
2. 已在 Cloudflare 注册并配置的域名
3. 已创建的 Cloudflare Tunnel 和 Tunnel Token

### 环境变量设置

在运行 Docker Compose 之前，您需要设置以下环境变量：

```bash
# 在 .env 文件中配置或直接导出
export CLOUDFLARE_TUNNEL_TOKEN=your-tunnel-token
```

这个 Token 可以从 Cloudflare Zero Trust 仪表板中获取，创建或管理隧道时会提供。

### 启动服务

```bash
# 启动 Cloudflare Tunnel 服务
docker-compose up -d
```

## 工作原理

1. `cloudflared` 守护进程在您的内部网络建立到 Cloudflare 的出站连接
2. 创建加密通道，通过隧道将请求转发到本地服务
3. 外部用户通过您配置的 Cloudflare 域名访问您的应用
4. 流量通过 Cloudflare 的网络服务到您的内部服务，无需公网 IP

## 配置说明

当前配置使用 `host` 网络模式运行 `cloudflared` 容器，以便直接访问本地网络中的所有服务。配置简单，只需提供 Cloudflare Tunnel Token。

```yaml
services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    network_mode: host
    restart: always
    command: tunnel --no-autoupdate run --token ${CLOUDFLARE_TUNNEL_TOKEN}
    environment:
      - TUNNEL_TOKEN=${CLOUDFLARE_TUNNEL_TOKEN}
```

## 常见问题

- **连接问题**：确保 `CLOUDFLARE_TUNNEL_TOKEN` 正确设置，并检查容器日志
- **网络问题**：使用 `host` 网络模式可能需要特定权限，请确保 Docker 有适当权限
- **应用不可访问**：检查 Cloudflare 仪表板中的隧道配置，确保正确设置了服务路由

## 更多资源

- [Cloudflare Tunnel 官方文档](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)
- [Cloudflare Zero Trust 介绍](https://developers.cloudflare.com/cloudflare-one/)
- [Dokploy 项目文档](https://github.com/imjszhangimjszhang/dokploy-vagrant)
