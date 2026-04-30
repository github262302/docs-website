---
title: notWritable
category: n8n
date: 2025-04-01
tags: []
---



## Not Write 出错

`NodeApiError: The file "/home/user/n8n/test.png" is not writable.`



# 打开配置文件
`nano ~/.zshrc`

# 添加以下内容
```bash
export N8N_BINARY_DATA_MODE=filesystem
export N8N_RESTRICT_FILE_ACCESS_TO=/home/user/n8n
```
# 保存后执行
```bash
source ~/.zshrc
```

# 启动 n8n
```bash
n8n
```


#### link

https://www.cnblogs.com/hesw/p/19822070
