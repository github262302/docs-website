---
title: cleanPath
category: wsl
date: 2025-04-01
tags: []
---

Linux 从WSL移除Win10的PATH


方法1：通过wsl.conf永久配置
创建或编辑WSL配置文件：
`sudo nano /etc/wsl.conf`
添加以下内容：
```bash
[interop]
# 禁止自动挂载Windows驱动器
enabled = false
# 禁止将Windows PATH附加到Linux PATH
appendWindowsPath = false
[boot]
systemd=true
[user]
#启动后默认用户
default=love
```
保存文件并退出WSL实例
在PowerShell中执行以下命令完全关闭WSL：

`wsl --shutdown`

注意：此更改需要完全重启WSL才能生效