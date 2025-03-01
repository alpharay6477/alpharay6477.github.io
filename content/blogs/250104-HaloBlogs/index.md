---
title: 部署 Halo 博客 | 连接到外部 Docker postgres 数据库
summary: 部署 Halo 博客，并链接到不同 docker compose 部署的 `postgres SQL`​ 数据库。
tags:
  - Web
  - Html
date: 2025-01-04T19:22:20Z
categories:
  - Life
draft: false
isCJKLanguage: true
showAuthor: true
authors:
  - 葛世杰
---

部署 Halo 博客，并链接到不同 docker compose 部署的 `postgres SQL`​ 数据库（postgres SQL docker 是之前部署 lobe chat database 所建立的）。

## 一、配置数据库

在已有的 `postgres SQL`​ 创建新数据库 名称为 halo

```sh
docker exec -it lobe-postgres bash      # 进入 postgres SQL 容器
root@c885cce584ec:/# su postgres        # 切换用户
postgres@c885cce584ec:~$ psql           # 登录 postgre sql
psql (16.4 (Debian 16.4-1.pgdg120+2))
Type "help" for help.

postgres=# \list                        # 列出已有的数据库
postgres=# CREATE DATABASE halo;        # 建立新数据库
CREATE DATABASE     # 已经建立好数据库
```

查看之前 lobe chat database 中的环境变量得知：

```env
# Postgres 相关，也即 DB 必需的环境变量
# 必填，用于加密敏感信息的密钥，可以使用 openssl rand -base64 32 生成
KEY_VAULTS_SECRET=Kix******X51E/ZPAd36B********KjPtz2sGztqQ=
# 必填，Postgres 数据库连接字符串，用于连接到数据库
# 格式：postgresql://username:password@host:port/dbname，如果你的 pg 实例为 Docker 容器且位于同一 docker-compose 文件中，亦可使用容器名作为 host
DATABASE_URL=postgresql://postgres:uWNZ*****xf8dxC@postgresql:5432/lobe
```

已经建立的 Docker postgres 数据库的 docker-compose.yaml 文件，注意不能更改网络模式，否则原来的 lobe chat database 无法连接到 `postgres SQL`​ 数据库！

```yaml
services:
  postgresql:
    image: pgvector/pgvector:pg16
    container_name: lobe-postgres
    ports:
      - '5432:5432'                            # docker 容器的端口转发，使用 localhost:5432 可访问
    volumes:
      - './data:/var/lib/postgresql/data'
    environment:
      - 'POSTGRES_DB=lobe'   # 数据库名称
      - 'POSTGRES_PASSWORD=uWNZ******f8dxC'    # 密码（管理员 postgres 的密码）
    healthcheck:
      test: ['CMD-SHELL', 'pg_isready -U postgres']
      interval: 5s
      timeout: 5s
      retries: 5
    restart: always
```

## 二、参考文档部署 Halo博客

参考 Halo博客 [docker compose install 文档](https://docs.halo.run/getting-started/install/docker-compose?current=external-db) 

建立 Halo 博客容器，编辑 `docker-compose.yaml`​

```sh
services:
  halo:
    container_name: halo
    image: halohub/halo:2.20.13
    restart: on-failure:3
    network_mode: "host"
    volumes:
      - ./halo2:/root/.halo2
    environment:
      # JVM 参数，默认为 -Xmx256m -Xms256m，可以根据实际情况做调整，置空表示不添加 JVM 参数
      - JVM_OPTS=-Xmx256m -Xms256m
    command:
      # 修改为自己已有的 MySQL 配置
      - --spring.r2dbc.url=r2dbc:pool:postgresql://localhost:5432/halo  # 查看文档部署，对应参数
      - --spring.r2dbc.username=postgres
      - --spring.r2dbc.password=uWN*****f8dxC
      - --spring.sql.init.platform=postgresql
      # 外部访问地址，请根据实际需要修改
      - --halo.external-url=https://blog.alpharay.space/
      # 端口号 默认8090
      - --server.port=8090
```

> 格式：postgresql://username:password@host:port/dbname，如果你的 pg 实例为 Docker 容器且位于同一 docker-compose 文件中，亦可使用容器名作为 host  
> DATABASE_URL=postgresql://postgres:uW*******8dxC@postgresql:5432/lobe

> 如果 PostgreSQL 部署在本地机器上，host 可以是 localhost 或 127.0.0.1，表示本机。  
> 如果 PostgreSQL 部署在远程机器上，host 就是 PostgreSQL 服务器的 IP 地址或域名。  
> 如果 PostgreSQL 运行在 Docker 容器中，并且使用 Docker Compose 进行管理，host 是指容器的名称或 Docker Compose 文件中的服务名称。

然后使用 `docker compose up -d`​ 命令启用容器，访问服务器ip:8090 初始化网站，在 Nginx 中设置代理，Halo 博客部署完毕。

## 附：数据备份

对 dockers 挂载的 halo2 目录结构发现没有文章存放的位置，而网站的备份工具是将当前文件夹 zip

```yaml
./halo
└──attachments    # 附件
└──backups        # 备份
└──indices       # 索引
└──keys          # 密钥
└──logs          # 日志
└──plugins      # 插件
└──themes        # 主题，对 ./theme-earth/templates/modules/hero.html 编辑可进一步自定义主页样式
```

于是测试是否会备份存储在 SQL 数据库中的文章，先在控制台备份数据到本地，将 docker 和 database 重置，然后在控制台上传 backpack 文件恢复，发现所有数据包括文章已经得到恢复。

> 只重置 docker 不重置 SQL 数据库，访问网站会出现 404 报错。
> 
> docker 重置是删除容器，重命名挂载文件夹；SQL 数据库重置是删除数据库然后新建同名数据库。
