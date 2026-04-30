---
title: 安全环境变量
description: 在自托管 n8n 实例中配置身份验证和环境变量访问。
contentType: reference
tags:
  - environment variables
hide:
  - toc
  - tags
category: n8n
---

# 安全环境变量

--8<-- "_snippets/self-hosting/file-based-configuration.md"

| 变量 | 类型  | 默认值  | 描述 |
| :------- | :---- | :------- | :---------- |
| `N8N_BLOCK_ENV_ACCESS_IN_NODE` | Boolean | `false` | 是否允许用户在表达式和 Code 节点中访问环境变量。设为 false 表示允许，true 表示不允许。 |
| `N8N_BLOCK_FILE_ACCESS_TO_N8N_FILES` | Boolean | `true` | 设为 `true` 可阻止访问 `.n8n` 目录中的所有文件以及用户定义的配置文件。 |
| `N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS` | Boolean | `false` | 设为 `true` 以尝试为设置文件设置 0600 权限，仅授予所有者读写访问权限。 |
| `N8N_RESTRICT_FILE_ACCESS_TO` | String |  | 限制对这些目录中文件的访问。多个目录请使用分号分隔（"`;`"）的列表形式提供。 |
| `N8N_SECURITY_AUDIT_DAYS_ABANDONED_WORKFLOW` | Number | 90 | 如果工作流未被执行，将其视为废弃工作流的天数。 |
|`N8N_CONTENT_SECURITY_POLICY`| String | `{}` | 将 [Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 标头设置为 [helmet.js](https://helmetjs.github.io/#content-security-policy) 嵌套指令对象。例如 `{ "frame-ancestors": ["http://localhost:3000"] }` |
| `N8N_SECURE_COOKIE` | Boolean | `true` | 确保 Cookie 仅通过 HTTPS 发送，以增强安全性。|
| `N8N_SAMESITE_COOKIE` | Enum string: `strict`, `lax`, `none` | `lax` | 控制跨站 Cookie 行为（[了解更多](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)）：<ul><li>`strict`：仅对第一方请求发送。</li><li>`lax`（默认）：随顶级导航请求发送。</li><li>`none`：在所有上下文中发送（需要 HTTPS）。</li></ul> |
| `N8N_GIT_NODE_DISABLE_BARE_REPOS` | Boolean | `false` | 设为 `true` 可阻止 [Git 节点](/integrations/builtin/core-nodes/n8n-nodes-base.git.md) 与裸仓库一起工作，以增强安全性。 |
| `N8N_GIT_NODE_ENABLE_HOOKS` | Boolean | `false` | 设为 `true` 以允许 [Git 节点](/integrations/builtin/core-nodes/n8n-nodes-base.git.md) 执行 Git 钩子。 |