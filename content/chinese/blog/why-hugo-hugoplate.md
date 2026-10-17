---
title: "从零搭建一个 Hugo 博客：为什么我选择了 Hugoplate"
meta_title: ""
description: "记录这个博客的搭建过程：从静态站点生成器选型，到 Hugo Modules、Tailwind CSS v4 与自动化部署。"
date: 2026-10-16T09:30:00+08:00
categories: ["技术"]
tags: ["Hugo", "前端", "工具"]
author: "zhiping"
draft: false
---

搭建个人博客这件事，我拖了很久。不是因为没有内容可写，而是因为选择太多：要自己写前端，还是用现成主题？要动态站点，还是静态生成？

最终我选择了 **Hugo + Hugoplate**，原因有三点。

## 为什么是静态站点

静态站点把「内容」和「运行」彻底分开：

- 写作只发生在 Markdown 文件里
- 构建产物是纯 HTML/CSS/JS，没有数据库和服务器进程
- 部署可以放在任何对象存储或 CDN 上

对个人博客来说，这意味着**几乎没有维护成本**。

## Hugo 的构建速度

Hugo 用 Go 编写，构建一个几十页的站点通常只需要几十毫秒。对比之下，很多基于 JavaScript 的生成器需要数秒甚至更久。

对于习惯了「保存即刷新」的写作者，这个差距会直接影响写作节奏。

## 主题与可定制性

Hugoplate 提供了现代的工具链：

1. Tailwind CSS v4 的 CSS-first 配置
2. Hugo Modules 管理可复用的组件
3. 内置搜索、标签、RSS 与 SEO 支持

更重要的是，它的模板结构清晰，覆盖任意页面都不需要 fork 整个主题。

```bash
pnpm dev      # 本地开发
pnpm build    # 生产构建
```

## 接下来

这个博客会记录我在技术、系统设计与阅读上的思考。如果你也在搭建自己的站点，欢迎交流。
