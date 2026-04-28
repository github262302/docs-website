## ssh反向代理断电后服务端端口仍被占用怎么处理

`ssh -R` 反向代理后，如果客户端异常断电，服务端端口还被占用，通常不是 TCP 残留，而是服务端 `sshd` 会话还活着。

优先用下面的命令找到占用进程：

```bash
ss -ltnp | grep :端口
lsof -nP -iTCP:端口 -sTCP:LISTEN
```

找到 PID 后执行：

```bash
kill PID
```

必要时再执行：

```bash
kill -9 PID
```

如果仍异常，可继续检查：

```bash
ps -ef | grep sshd
```

最后再考虑重启 `sshd` 服务。