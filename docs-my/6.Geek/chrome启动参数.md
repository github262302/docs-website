---
title: chrome启动参数
category: geek
date: 2025-04-01
tags: []
---

# Chrome 浏览器启动参数

常用启动参数配置指南。

## 用户目录

指定独立的用户数据目录，实现多开隔离：

```bash
chrome --user-data-dir="/path/to/profile"
```

## 远程调试端口

开启 DevTools Protocol，用于自动化控制：

```bash
chrome --remote-debugging-port=9222
```

访问 `http://localhost:9222` 查看可调试页面。

## 关闭 GPU 加速

解决部分环境的渲染问题：

```bash
chrome --disable-gpu
```

## 无头模式

后台运行，不显示界面：

```bash
chrome --headless --disable-gpu
```

## 完整示例

```bash
chrome \
  --user-data-dir="./chrome-profile" \
  --remote-debugging-port=9222 \
  --disable-gpu \
  --no-first-run
```
