---
name: posts-content-optimization-analysis
overview: 分析 `_posts` 目录文章标题、主题重叠和内容结构，形成可优化、可合并、可补充的博客整理方案。
todos:
  - id: scan-posts
    content: 使用 [subagent:code-explorer] 扫描 `_posts` 文章标题、结构和主题
    status: completed
  - id: cluster-topics
    content: 按 Vue、React、构建、CSS、JS、后端、面试聚类文章
    status: completed
    dependencies:
      - scan-posts
  - id: identify-merge-candidates
    content: 识别可合并、需互链、应保留独立的文章组合
    status: completed
    dependencies:
      - cluster-topics
  - id: review-title-quality
    content: 整理标题过泛、命名不一致和可优化标题清单
    status: completed
    dependencies:
      - cluster-topics
  - id: flag-content-issues
    content: 标记内容过薄、过旧、引用较多和待补充文章
    status: completed
    dependencies:
      - scan-posts
  - id: produce-report
    content: 输出分优先级的文章优化与合并建议报告
    status: completed
    dependencies:
      - identify-merge-candidates
      - review-title-quality
      - flag-content-issues
---

## User Requirements

用户希望对 `_posts` 目录下的博客文章进行系统分析，重点查看文章标题和正文内容是否存在可优化、可合并的问题。

## Product Overview

本次工作面向现有博客文章库，输出一份内容整理建议，帮助识别重复主题、标题不统一、内容过薄、内容过旧、系列化不清晰等问题。

## Core Features

- 分析所有文章标题、文件名、正文标题层级和内容主题。
- 识别主题相近、内容重叠、适合合并或改造成系列的文章。
- 找出标题过泛、命名风格不一致、表达不清晰的文章。
- 标记内容较薄、结构较散、引用较多或需要补充更新的文章。
- 输出按主题分组的优化建议，并给出优先级和代表性文件路径。

## Tech Stack Selection

- 项目类型：Jekyll 静态博客。
- 内容格式：`_posts/*.markdown`，包含 front matter、正文标题和 Markdown 内容。
- 分析方式：只读扫描 Markdown 文件，结合文件名、front matter title、一级和二级标题、正文长度、关键词主题聚类进行内容审计。
- 输出形式：优先在对话中输出结构化分析报告；如后续用户确认需要落地，可新增独立整理文档，不直接改动原文章。

## Implementation Approach

先对 `_posts` 下 106 篇文章进行只读内容盘点，提取标题、文件名主题、标题层级、正文长度和明显引用标记；再按技术主题聚类，判断文章之间的内容边界、重复程度和合并价值。最终输出可执行的整理策略，包括“建议合并”“建议保留但互链”“建议改标题”“建议补充更新”“建议归档”的分类结果。

关键决策：

- 不直接批量修改文章，避免破坏历史链接、发布日期和 SEO 收录。
- 对高重叠主题优先建议“系列化和互链”，只有内容明显短小或边界重复时才建议合并。
- 对面试类文章和知识类文章保持边界，避免把备考问答和体系文章混在一起。
- 对旧文章以“保留历史版本加更新段落”为主，减少大规模重写成本。

## Implementation Notes

- 保持只读分析，不改动 `_posts` 原文。
- 重点关注已识别的主题簇：Vue、React、Webpack 和构建、CSS 和 HTML、JS 和 TS、Node 和后端、面试和职业。
- 对短内容文章优先检查是否适合并入主文章，例如国际化、Element 源码、first blog。
- 对含大量“链接、转载、原文、参考、TODO”标记的文章，单独提出引用清理和原创内容增强建议。
- 输出建议时使用代表性文件路径，不展开 106 篇完整清单，避免报告过长。

## Architecture Design

本任务不引入新架构，只围绕现有博客内容目录进行只读审计：

1. 内容采集层：读取 `_posts/*.markdown`。
2. 元数据分析层：提取 title、description、文件名、标题层级和正文长度。
3. 主题聚类层：按关键词和文章标题归类到技术主题。
4. 决策层：判断合并、改名、补充、互链或归档建议。
5. 报告层：输出结构化内容优化建议。

## Directory Structure

本次计划默认不修改文件，仅分析以下目录：

```
QinZhen001.github.io/
└── _posts/                                      # [READ] 现有博客文章目录。分析所有 markdown 文章的标题、正文结构、主题重叠和优化空间。
    ├── 2017-07-24-Vue相关基础-myblog.markdown   # [READ] Vue 主题代表文章，用于判断 Vue 系列合并和互链策略。
    ├── 2018-01-11-React相关-myblog.markdown     # [READ] React 主题代表文章，用于判断 React 系列结构优化。
    ├── 2017-12-12-webpack相关-myblog.markdown   # [READ] 构建工具主题代表文章，用于判断 loader、plugin、Babel、Vite 等文章边界。
    ├── 2018-05-01-css基础相关-myblog.markdown   # [READ] CSS 主题代表文章，用于判断 CSS 基础、专项细节和面试文章关系。
    └── 2018-05-01-js重要基础-myblog.markdown    # [READ] JS 主题代表文章，用于判断 Promise、async、this、TS 等内容组织方式。
```

如用户后续要求生成落地文档，可新增：

```
QinZhen001.github.io/
└── docs/
    └── posts-optimization-report.md             # [NEW] 文章标题与内容合并优化报告，记录主题分组、合并建议、标题优化建议和执行优先级。
```

## Agent Extensions

### SubAgent

- **code-explorer**
- Purpose: 对 `_posts` 目录进行多文件只读扫描，提取标题、正文结构、主题分组和潜在重复内容。
- Expected outcome: 形成可靠的文章聚类依据，支撑后续合并、改名、补充和归档建议。