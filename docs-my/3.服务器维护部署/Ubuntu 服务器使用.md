# Ubuntu 服务器使用 🐧🛠️

这份笔记聚焦 Ubuntu 服务器的常见维护操作，包括开机环境变量、开机自启、自动化、磁盘管理和内存管理。

定位是“能直接照着操作的维护手册”，所以每个命令尽量说明用途、常见场景、验证方式和注意事项。

## 🔎 基础信息查看

先确认服务器当前的系统版本、网络状态、时间设置和监听端口。很多部署问题本质上不是程序本身问题，而是系统信息不一致。

```bash
hostnamectl
uname -a
lsb_release -a
timedatectl
ip addr
ss -tulpn
```

常用判断方式：

- `hostnamectl`：查看主机名、系统版本、虚拟化类型。适合先确认当前机器是不是你以为的那台。
- `uname -a`：查看内核版本。排查驱动、容器兼容、某些内核参数问题时常用。
- `lsb_release -a`：查看 Ubuntu 发行版版本，例如 20.04、22.04、24.04。
- `timedatectl`：查看时区、NTP 是否同步。服务器时间不准会直接影响日志、证书、定时任务。
- `ip addr`：看网卡、IP、子网。适合确认是公网、内网还是 Docker 网桥地址。
- `ss -tulpn`：查看监听端口和对应进程。排查“服务启动了但访问不到”时非常常用。

补充几条经常一起用的命令：

```bash
whoami
pwd
id
df -h
free -h
uptime
```

- `whoami` / `id`：确认当前是不是 `root` 或具备 sudo 权限的运维用户。
- `df -h`：部署前先看磁盘，避免构建到一半空间满。
- `free -h`：部署前先看内存，尤其是编译 Node、Java 项目时。
- `uptime`：看系统负载和运行时长，判断机器是否刚重启过。

## 🌱 开机环境变量脚本

Ubuntu 下常见的环境变量配置位置：

- `/etc/environment`：适合全局静态变量，语法简单，不支持复杂 Shell 语法。
- `/etc/profile`：登录 Shell 生效。
- `/etc/profile.d/*.sh`：推荐方式，适合集中维护自定义环境变量脚本。
- `~/.bashrc`：仅对当前用户的交互式 Shell 生效。

怎么选：

- 需要全局、简单键值：优先 `/etc/environment`。
- 需要 `export`、PATH 拼接、条件判断：优先 `/etc/profile.d/*.sh`。
- 只想影响某个登录用户：写用户自己的 `~/.bashrc` 或 `~/.profile`。
- 给 `systemd` 服务使用：优先写进服务文件，不要假设服务能读到登录 Shell 的环境变量。

推荐把全局变量写到 `/etc/profile.d/custom-env.sh`：

```bash
sudo nano /etc/profile.d/custom-env.sh
```

```bash
export JAVA_HOME=/opt/jdk
export NODE_HOME=/opt/node
export APP_ENV=production
export PATH=$JAVA_HOME/bin:$NODE_HOME/bin:$PATH
```

说明：

- 这里使用 `export`，表示变量会传递给子进程。
- `PATH=$JAVA_HOME/bin:$NODE_HOME/bin:$PATH` 的意思是把新路径追加到系统原有 PATH 前面，优先使用指定版本。
- 文件名建议使用有业务意义的名字，例如 `java-env.sh`、`node-env.sh`、`myapp-env.sh`，便于后续维护。

赋予可读权限后重新加载：

```bash
sudo chmod 644 /etc/profile.d/custom-env.sh
source /etc/profile
echo $APP_ENV
```

验证要点：

- `chmod 644` 表示所有用户可读，只有所有者可写。对环境变量脚本来说通常够用。
- `source /etc/profile` 是在当前 Shell 内重新加载配置，不需要退出重登。
- `echo $APP_ENV` 看到 `production` 才说明当前 Shell 已生效。

