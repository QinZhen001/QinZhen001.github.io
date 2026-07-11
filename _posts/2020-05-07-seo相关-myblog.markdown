---
layout:     post
title:      "SEO 基础与前端优化实践总结"
description: "本文整理《SEO 基础与前端优化实践总结》相关的核心知识点、实践经验与参考资料。"
date:       2020-05-07 10:21:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - SEO
---

> “Yeah It's on. ”
>

## 正文

蜘蛛 spider 在访问一个网站时，通常会先检查该网站根域下是否存在 `robots.txt` 纯文本文件。该文件用于声明不同爬虫在网站上的抓取范围。早期 robots 协议主要用于避免搜索引擎抓取站点不希望公开曝光的页面。

### SEO 核心目标

SEO（Search Engine Optimization，搜索引擎优化）的核心不是“欺骗搜索引擎”，而是让搜索引擎更高效地发现、理解、收录并展示网站内容，同时让用户获得更好的访问体验。

可以从 4 个层面理解 SEO：

1. **可抓取**：搜索引擎能访问页面、资源、链接和站点地图。
2. **可索引**：页面没有被 `robots.txt`、`noindex`、错误状态码、重复内容等阻断。
3. **可理解**：页面标题、正文、结构化数据、语义化 HTML 能准确表达主题。
4. **可体验**：页面加载快、移动端友好、内容可信、交互稳定。

### SEO 工作流程

1. **抓取（Crawling）**：搜索引擎通过链接、站点地图、主动提交等方式发现 URL。
2. **渲染（Rendering）**：对依赖 JavaScript 的页面，搜索引擎可能需要执行 JS 后再理解页面内容。
3. **索引（Indexing）**：搜索引擎分析页面内容、标题、链接、规范 URL、结构化数据等，并决定是否加入索引库。
4. **排序（Ranking）**：根据相关性、内容质量、页面体验、权威性、时效性等因素排序。
5. **展现（Serving / Presentation）**：搜索结果中展示标题、摘要、面包屑、富媒体结果等。

### SEO 分类

| 类型 | 重点 | 常见动作 |
| --- | --- | --- |
| 技术 SEO | 解决抓取、索引、渲染和性能问题 | `robots.txt`、`sitemap.xml`、状态码、canonical、移动端适配、Core Web Vitals |
| 站内 SEO | 提升页面内容质量和主题相关性 | 标题、描述、正文结构、内链、图片 `alt`、结构化数据 |
| 站外 SEO | 提升站点可信度和权威度 | 高质量外链、品牌曝光、权威引用、社交传播 |
| 本地 SEO | 面向本地搜索结果优化 | 商家资料、地址电话一致性、地图信息、用户评价 |

####  **搜索引擎爬虫工作原理**

