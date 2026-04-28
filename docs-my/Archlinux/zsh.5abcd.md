这是典型的 **终端键位序列没被 zsh 正确识别**，不是你按键坏了。

你现在看到 `;5C` / `;5D`，说明终端发的是类似：

```
^[[1;5C   (Ctrl + →)
^[[1;5D   (Ctrl + ←)
```

但 zsh 没绑定。

---

## 直接给你最干净的解决方案（加到 `.zshrc`）

```zsh
# Ctrl + ← →
bindkey '^[[1;5C' forward-word
bindkey '^[[1;5D' backward-word

# 兼容另一种终端
bindkey '^[[5C' forward-word
bindkey '^[[5D' backward-word
```

然后执行：

```bash
source ~/.zshrc
```

---

## 如果还不行（更稳）

先用这个命令抓真实按键：

```bash
cat
```

然后按 `Ctrl + ←`，看看输出是什么，比如：

```
^[[1;5D
```

就按这个格式绑定：

```zsh
bindkey '你刚刚看到的内容' backward-word
```

---

## 根本原因（给你讲清楚）

* 你现在用的终端（可能是 Alacritty / Kitty / SSH）
* 发送的是 **ANSI escape sequence**
* zsh 默认没帮你绑定 Ctrl+方向键（只支持 Alt+←→）

---

## 更高级一点（推荐）

直接启用 zsh 官方风格键位：

```zsh
bindkey -e
```

或者用 vim 模式：

```zsh
bindkey -v
```

---

## 结论

你这个不是 bug，就是：
👉 **zsh 没默认绑定 Ctrl+方向键**

加 `bindkey` 就完事，别折腾别的。
