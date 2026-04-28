## pnpm如何把cli.js注册成全局命令

想把项目里的 `cli.js` 变成全局可用的命令，分两步。

第一步，在 `package.json` 里添加 `bin` 字段：

```json
{
  "bin": {
    "work-video": "./cli.js"
  }
}
```

其中 `work-video` 是最终的命令名，值指向入口脚本。

入口脚本首行必须有：

```js
#!/usr/bin/env node
```

第二步，执行全局链接：

```bash
pnpm link --global
```

执行后，pnpm 会在全局 bin 目录创建符号链接，之后就可以在任意目录运行 `work-video <command>`。

补充：

- 如果改了 `bin` 的命令名，需要重新执行 `pnpm link --global`
- 取消链接可用 `pnpm unlink --global`
- 如果是 npm，则对应命令是 `npm link`