[https://nextjs.org/learn-pages-router/seo/introduction-to-seo/webcrawlers](https://nextjs.org/learn-pages-router/seo/introduction-to-seo/webcrawlers)

爬行抓取、预处理、服务输出

Web crawlers identify themselves using custom [user-agents](https://developer.mozilla.org/docs/Web/HTTP/Headers/User-Agent). Google [has several web crawlers](https://developers.google.com/search/docs/advanced/crawling/overview-google-crawlers), but the ones that are used more often are **Googlebot Desktop** and **Googlebot Smartphone**.

网络爬虫使用自定义用户代理来识别自己。 Google 有多种网络爬虫，但最常用的是 Googlebot Desktop 和 Googlebot Smartphone。

####  **白帽SEO**

定义：通过正常的手段对网站内部(包括网站标题，网站结构，网站代码，网站内容，关键字密度等)调整、网站外部的链接建设，来提高网站关键字在搜索引擎排名的一种seo技术

优点：

排名稳定，浮动不大

① 对搜索引擎的依赖度更小，不会因为排名算法的调整导致网站排名不稳定；

② 不用担心因为SEO操作而降权

缺点：

生效时间相对较长

####  **黑帽SEO**

定义：所有使用作弊手段或一些可以手段的都可称为黑帽SEO。例如隐藏网页、关键词堆砌、垃圾链接、桥页等

特点：

短、平、快，为了短期内的利益而采用的作弊手段。

能快速带来一定的用户量和排名

缺点：

随时因为搜索引擎算法的改变而面临惩罚

被搜索引擎发现就会被K掉，且恢复期至少需要半年

####  **SEO-收录**

1、尽量使用文本

2、清晰的网站结构

首页-栏目页-详情页：最好不超过3级

面包屑：知道用户所处的位置，方便返回栏目页/首页

3、子域名及目录的选择

网页数量：网页数量较少时，考虑直接采用一级目录，可以继承到主域一定的权重，有利于收录。

内容相关性：内容差别较大时，可考虑二级域名

4、URL设定

URL唯一性：1个页面仅能使用1个URL访问。若同一个页面，动态URL与静态URL并存，将动态URL进行ROBOTS屏蔽。

避免多参数：URL样式简短美观，避免URL中存在多个参数

5、返回码设定

404：网页中存在失效或者不存在的页面

301：当网站改版，原URL发生变化时，需使用301将旧的URL跳转到新版URL。以便继承权重，避免流量流失。

6、索引控制

* `robots.txt`：控制爬虫是否抓取，不等于控制页面是否进入索引。已经被外链发现的 URL，即使被 robots 屏蔽，也可能只显示 URL 而没有摘要。
* `noindex`：控制页面不要被索引，通常放在 `<meta name="robots" content="noindex">` 或 HTTP Header 中。
* `canonical`：用于声明多个相似 URL 中的规范版本，减少重复内容问题。
* `sitemap.xml`：告诉搜索引擎站点中有哪些重要 URL，适合大站、更新频繁站点、内链较弱页面。

####  **SEO-排序**

1、Title标签：

搜索引擎判断网页内容主题的重要信息之一，每个页面的title需不重复

1）首页

网站名称_网站相关服务

2）栏目页

栏目名称_网站名称

3）详情页

文章title_栏目名称_网站名称

title写法：

① 每个页面都有一个唯一的title

② 每个title都能紧扣页面主题

③ 重要的内容应放在title前面

2、内容建设

1）相关内容

提供与网站核心价值相关的内容

2）锚文本

写好锚文本。搜索引擎初识页面主题的参考因素之一。内链权重传递的作用。

3、alt标签

让搜索引擎了解到页面的内容。特别是对图片网站作用很大

```html
<img src="/i/eg_tulip.jpg" alt="上海鲜花港-郁金香"/>
```

4、内容质量

* 优先满足用户搜索意图，而不是机械堆关键词。
* 一个页面聚焦一个主要主题，围绕主题补充完整、准确、可信的内容。
* 避免采集、拼接、低质量 AI 批量生成、重复页面和无价值聚合页。
* 重要内容尽量直接出现在 HTML 中，不要完全依赖复杂交互后才加载。

5、内链结构

* 重要页面应能从首页、栏目页或相关文章中被链接到。
* 锚文本应自然描述目标页面内容，避免大量“点击这里”。
* 避免孤岛页面，即没有任何站内链接指向的页面。

####  **SEO-展现**

1、title

* 内容符合页面信息。使用上用户常搜索的关键词，同时避免关键词堆积

* 字数切忌过多。PC百度最多展示30个汉字。

2、description

* 在搜索结果中通常作为摘要展现，会影响用户的点击。

* 控制字数，最多不超过70字

* 完整、有效的将页面信息概括性的写出

#### metadata

In web development, metadata provides additional details about a webpage. Metadata is not visible to the users visiting the page. Instead, it works behind the scenes, embedded within the page's HTML, usually within the `<head>` element. This hidden information is crucial for search engines and other systems that need to understand your webpage's content better.

在 web 开发中，元数据提供了关于网页的额外细节。元数据对于访问页面的用户通常不可见，而是嵌入在 HTML 的 `<head>` 中，帮助搜索引擎、浏览器和社交平台理解页面内容。

Metadata plays a significant role in enhancing a webpage's SEO, making it more accessible and understandable for search engines and social media platforms.

元数据在 SEO 中的主要作用：

* 帮助搜索引擎理解页面主题。
* 影响搜索结果中的标题和摘要展示。
* 帮助社交平台生成分享卡片。
* 配合 canonical、robots 等标签控制索引和重复内容。

常见写法：

```html
<head>
  <title>SEO 入门指南_前端工程化笔记</title>
  <meta name="description" content="整理 SEO 基础、robots.txt、sitemap、metadata、结构化数据和官方参考文档。">
  <link rel="canonical" href="https://example.com/seo-guide">
  <meta name="robots" content="index, follow">

  <meta property="og:title" content="SEO 入门指南">
  <meta property="og:description" content="SEO 基础、技术 SEO 和官方参考资料整理。">
  <meta property="og:type" content="article">
  <meta property="og:url" content="https://example.com/seo-guide">
  <meta property="og:image" content="https://example.com/seo-cover.png">
</head>
```

注意：

* `keywords` meta 标签现在基本不再作为主流搜索排序依据，不建议花大量时间堆关键词。
* `description` 不直接保证排名，但会影响搜索结果摘要和点击率。
* 每个可索引页面都应尽量有唯一的 `title` 和 `description`。

####   robots.txt

[https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/robots-txt](https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/robots-txt)

robots.txt声明网站中哪些目录不让搜索引擎收录，**执行顺序从上到下**，robots.txt写法，添加sitemap链接。sitemap是谷歌创造出来的，搜索引擎会优先读取sitemap.xml文件，如果没有就逐个抓取URL。

##### 基本语法

```text
User-agent:定义搜索引擎爬虫名称。百度(Baiduspider)、谷歌(Googlebot)、360(360Spider)等。*号代表全部搜索引擎
Disallow:不允许收录
Allow:允许收录
#:注释
```

##### 全站屏蔽所有蜘蛛

网站改版之前，为了避免蜘蛛抓取错误或临时的链接，可以采用全站屏蔽蜘蛛的操作，在根目录建立robots.txt，添加如下内容：

```text
User-agent:*
Disallow:/
```

##### 屏蔽某个目录、文件

案例：屏蔽所有蜘蛛抓取根目录下的inc文件夹及里面的所有内容，根目录下的wap目录下的index.html文件。robots.txt文件添加如下内容：

```text
User-agent:*
Disallow:/inc/
Disallow:/wap/index.html
```

##### 屏蔽某个目录，但抓取目录下的文件或文件夹

案例：屏蔽所有蜘蛛抓取根目录下的wap文件夹，但抓取里面后缀名为html的文件

```text
User-agent:*
Disallow:/wap/
Allow:/wap/*.html
```

##### 屏蔽动态URL

网站有些动态页面可能会和静态页面相同，造成重复收录。

 用robots屏蔽动态URL

```text
User-agent:*
Disallow:/*?*
```

仅允许访问“.html”为后缀的URL

```text
User-agent:*
Allow:.html$
Disallow:/
```

##### sitemap索引在robots.txt的位置

 sitemap 索引的位置最好放在 robots.txt 的**最下面**，蜘蛛先遵循前面的原则，再按照网站地图爬取。

```yaml
Sitemap: http://www.vi586.com/sitemap.xml
Sitemap: http://www.vi586.com/sitemap.html
```

#### XML Sitemaps

[https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/xml-sitemaps](https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/xml-sitemaps)

`sitemap.xml` 用来帮助搜索引擎发现站点中的重要 URL，尤其适合以下场景：

* 网站页面数量较多，内链结构复杂。
* 新页面更新频繁，希望搜索引擎更快发现。
* 有些页面无法通过普通链接很好地被发现。
* 多语言、多地区页面需要配合 `hreflang` 声明。

常见字段：

```xml
<url>
  <loc>https://example.com/posts/seo-guide</loc>
  <lastmod>2026-07-09</lastmod>
  <changefreq>weekly</changefreq>
  <priority>0.8</priority>
</url>
```

注意：

* `loc` 必须使用规范 URL，尽量不要混入重定向 URL、404 URL、带追踪参数的 URL。
* `lastmod` 应反映内容真实更新时间，不建议每次构建都全部刷新为当天。
* sitemap 不能保证收录，它只是帮助发现 URL。

#### Special Meta Tags

[https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/metatags](https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/metatags)

#### AMP

 a technology that enables developers to create web pages that load faster on mobile devices – at the cost of building and maintaining them over time.

该技术使开发人员能够创建在移动设备上加载速度更快的网页，但代价是随着时间的推移构建和维护它们。

#### 结构化数据

结构化数据（Structured Data）是用机器可读的方式描述页面内容，常用格式是 `JSON-LD`。它可以帮助搜索引擎理解页面类型，并有机会触发富媒体搜索结果。

常见类型：

* `Article`：文章页。
* `BreadcrumbList`：面包屑导航。
* `FAQPage`：问答内容。
* `Product`：商品页。
* `Organization`：组织信息。
* `WebSite`：站点信息和站内搜索。

示例：

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "SEO 入门指南",
  "datePublished": "2020-05-07",
  "dateModified": "2026-07-09",
  "author": {
    "@type": "Person",
    "name": "Qz"
  }
}
</script>
```

注意：结构化数据必须和页面真实可见内容一致，不要标记页面中不存在的内容。

#### Core Web Vitals

Core Web Vitals 是 Google 用于衡量页面体验的核心性能指标：

| 指标 | 含义 | 建议范围 |
| --- | --- | --- |
| LCP | Largest Contentful Paint，最大内容绘制时间 | 小于 2.5s |
| INP | Interaction to Next Paint，交互响应延迟 | 小于 200ms |
| CLS | Cumulative Layout Shift，累计布局偏移 | 小于 0.1 |

优化方向：

* 减少首屏阻塞资源，压缩和延迟加载非关键 JS。
* 图片使用合适尺寸、现代格式和懒加载。
* 字体设置 `font-display`，避免布局抖动。
* 为图片、广告、iframe 等预留宽高，降低 CLS。
* 服务端缓存、CDN、静态资源长期缓存。

#### SEO 检查清单

上线前可以按下面清单检查：

- [ ] 每个页面有唯一且准确的 `title`。
- [ ] 重要页面有清晰的 `description`。
- [ ] 页面只有一个主要 `h1`，标题层级合理。
- [ ] 重要内容不是只存在于图片、Canvas 或复杂 JS 交互中。
- [ ] 重要图片有描述性的 `alt`。
- [ ] 重要 URL 返回 `200`，旧 URL 使用 `301` 到新 URL。
- [ ] 重复页面配置了 `canonical`。
- [ ] 不希望收录的页面使用 `noindex`，不要只依赖 `robots.txt`。
- [ ] `robots.txt` 没有误屏蔽重要资源和页面。
- [ ] `sitemap.xml` 只包含规范、可索引、返回 `200` 的 URL。
- [ ] 移动端布局可用，按钮、字体、间距适合触摸操作。
- [ ] 页面性能满足 Core Web Vitals 基本要求。
- [ ] 使用 Google Search Console / Bing Webmaster Tools / 百度搜索资源平台观察抓取和索引问题。

## 补充

### Google Search Console

[https://www.youtube.com/watch?v=Pe_-TkdbXN0](https://www.youtube.com/watch?v=Pe_-TkdbXN0)

### 富媒体搜索

富媒体搜索结果是指我们在各种 Google 平台（例如 Google 搜索）上提供的超越了普通蓝色链接的用户体验。这类搜索结果可能包括轮换展示内容、图片以及其他非文字元素。

[https://search.google.com/test/rich-results](https://search.google.com/test/rich-results)

## 官方权威 SEO 文章链接

### Google Search Central

* [Google 搜索中心](https://developers.google.cn/search?hl=zh-cn)
* [SEO 新手指南](https://developers.google.cn/search/docs/fundamentals/seo-starter-guide?hl=zh-cn)
* [Google 搜索工作原理](https://developers.google.cn/search/docs/fundamentals/how-search-works?hl=zh-cn)
* [Google 抓取和索引编制文档](https://developers.google.cn/search/docs/crawling-indexing/overview-google-crawlers?hl=zh-cn)
* [robots.txt 简介与指南](https://developers.google.cn/search/docs/crawling-indexing/robots/intro?hl=zh-cn)
* [Sitemap 站点地图指南](https://developers.google.cn/search/docs/crawling-indexing/sitemaps/overview?hl=zh-cn)
* [规范网址 canonical 指南](https://developers.google.cn/search/docs/crawling-indexing/consolidate-duplicate-urls?hl=zh-cn)
* [结构化数据简介](https://developers.google.cn/search/docs/appearance/structured-data/intro-structured-data?hl=zh-cn)
* [网页体验和 Core Web Vitals](https://developers.google.cn/search/docs/appearance/page-experience?hl=zh-cn)
* [Google Search Console 帮助文档](https://support.google.com/webmasters/?hl=zh-Hans)
* [富媒体搜索结果测试](https://search.google.com/test/rich-results)

### Bing Webmaster Tools

* [Bing Webmaster Guidelines](https://www.bing.com/webmasters/help/webmaster-guidelines-30fba23a)
* [Bing Webmaster Tools](https://www.bing.com/webmasters/about)
* [How to Create a robots.txt File](https://www.bing.com/webmasters/help/how-to-create-a-robots-txt-file-cb7c31ec)
* [Bing URL Submission](https://www.bing.com/webmasters/help/url-submission-62f2860b)
* [IndexNow 官方文档](https://www.indexnow.org/zh_cn/documentation)

### 百度搜索资源平台

* [百度搜索资源平台](https://ziyuan.baidu.com/)
* [百度搜索资源平台入门进阶优化指南](https://ziyuan.baidu.com/doc/index)
* [百度 Robots 检测工具](https://ziyuan.baidu.com/robots/index)

### 标准与性能工具

* [Schema.org 官方文档](https://schema.org/docs/documents.html)
* [PageSpeed Insights](https://pagespeed.web.dev/)
* [PageSpeed Insights 简介](https://developers.google.cn/speed/docs/insights/v5/about?hl=zh-cn)
* [Web Vitals 官方说明](https://web.developers.google.cn/articles/vitals?hl=zh-cn)
