---
title: Shell脚本入门
category: tutorial
date: 2025-04-01
tags: []
---


# Shell脚本入门：让电脑自动干活

Shell脚本就是"命令清单"。把一堆命令写进文件，让电脑自动执行，省去重复敲键盘的麻烦。

**1. 写第一个脚本**
创建文件 `hello.sh`：
```bash
#!/bin/bash
echo "你好，世界！"
```
运行前先给执行权限：`chmod +x hello.sh`，然后运行：`./hello.sh`。第一行`#!/bin/bash`是"说明书"，告诉系统用Bash来翻译。

**2. 变量和数组**
变量是"临时仓库"：
```bash
name="小明"
age=15
echo "我叫$name，今年$age岁"
```
数组是"一排仓库"：
```bash
colors=("红" "绿" "蓝")
echo ${colors[0]}    # 输出"红"（从0开始数）
echo ${colors[@]}    # 输出全部
```

**3. 命令行参数**
运行脚本时传数据：`./script.sh 苹果 香蕉`
脚本里用`$1`、`$2`接收：
```bash
echo "第一个礼物：$1"
echo "第二个礼物：$2"
echo "总共给了$#个礼物"
```

**4. 做决定和循环**
if判断：
```bash
if [ $1 -gt 10 ]; then
    echo "大于10"
elif [ $1 -eq 10 ]; then
    echo "等于10"
else
    echo "小于10"
fi
```
for循环：
```bash
for i in 1 2 3 4 5; do
    echo "第${i}次"
done
```
while循环：
```bash
i=0
while [ $i -lt 5 ]; do
    echo $i
    ((i++))    # 数字加1
done
```

**5. 函数（乐高积木）**
把常用代码块包起来复用：
```bash
say_hello() {
    echo "你好，$1"
    return 0
}
say_hello "小明"    # 调用
```

**6. 计算和文字处理**
算术运算：
```bash
echo $((3+5))        # 输出8
let "a=10/2"
```
字符串操作：
```bash
text="HelloWorld"
echo ${#text}        # 长度：10
echo ${text:0:5}     # 截取前5个：Hello
```

**7. 特殊变量和防错**
`$?`看上条命令是否成功（0是成功，其他是失败）。
`trap`命令防误触：
```bash
trap "echo '别急，正在清理...'" EXIT    # 退出前执行
trap "" INT                             # 忽略Ctrl+C
```

**8. 文件测试**
```bash
if [ -f "a.txt" ]; then      # 是普通文件？
    echo "文件存在"
elif [ -d "mydir" ]; then    # 是目录？
    echo "这是个文件夹"
fi
# -r可读 -w可写 -x可执行 -e存在
```

**9. 实战：文件备份器**
```bash
#!/bin/bash
if [ $# -eq 0 ]; then
    echo "用法：$0 文件名"
    exit 1
fi

if [ ! -f "$1" ]; then
    echo "错误：文件不存在"
    exit 1
fi

backup="$1.bak_$(date +%Y%m%d)"
cp "$1" "$backup"
echo "已备份为：$backup"
```
保存为`backup.sh`，运行`./backup.sh 重要文档.txt`，自动加上日期备份。