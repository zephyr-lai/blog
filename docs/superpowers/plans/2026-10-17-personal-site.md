# 个人网站（Hugoplate）实施计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 用 hugoplate 在 `https://zephyr-lai.github.io/` 根路径重建中文个人主站，首页做导航，内容分到「项目 / 文章 / 个人简介」三个页面。

**Architecture:** 纯静态 Hugo 站点。站点级 `layouts/` 覆盖主题的首页与关于页；个人经历与论文放在 `data/` 的 YAML 数据文件里，由模板渲染；项目与文章各自是 content section，空状态由站点级 list 模板处理。样式继续使用现有的「预编译 Tailwind CSS」流程。

**Tech Stack:** Hugo 0.158.0 extended（mise 锁定）、Hugoplate 3.5.1、Tailwind CSS v4（预编译）、GitHub Pages（Actions 工作流）。

**Spec:** `docs/superpowers/specs/2026-10-17-personal-site-design.md`

## Global Constraints

- 所有 Hugo / pnpm 命令都用 `mise exec -- <command>` 执行（`mise.toml` 已锁定 `hugo-extended = "0.158.0"`）。
- 保持 project-setup：站点文件在仓库根目录，主题在 `themes/hugoplate/`；**不要修改 `themes/` 或 `_vendor/`**，所有覆盖写在根目录 `layouts/`、`assets/`、`config/`。
- 只做中文；不恢复英文语言、联系表单、评论系统。
- 站内链接一律用 `relLangURL` / `relURL`，不要写死 `/blog/` 之类的子路径。
- 改过模板或类名后，构建顺序必须是：`mise exec -- pnpm build`（刷新 `hugo_stats.json`）→ `mise exec -- pnpm css:build`（重编译 `assets/css/style-prebuilt.css`）→ `mise exec -- pnpm build`。
- 公司、学校、时间、论文标题/会议/链接必须与旧站 `content/zh/` 完全一致。
- **未经用户明确确认，不得 push、不得改名或归档任何 GitHub 仓库**（Task 6 有单独确认门）。

## Review Focus

- **空 section**：`/blog/` 与 `/projects/` 都没有内容时，必须渲染空状态文案，且分页逻辑不能报错。
- **头像资源**：`assets/images/avatar.jpg` 必须存在；缺失时构建不能崩。
- **深色模式**：自定义文字/背景类必须带 `dark:` 变体，浅色下发灰的文字在深色下仍要可读。
- **根路径部署**：所有内部链接在 `https://zephyr-lai.github.io/` 下可点，不能残留 `/blog/` 前缀。
- **窄屏**：约 400px 宽时导航折叠、首页入口换行、简介页条目不横向溢出。

---

### Task 1: 站点身份、导航与品牌清理

**Files:**
- Modify: `hugo.toml:3`
- Modify: `config/_default/params.toml`
- Modify: `config/_default/menus.zh.toml`
- Modify: `data/social.json`
- Modify: `content/chinese/_index.md`
- Modify: `content/chinese/blog/_index.md`
- Modify: `package.json`（build 脚本加 `--cleanDestinationDir`）
- Modify: `.gitignore`（忽略 `.superpowers/`）
- Rename: `content/chinese/authors/zephyrus.md` → `content/chinese/authors/zhiping.md`
- Delete: `content/chinese/blog/welcome.md`
- Delete: `content/chinese/contact/_index.md`（连同空目录）

**Interfaces:**
- Produces: `data/social.json` 的 `main[]` 项字段 `{name, label, icon, link}`；footer 与 about 页共用。
- Produces: 主导航四项 `首页 / 项目 / 文章 / 个人简介`，URL 分别为 `/`、`/projects/`、`/blog/`、`/about/`。

- [ ] **Step 1: 先跑失败检查**

```bash
mise exec -- pnpm build >/dev/null
! grep -q '赖志平' public/index.html
grep -q '几何映射' public/index.html
```

Expected: 两条都成立（新名字还没出现，旧名字还在）。

- [ ] **Step 2: 改站点标题与参数**

`hugo.toml` 第 3 行：

```toml
title = "赖志平"
```

`config/_default/params.toml` 中修改以下键（其余保持）：

```toml
logo = ""                      # 改为文字 logo
logo_text = "赖志平"
copyright = "© 2026 赖志平"

[metadata]
keywords = ["赖志平", "EDA", "电子设计自动化", "人工智能", "脑机接口", "个人网站"]
description = "赖志平的个人网站：EDA 研发工程师，关注电子设计自动化、人工智能与脑机接口。"
author = "赖志平"
twitter = ""
image = "images/avatar.jpg"

[open_remark]
enable = false
```

