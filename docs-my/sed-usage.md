---
title: sed-usage
category: tech
date: 2025-04-01
tags: []
---

# sed 命令使用指南

`sed` (Stream Editor) 是 Linux/Unix 下强大的流式文本编辑器，常用于文本替换、删除、插入等操作。

## 基础替换

```bash
# 基本替换（每行第一个匹配）
sed 's/old/new/' file.txt

# 全局替换（所有匹配）
sed 's/old/new/g' file.txt

# 替换第 N 次匹配
sed 's/old/new/2' file.txt    # 替换每行第2个
```

## 常用选项

```bash
sed -i 's/foo/bar/g' file.txt   # 直接修改文件
sed -n '5p' file.txt            # 只打印第5行
sed '3d' file.txt               # 删除第3行
sed '2,5d' file.txt             # 删除2-5行
```

## 正则表达式

```bash
sed 's/^[0-9]*/XXX/' file.txt   # 替换行首数字
sed '/pattern/d' file.txt       # 删除匹配行
sed '/^$/d' file.txt            # 删除空行
```

## 多命令组合

```bash
sed -e 's/foo/bar/g' -e 's/baz/qux/g' file.txt
sed '3a\new line' file.txt      # 第3行后追加
sed '3i\new line' file.txt      # 第3行前插入
```

## 实用技巧

```bash
sed -n '/start/,/end/p' file    # 打印区间内容
sed 's#path/to#new/path#g'      # 使用 # 作分隔符避免转义 /
sed 's/old/&_suffix/' file      # & 表示匹配内容
```

> 💡 提示：使用 `-i.bak` 可在修改前自动备份原文件。