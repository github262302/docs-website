# Nginx 默认配置与使用

这份笔记整理 Nginx 在 Ubuntu 上的默认目录结构、常见配置方式，以及一个常见的反向代理模板。

## Ubuntu 下的默认路径

安装后常见目录：

- `/etc/nginx/nginx.conf`：主配置文件。
- `/etc/nginx/sites-available/`：站点配置模板目录。
- `/etc/nginx/sites-enabled/`：已启用站点的软链接目录。
- `/var/log/nginx/access.log`：访问日志。
- `/var/log/nginx/error.log`：错误日志。
- `/usr/share/nginx/html`：默认静态页面目录。

## 默认主配置文件

Ubuntu/Debian 系常见的 `/etc/nginx/nginx.conf` 大致如下：

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    types_hash_max_size 2048;
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log;

    gzip on;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

关注点：

- `events`：连接相关参数。
- `http`：HTTP 层全局配置。
- `include /etc/nginx/sites-enabled/*;`：实际业务站点通常从这里加载。

## 默认站点配置

Ubuntu 常见默认站点文件：

- `/etc/nginx/sites-available/default`

示例结构：

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

如果服务器只部署自己的业务，通常会新建一个独立站点配置，而不是长期直接改 `default`。

## 反向代理 Node.js 示例

```bash
sudo nano /etc/nginx/sites-available/myapp
```

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

启用站点：

```bash
sudo ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## 静态站点配置示例

```nginx
server {
    listen 80;
    server_name static.example.com;

    root /var/www/static-site;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

适合前端单页应用，例如 Vue、React 构建后的静态资源部署。

## HTTPS 基本思路

如果用 Let's Encrypt：

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d example.com -d www.example.com
```

证书续签测试：

```bash
sudo certbot renew --dry-run
```

## Nginx 常用命令

```bash
nginx -t
nginx -T
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl status nginx
```

说明：

- `nginx -t`：检查配置语法。
- `nginx -T`：输出完整配置，排查 include 时很好用。
- `reload`：平滑重载，优先于重启。

## 日志排查

```bash
tail -f /var/log/nginx/access.log
tail -f /var/log/nginx/error.log
```

常见场景：

- 502：上游应用没启动、端口错了、权限问题。
- 403：目录权限、root 路径、index 文件问题。
- 404：路由或静态资源路径不对。

## 推荐维护习惯

1. 修改配置后先跑 `nginx -t`。
2. 能 `reload` 就不要直接 `restart`。
3. 每个站点一个单独配置文件，方便迁移和排查。
4. 上游服务端口只监听本机时，优先走 `127.0.0.1`。
5. 配置证书和反向代理时，把日志排查流程一起固定下来。