- [ ] **Step 3: 改导航与社交链接**

`config/_default/menus.zh.toml` 全文替换为：

```toml
############# Chinese navigation ##############

# main menu
[[main]]
name = "首页"
url = "/"
weight = 1

[[main]]
name = "项目"
url = "/projects/"
weight = 2

[[main]]
name = "文章"
url = "/blog/"
weight = 3

[[main]]
name = "个人简介"
url = "/about/"
weight = 4
```

`data/social.json` 全文替换为：

```json
{
  "main": [
    {
      "name": "email",
      "label": "邮箱",
      "icon": "fa-solid fa-envelope",
      "link": "mailto:lzping1996@gmail.com"
    },
    {
      "name": "github",
      "label": "GitHub",
      "icon": "fa-brands fa-github",
      "link": "https://github.com/zephyr-lai"
    },
    {
      "name": "google scholar",
      "label": "Google Scholar",
      "icon": "fa-brands fa-google-scholar",
      "link": "https://scholar.google.com/citations?user=oYRK793tsGYC&hl=zh-CN"
    },
    {
      "name": "linkedin",
      "label": "LinkedIn",
      "icon": "fa-brands fa-linkedin",
      "link": "https://www.linkedin.com/in/%E5%BF%97%E5%B9%B3-%E8%B5%96-45ab5134a/"
    }
  ]
}
```

- [ ] **Step 4: 清理占位内容**

- 删除 `content/chinese/blog/welcome.md`。
- 删除 `content/chinese/contact/` 整个目录。
- 把 `content/chinese/authors/zephyrus.md` 重命名为 `content/chinese/authors/zhiping.md`，内容改为：

```yaml
---
title: 赖志平
email: lzping1996@gmail.com
image: "/images/avatar.jpg"
description: "EDA 研发工程师"
---
```

`content/chinese/blog/_index.md` 改为：

```yaml
---
title: "文章"
meta_title: "文章"
description: "赖志平的文章"
---
```

- [ ] **Step 5: 让构建清理旧产物**

`package.json` 的 `build` 脚本加 `--cleanDestinationDir`：

```json
"build": "node scripts/themeGenerator.js && hugo --gc --minify --cleanDestinationDir --templateMetrics --templateMetricsHints --forceSyncStatic",
```

`.gitignore` 末尾追加：

```text
.superpowers/
```

- [ ] **Step 6: 重新构建并验证**

```bash
mise exec -- pnpm build >/dev/null
grep -q '赖志平' public/index.html
grep -q 'href="/projects/"' public/index.html
grep -q 'mailto:lzping1996@gmail.com' public/index.html
! grep -q '几何映射' public/index.html
! grep -q 'zephyrus' public/index.html
test ! -f public/contact/index.html
```

Expected: 全部通过。

- [ ] **Step 7: 提交**

```bash
git add hugo.toml config/_default/params.toml config/_default/menus.zh.toml data/social.json content/chinese package.json .gitignore docs/superpowers
git commit -m "feat: rebrand site identity and navigation for personal site"
```

---

### Task 2: 首页导航布局

**Files:**
- Modify: `content/chinese/_index.md`
- Modify: `layouts/home.html`

**Interfaces:**
- Consumes: `.Params.role`、`.Params.bio`（首页 front matter）、`site.Menus.main`。
- Produces: 首页三个入口，href 为 `/projects/`、`/blog/`、`/about/`。

- [ ] **Step 1: 写失败检查**

```bash
grep -q 'EDA 研发工程师' public/index.html
```

Expected: FAIL（当前首页没有这行）。

- [ ] **Step 2: 补首页 front matter**

`content/chinese/_index.md` 全文：

```yaml
---
title: "赖志平"
role: "EDA 研发工程师 · 上海立芯软件科技有限公司"
bio: "复旦大学计算机应用技术硕士。关注电子设计自动化、人工智能与脑机接口。"
---
```

- [ ] **Step 3: 写首页布局**

`layouts/home.html` 全文：