如果没有生效，可以继续检查：

```bash
ls -l /etc/profile.d/custom-env.sh
grep -n 'custom-env' /etc/profile /etc/bash.bashrc
env | grep APP_ENV
```

如果是给 `systemd` 服务使用，不要只依赖 Shell 环境变量，建议直接在服务文件中写：

```ini
[Service]
Environment="APP_ENV=production"
Environment="PORT=3000"
EnvironmentFile=-/etc/default/myapp
```

说明：

- `Environment=`：直接定义变量，适合少量固定值。
- `EnvironmentFile=`：从外部文件读取变量，适合多环境切换。
- 前面的 `-` 表示文件不存在时也不报错，这是 `systemd` 的可选文件语法。

例如 `/etc/default/myapp` 可以写成：

```bash
APP_ENV=production
PORT=3000
LOG_LEVEL=info
```

修改完服务文件后别忘了重新加载：

```bash
sudo systemctl daemon-reload
sudo systemctl restart myapp.service
sudo systemctl show myapp.service --property=Environment
```

## 🚀 开机自启任务配置

### ⚙️ 用 systemd 管理服务

现代 Ubuntu 推荐优先使用 `systemd`，比 `rc.local` 更稳定、可观测、可重启。

示例：Node.js 应用开机自启。

```bash
sudo nano /etc/systemd/system/myapp.service
```

```ini
[Unit]
Description=My Node App
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/myapp
ExecStart=/usr/bin/node /home/ubuntu/myapp/server.js
Restart=always
RestartSec=5
Environment="NODE_ENV=production"
Environment="PORT=3000"

[Install]
WantedBy=multi-user.target
```

关键字段说明：

- `Description`：服务描述，只是给人看的。
- `After=network.target`：表示在网络相关目标之后启动，但不代表外网一定可达。如果程序强依赖网络初始化完成，可以考虑 `network-online.target`。
- `Type=simple`：适合前台常驻进程，`ExecStart` 启动后服务即视为运行中。
- `User=ubuntu`：尽量不要直接用 `root` 跑业务程序，除非确实需要特权。
- `WorkingDirectory`：服务执行目录，很多相对路径依赖它。
- `ExecStart`：启动命令必须写绝对路径。
- `Restart=always`：进程退出后自动重启。
- `RestartSec=5`：重启前等待 5 秒，避免频繁拉起。
- `WantedBy=multi-user.target`：允许通过 `enable` 挂到系统多用户启动目标。

更稳一点的 Node 服务写法可以是：

```ini
[Unit]
Description=My Node App
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu/myapp
ExecStart=/usr/bin/node /home/ubuntu/myapp/server.js
Restart=always
RestartSec=5
Environment="NODE_ENV=production"
Environment="PORT=3000"
StandardOutput=append:/var/log/myapp/stdout.log
StandardError=append:/var/log/myapp/stderr.log

[Install]
WantedBy=multi-user.target
```

启用与查看状态：

```bash
sudo systemctl daemon-reload
sudo systemctl enable myapp.service
sudo systemctl start myapp.service
sudo systemctl status myapp.service
journalctl -u myapp.service -f
```

每条命令的作用：

- `daemon-reload`：重新读取 `/etc/systemd/system/` 下的服务定义。改过服务文件后必须执行。
- `enable`：加入开机自启。
- `start`：立即启动一次。
- `status`：看当前状态、最近日志、退出码。
- `journalctl -u ... -f`：实时追踪该服务日志，类似 `tail -f`。

常见排错：

```bash
sudo systemctl cat myapp.service
sudo systemctl is-enabled myapp.service
sudo systemctl is-active myapp.service
sudo journalctl -u myapp.service -n 100 --no-pager
```

如果服务无法启动，优先看这些问题：

- `ExecStart` 路径不对。
- 服务用户没有目录权限。
- 程序依赖的环境变量没有加载进去。
- 服务启动后立即退出，导致 `Restart=always` 一直重启。

