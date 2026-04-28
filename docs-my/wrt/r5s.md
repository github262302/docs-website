
cat /etc/opkg/distfeeds.conf


#
# Use the following command to switch to the default source:
#     sed -i -e 's/mirrors.ustc.edu.cn\/openwrt/downloads.openwrt.org/g' /etc/opkg/distfeeds.conf
#     opkg update
#

src/gz openwrt_base https://mirrors.ustc.edu.cn/openwrt/releases/24.10.4/packages/aarch64_generic/base
src/gz openwrt_luci https://mirrors.ustc.edu.cn/openwrt/releases/24.10.4/packages/aarch64_generic/luci
src/gz openwrt_packages https://mirrors.ustc.edu.cn/openwrt/releases/24.10.4/packages/aarch64_generic/packages
src/gz openwrt_routing https://mirrors.ustc.edu.cn/openwrt/releases/24.10.4/packages/aarch64_generic/routing
src/gz openwrt_telephony https://mirrors.ustc.edu.cn/openwrt/releases/24.10.4/packages/aarch64_generic/telephony
src/gz friendlywrt_packages file://usr/local/packages



root@FriendlyWrt:~# 
```bash
cat /etc/os-release 
```

```bash


NAME="OpenWrt"
VERSION="24.10.4"
ID="openwrt"
ID_LIKE="lede openwrt"
PRETTY_NAME="OpenWrt 24.10.4"
VERSION_ID="24.10.4"
HOME_URL="https://openwrt.org/"
BUG_URL="https://bugs.openwrt.org/"
SUPPORT_URL="https://forum.openwrt.org/"
BUILD_ID="r28959-29397011cc"
OPENWRT_BOARD="rockchip/armv8"
OPENWRT_ARCH="aarch64_generic"
OPENWRT_TAINTS="busybox"
OPENWRT_DEVICE_MANUFACTURER="OpenWrt"
OPENWRT_DEVICE_MANUFACTURER_URL="https://openwrt.org/"
OPENWRT_DEVICE_PRODUCT="Generic"
OPENWRT_DEVICE_REVISION="v0"
OPENWRT_RELEASE="OpenWrt 24.10.4 r28959-29397011cc"
OPENWRT_BUILD_DATE="1760891865"
```



```bash

root@FriendlyWrt:~# uname -a
Linux FriendlyWrt 6.1.141 #1 SMP Tue Dec 30 13:51:06 CST 2025 aarch64 GNU/Linux


https://wiki.friendlyelec.com/wiki/index.php/NanoPi_R5S/zh#.E8.AE.BE.E7.BD.AE.E7.AC.AC.E4.B8.89.E6.96.B9.E8.BD.AF.E4.BB.B6.E6.BA.90


```

```bash

root@FriendlyWrt:~# cat /etc/openwrt_release 
DISTRIB_ID='OpenWrt'
DISTRIB_RELEASE='24.10.4'
DISTRIB_REVISION='r28959-29397011cc'
DISTRIB_TARGET='rockchip/armv8'
DISTRIB_ARCH='aarch64_generic'
DISTRIB_DESCRIPTION='OpenWrt 24.10.4 r28959-29397011cc'
DISTRIB_TAINTS='busybox'

```


```bash
#
# Use the following command to switch to the default source:
#     sed -i -e 's/mirrors.ustc.edu.cn\/openwrt/downloads.openwrt.org/g' /etc/opkg/distfeeds.conf
#     opkg update
#

aliyun


src/gz openwrt_base https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/base
src/gz openwrt_luci https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/luci
src/gz openwrt_packages https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/packages
src/gz openwrt_routing https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/routing
src/gz openwrt_telephony https://mirrors.aliyun.com/openwrt/releases/24.10.0/packages/aarch64_cortex-a53/telephony
src/gz friendlywrt_packages file://opt/packages


```