# qBittorrent 增强版 Docker 配置总结

## 1. 基本配置

- 使用 LinuxServer.io 的 qBittorrent 镜像
- 环境变量设置：
  - 用户/组 ID（PUID/PGID）
  - 时区（Asia/Shanghai）
  - WebUI 端口（8080）
- 数据卷挂载：
  - `/config`：存储配置文件
  - `/downloads`：存储下载内容
- 端口映射：
  - 8080：WebUI
  - 6881（TCP/UDP）：BitTorrent 协议

## 2. 自动重启

- 使用 `restart: unless-stopped` 策略
- 确保容器在崩溃或系统重启后自动重新启动

## 3. 健康检查

- 定期检查 WebUI 可访问性
- 配置项：
  - 检查间隔：1分钟
  - 超时时间：10秒
  - 重试次数：3次
  - 启动缓冲期：40秒

## 4. 可自定义项

- PUID 和 PGID：根据系统设置调整
- 数据卷挂载路径：可根据需要修改
- WebUI 端口：可更改以避免冲突

## 5. 使用方法

1. 将配置保存为 `docker-compose.yml` 文件
2. 使用命令 `docker-compose up -d` 启动容器
3. 通过 `http://localhost:8080` 访问 WebUI
   - 默认用户名：admin
   - 默认密码：adminadmin

## 6. Docker Compose 配置文件

```yaml
version: "3"
services:
  qbittorrent:
    image: ghcr.io/linuxserver/qbittorrent
    container_name: qbittorrent
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
      - WEBUI_PORT=8080
    volumes:
      - ./config:/config
      - ./downloads:/downloads
    ports:
      - 8080:8080
      - 6881:6881
      - 6881:6881/udp
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080"]
      interval: 1m
      timeout: 10s
      retries: 3
      start_period: 40s
```

这个配置提供了一个稳定、可靠且易于管理的 qBittorrent 环境，结合了基本功能设置、自动化管理和健康监控，以确保服务的持续可用性和性能。
