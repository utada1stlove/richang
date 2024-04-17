# 开启 Docker 服务
docker-compose -f docker-compose.yml -f docker-compose-db.yml -f docker-compose-local.yml up -d --remove-orphans

# 关闭 Docker 容器们
docker-compose -f docker-compose.yml -f docker-compose-db.yml -f docker-compose-local.yml down