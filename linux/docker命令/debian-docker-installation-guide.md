# Debian Docker和Docker Compose安装指南

本指南将介绍在Debian系统上安装Docker和Docker Compose的多种方法。我们将涵盖安全安装、脚本安装,以及单独安装Docker Compose的步骤。

## 1. Docker安全安装

1. 更新包索引并安装必要的依赖:
   ```bash
   sudo apt-get update
   sudo apt-get install ca-certificates curl gnupg
   ```

2. 添加Docker的官方GPG密钥:
   ```bash
   sudo install -m 0755 -d /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   sudo chmod a+r /etc/apt/keyrings/docker.gpg
   ```

3. 设置Docker的软件源:
   ```bash
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
     $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

4. 更新包索引:
   ```bash
   sudo apt-get update
   ```

5. 安装Docker:
   ```bash
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

6. 验证安装:
   ```bash
   sudo docker run hello-world
   ```

## 2. Docker脚本安装

1. 下载并运行安装脚本:
   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh
   ```

2. 验证Docker安装:
   ```bash
   sudo docker run hello-world
   ```

注意: 从安全角度考虑,建议在运行脚本前仔细检查其内容。

## 3. Docker Compose安装

### 方法1: 作为Docker插件安装（推荐）

如果您使用上述方法安装了Docker,Docker Compose可能已经作为插件安装好了。验证安装:

```bash
docker compose version
```

如果需要单独安装或更新Docker Compose插件:

```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin
```

### 方法2: 手动安装最新版本

1. 下载Docker Compose:
   ```bash
   DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
   mkdir -p $DOCKER_CONFIG/cli-plugins
   curl -SL https://github.com/docker/compose/releases/download/v2.17.2/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
   ```

2. 添加执行权限:
   ```bash
   chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
   ```

3. 验证安装:
   ```bash
   docker compose version
   ```

## 安全注意事项

1. 只从官方源安装Docker。
2. 定期更新Docker到最新版本。
3. 使用非root用户运行Docker:
   ```bash
   sudo usermod -aG docker $USER
   ```
4. 使用Docker内容信任来验证镜像。
5. 使用Docker安全扫描工具检查容器和镜像的漏洞。

按照本指南安装后,您应该能够在Debian系统上安全地使用Docker和Docker Compose了。如有任何问题,请随时询问。
