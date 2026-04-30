---
title: chrome-cleanup-commands
category: tech
date: 2025-04-01
tags: []
---

# Chrome 浏览器清理命令

> 关闭 Chrome 后执行以下命令清理浏览器数据

## 清理命令

```bash
# 关闭 Chrome 后执行

rm -rf ~/.config/google-chrome/Default/Extensions
rm -rf ~/.config/google-chrome/Default/Local\ Extension\ Settings
rm -rf ~/.config/google-chrome/Default/Local\ Storage
rm -rf ~/.config/google-chrome/Default/Sync\ Extension\ Settings
rm -rf ~/.config/google-chrome/Default/IndexedDB
rm -rf ~/.config/google-chrome/Default/Service\ Worker
rm -rf ~/.config/google-chrome/Default/GPUCache
rm -rf ~/.config/google-chrome/Default/Cache
```

```powershell
\AppData\Local\Microsoft\Edge\User Data
```

## 彻底清理（可选）

```bash
# ⚠️ 警告：这将删除整个 Default 目录，包括书签、历史记录等
rm -rf ~/.config/google-chrome/Default
```

## 说明

| 目录 | 内容 |
|------|------|
| Extensions | 安装的扩展程序 |
| Local Extension Settings | 扩展本地设置 |
| Sync Extension Settings | 扩展同步设置 |
| IndexedDB | 网页本地数据库 |
| Local Storage | 本地存储数据 |
| Service Worker | 后台服务工作进程 |
| GPUCache | GPU 缓存 |
| Cache | 浏览器缓存 |

## ⚠️ 注意事项

1. **执行前请确保 Chrome 已完全关闭**
2. 清理后扩展程序需要重新安装
3. 网站登录状态会丢失
4. 本地存储的数据会被清除

---
*文档创建时间：2026-04-21*