```html
{{ define "main" }}
  <section class="section">
    <div class="container max-w-3xl text-center">
      <h1 class="text-h2 mb-4">{{ .Title }}</h1>
      <p class="text-text-dark dark:text-darkmode-text-dark mb-2 text-lg">{{ .Params.role }}</p>
      <p class="mb-12 text-text-light dark:text-darkmode-text-light">{{ .Params.bio }}</p>
      <div class="flex flex-wrap justify-center gap-x-12 gap-y-6">
        {{ range site.Menus.main }}
          {{ if ne .Name "首页" }}
            <a
              class="text-text-dark hover:text-primary dark:text-darkmode-text-dark dark:hover:text-darkmode-primary text-lg"
              href="{{ .URL | relLangURL }}">
              {{ .Name }}
            </a>
          {{ end }}
        {{ end }}
      </div>
    </div>
  </section>
{{ end }}
```

- [ ] **Step 4: 构建并验证**

```bash
mise exec -- pnpm build >/dev/null
grep -q 'EDA 研发工程师' public/index.html
grep -q '复旦大学计算机应用技术硕士' public/index.html
grep -q 'href="/about/"' public/index.html
test "$(grep -c 'href="/projects/"' public/index.html)" -ge 1
! grep -q '字节跳动' public/index.html
```

Expected: 全部通过；首页不出现工作经历内容。

- [ ] **Step 5: 提交**

```bash
git add content/chinese/_index.md layouts/home.html
git commit -m "feat: build navigation-focused homepage"
```

---

### Task 3: 个人简介页（数据文件 + 布局）

**Files:**
- Create: `data/cv.yaml`
- Create: `data/publications.yaml`
- Create: `assets/images/avatar.jpg`（从旧站复制）
- Rewrite: `content/chinese/about/_index.md`
- Create: `layouts/about.html`

**Interfaces:**
- Produces: `site.Data.cv.work[]` 字段 `{company, position, start, end, summary}`；`site.Data.cv.education[]` 字段 `{school, degree, start, end, summary}`。
- Produces: `site.Data.publications[]` 字段 `{title, venue, year, first_author, url}`，顺序即展示顺序。
- 布局输出 class：每个工作条目 `work-item`、教育条目 `education-item`、论文条目 `publication-item`，供检查计数。

- [ ] **Step 1: 写失败检查**

```bash
test -f public/about/index.html
grep -q '工作经历' public/about/index.html
```

Expected: FAIL（当前 about 还是旧内容）。

- [ ] **Step 2: 复制头像**

```bash
cp /Users/lzping/workspace/profile/zephyr-lai.github.io/content/zh/authors/admin/avatar.jpg assets/images/avatar.jpg
```

- [ ] **Step 3: 写经历数据**

`data/cv.yaml` 全文：

```yaml
work:
  - company: 上海立芯软件科技有限公司
    position: 研发工程师
    start: 2022.07
    end: 至今
    summary: ""
  - company: 字节跳动
    position: 算法实习生
    start: 2021.10
    end: 2022.03
    summary: 参与头条搜索中台 query 分析、召回、排序等阶段算法优化，支持西瓜、剪映、Resso 等搜索业务。
  - company: 商汤科技
    position: 见习研究员
    start: 2021.01
    end: 2021.04
    summary: 负责业务模型接入模型池，适配 SenseParrots 深度学习框架，并为算子与相关 API 提供支持。
  - company: 复旦微电子集团
    position: 软件实习生
    start: 2019.06
    end: 2020.06
    summary: 参与基于线长驱动的 FPGA 全局布局多级框架搭建与算法优化，设计时延估计模型。

education:
  - school: 复旦大学
    degree: 理学硕士 计算机应用技术
    start: 2019.09
    end: 2022.06
    summary: 研究方向：基于表面肌电信号（sEMG）的深度学习与手势识别。
  - school: 福州大学
    degree: 工学学士 计算机科学与技术
    start: 2015.09
    end: 2019.06
    summary: ""
```

- [ ] **Step 4: 写论文数据**

`data/publications.yaml` 全文：

