# Git CLI 命令大全

## 基础配置

```bash
# 查看全局配置
git config --global --list

# 设置用户名和邮箱
git config --global user.name "ubuntuyj"
git config --global user.email "unyj@qq.com"

# 设置默认编辑器
git config --global core.editor vim

# 设置提交模板
git config --global commit.template ~/.gitmessage.txt
```

## 常用别名配置（推荐）

```bash
# 快速提交
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.cm "commit -m"

# 日志美化
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# 快速推送
git config --global alias.pom "push origin main"
git config --global alias.pos "push origin $(git branch --show-current)"

# 查看所有别名
git config --global alias.aliases "config --get-regexp alias"
```

## 仓库操作

```bash
# 初始化仓库
git init

# 克隆仓库
git clone https://github.com/user/repo.git

# 查看远程地址
git remote -v

# 修改远程地址
git remote set-url origin https://new-url.git
```

## 日常开发流程

```bash
# 查看状态
git status
# 或简写
git st

# 添加文件
git add filename.txt        # 添加单个文件
git add .                   # 添加所有改动
git add -p                  # 交互式选择添加

# 提交更改
git commit -m "描述信息"
git commit -am "描述"       # 添加并提交已跟踪文件

# 推送到远程
git push origin main
git push -u origin main     # 首次推送并关联分支
```

## 分支操作

```bash
# 查看分支
git branch                  # 本地分支
git branch -r               # 远程分支
git branch -a               # 所有分支

# 创建分支
git branch feature-xxx
git checkout -b feature-xxx # 创建并切换

# 切换分支
git checkout main
git switch main             # 新版本推荐

# 合并分支
git checkout main
git merge feature-xxx

# 删除分支
git branch -d feature-xxx   # 已合并
git branch -D feature-xxx   # 强制删除
```

## 查看与对比

```bash
# 查看提交历史
git log
git log --oneline           # 简洁显示
git log -5                  # 最近5条
git lg                      # 使用别名（图形化）

# 查看工作区改动
git diff
git diff --cached           # 暂存区改动
git diff HEAD~1             # 与上次提交对比

# 查看指定文件历史
git log -p filename.txt
```

## 撤销与回退

```bash
# 撤销工作区修改
git checkout -- filename.txt
git restore filename.txt    # 新版本

# 取消暂存
git reset HEAD filename.txt
git restore --staged filename.txt

# 修改上次提交
git commit --amend
git commit --amend --no-edit # 不改描述

# 回退到指定版本
git reset --hard HEAD~1     # 回退1个版本
git reset --hard commit_id  # 回退到指定commit
```

## 暂存与清理

```bash
# 临时保存
git stash
git stash save "描述信息"
git stash list              # 查看stash列表
git stash pop               # 恢复并删除
git stash apply             # 恢复不删除
git stash drop              # 删除stash

# 清理未跟踪文件
git clean -n                # 预览
git clean -f                # 强制删除
```

## 标签管理

```bash
# 创建标签
git tag v1.0.0
git tag -a v1.0.0 -m "版本1.0.0"

# 推送标签到远程
git push origin v1.0.0
git push origin --tags      # 推送所有标签

# 删除标签
git tag -d v1.0.0
```

## 快速复制粘贴版

```bash
# 一键配置常用别名
git config --global alias.st status && \
git config --global alias.co checkout && \
git config --global alias.br branch && \
git config --global alias.cm "commit -m" && \
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
