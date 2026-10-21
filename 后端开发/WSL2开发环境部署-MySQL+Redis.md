---
tags:
  - wsl
  - wsl2
  - mysql
  - redis
  - docker
  - 开发环境
created: 2026-05-14
updated: 2026-05-14
author: kuma
---
# WSL2开发环境部署-MySQL+Redis
## 1. 确认环境

确保 Docker Desktop 已启用 WSL2 后端（Settings → General → "Use WSL 2 based engine"），并在 Resources → WSL Integration 中启用你使用的 WSL 发行版。

## 2. Docker Compose 配置文件

创建 docker-compose.yml：
```
services:

  mysql:

    image: mysql:8.0

    container_name: dev-mysql

    environment:

      MYSQL_ROOT_PASSWORD: root123  # 请替换为你的MySQL root密码

      MYSQL_DATABASE: dev_db        # 初始化时自动创建的数据库名

    ports:

      - "3306:3306"

    volumes:

      - ./mysql_data:/var/lib/mysql    # 数据持久化到本地目录

    restart: always

    command: --default-authentication-plugin=mysql_native_password

  redis:

    image: redis:7

    container_name: dev-redis

    ports:

      - "6379:6379"

    volumes:

      - ./redis_data:/data                      # 数据持久化

    command: redis-server --appendonly yes      # 开启AOF持久化

    restart: always
```


## 3. 常用操作命令

### 基础管理

| 操作   | 命令                     | 说明       |
| ---- | ---------------------- | -------- |
| 启动   | docker-compose up -d   | 后台启动所有服务 |
| 停止   | docker-compose stop    | 停止容器，不删除 |
| 重启   | docker-compose restart | 重启所有服务   |
| 查看状态 | docker-compose ps      | 查看运行状态   |
| 查看日志 | docker-compose logs -f | 持续跟踪日志   |
| 拉取镜像 | docker-compose pull    | 拉取最新镜像   |

### 完整生命周期

| 操作 | 命令 | 说明 |
| --- | --- | --- |
| 启动+重建 | docker-compose up -d --build | 重新构建镜像后启动 |
| 完全停止 | docker-compose down | 停止并删除容器，保留 volume |
| 彻底清理 | docker-compose down -v | 停止+删除容器和 volume，数据丢失 |
| 查看资源 | docker-compose top | 查看各容器内进程 |

### 单服务操作

```
docker-compose restart mysql
docker-compose stop redis
docker-compose logs -f mysql
```

### 日常开发流程

```
# 首次部署
docker-compose up -d

# 改完配置后重新加载
docker-compose restart

# 下班前
docker-compose stop

# 第二天继续
docker-compose start

# 需要清理重建
docker-compose down
docker-compose up -d
```

## 4. Win10 连接配置

WSL2 IP 会变化，推荐用 localhost 连接，Docker Desktop 会把 WSL2 端口映射到 Windows localhost：

| 服务 | 主机 | 端口 |
| --- | --- | --- |
| MySQL | localhost | 3306 |
| Redis | localhost | 6379 |

### 连接示例

MySQL（命令行测试）：

```
mysql -h 127.0.0.1 -P 3306 -u root -proot123
```

Redis（命令行测试）：

```
redis-cli -h 127.0.0.1 -p 6379 ping
# 返回 PONG 即成功
```

## 5. 版本选择说明

### MySQL：推荐 mysql:8.0

| 原因 | 说明 |
| --- | --- |
| 稳定成熟 | 8.0 已发布多年，生态完善，生产验证充分 |
| 兼容性好 | 框架、ORM、驱动对 8.0 支持最成熟 |
| LTS 性质 | Oracle 对 8.0 提供长期支持 |
| 避免坑 | 8.4 是创新版非 LTS，9.0 更新驱动兼容性问题多 |

### Redis：推荐 redis:7-alpine

| 原因        | 说明                                  |     |
| --------- | ----------------------------------- | --- |
| 最新稳定      | Redis 7 功能完整（ACL、sharded pub/sub 等） |     |
| Alpine 镜像 | 体积小约 30MB，本地开发启动快                   |     |
| 兼容性好      | 主流客户端都支持 Redis 6/7                  |     |

### 不建议的版本

- mysql:latest 现在指向 9.0，驱动/ORM 兼容性有风险
- mysql:5.7 已 EOL（2023年结束支持），安全更新不再
- redis:6 功能少且不再积极维护，没必要

## 6. 别名建议（WSL2）

在 WSL2 ~/.bashrc 或 ~/.zshrc 中添加：

```
alias dc='docker-compose'
alias dcup='docker-compose up -d'
alias dcdn='docker-compose down'
alias dcstop='docker-compose stop'
alias dcrestart='docker-compose restart'
alias dclogs='docker-compose logs -f'
```
## 7. 网络等处理
### 使用代理
1.在dockerd service+里设置环境变量http_proxy和https_proxyt
2.在docker pull的命令行里设置环境变量
### 镜像
1. docker容器镜像监控网站 https://status.anye.xyz/
2. 2.镜像站 https://mirror.kentxxq.com/image
### 端口占用
```powershell
Cannot start Docker Compose application. Reason: compose [start] exit status 1. Container dev-redis Starting Container dev-mysql Starting Container dev-redis Started Error response from daemon: ports are not available: exposing port TCP 127.0.0.1:3306 -> 127.0.0.1:0: /forwards/expose returned unexpected status: 500
```

以管理员身份运行以下命令，重启 Windows NAT 服务来重置端口分配：

```powershell
net stop winnat
netsh int ipv4 reset (可选，彻底重置)
net start winnat
```