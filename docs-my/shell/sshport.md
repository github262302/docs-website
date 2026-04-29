## SSH 端口转发

### 正向代理（本地→远程）
本地端口映射到远程主机。
```
ssh -L 本地端口:目标主机:目标端口 跳板机
```
例：把本地8080映射到远程80。
```
ssh -L 8080:example.com:80 user@jump
```

### 反向代理（远程→本地）
远程端口映射到本地主机。
```
ssh -R 远程端口:本地主机:本地端口 远程服务器
```
例：把远程9090映射到本地3000。
```
ssh -R 9090:localhost:3000 user@vps
```

### 动态转发（SOCKS5）
建立动态SOCKS代理。
```
ssh -D 本地端口 跳板机
```
例：本地1080端口SOCKS5。
```
ssh -D 1080 user@proxy
```

### -N 参数
不执行远程命令仅转发。
```
ssh -N -L 8080:target:80 user@host
```

### 后台运行
转入后台执行。
```
ssh -f -N -L 8080:target:80 user@host
```

### 绑定地址
监听所有网卡。
```
ssh -L 0.0.0.0:8080:target:80 user@host
```

### 保活机制
防止连接断开。
```
ssh -o ServerAliveInterval=60 -N -L ...
```

### 网关模式
允许第三方使用。
```
ssh -g -L 8080:target:80 user@host
```