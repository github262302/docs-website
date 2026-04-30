---
title: wrt.r5s
category: mirror
date: 2025-04-01
tags: []
---

# wrt R5S 

4+16 hdmi 2.5G

## Use the following command to switch to the default source:
```
sed -i -e 's/mirrors.ustc.edu.cn\/openwrt/downloads.openwrt.org/g' /etc/opkg/distfeeds.conf

# /

opkg update
```
#

## 用阿里云 资源多

```txt

src/gz openwrt_base https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/base
src/gz openwrt_luci https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/luci
src/gz openwrt_packages https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/packages
src/gz openwrt_routing https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/routing
src/gz openwrt_telephony https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/telephony

```