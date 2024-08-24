# Gost v3 在 Alpine Linux 上的完整安装、配置与管理指南

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
apk add --no-cache curl bash
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
   apk add --no-cache git go
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
   mv gost /usr/local/bin/
   ```

5. 验证：
   ```bash
   gost --version
   ```

## 2. 配置 Gost v3

创建配置文件：
```bash
mkdir -p /etc/gost
nano /etc/gost/config.json
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

1. 创建专用用户和组：
   ```bash
   addgroup -S gost
   adduser -S -H -s /sbin/nologin -G gost gost
   ```

2. 设置文件权限：
   ```bash
   chown gost:gost /usr/local/bin/gost
   chown -R gost:gost /etc/gost
   ```

3. 创建服务文件：
   ```bash
   nano /etc/init.d/gost
   ```
   
   服务文件内容：
   ```sh
   #!/sbin/openrc-run
   name="Gost Proxy Service"
   description="Gost Proxy Service"
   command="/usr/local/bin/gost"
   command_args="-C /etc/gost/config.json"
   command_user="gost:gost"
   pidfile="/run/${RC_SVCNAME}.pid"
   command_background="yes"
   
   depend() {
       need net
       after firewall
   }
   
   start_pre() {
       checkpath --directory --owner $command_user --mode 0755 /run
   }
   ```

4. 设置执行权限：
   ```bash
   chmod +x /etc/init.d/gost
   ```

## 4. 启动和管理服务

```bash
rc-update add gost default
rc-service gost start
rc-service gost status
```

## 5. 查看日志

Alpine Linux 默认使用 Busybox 的 syslog。您可以通过以下方式查看日志：

```bash
# 查看全部日志
cat /var/log/messages | grep gost

# 实时查看最新日志
tail -f /var/log/messages | grep gost

# 查看最近50行日志
tail -n 50 /var/log/messages | grep gost
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
mv gost /usr/local/bin/
rc-service gost restart
```

## 8. 卸载 Gost v3

1. 停止并删除服务：
   ```bash
   rc-service gost stop
   rc-update del gost
   rm /etc/init.d/gost
   ```

2. 删除可执行文件：
   ```bash
   rm /usr/local/bin/gost
   ```

3. 删除配置文件和目录：
   ```bash
   rm -rf /etc/gost
   ```

4. 删除专用用户和组：
   ```bash
   deluser gost
   delgroup gost
   ```

5. 检查并终止残留进程：
   ```bash
   ps | grep gost
   # 如有需要，使用 kill 命令终止进程
   ```

注意：这个指南专门针对 Alpine Linux 进行了调整。某些命令和路径可能与其他 Linux 发行版不同。如果您在使用过程中遇到任何问题，请随时寻求帮助或查阅 Alpine Linux 的官方文档。
