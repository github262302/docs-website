---
title: Docker 与 Dockerfile 使用
category: devops
date: 2025-04-01
tags: []
---

# Docker 与 Dockerfile 使用

这份笔记整理 Docker 常用操作、镜像构建思路，以及 Dockerfile 的常见写法。

## Docker 基础概念

- `image`：镜像，应用运行模板。
- `container`：容器，镜像运行后的实例。
- `volume`：数据卷，用于持久化。
- `network`：容器网络。
- `Dockerfile`：定义镜像构建步骤的文件。

## 常用 Docker 命令

### 查看状态

```bash
docker version
docker info
docker ps
docker ps -a
docker images
docker volume ls
docker network ls
```

### 拉取与运行

```bash
docker pull nginx:latest
docker run -d --name web -p 8080:80 nginx:latest
```

### 进入容器

```bash
docker exec -it web /bin/bash
```

有些精简镜像没有 `bash`，改用：

```bash
docker exec -it web /bin/sh
```

### 查看日志

```bash
docker logs web
docker logs -f --tail=100 web
```

### 停止与删除

```bash
docker stop web
docker rm web
docker rmi nginx:latest
```

## 常见运行参数

```bash
docker run -d \
  --name app \
  --restart unless-stopped \
  -p 3000:3000 \
  -e NODE_ENV=production \
  -v /data/app/logs:/app/logs \
  myapp:latest
```

说明：

- `--restart unless-stopped`：推荐用于生产环境自动拉起。
- `-p`：宿主机端口映射到容器端口。
- `-e`：传环境变量。
- `-v`：挂载目录或卷。

## Dockerfile 基础结构

一个典型 Dockerfile：

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --omit=dev

COPY . .

EXPOSE 3000

CMD ["node", "server.js"]
```

执行构建：

```bash
docker build -t myapp:latest .
```

## Dockerfile 常见指令

- `FROM`：基础镜像。
- `WORKDIR`：工作目录。
- `COPY`：复制文件到镜像。
- `RUN`：构建阶段执行命令。
- `ENV`：设置环境变量。
- `EXPOSE`：声明容器监听端口。
- `CMD`：容器默认启动命令。
- `ENTRYPOINT`：固定入口命令。

## Node.js 项目示例

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --omit=dev

COPY . .

ENV NODE_ENV=production
EXPOSE 3000

CMD ["npm", "start"]
```

## 前端静态站点示例

如果是前端构建产物，可以用两阶段构建。

```dockerfile
FROM node:20-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:stable-alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

优点：

- 最终镜像更小。
- 不需要把 Node.js 运行时带进生产镜像。

## Dockerfile 编写建议

1. 优先选择体积较小的基础镜像，例如 `alpine`。
2. 把依赖安装步骤前置，利用构建缓存。
3. 使用 `.dockerignore` 排除 `node_modules`、日志、Git 文件。
4. 尽量一个容器只跑一个主进程。
5. 生产镜像不要把调试文件和源码垃圾一起打进去。

## .dockerignore 示例

```gitignore
node_modules
npm-debug.log
.git
.gitignore
Dockerfile
.env
dist
coverage
```

注意：是否忽略 `dist` 要根据项目类型判断。如果镜像里需要构建产物，就不要误排除。

## 数据持久化

数据库、上传文件、日志等不要直接依赖容器层，应该挂载卷：

```bash
docker run -d \
  --name mysql \
  -v /data/mysql:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=strongpassword \
  mysql:8
```

## 清理与维护

```bash
docker system df
docker image prune
docker container prune
docker volume prune
docker system prune
```

生产环境不要直接无脑执行：

```bash
docker system prune -a
```

因为这会删除所有未被使用的镜像，可能影响回滚。

## 常见问题排查

### 容器反复重启

```bash
docker ps -a
docker logs 容器名
docker inspect 容器名
```

### 端口不通

```bash
docker port 容器名
ss -tulpn
ufw status
```

### 容器内存限制

```bash
docker run -d --memory=512m --cpus=1 myapp:latest
```

如果容器被 OOM kill，通常要同时检查应用本身、Docker 限制、宿主机可用内存。