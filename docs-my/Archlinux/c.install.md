
# 手动快速安装


防止操作失误每次都得重来

```bsah
archinstall --config ./config.json

```


`./config.json`

```json
{
    "app_config": {
        "audio_config": {
            "audio": "pulseaudio"
        },
        "bluetooth_config": {
            "enabled": true
        }
    },
    "archinstall-language": "English",
    "auth_config": null,
    "bootloader_config": {
        "bootloader": "Grub",
        "removable": true,
        "uki": true
    },
    "custom_commands": [],
    "hostname": "archNB",
    "kernels": [
        "linux"
    ],
    "locale_config": {
        "kb_layout": "us",
        "sys_enc": "UTF-8",
        "sys_lang": "en_US.UTF-8"
    },
    "mirror_config": {
        "custom_repositories": [],
        "custom_servers": [],
        "mirror_regions": {
            "China": [
                "http://mirrors.163.com/archlinux/$repo/os/$arch",
                "http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.hust.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.hust.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.cqu.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.cqu.edu.cn/archlinux/$repo/os/$arch",
                "http://mirror.lzu.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.xjtu.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.sjtug.sjtu.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.nju.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.nju.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.hit.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.hit.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.wsyu.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.wsyu.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.aliyun.com/archlinux/$repo/os/$arch",
                "https://mirrors.aliyun.com/archlinux/$repo/os/$arch",
                "http://mirrors.jlu.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.jlu.edu.cn/archlinux/$repo/os/$arch",
                "http://mirror.nyist.edu.cn/archlinux/$repo/os/$arch",
                "https://mirror.nyist.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.qlu.edu.cn/archlinux/$repo/os/$arch",
                "http://mirrors.jcut.edu.cn/archlinux/$repo/os/$arch",
                "https://mirrors.jcut.edu.cn/archlinux/$repo/os/$arch"
            ]
        },
        "optional_repositories": [
            "multilib"
        ]
    },
    "network_config": {
        "type": "nm"
    },
    "ntp": true,
    "packages": [
        "openssh",
        "openssl",
        "nano",
        "os-prober"
    ],
    "parallel_downloads": 0,
    "profile_config": {
        "gfx_driver": "AMD / ATI (open-source)",
        "greeter": "lightdm-gtk-greeter",
        "profile": {
            "custom_settings": {
                "Niri": {
                    "seat_access": "polkit"
                }
            },
            "details": [
                "Niri"
            ],
            "main": "Desktop"
        }
    },
    "script": null,
    "services": [],
    "swap": {
        "algorithm": "zstd",
        "enabled": true
    },
    "timezone": "Asia/Shanghai",
    "version": "4.1"
}

```