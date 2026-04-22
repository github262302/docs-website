目录

- [Niri简介](安装Niri#什么是niri)
- [更换Shell](安装Niri#可选更换shell为fish)
- [安装Niri](安装Niri#安装)
- [显示器配置](安装Niri#显示器配置)
- [鼠标设置](安装Niri#关闭鼠标加速)
- [基础组件](安装Niri#重要程序)
- [系统语言](安装Niri#修改系统语言为中文)
- [中文输入法](安装Niri#安装中文输入法)
- [文件管理器](安装Niri#配置图形化文档管理器)
- [锁屏设置](安装Niri#锁屏)
- [电源管理](安装Niri#自动熄屏锁屏休眠)
- [蓝牙](安装Niri#蓝牙)
- [剪贴板](安装Niri#剪贴板)
- [截图配置](安装Niri#截图)
- [屏幕分享](安装Niri#屏幕分享)
- [软件商店](安装Niri#软件商城)
- [窗口切换](安装Niri#alttab切换窗口)
- [亮度调节](安装Niri#笔记本屏幕亮度调节)
- [壁纸管理](安装Niri#壁纸切换)
- [面板(Waybar)](安装Niri#面板任务栏)
- [进阶配置](安装Niri#niri配置文件修改)
- [Overview壁纸](安装Niri#设置overview背景壁纸)
- [大写锁定](安装Niri#可选大写锁定显示)
- [自动登录](安装Niri#可选自动登录)

---

## 这是你在这篇文章之后能够获得的niri环境

![](pictures/waybar-bottom-niri.png)

视频教程：[「Niri入门指南」颠覆传统桌面体验，最适合新手的窗口管理器](https://www.bilibili.com/video/BV1fgUEBMEMZ/?share_source=copy_web&vd_source=1c6a132d86487c8c4a29c7ff5cd8ac50)

如果你想用我的配置文件一键安装niri，看：<https://github.com/SHORiN-KiWATA/shorin-arch-setup>

由于Niri很特别，所以简单介绍一下。

## 什么是Niri？

Niri官方的demo视频用几分钟简单地演示了Niri的特性，绝对值得一看：[Niri演示视频](https://github.com/YaLTeR/niri)

Niri的一切都是围绕scrolling layout滚动布局设计的。niri的特点：

1. 无限卷轴式平铺

    传统桌面不论是win、mac、kde、gnome、hyprland、sway，它们的工作区都只有一个屏幕的大小，新开的窗口会挤占其他窗口的空间。而Niri的工作区可以横向无限延伸，像是一个无限长的“卷轴”。

2. 垂直工作区切换

    顾名思义，工作区切换是垂直的而不是常见的水平切换。

3. 动态工作区数量增减

4. column合并

    可以将窗口合并到同一列，甚至可以开启标签页，这让niri可以同时做到mater布局、网格布局和标签页布局。

5. 其他细节设计

    比如overview、快捷键教程、舒适的默认配置等等。

这些结合起来组成了极其舒适的桌面体验。

- niri的“致命”问题

    1. fcitx5导致gtk应用无法弹出窗口。比如nautilus的“重命名”、easyeffects的“添加效果”。解决方法是设置一个快捷键开关输入法。（后记：设置GTK_IM_MODULE=fcitx环境变量可以解决nautilus重命名的问题，其他异常的程序也可以尝试这个设置，但不一定有效，这个bug仍在排查中）

## 可选：更换shell为fish

[FishShell](https://fishshell.com/)

由于会频繁用到命令行，有一个便利的shell会方便很多

```
sudo pacman -S fish
```

运行`fish`命令即可打开fish。

## 安装

[Niri-Getting-Started](https://yalter.github.io/niri/Getting-Started.html)

1. 安装

    ```
    sudo pacman -S niri xwayland-satellite xdg-desktop-portal-gnome fuzzel alacritty（自己喜欢的终端仿真器）firefox   
    ```

    出现选项选择`pipewire-jack`

    >`niri`本体

    >`xwayland-satellite`提供xwayland功能

    >`xdg-desktop-portal-gnome`是niri推荐使用的桌面门户，提供文件选择、屏幕分享等功能

    >`fuzzel`是niri默认的程序启动器

    >`alacritty`是niri默认的终端仿真器

    >`firefox`是linux上好用的浏览器

2. 第一次运行（此时会生成配置文件）

    运行`niri-session`打开niri会话。有显示管理器的话在显示管理器切换会话。

3. 可选：修改默认终端

    如果你使用的是别的终端的话可以编辑配置文件修改按键打开的终端。

    super+shift+E退出niri

    ```shell
    vim ~/.config/niri/config.kdl
    ```

    PS：没有特指的话后面所有“编辑配置文件”指的都是这个文件。

    `/`键，搜索`alacritty`；

    找到：

    ```
    Mod+T hotkey-overlay-title="Open a Terminal: alacritty" { spawn "alacritty"; }
    ```

    >`hotkey-overlay-title="Open a Terminal: alacritty"`设置`super+shift+/`打开的界面里的显示内容。改成`=null`可以隐藏。

    把`spawn "alacritty"`改成`spawn "kitty" "-e" "fish"`

    - 关于niri设置自定义快捷键

        当命令有多个部分时，有两种写法。一种是`spawn`，需要把命令分段写在多个引号里。

        ```
        spawn "kitty" "-e" "fish"
        ```

        另一种是`spawn-sh`，命令不用分段，直接写在一个引号里

        ```
        spawn-sh "kitty -e fish"
        ```

        `-sh`的写法性能消耗会略高一点（但是方便）

4. 基础使用方法

    `super+shift+/`打开重要快捷键教程

    `super+t`打开终端

    `super+d`打开应用启动器

    `super+u/i`上下切换工作区

    知道这些就可以开始使用niri啦。

## 显示器配置

[niri_wiki_outputs](https://github.com/YaLTeR/niri/wiki/Configuration:-Outputs)

0. 可选：GUI

    ```
    sudo pacman -S wdisplays
    ```

    PS:`wdisplays`可以临时修改显示设置，但无法保存到niri文件。

1. 运行命令获取显示器信息

   ```
   niri msg outputs
   ```

   ```shell
   Output "BOE NE156QHM-NY1 Unknown" (eDP-1)
     Current mode: 2560x1440 @ 165.000 Hz (preferred)
     Variable refresh rate: supported, disabled
     Physical size: 340x190 mm
     Logical position: 0, 0
     Logical size: 1920x1080
     Scale: 1.3333333333333333
     Transform: normal
     Available modes:
       2560x1440@165.000 (current, preferred)
       2560x1440@60.000 (preferred)
   ……………………
   Output "Shenzhen KTC Technology Group H27T22C 0x00000001" (DP-2)
     Current mode: 2560x1440 @ 180.000 Hz
     Variable refresh rate: supported, disabled
     Physical size: 600x330 mm
     Logical position: 1925, 0
     Logical size: 2560x1440
     Scale: 1
     Transform: normal
     Available modes:
       2560x1440@59.951 (preferred)
       2560x1440@180.000 (current)
       2560x1440@164.999
   ………………
   ```

   记住``eDP-1``和``DP-2``这部分，然后在``avaliable modes``里找到自己需要的模式，格式为``分辨率@刷新率``。

2. 配置文件内修改``output{}``

   左斜杠搜索`output`在示例配置下面自己写一个

   ```rust
   output "eDP-1"{
    //分辨率和刷新率
    mode "2560x1440@165"
    //缩放倍率
    scale 1.33
    //位置，x=0 y=0代表最左上角
    position x=0 y=0
    //启动时聚焦此显示器
    focus-at-startup
    //取消下面这行的注释设置可变刷新率
    //variable-refresh-rate 
    //取消下面这行的注释可以设置旋转，参数有：90, 180, 270, flipped（水平翻转）, flipped-90（水平翻转后旋转）, flipped-180 and flipped-270
    //transform "90"
   
   }
   ```

   有多个显示器的话就再写一个``output{}``，把最左上角的显示器的位置设置为`position x=0 y=0`，然后计算其他显示器的位置。

   我的eDP-1显示器位置是``x=0 y=0``在最左上角，我想把DP-2显示器放在它的右边，那就要更改``x=0``的值。eDP-1是2560x1440分辨率，1.33缩放，那横向就是2560/1.33=1924.81203008，约等于1925。那我DP-2的位置就是``x=1925 y=0``。其他位置也是按照这个方法计算。想放在下面就修改y轴的值。设置了旋转的话计算的时候横竖的值也要对应旋转。

   ```rust
    output "DP-2"{
    mode "2560x1440@180"
    scale 1
    position x=1925 y=0
   }
   ```

## 关闭鼠标加速

[niri_wiki_input](https://github.com/YaLTeR/niri/wiki/Configuration:-Input)

找到``input{mouse{}``取消`accel-profile "flat"`的注释。

```
mouse {
    //取消下面这行注释修改鼠标速度，正数加快，负数减慢
    //accel-speed -0.2
    //鼠标加速是默认开启的，设置这一行可以关闭鼠标加速度
    accel-profile "flat"
    //滚轮滚动速度，如果是负号的话会变更方向
    //scroll-factor horizontal=2.0 vertical=-1.0 
}
```

## 重要程序

[Niri-List-of-Important_Software](https://yalter.github.io/niri/Important-Software.html)

[Niri-XWayland](https://yalter.github.io/niri/Xwayland.html)

1. 安装

    ```
    sudo pacman -S libnotify mako polkit-gnome 
    ```

    >`mako`精简好用的通知服务

    >`polkit-gnome`需要管理员权限的软件会通过polkit询问权限，没有polkit的话就无法启动，例如`btrfs-assistant`。

2. 设置重要程序随着niri启动

    niri会话是作为systemd服务启动的，所以可以用systemd启动那些重要程序，但是修改niri配置文件会更方便移植。

    ```
    vim ~/.config/niri/config.kdl
    ```

    搜索`spawn-at-startup`，在合适的地方写入：

    ```
    spawn-at-startup "/usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1"

    spawn-at-startup "mako"
    ```

3. 现在启动

    ```
    /usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1 & disown 
    ```

    ```
    mako & disown 
    ```

    >`&`代表在终端后台运行。
    
    >`disown`命令从终端解绑程序。

- 编辑mako的配置文件设置通知的显示时长

    ```
    mkdir -p ~/.config/mako
    vim ~/.config/mako/config
    ```

    ```
    default-timeout=8000
    border-radius=8
    ```

    ```
    makoctl reload
    ```

## 修改系统语言为中文

利用配置文件提供的环境变量功能设置系统语言。

```
environment {
    LANG "zh_CN.UTF-8"
    LC_CTYPE "en_US.UTF-8"
}
```

用`LANG`变量设置本地化使用中文。再把`LC_CTYPE`设置为英文解决输入法漏字的异常。

- 如果是archinstall安装，需要额外进行如下操作：

  1. ```
     sudo vim /etc/locale.gen 
     ```

  2. 左斜杠键搜索，取消`zh_CN.UTF-8`的注释

  3. ```
     sudo locale-gen
     ```

  4. 登出

## 安装中文输入法

0. 需要[添加archlinuxcn](安装桌面环境前的准备#AUR助手)

1. 安装

    看中文输入法一节：[中文输入法](中文输入法)

2. 设置环境变量

    在niri配置文件的`environment{}`里面写入`XMODIFIERS "@im=fcitx"`即可

3. 现在启动

    ```
    fcitx5 -d
    ```

4. 自动启动

    ```
    spawn-at-startup "fcitx5" "-d"
    ```

5. 可选：设置开关输入法的快捷键

    fcitx5会导致部分gtk应用无法弹出窗口，可以设置一个快捷键开启和关闭输入法。

    ```
    Mod+F1 {spawn-sh "pkill fcitx5 || fcitx5";} 
    ```

## 配置图形化文档管理器

### nautilus

安装`xdg-desktop-portal-gnome`会默认把`nautilus`装上，这是gnome的文档管理器。使用这条命令补全nautilus的功能。

```
sudo pacman -S ffmpegthumbnailer gvfs-smb nautilus-open-any-terminal file-roller gnome-keyring gst-plugins-base gst-plugins-good gst-libav
```

>`ffmpegthumbnailer`视频预览

>`gvfs-smb` 检查可挂载的外部设备，访问smb分享等功能

>`nautilus-open-any-terminal` 右键从此处打开终端

>`file-roller` 提供压缩解压缩功能

>`gnome-keyring`提供密码保存功能。第一次保存密码会让你设置keyring的密码，可以空着。

>`gst-plugins-base gst-plugins-good gst-libav`这些让你可以预览视频信息

>可选：

>`nautilus-image-converter`右键方便调整图片大小或者旋转

>`libheif webp-pixbuf-loader libopenraw gst-plugins-bad gst-plugins-ugly`提供更多视频、图片格式支持

>`gnome-font-viewer`字体缩略图

- 更改默认终端为kitty

    安装`xdg-terminal-exec`

    ```
    yay -S xdg-terminal-exec
    ```

    编辑`~/.config/xdg-terminals.list`

    ```
    echo "kitty.desktop" > ~/.config/xdg-terminals.list
    ```

    如果有多个终端的话就按照顺序写多行。

- 设置nautilus右键从此处打开kitty

    使用dbus设置dconf

    ```
    gsettings set com.github.stunkymonkey.nautilus-open-any-terminal terminal kitty
    ```

- 设置右键从此处打开终端的shell为fish

    从nautilus右键打开kiity默认是bash，这里介绍另一种打开kitty就是fish的方法:

    可以修改kitty的配置文件设置kitty的shell为fish

    ```
    vim ~/.config/kitty/kitty.conf
    ```

    写入

    ```
    shell fish
    ```

    美化kitty的部分在这里：[kitty美化](终端美化#Kitty美化)

- 设置快捷键

    ```
    vim ~/.config/niri/config.kdl
    ```

    搜索bind

    ```
    Mod+E { spawn "nautilus";}
    ```

- 如果nautilus启动慢

    是因为gtk4使用的新的渲染器和n卡驱动工具之间有一些兼容性问题，即这个包：'nvidia-utils'，解决办法是编辑nautilus的环境变量，使用旧的渲染器。

    ```
    env GSK_RENDERER=gl nautilus
    ```

- 如果无法打开重命名窗口

    加上`GTK_IM_MODULE`环境变量

    ```
    env GSK_RENDERER=gl GTK_IM_MODULE=fcitx nautilus
    ```

### thunar

[Xfce4-Projects](https://www.xfce.org/projects)

如果你不想用nautilus，我推荐使用`xdg-desktop-portal-gtk`的thunar。thunar功能强大且内存占用仅50MB，推荐。

```
sudo pacman -S --needed xdg-desktop-portal-gtk thunar tumbler ffmpegthumbnailer poppler-glib gvfs-smb file-roller thunar-archive-plugin gnome-keyring thunar-volman gvfs-mtp gvfs-gphoto2 webp-pixbuf-loader
```

>`xdg-dekstop-portal-gtk`是gtk的xdg桌面门户

>`tumbler`提供图片预览功能

>`ffmpegthumbnailer`视频预览

>`poppler-glib`pdf预览

>`gvfs-smb` 检查可挂载的外部设备，访问smb分享等功能

>`file-roller` 提供压缩解压缩功能

>`thunar-archive-plugin`在thunar的右键菜单添加压缩解压缩选项

>`gnome-keyring`提供密码保存功能。第一次保存密码会让你设置keyring的密码，可以空着。

>`thunar-volman`自动管理移动硬盘等设备

>`gvfs-mtp`连接手机

>`gvfs-gphoto2`连相机

>`webp-pixbuf-loader` webp缩略图

远程服务器上的图片的缩略图预览功能需要在首选项里手动开启。

- 清理没什么用的侧边栏书签

    在侧边栏的空白处右键即可设置显示哪些东西

- 右键从此处打开终端

    thunar提供了强大的自定义右键功能。

    点击左上角编辑 > 配置自定义动作 > 选中open in terminal here > 点击小齿轮 > 命令改成`kitty -e fish`

- 配置门户

    ```
    mkdir -p ~/.config/xdg-desktop-portal/
    vim ~/.config/xdg-desktop-portal/niri-portals.conf
    ```

    ```
    [preferred]
    default=gnome;gtk;
    org.freedesktop.impl.portal.FileChooser=gtk
    ```

    >`default=gnome;gtk;`设置默认portal是gnome，如果gnome不行就fallback到gtk。

    >`org.freedesktop.impl.portal.FileChooser=gtk`设置文件选择器用gtk

    如果你使用的是kde的dolphin，可以把gtk改成kde。

- 如果使用dolphin没有默认应用菜单可以看

    ```
    sudo pacman -S archlinux-xdg-menu
    ```

    编辑niri的环境变量

    ```
    XDG_MENU_PREFIX "arch-"
    ```

## 锁屏

niri默认使用swaylock

1. 安装

    ```
    sudo pacman -S swaylock-effects
    ```

2. 创建目录并编辑配置文件

    ```
    mkdir -p ~/.config/swaylock
    vim ~/.config/swaylock/config
    ```

    ```
    screenshots
    clock
    indicator
    indicator-radius=200
    indicator-thickness=15
    effect-blur=10x5
    font=Source Han Sans CN
    ```

    从上到下分别是：

    ```
    用桌面当背景
    显示时钟
    显示圆环
    圆环大小
    圆环粗细
    背景模糊
    字体（如果不设置这一项并且你没有设置fontconfig的话中文会乱码，具体使用什么字体取决于你安装了哪个字体。可以使用gnome-font-viewer查看）
    ```

niri配置文件默认设置了一个Mod+Alt+L锁屏的快捷键，可以搜索swaylock自行修改

## 自动熄屏锁屏睡眠

使用swayidle

1. 安装

    ```
    sudo pacman -S swayidle
    ```

2. 创建swayidle脚本

    虽然niri的wiki说可以使用systemd，但是为了方便移植，我使用脚本

    ```
    mkdir -p ~/.config/niri/scripts
    vim ~/.config/niri/scripts/swayidle.sh
    ```

    ```
    #!/usr/bin/env bash
    
    # 5分钟锁屏，10分钟熄屏，20分钟睡眠
    # swaylock -f是前台运行swaylock，如果不加的话后续的timeout命令会不生效
    
    swayidle -w \
        timeout 300  'swaylock -f' \
        timeout 600  'niri msg action power-off-monitors' \
        resume       'niri msg action power-on-monitors' \
        timeout 1200 'systemctl suspend' \
    ```
    > 因兼容性问题，此处会报错`Failed to parse get BlockInhibited property: Invalid argument`。属于正常情况，不影响脚本功能。


    添加可执行权限

    ```
    chmod +x ~/.config/niri/scripts/swayidle.sh
    ```

    现在开启（此处会有一个报错，正常，无视就行）

    ```
    bash ~/.config/niri/scripts/swayidle.sh & disown
    ```

    设置自动启动

    ```
    spawn-at-startup "~/.config/niri/scripts/swayidle.sh"
    ```

## 蓝牙

[ArchWiki-Bluetooth](https://wiki.archlinux.org/title/Bluetooth)

我这里使用bluetui。

```
sudo pacman -S --needed bluez bluetui
```

```
sudo systemctl enable --now bluetooth
```

`bluetui`可以打开终端交互界面

## 剪贴板

1. 安装

    ```
    sudo pacman -S cliphist wl-clipboard
    ```

    >`wl-clipboard`是wayland合成器标配剪贴板工具。

    >`cliphist`提供剪贴板历史记录功能。

2. 现在启动

    ```
    wl-paste --watch cliphist store & disown
    ```

    原理是开启`wl-paste --watch`监测剪贴板变化。每一次出现新条目时自动运行`cliphist store`保存到cliphist的历史记录里。

    cliphist保存的剪贴板历史可以通过`cliphist list`查看。

    `cliphist decode`通过传入的cliphist**列表序号**解码真实的数据，这个数据存放在`.cache/cliphist/db`

3. 设置守护进程自动启动

    ```
    spawn-at-startup "wl-paste" "--watch" "cliphist" "store"
    ```

- 小游戏

    这里我们可以玩一些小游戏。安装`fzf`，这是一个模糊搜索、tui菜单程序。

    ```
    sudo pacman -S fzf
    ```

    复制点东西，运行：

    ```
    cliphist list | fzf
    ```

    这条命令把cliphist的列表数据传给了fzf，你会得到一个可供选择的剪贴板历史菜单。回车任意选择一项，终端就会打印出`1    这是我的剪贴板历史`格式的内容，这是cliphist的列表数据格式。

    再试着运行以下命令：

    ```
    cliphist list | fzf | cliphist decode
    ```

    通过`decode`，可以将列表数据转换为原本的剪贴板内容并打印在终端（注意，这不是简单的去掉序号，图片数据经过`decode`会从纯文字变成二进制数据）。

    接下来再试试运行这段命令：

    ```
    cliphist list | fzf | cliphist decode | wl-copy
    ```

    >`wl-copy`将数据写入当前剪贴板。

    至此，一个简易的剪贴板TUI就做好了，我觉得这很有趣，分享给你。这个小游戏里我们使用`fzf`处理数据，还可以使用别的东西，比如类似`fuzzel`的菜单程序，万变不离其宗，原理都是一样的。

    ```
    cliphist | fuzzel -d | cliphist decode | wl-copy
    ```

1. 安装TUI

    我做的TUI，使用`kitty icat`进行图片预览，原理就是刚刚小游戏中的原理，占用仅7MB。

    <https://github.com/SHORiN-KiWATA/cliphist-tui>

    ```
    yay -S cliphist-tui-git
    ```

    运行`cliphist-tui`命令就能打开

    记得设置TUI的快捷键：

    ```rust
    Mod+Alt+V {spawn-sh "kitty --single-instance --class cliphist-tui -e cliphist-tui";}
    
    // 因为这是一个bash脚本，所以快捷键命令会长一些
    // --single-instance避免每次开kitty都新开进程导致不必要的内存占用
    // --class 指定要打开的kitty的app-id
    // -e 指定打开时自动运行的命令
    ```

    以浮动模式打开：

    ```rust
    window-rule{
        //用app-id指定窗口
     match app-id="cliphist-tui"
        // 默认长宽

    default-column-width { fixed 625; }
        default-window-height { fixed 700; }
     //以浮动模式打开
        open-floating true
     //默认打开位置，这里的x和y是偏移量。这里的例子是在顶部中心打开，y轴向下18个逻辑像素
        default-floating-position x=0 y=18 relative-to="top"
    }
    ```

## 截图

niri自带了截图功能，不仅可以截区域，还可以截取窗口或者全屏，很好用。在配置文件搜索`screenshot`可以找到键位。`screenshot-path`可以设置壁纸保存的位置和文件名模板。

### 编辑截图

```
sudo pacman -S satty
```

- 设置satty的复制命令为`wl-copy`

    ```bash
    mkdir -p ~/.config/satty
    vim ~/.config/satty/config.toml
    ```

    ```toml
    [general]
    copy-command = "wl-copy"
    focus-toggles-toolbars= true
    actions-on-right-click = ["save-to-clipboard"]
    ```

​  >`focus-toggles-toolbars= true`设置自动隐藏工具栏

​  >`actions-on-right-click = ["save-to-clipboard"]`设置右键复制到剪贴板

niri截图的命令行工具暂时不支持把图片的数据传给别的软件，但截图数据会自动保存到剪贴板，我们可以从剪贴板获取图片数据。

```
Mod+Shift+S {spawn-sh "wl-paste | satty -f -";}
```

先用niri的截图工具截图（不论全屏、选择区域、聚焦窗口截图都可以），然后按下这个快捷键手动让satty读取剪贴板里的那张截图。最可控，最简单，无额外性能开销。

## 屏幕分享

录屏推荐使用obs或者kooha。想要最好的录屏效果可以了解一下`wf-recorder`或`wl-screenrec`，`wl-screenrec`性能更好。

niri通过gnome的xdg桌面门户进行屏幕分享，我们已经安装了，此时应该已经可以用了。如果无法使用的话设置：

```
spawn-sh-at-startup "dbus-update-activation-environment --systemd WAYLAND_DISPLAY XDG_CURRENT_DESKTOP=niri & /usr/lib/xdg-desktop-portal-gnome"
```

做两件事，通过`WAYLAND_DISPLAY`和`XDG_CURRENT_DESKTOP=niri`这两个变量让dbus和systemd知道我的当前wayland会话是哪个，桌面环境是什么。然后确保启动gnome的桌面门户提供屏幕分享功能。设置完之后要重启niri。

## 软件商城

```
sudo pacman -S bazaar
```

## alt+tab切换窗口

- niri自带

    <https://github.com/YaLTeR/niri/wiki/Configuration:-Recent-Windows>

- 可选：niriswitcher

    （niri从25.11版本开始自带有缩略图预览的alt+tab切换功能，此配置不再必须）

    1. 安装

        ```
        yay -S niriswitcher
        ```

        现在启动

        ```
        niriswitcher & disown
        ```

    2. 自动启动

        ```
        spawn-at-startup "niriswitcher"
        ```

    3. 快捷键

        ```
        Alt+Tab repeat=false {spawn "niriswitcherctl" "show" "--window";}
        ```

    4. 编辑niriswitcher的配置文件

        ```
        mkdir -p ~/.config/niriswitcher
        vim ~/.config/niriswitcher/config.toml
        ```

        设置只显示当前显示器

        ```
        current_output_only = true
        ```

    5. 现在启动

        ```
        niriswitcher & disown
        ```

## 笔记本屏幕亮度调节

笔记本用户装这个包之后可以用笔记本键盘的快捷键调节屏幕亮度

```
sudo pacman -S brightnessctl
```

## 壁纸切换

使用`awww`和`waypaper`。

1. 安装

    ```
    yay -S awww waypaper
    ```

2. 打开waypaper切换壁纸

3. 设置`awww-daemon`自动启动

    ```
    spawn-at-startup "awww-daemon"
    ```

4. 设置快捷键

    ```
    Mod+Alt+W {spawn "waypaper";}
    ```

5. 设置窗口规则

    打开waypaper窗口，运行：

    ```
    niri msg pick-window
    ```

    选择waypaper窗口获取窗口信息，然后在niri的配置文件里面设置窗口规则

    ```
    window-rule {
    
        match app-id="waypaper"
    
        open-floating true
    }
    ```

## 面板（任务栏）

[Github-Wiki-Waybar](https://github.com/Alexays/Waybar/wiki)

初学使用waybar即可

1. 安装

   ```
   sudo pacman -S waybar ttf-jetbrains-mono-nerd otf-font-awesome
   ```

   >`ttf-jetbrains-mono-nerd`提供图标文字

   >`otf-font-awesome`是wayabr的基础字体包依赖

2. 现在启动

   ```
   waybar & disown
   ```

3. 开机自启

    编辑niri的配置文件

    ```
   vim ~/.config/niri/config.kdl
   ```

   ```
   spawn-at-startup "waybar"
   ```

4. 设置重启waybar的快捷键

   后续自定义waybar需要频繁更新waybar配置，所以设置一个重启waybar的快捷键。

   ```
   vim ~/.config/niri/config.kdl
   ```

   找到快捷键的部分，新增以下内容

   ```
    Mod+F12 {spawn-sh "pkill waybar || true && waybar";}
   ```

   >`Mod+F12`设置具体的快捷键为Mod+F12

   >`{spawn-sh "";}`设置具体命令，与`spawn`不同，`spawn-sh`可以在引号里写入完整的命令，而`spawn`要把命令拆开放在多个引号里。

   >`pkill waybar`按照进程名字关闭waybar

   >`||`如果左边的命令运行失败则运行右边的命令

   >`true`输出一个运行成功的信号

   >`&&`如果运行成功则运行下一条命令

   >`waybar`开启waybar

   这段命令的完整意思是：尝试`pkill waybar`杀死waybar进程，成功则运行`waybar`开启waybar，失败（waybar尚未启动的情况下pkill会运行失败）则运行`true`发送成功信号触发`waybar`命令开启waybar。

### 使用我的配置

我之前直播重装niri的时候新配置了一个类似win11布局的waybar，可以基于壁纸自动更改颜色，有兴趣的可以使用。

1. 安装依赖

    ```
    yay -S --needed cava grim slurp hyprpicker waybar-niri-taskbar-git ddcutil-service
    ```

    >`grim` 截图工具

    >`slurp`选取区域工具

    >`hyprpicker`提取屏幕颜色

    >`waybar-niri-taskbar-git`waybar的niri任务栏模块

    >`ddcutil-service`ddc调节外接屏幕亮度


    - ddcutil安装和使用方法

         [我的GNOME自定义设置#调节外接屏幕亮度](我的GNOME自定义设置#调节外接屏幕亮度)

        需要添加组然后重启

        ```
        sudo gpasswd -a $USER i2c
        reboot
        ```

2. 下载我的仓库

    ```
    git clone https://github.com/SHORiN-KiWATA/ShorinArchExperience-ArchlinuxGuide.git
    ```

    找到`dotfiles/.config/waybar-niri-LikeWin11`文件夹，复制到`~/.config/`目录下，把文件夹重命名为`waybar`

    重启waybar就可以啦

    PS：如果有模块无法正常使用的话检查一下`.config/waybar/modules.jsonc`里的脚本路径对不对。还有部分依赖由`shorin-conrtib-git`提供，按需安装和调整吧。

#### 根据壁纸自动更改颜色

使用matugen根据壁纸生成颜色，让waybar自动变更颜色。

1. 安装matugen

    ```
    yay -S matugen 
    ```

2. 创建需要的目录和配置文件

    ```
    mkdir -p ~/.config/matugen
    mkdir -p ~/.config/matugen/templates
    ```

3. 复制颜色模板

    找到我文档里的`dotfiles/.config/matugen/templates/`目录，把`colos.css`文件复制粘贴到`~/.config/matugen/templates`目录里。

4. 编辑matugen配置文件

    ```
    vim ~/.config/matugen/config.toml
    ```

    写入如下内容：

    ```
    [config.wallpaper]
    command = "awww"
    
    [templates.waybar]
    input_path = '~/.config/matugen/templates/colors.css'
    output_path = '~/.config/waybar/colors.css'
    ```

5. 编辑waypaper的配置文件

    ```
    vim ~/.config/waypaper/config.ini
    ```

    把`post_command`改成：

    ```
    post_command = matugen image $wallpaper
    ```

6. 使用wayppaer切换壁纸

## niri配置文件修改

1. 全局窗口规则

    搜索`geometry-corner-radius`，会搜到一个被注释掉的window-rule，这是niri默认配置配置文件自带的全局生效的窗口规则。删除前面的`/-`取消注释：

    ```
    window-rule {
        //圆角
        geometry-corner-radius 8
        //剪掉圆角外的窗口内容
        clip-to-geometry true
        //透明度
        opacity 0.99
        //禁止边框画到窗口后面
        draw-border-with-background false
    }
    ```

2. 边框

    `focus-ring`可以调整窗口的边框。

3. 隐藏窗口的标题栏

    ```
    prefer-no-csd
    ```

4. 聚焦跟随鼠标

    搜索`focus-follows-mouse`

5. 切换聚焦自动移动鼠标到聚焦窗口上

    搜索`warp-mouse-to-focus`

6. 切换光标主题

    强烈推荐breeze光标主题
    ```
    sudo pacman -S breeze-cursors
    ```
    在合适的位置写入：

    ```
    cursor {
        //鼠标的光标主题
        xcursor-theme "breeze_cursors"
    
        //大小
        xcursor-size 30
    
        //闲置15s自动隐藏光标
        hide-after-inactive-ms 15000
    
    }
    ```

## 设置overview背景壁纸

任意壁纸程序都可以，这里以awww为例。

1. 启用壁纸程序

    awww可以同时运行多个不同namespace的守护进程，所以我们可以创建一个专门用于overview壁纸的awww守护进程。

    ```
    awww-daemon -n overview
    ```

2. 获取layer的namespace

    ```
    niri msg layers
    ```

    会找到`awww-daemonoverview`这个layer

3. niri配置文件里设置layer规则

    通过`place-within-backdrop true`这个规则把刚刚运行的壁纸程序放进overview

    ```
    layer-rule{
        match namespace="awww-daemonoverview"
        place-within-backdrop true
    }
    ```

4. 设置一个喜欢的壁纸

    需要用`-n`选项指定overview的awww守护进程

    ```
    awww img 你喜欢的壁纸.png -n overview
    ```

然后你就可以发挥想象力了。比如利用`imagemagick`提供的`magick`命令自动把当前壁纸处理成模糊暗色版本放进overview之类的。

## 可选：大写锁定显示

- swayosd

    ```
    sudo pacman -S swayosd
    ```

    开启监控输入的后端

    ```
    sudo systemctl enable --now swayosd-libinput-backend.service
    ```

    设置自动启动

    ```
    spawn-at-startup "swayosd-server"
    ```

## 可选：自动登录

这一节配置完的效果是进入tty1自动登录，然后自动打开niri会话。完全不用输密码，不用输命令。适合单用户单桌面会话的情况。

[ArchWiki-getty](https://wiki.archlinux.org/title/Getty)

- 自动登录tty

  ```
  sudo mkdir -p /etc/systemd/system/getty@tty1.service.d/
  ```

  ```
  sudo vim /etc/systemd/system/getty@tty1.service.d/autologin.conf
  ```

  ```
  [Service]
  ExecStart= 
  ExecStart=-/sbin/agetty --noreset --noclear --autologin shorin - ${TERM}
  ```

  `shorin`改成要自动登录的账户的用户名

- 自动启动niri

  [Autostarting Niri without a DM](https://www.reddit.com/r/niri/comments/1kdgc08/autostarting_niri_without_a_dm/)

  ```
  mkdir -p ~/.config/systemd/user
  ```

  ```
  vim ~/.config/systemd/user/niri-autostart.service
  ```

  ```
  [Service]
  ExecStart=/usr/bin/niri-session
  
  [Install]
  WantedBy=default.target
  ```

  ```
  systemctl --user enable niri-autostart.service
  ```

- 重启电脑

## 拓展内容：带blur的niri

安装`niri-git`包即可。

1. 编辑niri配置文件

    ```
    vim ~/.config/niri/config.kdl
    ```

    以下是我的推荐配置：

    ```rust
    // --- NIRI BLUR START ---
    // 顶层的blur配置，细节调整blur的效果
    blur {
        passes 3
        offset 3
        noise 0.02
        saturation 1.5
    }
    // 全局窗口规则。让所有普通窗口以xray的形式显示blur。xray仅渲染一次模糊版本的背景，然后将其以类似“壁纸”的形式显示在窗口后面，不是实时渲染模糊，所以完全没有性能消耗。
    window-rule {
        background-effect {
            xray true
            blur true
        }
    }
    // 浮动窗口禁用xray可以实时渲染模糊效果但是性能消耗高，开启xray会穿过浮动窗口底下的窗口直接透视到桌面。看个人喜好选择禁用与否吧。
    window-rule {
        match is-floating=true
        background-effect {
            xray true
            blur true
        }
    }
    // fuzzle专属的layer规则
    layer-rule {
        match namespace="^launcher$"
        geometry-corner-radius 8
        background-effect {
            xray false
            blur true
        }
    }
    // --- NIRI BLUR END ---
    ```

### 接下来你可以按照自己的需求决定安装什么软件。当然，你也可以选择参考我的

## 下一节：[软件安装相关](软件安装相关)