```yaml
- title: "Contrastive Domain Adaptation: A Self-Supervised Learning Framework for sEMG-Based Gesture Recognition"
  venue: "IEEE IJCB"
  year: 2022
  first_author: true
  url: "https://ieeexplore.ieee.org/abstract/document/10008005"
- title: "A Teleoperated Manipulator Control System Based on Surface Electromyography Using Deep Learning"
  venue: "IEEE CYBER"
  year: 2022
  first_author: true
  url: "https://ieeexplore.ieee.org/abstract/document/9907033"
- title: "STCN-GR: Spatial-Temporal Convolutional Networks for Surface-Electromyography-Based Gesture Recognition"
  venue: "ICONIP"
  year: 2021
  first_author: true
  url: "https://link.springer.com/chapter/10.1007/978-3-030-92238-2_3"
- title: "A Self-Adaptive LSE Wirelength Model for VLSI Global Placement"
  venue: "IEEE ICSICT"
  year: 2018
  first_author: true
  url: "https://ieeexplore.ieee.org/abstract/document/8564925"
- title: "Constrained Contrastive Representation: Classification On Chest X-Rays With Limited Data"
  venue: "IEEE ICME"
  year: 2021
  first_author: false
  url: "https://ieeexplore.ieee.org/abstract/document/9428273"
- title: "GTGR-Net: Graph Attentional-Temporal Network for Surface-Electromyography-Based Gesture Recognition"
  venue: "CNIOT"
  year: 2022
  first_author: false
  url: "https://ieeexplore.ieee.org/abstract/document/9814747"
- title: "Semantic Segmentation of Surgical Instruments based on Enhanced Multi-scale Receptive Field"
  venue: "Journal of Physics: Conference Series"
  year: 2021
  first_author: false
  url: "https://dx.doi.org/10.1088/1742-6596/2003/1/012006"
- title: "A low-cost passenger safety protection system based on laser ranging"
  venue: "Journal of Physics: Conference Series"
  year: 2021
  first_author: false
  url: "https://dx.doi.org/10.1088/1742-6596/2003/1/012009"
```

- [ ] **Step 5: 写关于页内容**

`content/chinese/about/_index.md` 全文：

```markdown
---
title: "个人简介"
meta_title: "个人简介"
description: "赖志平的个人简介：工作经历、教育背景与论文。"
layout: "about"
image: "images/avatar.jpg"
draft: false
---

我目前任职于上海立芯软件科技有限公司，担任研发工程师，专注于电子设计自动化及相关领域的技术研发。此前曾在复旦微电子集团 FPGA 事业部、商汤科技和字节跳动从事研发实习。2019 年获得福州大学计算机科学与技术工学学士学位，2022 年获得复旦大学计算机应用技术理学硕士学位。主要兴趣方向为电子设计自动化、人工智能与脑机接口。
```

- [ ] **Step 6: 写关于页布局**

`layouts/about.html` 全文：

```html
{{ define "main" }}
  <section class="section-sm">
    <div class="container">
      <div class="row justify-center">
        <div class="md:col-10 lg:col-8">
          <div class="mb-10 flex flex-col items-center gap-6 sm:flex-row">
            {{ with .Params.image }}
              {{ partial "image" (dict "Src" . "Alt" site.Title "Size" "112x112" "Class" "h-28 w-28 rounded-full object-cover") }}
            {{ end }}
            <div class="text-center sm:text-left">
              <h1 class="h3 mb-2">{{ site.Title }}</h1>
              <p class="text-text-light dark:text-darkmode-text-light">{{ site.Params.metadata.description }}</p>
            </div>
          </div>

          <div class="content">{{ .Content }}</div>

          <h2 class="h4 mt-14 mb-6">工作经历</h2>
          {{ range site.Data.cv.work }}
            <div class="work-item border-border dark:border-darkmode-border mb-6 border-b pb-6 last:border-0">
              <div class="flex flex-wrap items-baseline justify-between gap-2">
                <h3 class="text-lg font-semibold">{{ .company }} · {{ .position }}</h3>
                <span class="text-text-light dark:text-darkmode-text-light text-sm">{{ .start }} – {{ .end }}</span>
              </div>
              {{ with .summary }}<p class="text-text-light dark:text-darkmode-text-light mt-2">{{ . }}</p>{{ end }}
            </div>
          {{ end }}

          <h2 class="h4 mt-14 mb-6">教育经历</h2>
          {{ range site.Data.cv.education }}
            <div class="education-item border-border dark:border-darkmode-border mb-6 border-b pb-6 last:border-0">
              <div class="flex flex-wrap items-baseline justify-between gap-2">
                <h3 class="text-lg font-semibold">{{ .school }} · {{ .degree }}</h3>
                <span class="text-text-light dark:text-darkmode-text-light text-sm">{{ .start }} – {{ .end }}</span>
              </div>
              {{ with .summary }}<p class="text-text-light dark:text-darkmode-text-light mt-2">{{ . }}</p>{{ end }}
            </div>
          {{ end }}

          <h2 class="h4 mt-14 mb-6">论文</h2>
          <ul class="space-y-5">
            {{ range site.Data.publications }}
              <li class="publication-item">
                <a class="text-text-dark dark:text-darkmode-text-dark font-medium hover:underline" href="{{ .url }}" target="_blank" rel="noopener">
                  {{ .title }}
                </a>
                <div class="text-text-light dark:text-darkmode-text-light mt-1 text-sm">
                  {{ .venue }} · {{ .year }}{{ if .first_author }} · 第一作者{{ end }}
                </div>
              </li>
            {{ end }}
          </ul>

          <h2 class="h4 mt-14 mb-6">联系方式</h2>
          <ul class="flex flex-wrap gap-x-6 gap-y-3">
            {{ range hugo.Data.social.main }}
              <li>
                <a class="hover:text-primary dark:hover:text-darkmode-primary" href="{{ .link | safeURL }}" {{ if hasPrefix .link "http" }}target="_blank" rel="noopener"{{ end }}>
                  {{ .label | default .name }}
                </a>
              </li>
            {{ end }}
          </ul>
        </div>
      </div>
    </div>
  </section>
{{ end }}
```

