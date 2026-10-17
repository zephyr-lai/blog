---
title: "我的终端工作流：mise、Hugo 与日常自动化"
meta_title: ""
description: "分享我目前在用的终端工具组合：版本管理、编辑器、构建与部署。"
date: 2026-09-20T10:00:00+08:00
categories: ["技术"]
tags: ["工具", "效率", "Hugo"]
author: "zephyrus"
draft: false
---

工具的价值不在于多，而在于**减少切换成本**。下面是我目前在用的组合。

## 版本管理：mise

以前我用 nvm 管理 Node，用 brew 管理其他工具，版本经常对不上。mise 把这些问题统一了：

```bash
mise use hugo-extended@0.158.0
mise use node@lts
```

项目根目录生成 `mise.toml`，进入目录时自动切换版本。

## 编辑器：VS Code

插件不多，核心是三个：

- 语言服务器
- Git 集成
- Markdown 预览

## 构建与部署

博客使用 Hugo，部署交给 CI：

```bash
pnpm build
```

构建产物是纯静态文件，推送到仓库后由 GitHub Actions 发布。

## 一点原则

- 能用一条命令解决的事情，不写脚本
- 能自动化的重复操作，不靠记忆
- 工具链越短，越容易维护