### ⏰ 用 crontab 配置开机任务

适合启动脚本、同步任务、清理任务。

```bash
crontab -e
```

```cron
@reboot /home/ubuntu/scripts/startup.sh >> /home/ubuntu/logs/startup.log 2>&1
```

这行的意思：

- `@reboot`：系统启动后执行一次。
- `/home/ubuntu/scripts/startup.sh`：要执行的脚本。
- `>> ...log`：把标准输出追加到日志文件。
- `2>&1`：把错误输出也并到同一个日志里，方便排查。

注意：

- `cron` 运行环境非常精简，命令路径最好写绝对路径。
- 脚本内部建议先写日志，便于排查。
- 涉及网络依赖时，`@reboot` 不一定等网络完全就绪，更推荐 `systemd`。

建议脚本头部至少这样写：

```bash
#!/usr/bin/env bash
set -euo pipefail

export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

查看当前用户 crontab：

```bash
crontab -l
```

查看 cron 服务状态：

```bash
sudo systemctl status cron
journalctl -u cron -n 50 --no-pager
```

### 🧩 保留 rc.local 兼容方式

老项目可能仍使用 `/etc/rc.local`，但默认不推荐作为新方案。

如果确实要兼容旧脚本，可以这样：

```bash
sudo nano /etc/rc.local
```

```bash
#!/bin/bash
/home/ubuntu/scripts/legacy-start.sh >> /var/log/legacy-start.log 2>&1
exit 0
```

然后赋权：

```bash
sudo chmod +x /etc/rc.local
```

但要明确：

- `rc.local` 可观测性差。
- 失败时不如 `systemd` 好排查。
- 不适合管理长期常驻进程。

## 🤖 自动化脚本

### 📌 常见自动化场景

- 代码拉取与重启
- 日志清理
- 备份数据库
- 定时同步文件
- 证书续签

示例：部署脚本。

```bash
#!/usr/bin/env bash
set -e

cd /home/ubuntu/myapp
git pull origin main
npm ci
npm run build
sudo systemctl restart myapp.service
```

更推荐写得更稳一点：

```bash
#!/usr/bin/env bash
set -euo pipefail

LOG_FILE=/home/ubuntu/logs/deploy.log
APP_DIR=/home/ubuntu/myapp
SERVICE_NAME=myapp.service

mkdir -p /home/ubuntu/logs

exec >> "$LOG_FILE" 2>&1