- [ ] **Step 7: 构建并验证**

```bash
mise exec -- pnpm build >/dev/null
grep -q '工作经历' public/about/index.html
grep -q '上海立芯软件科技有限公司' public/about/index.html
grep -q '复旦大学' public/about/index.html
grep -q 'Contrastive Domain Adaptation' public/about/index.html
grep -q 'avatar' public/about/index.html
test "$(grep -c 'work-item' public/about/index.html)" -eq 4
test "$(grep -c 'education-item' public/about/index.html)" -eq 2
test "$(grep -c 'publication-item' public/about/index.html)" -eq 8
test "$(grep -o '第一作者' public/about/index.html | wc -l | tr -d ' ')" -eq 4
```

Expected: 全部通过。

- [ ] **Step 8: 提交**

```bash
git add data/cv.yaml data/publications.yaml assets/images/avatar.jpg content/chinese/about/_index.md layouts/about.html
git commit -m "feat: add about page with experience, education and publications"
```

---

### Task 4: 项目与文章空状态

**Files:**
- Create: `content/chinese/projects/_index.md`
- Create: `layouts/projects/list.html`
- Modify: `layouts/blog/list.html`

**Interfaces:**
- Consumes: `.RegularPages` 是否为空。
- Produces: `/projects/` 空状态文案「项目正在整理中，敬请期待。」；`/blog/` 空状态文案「还没有文章，敬请期待。」

- [ ] **Step 1: 写失败检查**

```bash
test -f public/projects/index.html
grep -q '项目正在整理中' public/projects/index.html
```

Expected: FAIL（项目页还不存在）。

- [ ] **Step 2: 建项目 section**

`content/chinese/projects/_index.md` 全文：

```yaml
---
title: "项目"
meta_title: "项目"
description: "赖志平的项目"
---
```

- [ ] **Step 3: 写项目列表模板**

`layouts/projects/list.html` 全文：

```html
{{ define "main" }}
  <section class="section">
    <div class="container max-w-3xl">
      <h1 class="text-h3 mb-10">{{ .Title }}</h1>
      {{ if .RegularPages }}
        <ul class="space-y-4">
          {{ range .RegularPages }}
            <li>
              <a class="text-text-dark dark:text-darkmode-text-dark text-lg hover:underline" href="{{ .RelPermalink }}">{{ .Title }}</a>
            </li>
          {{ end }}
        </ul>
      {{ else }}
        <p class="text-text-light dark:text-darkmode-text-light">项目正在整理中，敬请期待。</p>
      {{ end }}
    </div>
  </section>
{{ end }}
```

- [ ] **Step 4: 给文章列表加空状态**

`layouts/blog/list.html` 全文：

```html
{{ define "main" }}
  <section class="section">
    <div class="container max-w-3xl">
      <h1 class="text-h3 mb-10">{{ .Title }}</h1>
      {{ if .RegularPages }}
        {{ $paginator := .Paginate .RegularPages }}
        {{ range $paginator.Pages }}
          {{ partial "components/post-list-item" . }}
        {{ end }}
        <div class="mt-10">
          {{ partial "components/pagination.html" . }}
        </div>
      {{ else }}
        <p class="text-text-light dark:text-darkmode-text-light">还没有文章，敬请期待。</p>
      {{ end }}
    </div>
  </section>
{{ end }}
```

- [ ] **Step 5: 构建并验证**

```bash
mise exec -- pnpm build >/dev/null
test -f public/projects/index.html
grep -q '项目正在整理中' public/projects/index.html
grep -q '还没有文章' public/blog/index.html
test ! -f public/blog/welcome/index.html
```

