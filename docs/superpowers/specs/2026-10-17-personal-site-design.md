# 个人网站设计文档（替换 zephyr-lai.github.io）

日期：2026-10-17
状态：待用户确认

## 意图与原因

赖志平（Zhiping Lai）需要一个以 hugoplate 为模板重建的个人主站，整体替换现有的
zephyr-lai.github.io（Hugo Blox 学术主题）。旧站内容冗长、模板痕迹重；新站以「首页做导航」
为核心，只保留个人简介、工作经历、论文与博客入口，信息精简、观感干净可信。

## 目标

- 用 hugoplate 仓库（`/Users/lzping/workspace/profile/hugoplate`）重建个人主站，发布在
  `https://zephyr-lai.github.io/` 根路径。
- 中文单语。
- 首页只承担导航作用：姓名、职位、一句话简介，以及「项目 / 文章 / 个人简介」三个入口。
- 独立页面承载实质内容：
  - `/about/`：个人简介、工作经历、教育经历、论文（8 篇）。
  - `/projects/`：项目列表，当前为空页。
  - `/blog/`：文章列表，当前为空页（导航标签叫「文章」）。
- 视觉沿用 hugoplate 的简约干净风格，保留浅色/深色主题切换。

## 非目标

- 不做英文版或多语言切换。
- 不迁移旧站的奖项列表、联系表单、教学/活动等板块。
- 本次不编写项目内容与博客文章（只搭页面和空状态）。
- 不接入评论系统、CMS、统计服务。
- 不做花哨的动效、插画或大面积渐变。

## 反目标（即使技术上能用也算失败）

- 首页再次堆满内容，需要滚动很久才能看完。
- 视觉上像一份模板演示站，而不是个人站。
- 为了改样式而破坏现有的「预编译 CSS」构建方式，导致本机无法构建。
- 未经用户确认就执行 GitHub 仓库改名、归档、推送等对外操作。

## 已定决策

### 站点身份

- 站名 / logo 文字：`赖志平`
- 默认标题：`赖志平`
- 描述：`赖志平的个人网站：EDA 研发工程师，关注电子设计自动化、人工智能与脑机接口。`
- 头像：使用旧站头像（`zephyr-lai.github.io/content/zh/authors/admin/avatar.jpg`），复制到
  `assets/images/` 下使用。**头像只出现在 `/about/` 页**，首页不放头像。
- 社交链接（页脚 / 简介页）：邮箱 `lzping1996@gmail.com`、GitHub `zephyr-lai`、
  Google Scholar、LinkedIn（URL 取自旧站）。
- 移除现有「几何映射 / zephyrus」品牌信息（标题、logo_text、metadata、copyright、作者文件）。

### 页面与导航

顶部导航（`config/_default/menus.zh.toml`）：

1. 首页 `/`
2. 项目 `/projects/`
3. 文章 `/blog/`
4. 个人简介 `/about/`

首页：

- 沿用 hugoplate 的 header / footer。
- 主内容：居中单栏。姓名、职位（EDA 研发工程师 · 上海立芯软件科技有限公司）、一句话简介
  （复旦大学计算机应用技术硕士，关注电子设计自动化、人工智能与脑机接口），下方三个文字入口：
  项目、文章、个人简介。
- 页脚：邮箱 + GitHub + Scholar + LinkedIn。
- 不展示经历列表、论文列表或文章列表。

个人简介页 `/about/`：

- 顺序：简介段落 → 工作经历 → 教育经历 → 论文 → 联系方式。
- 简介段落配头像（旧站头像），位置（旁侧或上方）与尺寸留给实现者，以简洁为准。
- 工作经历（来自旧站）：
  - 上海立芯软件科技有限公司 · 研发工程师，2022.07 – 至今
  - 字节跳动 · 算法实习生，2021.10 – 2022.03（摘要：头条搜索中台，query 分析/召回/排序）
  - 商汤科技 · 见习研究员，2021.01 – 2021.04（模型池、SenseParrots 适配）
  - 复旦微电子集团 · 软件实习生，2019.06 – 2020.06（FPGA 全局布局、时延估计）
