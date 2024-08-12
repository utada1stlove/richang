# Gost v3 完整安装、配置与管理指南

## 目录

1. [安装 Gost v3](#1-安装-gost-v3)
2. [配置 Gost v3](#2-配置-gost-v3)
3. [创建系统服务](#3-创建系统服务)
4. [启动和管理服务](#4-启动和管理服务)
5. [查看日志](#5-查看日志)
6. [故障排除](#6-故障排除)
7. [更新 Gost v3](#7-更新-gost-v3)
8. [卸载 Gost v3](#8-卸载-gost-v3)

## 1. 安装 Gost v3

### 方法A：使用官方安装脚本（推荐）

#### 安装最新版本

```bash
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh) --install
```

#### 选择特定版本安装

```bash
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh)
```

#### 验证安装

```bash
gost --version
```

### 方法B：从源码编译安装

1. 安装依赖：

   ```bash
   # Ubuntu/Debian
   sudo apt-get update
   sudo apt-get install git golang-go

   # CentOS/RHEL
   sudo yum install git golang
   ```

2. 克隆仓库：

   ```bash
   git clone https://github.com/go-gost/gost.git
   ```

3. 编译：

   ```bash
   cd gost/cmd/gost
   go build
   ```

4. 安装：

   ```bash
   sudo mv gost /usr/local/bin/
   ```

5. 验证：

   ```bash
   gost --version
   ```

## 2. 配置 Gost v3

创建配置文件：

```bash
sudo mkdir /etc/gost
sudo nano /etc/gost/config.json
```

配置文件示例：

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

## 3. 创建系统服务

1. 创建专用用户：

   ```bash
   sudo useradd -r -s /bin/false gost
   ```

2. 设置文件权限：

   ```bash
   sudo chown gost:gost /usr/local/bin/gost
   sudo chown -R gost:gost /etc/gost
   ```

3. 创建服务文件：

   ```bash
   sudo nano /etc/systemd/system/gost.service
   ```

   服务文件内容：

   ```ini
   [Unit]
   Description=Gost Proxy Service
   After=network.target
   Wants=network-online.target

   [Service]
   Type=simple
   User=gost
   Group=gost
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

```bash
sudo systemctl daemon-reload
sudo systemctl enable gost.service
sudo systemctl start gost.service
sudo systemctl status gost.service
```

## 5. 查看日志

```bash
# 查看全部日志
sudo journalctl -u gost.service

# 实时查看最新日志
sudo journalctl -f -u gost.service

# 查看最近50行日志
sudo journalctl -u gost.service -n 50
```

## 6. 故障排除

1. 检查可执行文件路径
2. 验证配置文件
3. 检查端口占用
4. 确认防火墙设置
5. 查看系统日志

手动运行 Gost 查看错误：

```bash
/usr/local/bin/gost -C /etc/gost/config.json
```

## 7. 更新 Gost v3

### 使用官方脚本更新

```bash
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh) --install
```

### 从源码更新

```bash
cd path/to/gost
git pull
cd cmd/gost
go build
sudo mv gost /usr/local/bin/
sudo systemctl restart gost
```

## 8. 卸载 Gost v3

1. 停止并禁用服务：

   ```bash
   sudo systemctl stop gost.service
   sudo systemctl disable gost.service
   ```

2. 删除服务文件：

   ```bash
   sudo rm /etc/systemd/system/gost.service
   sudo systemctl daemon-reload
   ```

3. 删除可执行文件：

   ```bash
   sudo rm /usr/local/bin/gost
   ```

4. 删除配置文件和目录：

   ```bash
   sudo rm -rf /etc/gost
   ```

5. 删除专用用户：

   ```bash
   sudo userdel gost
   ```

6. 检查并终止残留进程：

   ```bash
   ps aux | grep gost
   # 如有需要，使用 kill 命令终止进程
   ```
