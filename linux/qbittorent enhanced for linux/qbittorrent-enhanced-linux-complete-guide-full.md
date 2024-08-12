# qBittorrent增强版Linux完整指南（详细版）

## 1. 安装

### 1.1 安装依赖
```bash
sudo apt update
sudo apt install build-essential pkg-config automake libtool git zlib1g-dev libssl-dev libgeoip-dev libboost-dev libboost-system-dev libboost-chrono-dev libboost-random-dev libtorrent-rasterbar-dev qtbase5-dev qttools5-dev-tools libqt5svg5-dev
```

### 1.2 获取源码
```bash
git clone https://github.com/c0re100/qBittorrent-Enhanced-Edition.git
cd qBittorrent-Enhanced-Edition
```

### 1.3 编译安装
```bash
./configure
make
sudo make install
```

## 2. 创建专用用户账户

### 2.1 创建新用户
```bash
sudo adduser --system --group qbittorrent
```

### 2.2 创建必要目录
```bash
sudo mkdir -p /var/lib/qbittorrent/Downloads
sudo mkdir -p /var/lib/qbittorrent/.config/qBittorrent
```

### 2.3 设置权限
```bash
sudo chown -R qbittorrent:qbittorrent /var/lib/qbittorrent
sudo chmod -R 755 /var/lib/qbittorrent
```

## 3. 设置系统服务

### 3.1 创建服务文件
```bash
sudo nano /etc/systemd/system/qbittorrent.service
```

添加以下内容：
```
[Unit]
Description=qBittorrent Enhanced Edition Service
After=network.target

[Service]
Type=simple
User=qbittorrent
Group=qbittorrent
ExecStart=/usr/local/bin/qbittorrent-nox
Restart=on-failure
Environment="HOME=/var/lib/qbittorrent"

[Install]
WantedBy=multi-user.target
```

### 3.2 重新加载systemd配置
```bash
sudo systemctl daemon-reload
```

### 3.3 启用并启动服务
```bash
sudo systemctl enable qbittorrent.service
sudo systemctl start qbittorrent.service
```

### 3.4 检查服务状态
```bash
sudo systemctl status qbittorrent.service
```

## 4. 配置 qBittorrent

### 4.1 停止服务
```bash
sudo systemctl stop qbittorrent.service
```

### 4.2 生成密码哈希
```bash
qbittorrent-nox --hashed-password
```
输入您想要的密码，记录生成的哈希值。

### 4.3 编辑配置文件
```bash
sudo nano /var/lib/qbittorrent/.config/qBittorrent/qBittorrent.conf
```

添加或修改以下设置：
```
[LegalNotice]
Accepted=true

[Preferences]
WebUI\Port=8080
WebUI\Username=admin
WebUI\Password_ha1=您生成的密码哈希

[BitTorrent]
Session\DefaultSavePath=/var/lib/qbittorrent/Downloads
```

### 4.4 重启服务
```bash
sudo systemctl start qbittorrent.service
```

## 5. 使用

- 通过Web UI管理：访问 `http://your_server_ip:8080`
- 使用您设置的用户名和密码登录

## 6. 更新

### 6.1 停止服务
```bash
sudo systemctl stop qbittorrent.service
```

### 6.2 更新源码
```bash
cd qBittorrent-Enhanced-Edition
git pull
```

### 6.3 重新编译安装
```bash
./configure
make
sudo make install
```

### 6.4 重启服务
```bash
sudo systemctl start qbittorrent.service
```

## 7. 卸载

### 7.1 停止并禁用服务
```bash
sudo systemctl stop qbittorrent.service
sudo systemctl disable qbittorrent.service
```

### 7.2 删除服务文件
```bash
sudo rm /etc/systemd/system/qbittorrent.service
```

### 7.3 卸载软件
```bash
cd qBittorrent-Enhanced-Edition
sudo make uninstall
```

### 7.4 删除配置和下载目录
```bash
sudo rm -rf /var/lib/qbittorrent
```

### 7.5 删除专用用户
```bash
sudo userdel qbittorrent
```

### 7.6 删除源码目录
```bash
cd ..
rm -rf qBittorrent-Enhanced-Edition
```

## 8. 高级配置

### 8.1 设置防火墙
如果您使用 UFW：
```bash
sudo ufw allow 8080/tcp
```

### 8.2 配置反向代理（可选，使用 Nginx）
安装 Nginx：
```bash
sudo apt install nginx
```

创建 Nginx 配置文件：
```bash
sudo nano /etc/nginx/sites-available/qbittorrent
```

添加以下内容：
```nginx
server {
    listen 80;
    server_name your_domain.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

启用配置：
```bash
sudo ln -s /etc/nginx/sites-available/qbittorrent /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## 9. 故障排除

### 9.1 查看日志
```bash
sudo journalctl -u qbittorrent.service
```

### 9.2 检查权限
```bash
ls -l /var/lib/qbittorrent
```

### 9.3 测试网络连接
```bash
curl http://localhost:8080
```

## 10. 注意事项

1. 定期备份配置：
   ```bash
   sudo cp -r /var/lib/qbittorrent/.config/qBittorrent /path/to/backup/
   ```

2. 监控磁盘空间：
   ```bash
   df -h /var/lib/qbittorrent/Downloads
   ```

3. 更新系统和依赖：
   ```bash
   sudo apt update && sudo apt upgrade
   ```

4. 检查 qBittorrent 版本：
   ```bash
   qbittorrent-nox -v
   ```

5. 如需修改配置，总是先停止服务，修改后再启动服务。

6. 使用 `sudo -u qbittorrent` 来以 qbittorrent 用户身份运行命令，例如：
   ```bash
   sudo -u qbittorrent qbittorrent-nox --help
   ```

7. 定期检查 qBittorrent 的 GitHub 页面以获取最新的更新和安全公告。

---

这个完整版指南涵盖了从安装到高级配置的所有方面，包括：

1. 详细的安装步骤
2. 创建专用用户账户
3. 设置系统服务
4. 配置 qBittorrent
5. 基本使用说明
6. 更新过程
7. 完整的卸载步骤
8. 高级配置选项（如防火墙设置和反向代理）
9. 故障排除建议
10. 重要的注意事项和最佳实践

这个指南应该能够满足大多数用户的需求，从初学者到高级用户都能找到有用的信息。它提供了一个安全、高效的方式来设置和管理 qBittorrent 增强版。

