---
title: Ubuntu如何在当前目录再开一个zsh终端窗口
category: devops
date: 2025-04-01
tags: []
---

## Ubuntu如何在当前目录再开一个zsh终端窗口

如果想在 Ubuntu 的“当前目录”再打开一个新的 `zsh` 终端窗口，可以执行：

```bash
gnome-terminal --working-directory="$PWD" -- zsh
```

如果只是想在当前窗口进入一层新的 shell，直接执行：

```bash
zsh
```

常用的话可以在 `~/.zshrc` 里加别名：

```bash
alias dupzsh='gnome-terminal --working-directory="$PWD" -- zsh'
```