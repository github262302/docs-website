---
title: Linux 包管理工具使用与区别
category: devops
date: 2025-04-01
tags: []
---

# Linux 包管理工具使用与区别 📦🐧

这份笔记用于快速看懂常见 Linux 包管理工具的用途、区别和基本命令。

核心结论先放前面：

- Ubuntu / Debian 系：主要用 `apt`
- CentOS / RHEL / Rocky / AlmaLinux 新版本：主要用 `dnf`
- Alpine：主要用 `apk`
- FreeBSD：主要用 `pkg`
- Arch Linux：主要用 `pacman`

如果你只维护 Ubuntu 服务器，先重点掌握 `apt` 就够了。其他工具主要是跨发行版时需要认得、会查、会装。

## 👀 一眼看懂区别

| 工具       | 常见系统                           | 包格式              | 特点                 | 适合记住的定位      |
| -------- | ------------------------------ | ---------------- | ------------------ | ------------ |
| `apt`    | Ubuntu、Debian                  | `.deb`           | 最常见，资料多，适合服务器和桌面   | Debian 系标准工具 |
| `dnf`    | Fedora、Rocky、AlmaLinux、RHEL 新版 | `.rpm`           | 依赖处理比旧版 `yum` 更好   | RHEL 系主力工具   |
| `yum`    | CentOS 7、老 RHEL                | `.rpm`           | 老系统常见，很多旧教程还在用     | 老 RHEL 系工具   |
| `pkg`    | FreeBSD                        | FreeBSD packages | 不是 Linux，但服务器里也常遇到 | BSD 包管理工具    |
| `apk`    | Alpine Linux                   | `.apk`           | 轻量、快，Docker 镜像里很常见 | Alpine 专用    |
| `pacman` | Arch Linux                     | Arch packages    | 简洁直接，滚动更新          | Arch 专用      |

最重要的不是死记命令，而是先认出系统属于哪一派。

## 🧭 先确认服务器用哪个工具

先看系统版本：

```bash
cat /etc/os-release
```

看输出里的这些字段：

- `ID=ubuntu`、`ID=debian`：优先用 `apt`
- `ID="rhel"`、`ID=rocky`、`ID=almalinux`、`ID=fedora`：优先用 `dnf`
- `ID=centos` 且版本较老：可能还在用 `yum`
- `ID=alpine`：用 `apk`

也可以直接试：

```bash
which apt
which dnf
which yum
which apk
which pkg
```

哪个存在，就说明系统里装了哪个工具。但真正判断时，还是以系统发行版为准，不要乱装不属于该系统的软件包格式。

## 🟠 `apt` 的使用

### 🖥️ 适用系统

- Ubuntu
- Debian
- 很多基于 Debian 的发行版

### 🧪 最常用命令

更新软件源索引：

```bash
sudo apt update
```

说明：

- 这是“更新可安装软件列表”，不是升级软件本身。
- 新机器装软件前通常先执行一次。

升级已安装软件：

```bash
sudo apt upgrade -y
```

安装软件：

```bash
sudo apt install -y nginx
sudo apt install -y git curl wget
```

删除软件但保留配置：

```bash
sudo apt remove -y nginx
```

删除软件和配置：

```bash
sudo apt purge -y nginx
```

清理不再需要的依赖：

```bash
sudo apt autoremove -y
```

清理缓存：

```bash
sudo apt clean
```

搜索软件：

```bash
apt search nginx
```

查看包信息：

```bash
apt show nginx
```

查看某个命令属于哪个包：

```bash
dpkg -S /usr/bin/ssh
```

查看已安装包：

```bash
dpkg -l | grep nginx
```

### 🛠️ 实战理解

`apt` 常见工作流：

```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

判断有没有装成功：

```bash
nginx -v
systemctl status nginx
```

### ⚠️ 容易搞混的点

- `apt update` 不是升级系统，只是刷新软件列表。
- `apt upgrade` 才是升级已安装软件。
- `apt remove` 和 `apt purge` 的区别在于是否删配置。
- Ubuntu 里平时优先用 `apt`，底层仍然是 `dpkg` 在干活。

## 🔴 `dnf` 的使用

### 🖥️ 适用系统

- Fedora
- Rocky Linux
- AlmaLinux
- RHEL 8 / 9
- 其他新一代 RHEL 系发行版

### 🧪 最常用命令

刷新仓库元数据：

```bash
sudo dnf makecache
```

检查更新：

```bash
sudo dnf check-update
```

升级软件：

```bash
sudo dnf upgrade -y
```

安装软件：

```bash
sudo dnf install -y nginx
sudo dnf install -y git curl wget
```

删除软件：

```bash
sudo dnf remove -y nginx
```

搜索软件：

```bash
dnf search nginx
```

查看包信息：

```bash
dnf info nginx
```

列出已安装包：

```bash
dnf list installed
```

查看软件由哪个包提供：

```bash
dnf provides /usr/sbin/nginx
```

清理缓存：

```bash
sudo dnf clean all
```

### 🛠️ 实战理解

`dnf` 的习惯和 `apt` 很像，但命令名称不完全一样：

```bash
sudo dnf install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

### 🔄 和 `yum` 的关系

- `yum` 是老一代 RHEL 系工具。
- `dnf` 可以理解为更新一代的 `yum`。
- 新系统优先记 `dnf`，老教程里看到 `yum` 不要慌，很多操作思路是一样的。

## 🟤 `yum` 的使用

### 🖥️ 适用系统

- CentOS 7
- 老版本 RHEL

### 🧪 常用命令

