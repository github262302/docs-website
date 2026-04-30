---
title: ubuntu24-apt-http-proxy
category: tech
date: 2025-04-01
tags: []
---

# Ubuntu 24 配置 APT HTTP 代理教程

## 概述

本文介绍如何在 Ubuntu 24.04 LTS 中为 APT 包管理工具配置 HTTP/HTTPS 代理，适用于需要通过代理服务器访问外网的场景。

---

## 方法一：临时配置（仅当前会话有效）

### 命令行方式

```bash
# 设置临时代理（当前终端会话有效）
export http_proxy="http://proxy.example.com:8080"
export https_proxy="http://proxy.example.com:8080"
export ftp_proxy="http://proxy.example.com:8080"

# 然后运行 apt 命令
sudo apt update
sudo apt upgrade
```

### 带代理运行单次 apt 命令

```bash
# 在一条命令中设置代理并执行 apt
sudo http_proxy=http://proxy.example.com:8080 apt update
```

---

## 方法二：APT 配置文件方式（推荐，持久化）

### 1. 创建/编辑 APT 代理配置文件

```bash
# 创建或编辑代理配置文件
sudo nano /etc/apt/apt.conf.d/proxy.conf
```

### 2. 添加代理配置

```
// HTTP 代理
Acquire::http::Proxy "http://proxy.example.com:8080";

// HTTPS 代理
Acquire::https::Proxy "http://proxy.example.com:8080";

// FTP 代理（可选）
Acquire::ftp::Proxy "http://proxy.example.com:8080";
```

### 3. 如果代理需要身份验证

```
// 带认证的代理
Acquire::http::Proxy "http://username:password@proxy.example.com:8080";
Acquire::https::Proxy "http://username:password@proxy.example.com:8080";
```

⚠️ **安全提示**：密码会以明文存储，确保文件权限安全！

### 4. 保存并测试

```bash
# 保存后测试配置
sudo apt update
```

---

## 方法三：环境变量方式（系统级）

### 编辑系统环境变量文件

```bash
# 编辑 /etc/environment 文件
sudo nano /etc/environment
```

### 添加代理环境变量

```bash
http_proxy="http://proxy.example.com:8080"
https_proxy="http://proxy.example.com:8080"
ftp_proxy="http://proxy.example.com:8080"
no_proxy="localhost,127.0.0.1,192.168.0.0/16"
```

### 重新登录或重启系统使配置生效

```bash
# 或者手动 source（仅当前会话）
source /etc/environment
```

---

## 方法四：sudo 保留环境变量

### 问题现象

如果设置了环境变量但 `sudo apt` 时代理不生效，是因为 sudo 会清理环境变量。

### 解决方案

```bash
# 编辑 sudoers 文件
sudo visudo
```

添加以下行，保留代理变量：

```
Defaults env_keep += "http_proxy https_proxy ftp_proxy no_proxy"
```

---

## 常用代理配置示例

### 1. Clash 本地代理

```
Acquire::http::Proxy "http://127.0.0.1:7890";
Acquire::https::Proxy "http://127.0.0.1:7890";
```

### 2. v2rayN / NekoRay 代理

```
Acquire::http::Proxy "http://127.0.0.1:10809";
Acquire::https::Proxy "http://127.0.0.1:10809";
```

### 3. 公司/校园代理（带认证）

```
Acquire::http::Proxy "http://user:pass@proxy.company.com:8080";
Acquire::https::Proxy "http://user:pass@proxy.company.com:8080";
```

---

## 取消/删除代理配置

### 方法 1：删除配置文件

```bash
sudo rm /etc/apt/apt.conf.d/proxy.conf
```

### 方法 2：注释配置内容

```bash
sudo nano /etc/apt/apt.conf.d/proxy.conf
```

在每行开头添加 `//` 注释掉：

```
// Acquire::http::Proxy "http://proxy.example.com:8080";
// Acquire::https::Proxy "http://proxy.example.com:8080";
```

---

## 验证代理是否生效

### 测试连接

```bash
# 更新软件包列表，观察是否通过代理
sudo apt update -o Debug::Acquire::http=true
```

### 查看详细日志

```bash
# 使用调试模式查看连接详情
sudo apt update -o Debug::Acquire::http=true 2>&1 | grep -i proxy
```

### 检查当前 APT 配置

```bash
# 查看 apt 配置
apt-config dump | grep -i proxy
```

---

## 故障排查

| 问题 | 解决方案 |
|------|----------|
| 代理连接超时 | 检查代理地址和端口是否正确 |
| 认证失败 | 确认用户名密码无误，特殊字符需要 URL 编码 |
| sudo 时代理失效 | 配置 sudoers 保留环境变量 |
| HTTPS 连接失败 | 检查系统 CA 证书或尝试使用 HTTP 代理 |

---

## 参考文档

- [Ubuntu Official Documentation](https://help.ubuntu.com/)
- [apt.conf 手册页](https://manpages.ubuntu.com/manpages/noble/en/man5/apt.conf.5.html)

---

*文档创建时间：2025年4月*
*适用系统：Ubuntu 24.04 LTS (Noble Numbat)*
