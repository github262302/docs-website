# Ubuntu Docker 常用命令

## 启动容器
```bash
docker run -d --name myapp -p 8080:80 nginx  # 运行新容器
docker start myapp                           # 启动已停止容器
```

## 查看容器
```bash
docker ps      # 运行中的容器
docker ps -a   # 所有容器
```

## 停止与删除
```bash
docker stop myapp   # 优雅停止
docker kill myapp   # 强制停止
docker rm myapp     # 删除容器
docker rm -f myapp  # 强制删除
```

## 镜像管理
```bash
docker pull ubuntu   # 拉取镜像
docker images        # 查看镜像
docker rmi ubuntu    # 删除镜像
```

## Docker 服务管理
```bash
sudo systemctl start docker    # 启动 Docker 服务
sudo systemctl stop docker     # 停止 Docker 服务
sudo systemctl restart docker  # 重启 Docker 服务
sudo systemctl status docker   # 查看 Docker 状态
sudo systemctl enable docker   # 开机自启 Docker
```

参数：`-d` 后台运行，`-p` 端口映射，`--name` 命名。