echo "===== $(date '+%F %T') start deploy ====="
cd "$APP_DIR"
git fetch --all --prune
git checkout main
git pull origin main
npm ci
npm run build
sudo systemctl restart "$SERVICE_NAME"
sudo systemctl status "$SERVICE_NAME" --no-pager
echo "===== $(date '+%F %T') deploy done ====="
```

这样写的好处：

- `set -euo pipefail` 比单纯 `set -e` 更严格。
- 所有输出进入统一日志，方便回溯。
- 变量集中定义，后面改路径不容易漏。
- 部署结束后立刻检查服务状态。

保存为：`/home/ubuntu/scripts/deploy.sh`

```bash
chmod +x /home/ubuntu/scripts/deploy.sh
```

如果要配合定时执行：

```cron
0 4 * * * /home/ubuntu/scripts/cleanup.sh >> /home/ubuntu/logs/cleanup.log 2>&1
```

如果是证书续签类任务，建议执行前后都打日志，并保留失败通知机制。

### ✅ 建议的自动化原则

1. 脚本加 `set -e`，避免错误被忽略。
2. 所有关键步骤输出日志。
3. 路径写绝对路径，避免环境差异。
4. 重启服务前先做配置检查，例如 `nginx -t`。
5. 对会删除文件、清空目录的脚本，先手动 dry-run 一次。
6. 涉及数据库操作时，先备份再执行变更。

常见验证方式：

```bash
bash -n /home/ubuntu/scripts/deploy.sh
shellcheck /home/ubuntu/scripts/deploy.sh
```

- `bash -n`：检查脚本语法。
- `shellcheck`：静态检查 Shell 脚本，值得装。

## 💽 磁盘管理

### 📂 查看磁盘和分区

```bash
lsblk
sudo fdisk -l
df -h
du -sh /var/log/* | sort -h
```

用途区分：

- `lsblk`：看块设备结构。
- `df -h`：看挂载点空间占用。
- `du -sh`：看具体目录谁占空间。

常用补充：

```bash
find / -xdev -type f -size +500M 2>/dev/null
sudo du -xh / | sort -h | tail -n 30
```

- 第一条用于找超大文件。
- 第二条用于粗略看全盘哪些目录最占空间。

### 🔌 挂载新磁盘基础流程

```bash
lsblk
sudo mkfs.ext4 /dev/sdb
sudo mkdir -p /data
sudo mount /dev/sdb /data
df -h
```

每一步都要确认：

- 先 `lsblk` 看清楚新盘是不是 `/dev/sdb`，不要格式化错盘。
- `mkfs.ext4` 会清空该磁盘原有数据，确认是新盘再执行。
- `mount` 成功后再用 `df -h` 看 `/data` 是否真的挂载上去。

如果磁盘已经分区，常见设备名会是 `/dev/sdb1` 而不是 `/dev/sdb`，这点不要搞混。

要实现重启自动挂载，先取 UUID：

```bash
sudo blkid
```

编辑 `/etc/fstab`：

```fstab
UUID=xxxx-xxxx /data ext4 defaults,nofail 0 2
```

字段解释：

- `UUID=xxxx-xxxx`：磁盘唯一标识，比写 `/dev/sdb1` 更稳。
- `/data`：挂载目录。
- `ext4`：文件系统类型。
- `defaults,nofail`：默认挂载参数，且磁盘异常时不阻塞系统启动。
- 最后两个数字通常保持 `0 2`，分别用于 dump 和 fsck 顺序。

验证配置：

```bash
sudo mount -a
```

如果这条命令报错，先不要重启。说明 `fstab` 写错了，贸然重启可能直接导致系统启动异常或进入紧急模式。

### 🧹 磁盘清理重点目录

```bash
sudo du -sh /var/log
sudo journalctl --disk-usage
docker system df
```

重点说明：

- `/var/log`：日志过大最常见。
- `journalctl --disk-usage`：查看 systemd journal 占了多少空间。
- `docker system df`：看镜像、容器、volume、构建缓存占用。

常见清理：

```bash
sudo journalctl --vacuum-time=7d
sudo apt autoremove -y
sudo apt clean
docker image prune -a
docker container prune
```

说明：

- `journalctl --vacuum-time=7d`：只保留最近 7 天日志。
- `apt autoremove -y`：删除不再需要的依赖包。
- `apt clean`：清理下载缓存。
- `docker image prune -a`：删除未被容器使用的镜像，执行前要确认没有历史镜像回滚需求。
- `docker container prune`：删除已停止容器。

如果要继续清理 Docker 卷：

```bash
docker volume ls
docker volume prune
```

如果服务器使用 Docker，磁盘暴涨往往来自镜像、停止的容器和匿名 volume。

另外还要关注：

- 应用日志是否直接打在宿主机且没轮转。
- 构建目录、上传目录、备份目录是否长期未清理。
- 数据库二进制日志、慢日志是否持续增长。

## 🧠 内存管理

### 📊 查看内存使用

```bash
free -h
top
htop
vmstat 1 5
ps aux --sort=-%mem | head
```

重点理解：

- `used` 高不一定异常，Linux 会使用空闲内存做缓存。
- 更应关注 `available` 是否持续过低。
- 观察是否频繁触发 swap。

简单判断思路：

- `free -h` 中 `available` 还有较多余量，通常问题不大。
- `top` / `htop` 中单个进程持续上涨，要怀疑内存泄漏。
- `vmstat` 中 `si`、`so` 持续非 0，说明 swap 读写活跃，系统可能已经在抖。

### 🚨 查看 OOM 和内核日志

```bash
dmesg -T | grep -i -E 'killed process|out of memory|oom'
journalctl -k | grep -i oom
```

如果看到 `Killed process`，说明内核 OOM killer 已经杀过进程，要继续回查：

```bash
journalctl -k -n 200 --no-pager
ps aux --sort=-%mem | head -n 20
```

### 💾 查看 swap

```bash
swapon --show
cat /proc/swaps
```

判断点：

- `swapon --show` 能直接看到 swap 文件或分区的大小、使用量和优先级。
- 如果长期大量使用 swap，通常代表内存配置不足或应用占用异常。

创建一个 2G swap 文件示例：

```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

步骤解释：

- `fallocate -l 2G`：创建 2G 文件。
- `chmod 600`：只有 root 可读写，swap 文件权限必须严格。
- `mkswap`：把普通文件标记成 swap 区。
- `swapon`：立即启用。
- 追加到 `/etc/fstab`：保证重启后自动启用。

创建后验证：

```bash
swapon --show
free -h
```

如果云服务器磁盘较小，不要为了“看起来有 swap”而设置过大 swap 文件，否则会把磁盘 IO 拖垮。

### 🎛️ 调整 swappiness

```bash
cat /proc/sys/vm/swappiness
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/99-swappiness.conf
```

说明：

- `swappiness` 越低，内核越倾向于尽量少用 swap。
- 常见服务器可以从默认值调低到 10 或 20，但不是绝对越低越好。
- 写入 `/etc/sysctl.d/99-swappiness.conf` 后是持久配置，重启仍生效。

立即重载持久配置：

```bash
sudo sysctl --system
```

### 🧭 实际维护建议

1. 先确认是进程真实占用还是页缓存。
2. 再确认是否存在内存泄漏、日志暴涨、容器限制不合理。
3. 内存紧张时优先优化应用和容器限制，不要只靠加 swap。
4. Java、Node、Docker 容器都要分别看自身内存上限设置。
5. 如果是数据库机器，内存压力还要结合缓存命中率一起看。

## 🧰 常用服务管理命令

```bash
systemctl list-units --type=service
systemctl --failed
systemctl restart nginx
systemctl status docker
journalctl -xe
```

常见用途：

- `systemctl list-units --type=service`：查看当前已加载服务。
- `systemctl --failed`：快速看失败服务。
- `systemctl restart nginx`：重启指定服务。
- `systemctl status docker`：查看 Docker 服务状态。
- `journalctl -xe`：看最近较详细的系统日志。

补充几个非常实用的命令：

```bash
systemctl reload nginx
systemctl stop myapp.service
systemctl disable myapp.service
journalctl -p err -b
```

- `reload`：适合 Nginx 这类支持平滑重载配置的服务。
- `disable`：取消开机自启，但不代表当前会立刻停止。
- `journalctl -p err -b`：查看本次开机以来的错误级别日志。

## 📋 巡检清单

```bash
uptime
df -h
free -h
systemctl --failed
docker ps
ss -tulpn
```

日常维护时，建议把这些命令整理成一个巡检脚本，定时记录输出。

一个简单巡检思路：

1. 先看 `uptime`，判断系统负载和运行时长。
2. 看 `df -h`、`free -h`，确认资源有没有红线。
3. 看 `systemctl --failed`，确认有没有关键服务失败。
4. 看 `docker ps` 和 `ss -tulpn`，确认容器和端口是否符合预期。

如果要做成脚本，输出至少包含时间戳、主机名、磁盘、内存、失败服务、核心端口信息，便于后面回溯。