```bash
sudo yum makecache
sudo yum install -y nginx
sudo yum update -y
sudo yum remove -y nginx
yum search nginx
yum info nginx
```

### 🕰️ 什么时候你还需要认得它

- 维护老项目老机器时。
- 看旧教程、旧脚本时。
- 某些企业内网环境还没升级到新系统时。

如果服务器是新装的 Rocky / Alma / RHEL 8+，优先学 `dnf`，不要把精力放在 `yum` 上。

## 🔵 `pkg` 的使用

### ℹ️ 先说明白

`pkg` 大多数时候指 FreeBSD 的包管理工具，不是 Ubuntu、CentOS 那一套。

如果你看到服务器里用 `pkg`，大概率这台机器不是 Linux，而是 FreeBSD。

### 🧪 常用命令

更新仓库：

```bash
sudo pkg update
```

升级软件：

```bash
sudo pkg upgrade -y
```

安装软件：

```bash
sudo pkg install -y nginx
```

删除软件：

```bash
sudo pkg delete -y nginx
```

搜索软件：

```bash
pkg search nginx
```

查看已安装包：

```bash
pkg info
pkg info nginx
```

### 🧠 适合理解成什么

- 它和 `apt` / `dnf` 属于“同类问题的不同系统答案”。
- 用法也不难，但场景不是 Ubuntu 服务器，而是 BSD 机器。

## 🟢 `apk` 的使用

### 🖥️ 适用系统

- Alpine Linux
- 很多精简 Docker 镜像

### 🧪 常用命令

更新索引：

```bash
sudo apk update
```

升级软件：

```bash
sudo apk upgrade
```

安装软件：

```bash
sudo apk add nginx
sudo apk add git curl
```

删除软件：

```bash
sudo apk del nginx
```

搜索软件：

```bash
apk search nginx
```

查看包信息：

```bash
apk info
apk info nginx
```

### 📦 最常见场景

不是传统云服务器，而是 Dockerfile 里：

```dockerfile
RUN apk add --no-cache curl nginx
```

这里的 `--no-cache` 很常见，作用是避免额外缓存文件留在镜像里，减小镜像体积。

## 🟡 `pacman` 的使用

### 🖥️ 适用系统

- Arch Linux
- Manjaro 等 Arch 系发行版

### 🧪 常用命令

同步并更新系统：

```bash
sudo pacman -Syu
```

安装软件：

```bash
sudo pacman -S nginx
```

删除软件：

```bash
sudo pacman -R nginx
```

删除软件及无用依赖：

```bash
sudo pacman -Rns nginx
```

搜索软件：

```bash
pacman -Ss nginx
```

查看已安装包：

```bash
pacman -Qs nginx
```

### 📝 说明

服务器场景中没前面几种常见，但如果你碰到 Arch 系机器，至少要认得它。

## 📚 最常用操作对照表

| 需求 | `apt` | `dnf` | `yum` | `pkg` | `apk` | `pacman` |
| --- | --- | --- | --- | --- | --- | --- |
| 刷新软件列表 | `apt update` | `dnf makecache` | `yum makecache` | `pkg update` | `apk update` | `pacman -Sy` |
| 升级系统软件 | `apt upgrade` | `dnf upgrade` | `yum update` | `pkg upgrade` | `apk upgrade` | `pacman -Syu` |
| 安装软件 | `apt install nginx` | `dnf install nginx` | `yum install nginx` | `pkg install nginx` | `apk add nginx` | `pacman -S nginx` |
| 删除软件 | `apt remove nginx` | `dnf remove nginx` | `yum remove nginx` | `pkg delete nginx` | `apk del nginx` | `pacman -R nginx` |
| 搜索软件 | `apt search nginx` | `dnf search nginx` | `yum search nginx` | `pkg search nginx` | `apk search nginx` | `pacman -Ss nginx` |
| 查看包信息 | `apt show nginx` | `dnf info nginx` | `yum info nginx` | `pkg info nginx` | `apk info nginx` | `pacman -Qi nginx` |

这张表的作用不是让你全背下来，而是跨系统时能快速对照。

## 🎯 到底该学哪个

按实际工作场景选：

- 你主要维护 Ubuntu：重点学 `apt`
- 你主要维护 Rocky / Alma / RHEL：重点学 `dnf`
- 你经常写 Alpine Dockerfile：补学 `apk`
- 你碰到 FreeBSD：再学 `pkg`

不要把不同系统的工具混用。

例如这些都是错的：

- 在 Ubuntu 上执行 `dnf install nginx`
- 在 CentOS 上执行 `apt install nginx`
- 在 Alpine 容器里执行 `apt update`

先认系统，再下命令，这是最重要的习惯。

## 🛡️ 服务器实际维护建议

1. 装软件前先确认系统类型和版本。
2. 生产服务器升级前先看变更范围，不要无脑全量升级。
3. 安装完服务后立刻验证版本、进程、端口和自启动状态。
4. 清理缓存和无用依赖时，确认不会影响回滚。
5. 企业环境里如果用了内网源、镜像源、代理源，优先检查仓库配置再排错。

一个最常见的安装后检查流程：

```bash
nginx -v
systemctl status nginx
ss -tulpn | grep 80
systemctl is-enabled nginx
```

## 🧷 最后记忆法

可以直接这样记：

- Ubuntu 看 `apt`
- 红帽系看 `dnf`
- 老红帽教程看 `yum`
- Alpine 看 `apk`
- BSD 看 `pkg`

先记系统归属，再记命令。这样最不容易乱。