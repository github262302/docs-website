# starship提示符美化


   ## 安装nerd字体和starship


   ` sudo pacman -S ttf-jetbrains-mono-nerd starship`



    编辑配置文件激活starship



`zsh`

```bash
        vim ~/.zshrc

        eval "$(starship init zsh)"
```

    https://starship.rs/presets/

    到这个网站下载一个自己喜欢的预设主题，重命名为starship.toml，放到~/.config，或者你有兴趣的话也可以自己做一个。

    重启终端
