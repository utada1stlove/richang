# Gost v3 安装与配置完整教程

## 目录

1. [安装 Gost v3](#1-安装-gost-v3)
   - [方法A：使用官方安装脚本（推荐）](#方法a使用官方安装脚本推荐)
   - [方法B：从源码编译安装](#方法b从源码编译安装)
2. [配置 Gost v3](#2-配置-gost-v3)
3. [创建系统服务](#3-创建系统服务)
4. [启动和管理服务](#4-启动和管理服务)
5. [查看日志](#5-查看日志)
6. [更新 Gost v3](#6-更新-gost-v3)
7. [故障排除](#7-故障排除)

## 1. 安装 Gost v3

### 方法A：使用官方安装脚本（推荐）

Gost 提供了官方安装脚本，这是安装 Gost 最简单和最可靠的方法。您有两个选项：

#### 选项 1：安装最新版本

要安装最新版本的 Gost，请运行以下命令：

```bash
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh) --install
```

这个命令会自动下载并安装最新版本的 Gost。

#### 选项 2：选择特定版本安装

如果您想选择安装特定版本的 Gost，请运行以下命令：

```bash
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh)
```

这个命令会提供一个交互式界面，让您选择想要安装的具体版本。

#### 验证安装

安装完成后，您可以使用以下命令验证 Gost 是否正确安装：

```bash
gost --version
```

这应该会显示已安装的 Gost 版本信息。

### 方法B：从源码编译安装

如果您希望从源码编译安装 Gost，请按照以下步骤操作：

1. 确保您的系统已安装 Git 和 Go 编程语言环境。如果没有，请先安装它们：

   ```bash
   # 在 Ubuntu/Debian 系统上
   sudo apt-get update
   sudo apt-get install git golang-go

   # 在 CentOS/RHEL 系统上
   sudo yum install git golang
   ```

2. 克隆 Gost 仓库：

   ```bash
   git clone https://github.com/go-gost/gost.git
   ```

3. 进入 Gost 目录：

   ```bash
   cd gost/cmd/gost
   ```

4. 编译 Gost：

   ```bash
   go build
   ```

5. 编译完成后，您可以将生成的 `gost` 可执行文件移动到系统路径中：

   ```bash
   sudo mv gost /usr/local/bin/
   ```

6. 验证安装：

   ```bash
   gost --version
   ```

## 2. 配置 Gost v3

Gost v3 使用 JSON 格式的配置文件。以下是一个基本的配置文件示例：

```bash
sudo mkdir /etc/gost
sudo nano /etc/gost/config.json
```

将以下内容粘贴到文件中（根据您的需求修改）：

```json
{
  "services": [
    {
      "name": "service-0",
      "addr": ":8080",
      "handler": {
        "type": "http"
      },
      "listener": {
        "type": "tcp"
      }
    }
  ],
  "chains": [
    {
      "name": "chain-0",
      "hops": [
        {
          "name": "hop-0",
          "nodes": [
            {
              "name": "node-0",
              "addr": "example.com:8123",
              "connector": {
                "type": "http"
              },
              "dialer": {
                "type": "tcp"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

这个配置文件设置了一个本地 HTTP 代理服务器，监听在 8080 端口，并将流量转发到 `example.com:8123`。

## 3. 创建系统服务

为了使 Gost v3 在系统启动时自动运行，我们创建一个 systemd 服务文件：

```bash
sudo nano /etc/systemd/system/gost.service
```

将以下内容粘贴到文件中：

```ini
[Unit]
Description=Gost Proxy Service
After=network.target
Wants=network-online.target

[Service]
Type=simple
User=nobody
Group=nogroup
ExecStart=/usr/local/bin/gost -C /etc/gost/config.json
Restart=always
RestartSec=5

# 安全设置
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=full
ProtectHome=true

[Install]
WantedBy=multi-user.target
```

## 4. 启动和管理服务

设置好服务文件后，我们可以启动并启用服务：

```bash
# 重新加载systemd管理器配置
sudo systemctl daemon-reload

# 启用服务，使其在启动时自动运行
sudo systemctl enable gost.service

# 启动服务
sudo systemctl start gost.service

# 检查服务状态
sudo systemctl status gost.service
```

## 5. 查看日志

要查看 Gost v3 服务的日志，可以使用以下命令：

```bash
# 查看全部日志
sudo journalctl -u gost.service

# 实时查看最新日志
sudo journalctl -f -u gost.service

# 查看最近50行日志
sudo journalctl -u gost.service -n 50
```

## 6. 更新 Gost v3

### 使用官方脚本更新

要更新 Gost v3 到最新版本，最简单的方法是重新运行官方安装脚本：

```bash
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh) --install
```

如果您想选择特定版本进行更新，可以运行：

```bash
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh)
```

### 从源码更新

如果您是从源码编译安装的，可以按照以下步骤更新：

1. 进入 Gost 目录：

   ```bash
   cd path/to/gost
   ```

2. 拉取最新代码：

   ```bash
   git pull
   ```

3. 重新编译：

   ```bash
   cd cmd/gost
   go build
   ```

4. 替换旧的可执行文件：

   ```bash
   sudo mv gost /usr/local/bin/
   ```

5. 重启 Gost 服务：

   ```bash
   sudo systemctl restart gost
   ```

## 7. 故障排除

如果遇到问题，请检查以下几点：

1. 确保 Gost 可执行文件路径正确。
2. 检查配置文件是否正确。
3. 检查端口是否被其他程序占用。
4. 确保防火墙允许相应的端口通信。
5. 查看系统日志以获取更多错误信息。

如果问题仍然存在，可以尝试手动运行 Gost 命令来查看更详细的错误输出：

```bash
/usr/local/bin/gost -C /etc/gost/config.json
```

---

希望这个完整的 Gost v3 教程对您有所帮助。如果您有任何问题或需要进一步的说明，请随时询问。