Expected: 全部通过。

- [ ] **Step 6: 提交**

```bash
git add content/chinese/projects/_index.md layouts/projects/list.html layouts/blog/list.html
git commit -m "feat: add projects page and empty states for projects and blog"
```

---

### Task 5: 样式重编译与全站验收

**Files:**
- Modify (generated): `assets/css/style-prebuilt.css`
- Modify (generated): `hugo_stats.json`

**Interfaces:**
- Consumes: Task 1–4 的全部模板与内容。

- [ ] **Step 1: 按顺序重建**

```bash
mise exec -- pnpm build >/dev/null
mise exec -- pnpm css:build
mise exec -- pnpm build >/dev/null
```

- [ ] **Step 2: 全站内容检查**

```bash
grep -q '赖志平' public/index.html
grep -q 'EDA 研发工程师' public/index.html
grep -q 'href="/projects/"' public/index.html
grep -q 'href="/blog/"' public/index.html
grep -q 'href="/about/"' public/index.html
grep -q '工作经历' public/about/index.html
grep -q '项目正在整理中' public/projects/index.html
grep -q '还没有文章' public/blog/index.html
! grep -rq '几何映射\|zephyrus\|Welcome to Hugoplate' public/
! grep -rq 'open-remark' public/
```

Expected: 全部通过。

- [ ] **Step 3: 检查 CSS 已包含新用到的工具类**

```bash
grep -q 'max-w-3xl' assets/css/style-prebuilt.css
grep -q 'rounded-full' assets/css/style-prebuilt.css
grep -q 'space-y-5' assets/css/style-prebuilt.css
```

Expected: 三条都通过。

- [ ] **Step 4: 本地预览人工验收**

```bash
mise exec -- pnpm dev
```

打开 `http://localhost:1313/`，逐项确认：

- 首页一屏内：姓名、职位、一句话简介、三个入口。
- `/about/`：头像、简介、4 段工作、2 段教育、8 篇论文（4 篇标第一作者）、联系方式。
- `/projects/`、`/blog/`：空状态文案正常。
- 切换到深色模式，文字仍清晰。
- 浏览器窗口缩到约 400px，导航折叠、入口换行、不横向滚动。

若无法人工查看，用 curl 检查上述 HTML 后请用户确认视觉效果。

- [ ] **Step 5: 提交**

```bash
git add assets/css/style-prebuilt.css hugo_stats.json
git commit -m "chore: rebuild prebuilt tailwind css for personal site"
```

---

### Task 6: 部署（需用户明确确认后才执行）

**Files:**
- 无代码改动；GitHub 仓库操作。

**Interfaces:**
- Consumes: 本地 `main` 分支已提交、Task 5 验收通过。

- [ ] **Step 1: 向用户复述并取得确认**

复述将要执行的对外操作：旧仓库改名 → 当前仓库改名 → 推送 → 验证 → 归档旧仓库。**用户明确同意后再继续。**

- [ ] **Step 2: 释放旧仓库名**

```bash
gh repo rename zephyr-lai.github.io-legacy -R zephyr-lai/zephyr-lai.github.io
```

- [ ] **Step 3: 当前仓库接管用户站名**

```bash
gh repo rename zephyr-lai.github.io -R zephyr-lai/blog
git remote set-url origin git@github.com:zephyr-lai/zephyr-lai.github.io.git
```

- [ ] **Step 4: 确认 Pages 来源**

```bash
gh api repos/zephyr-lai/zephyr-lai.github.io/pages --jq .build_type
```

Expected: `workflow`。若返回 404，启用：

```bash
gh api -X POST repos/zephyr-lai/zephyr-lai.github.io/pages -f build_type=workflow
```

- [ ] **Step 5: 推送并等待部署**

```bash
git push origin main
gh run list --repo zephyr-lai/zephyr-lai.github.io --limit 1
gh run watch --repo zephyr-lai/zephyr-lai.github.io
```

Expected: 工作流 `Deploy Hugo site to Pages` 成功。

- [ ] **Step 6: 线上验证**

```bash
curl -s https://zephyr-lai.github.io/ | grep -q '赖志平'
curl -s https://zephyr-lai.github.io/about/ | grep -q '工作经历'
curl -s https://zephyr-lai.github.io/projects/ | grep -q '项目正在整理中'
```

Expected: 三条都通过。

- [ ] **Step 7: 归档旧站仓库**

```bash
gh repo archive zephyr-lai/zephyr-lai.github.io-legacy -y
```