- 教育经历：
  - 复旦大学 · 理学硕士 计算机应用技术，2019.09 – 2022.06
  - 福州大学 · 工学学士 计算机科学与技术，2015.09 – 2019.06
- 论文：全部 8 篇，按旧站 `weight`（即展示顺序）排列，标注第一作者；标题、会议/期刊、年份、
  DOI/链接取自旧站 `content/zh/publication/*/index.md`。

项目页 `/projects/`：

- 列表页结构复用博客列表样式；无内容时显示一句得体的空状态（例如「项目正在整理中，敬请期待。」）。

文章页 `/blog/`：

- 导航标签为「文章」，URL 保持 `/blog/`。
- 删除现有占位文章 `content/chinese/blog/welcome.md`（如需保留可随时找回）。
- 空列表时显示「还没有文章，敬请期待。」之类的空状态。

### 内容来源

- 旧站仓库：`/Users/lzping/workspace/profile/zephyr-lai.github.io`，尤其是
  `content/zh/authors/admin/_index.md` 与 `content/zh/publication/`。
- 简介文案可基于旧站信息改写，但事实（学校、公司、时间、论文）必须一致。
- 删除 hugoplate 遗留的演示/占位内容：`contact` 页面、「几何映射」品牌文案、占位文章。

### 技术约束

- 项目已是 project-setup 结构（站点在根目录，主题在 `themes/hugoplate/`），不要改回
  theme-setup，也不要手工移动文件。
- Hugo 版本：CI 与 `netlify.toml` 要求 `0.158.0` extended。本机现有 hugo 为 0.167.0 标准版，
  与要求不一致；本机已装 mise，优先用 `mise use hugo-extended@0.158.0` 提供构建环境。
  若网络不可用导致无法安装，再退回本机 0.167.0 + 预编译 CSS，但需在交付时说明。
- 本机 Hugo 执行 tailwind 会因 macOS AssetCache 权限失败；现有站点级补丁
  `layouts/_partials/essentials/style.html` 改为读取预编译的
  `assets/css/style-prebuilt.css`。**任何样式/模板类名改动后，必须运行
  `pnpm css:build` 重新生成该文件**，否则页面样式不完整。
- 包管理器：pnpm（`pnpm-lock.yaml` + `packageManager` 字段）。
- 本地预览默认端口 1313；旧站 1313 若在运行，改用 1314 等空闲端口。
- 模板类名与 Hugo 新模板系统（`layouts/_partials/`、`layouts/home.html`）保持现状，不按旧命名改。

### 部署

- GitHub 仓库：当前 `zephyr-lai/blog`，工作流已存在
  （push main → GitHub Actions → Pages，构建时注入 baseURL）。
- 目标：新站发布在用户站根路径。执行顺序（全部需要用户在场确认）：
  1. 先把旧站仓库 `zephyr-lai.github.io` 改名（如 `zephyr-lai.github.io-legacy`），释放名称；
  2. 再把当前 `blog` 仓库改名为 `zephyr-lai.github.io`；
  3. 确认 Pages Source 为 GitHub Actions；
  4. 推送 `main` 触发部署，验证 `https://zephyr-lai.github.io/`。
- 在本地验收通过前，不执行任何改名/推送；旧站保持线上。

## 留给实现者的决定

- 经历、教育、论文的数据组织方式（建议 `data/` 下的 YAML 数据文件 + 布局渲染，便于维护）。
- 空状态的具体文案与样式。
- 首页入口的具体排版细节（间距、字号），以「一屏内、干净」为准。
- 头像裁切与文件格式。
- 是否保留 hugoplate 的站内搜索（当前只索引 blog；文章为空时影响很小）。
