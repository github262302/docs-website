在 Arch Linux 上创建普通用户

在 Arch Linux 中创建普通用户是一个基本操作，以下是详细步骤：

### 1. 创建普通用户

使用 `useradd` 命令创建用户并生成主目录：
`sudo useradd -m -s /bin/bash 用户名`

`    -m`：为用户创建主目录。

`    -s`：指定默认的 Shell（如 /bin/bash）。

例如，创建名为 `testuser` 的用户：
`sudo useradd -m -s /bin/bash testuser`

### 2. 设置用户密码

为新用户设置密码：
`sudo passwd 用户名`

例如：
`sudo passwd testuser`

系统会提示输入两次密码以确认。

### 3. 将用户添加到特定组（可选）

如果需要赋予用户额外权限，例如加入 wheel 组以使用 sudo，可以执行以下命令：
`sudo usermod -aG wheel 用户名`

例如：
`sudo usermod -aG wheel testuser`

### 4. 配置 `sudo` 权限（可选）

编辑 `/etc/sudoers` 文件，确保 wheel 组的权限已启用：
`sudo visudo`

找到以下行并取消注释（删除行首的 #）：
`%wheel ALL=(ALL) ALL`

保存并退出。

### 5. 验证新用户

切换到新用户并测试：
`su - 用户名`

例如：
`su - testuser`

验证是否可以使用 `sudo` 命令：
`sudo pacman -Syu`

注意事项

    安全性：避免直接使用 `root` 用户执行日常任务。

    管理权限：仅为需要的用户授予 `sudo` 权限。

    备份配置：修改系统文件前建议备份。

通过以上步骤，您可以成功在 `Arch Linux` 上创建普通用户并根据需要配置权限。