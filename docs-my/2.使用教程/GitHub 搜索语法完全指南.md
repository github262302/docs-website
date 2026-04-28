# GitHub 搜索语法完全指南

GitHub 拥有强大的搜索功能，掌握这些搜索语法能让你高效找到优质项目和代码。

## 基础搜索

### 关键词搜索
直接输入关键词即可搜索仓库、代码、Issues 等：
```
machine learning
```

### 精确匹配
使用引号进行精确短语匹配：
```
"deep learning"
```

## 仓库搜索限定符

### 按语言筛选
```
language:python
language:javascript
```

### 按星标数量
```
stars:>1000        # 超过1000星
stars:100..500     # 100到500星之间
stars:>=5000       # 至少5000星
```

### 按更新时间
```
pushed:>2024-01-01     # 2024年后更新
pushed:>2023-12-01     # 最近3个月更新
```

### 按创建时间
```
created:>2023-01-01    # 2023年后创建
```

### 按仓库大小
```
size:>10000            # 大于10MB
size:1000..5000        # 1MB到5MB之间
```

### 按主题标签
```
topic:machine-learning
topic:react
```

### 按仓库名
```
in:name react
```

### 按描述
```
in:description "backend framework"
```

### 按 README
```
in:readme "getting started"
```

## 代码搜索限定符

### 文件扩展名
```
extension:py
extension:js
```

### 文件路径
```
path:src/main
path:test
```

### 文件内容
```
filecontent:"import os"
```

### 按文件名
```
filename:config.json
filename:README.md
```

### 按组织或个人
```
user:torvalds
org:google
repo:microsoft/vscode
```

## Issues 和 PR 搜索

### 按状态
```
is:open
is:closed
is:merged
```

### 按标签
```
label:bug
label:"help wanted"
label:enhancement
```

### 按作者
```
author:username
```

### 按指派人
```
assignee:username
```

### 按评论数
```
comments:>10
```

## 组合搜索示例

### 找高质量 Python 项目
```
language:python stars:>1000 pushed:>2023-12-01
```

### 找热门前端框架
```
language:javascript stars:>5000 topic:framework
```

### 找机器学习教程
```
"machine learning" tutorial in:readme stars:>100
```

### 找待解决的 Bug
```
is:open is:issue label:bug language:python
```

### 找特定功能的代码
```
"async def" language:python stars:>100
```

## 高级技巧

### 排除关键词
使用 `-` 排除不需要的结果：
```
web framework -php
```

### 逻辑运算
```
react AND vue
react OR vue
```

### 按许可证筛选
```
license:mit
license:apache-2.0
```

### 按 Fork 状态
```
fork:true      # 包含 Fork
fork:false     # 不包含 Fork
```

### 按归档状态
```
archived:false    # 只显示活跃仓库
```

## 实用搜索场景

| 需求 | 搜索语法 |
|------|----------|
| 找面试题 | `interview stars:>100` |
| 找教程项目 | `tutorial language:python stars:>50` |
| 找脚手架 | `boilerplate react stars:>500` |
| 找微服务案例 | `microservices example language:go` |
| 找爬虫项目 | `spider
crawler language:python stars:>200` |

## 总结

GitHub 搜索语法格式为：`关键词 限定符:值`

常用限定符：
- `in` - 搜索范围
- `language` - 编程语言
- `stars` - 星标数量
- `pushed` - 最后推送时间
- `user/org/repo` - 用户/组织/仓库
- `topic` - 主题标签

掌握这些语法，让你在 GitHub 海量资源中精准定位所需内容！
