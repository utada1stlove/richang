# qBittorrent 增强版 Docker 配置总结

## 1. 关于 qBittorrent 增强版

qBittorrent 增强版是由社区维护的 qBittorrent 修改版，提供了额外的功能和优化。这个版本在标准 qBittorrent 的基础上增加了一些受欢迎的功能。

## 2. 主要特性

- 额外的 IP 过滤选项
- 增强的自动管理功能
- 更多的 UI 自定义选项
- 性能优化
- 所有原版 qBittorrent 的功能

## 3. Docker 配置

### 3.1 Docker Compose 文件

```yaml
version: "3"
services:
  qbittorrent-enhanced:
    image: superng6/qbittorrent:latest
    container_name: qbittorrent-enhanced
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

### 3.2 配置说明

- **镜像**：使用 `superng6/qbittorrent:latest`，这是一个包含增强功能的版本。
- **环境变量**：
  - `PUID` 和 `PGID`：设置用户和组 ID，确保文件权限正确。
  - `TZ`：设置时区为 Asia/Shanghai。
  - `WEBUI_PORT`：设置 Web UI 端口为 8080。
- **卷挂载**：
  - `./config:/config`：存储配置文件。
  - `./downloads:/downloads`：存储下载的文件。
- **端口映射**：
  - 8080：Web UI 访问端口。
  - 6881：BitTorrent 协议使用的端口（TCP 和 UDP）。
- **重启策略**：`unless-stopped` 确保容器在崩溃或系统重启后自动重启。
- **健康检查**：定期检查 Web UI 是否可访问，确保服务正常运行。

## 4. 使用方法

1. 将上述 Docker Compose 配置保存为 `docker-compose.yml` 文件。
2. 在保存该文件的目录中运行以下命令启动容器：
   ```
   docker-compose up -d
   ```
3. 访问 `http://localhost:8080` 打开 Web UI。
   - 默认用户名：admin
   - 默认密码：adminadmin

## 5. 注意事项

- 首次使用时，请及时修改默认密码以确保安全。
- 可以根据需要调整 `PUID` 和 `PGID` 的值，以匹配您系统的用户权限。
- 如果 8080 端口已被占用，可以修改端口映射，例如改为 `8081:8080`。
- 定期备份 `/config` 目录以保存您的设置和数据。

## 6. 故障排除

- 如果容器无法启动，检查端口是否被占用或者卷挂载路径是否正确。
- 如果下载速度异常，可能需要在 qBittorrent 设置中调整连接数和带宽限制。
- 如果遇到权限问题，确保 `PUID` 和 `PGID` 设置正确，并检查挂载目录的权限。

使用这个增强版 qBittorrent，您可以享受到更多高级功能和优化，同时保持了 Docker 环境的简便性和可维护性。

---

# web端口修改

更改这个Docker Compose配置文件中的web端口。目前配置中的web端口是8080。如果您想更改它，需要修改两个地方：

1. 在`environment`部分:
   将`WEBUI_PORT=8080`改为您想要的端口号，例如：
   ```yaml
   - WEBUI_PORT=8888
   ```

2. 在`ports`部分:
   将`8080:8080`中的第一个8080改为您想要的端口号，例如：
   ```yaml
   - 8888:8080
   ```

这里是一个修改端口为8888的例子：

```yaml
version: "3"
services:
  qbittorrent-enhanced:
    image: superng6/qbittorrent:latest
    container_name: qbittorrent-enhanced
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Asia/Shanghai
      - WEBUI_PORT=8888
    volumes:
      - ./config:/config
      - ./downloads:/downloads
    ports:
      - 8888:8080
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

请注意，在`healthcheck`部分，`test`命令中的URL仍然使用8080端口。这是因为这个健康检查是在容器内部进行的，而容器内部的端口映射仍然是8080。

修改后，您可以通过新的端口（例如http://localhost:8888）来访问qBittorrent的Web界面。记得在修改配置后重新启动容器以使更改生效。
