---
layout:     post
title:      "seo相关"
date:       2020-05-07 10:21:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>




# 正文

蜘蛛spider在访问一个网站时，会首先会检查该网站的根域下是否有一个叫做 robots.txt的纯文本文件，这个文件用于指定spider在您网站上的抓取范围。早期robots协议是防止网站内的隐私，防止抓取用户不想让搜索引擎曝光的页面。



###  **搜索引擎爬虫工作原理** 

[https://nextjs.org/learn-pages-router/seo/introduction-to-seo/webcrawlers](https://nextjs.org/learn-pages-router/seo/introduction-to-seo/webcrawlers)

爬行抓取、预处理、服务输出



Web crawlers identify themselves using custom [user-agents](https://developer.mozilla.org/docs/Web/HTTP/Headers/User-Agent). Google [has several web crawlers](https://developers.google.com/search/docs/advanced/crawling/overview-google-crawlers), but the ones that are used more often are **Googlebot Desktop** and **Googlebot Smartphone**.

网络爬虫使用自定义用户代理来识别自己。 Google 有多种网络爬虫，但最常用的是 Googlebot Desktop 和 Googlebot Smartphone。



###  **白帽SEO** 

定义：通过正常的手段对网站内部(包括网站标题，网站结构，网站代码，网站内容，关键字密度等)调整、网站外部的链接建设，来提高网站关键字在搜索引擎排名的一种seo技术



优点：

排名稳定，浮动不大

① 对搜索引擎的依赖度更小，不会因为排名算法的调整导致网站排名不稳定；

② 不用担心因为SEO操作而降权



缺点：

生效时间相对较长







###  **黑帽SEO** 

定义：所有使用作弊手段或一些可以手段的都可称为黑帽SEO。例如隐藏网页、关键词堆砌、垃圾链接、桥页等



特点：

短、平、快，为了短期内的利益而采用的作弊手段。

能快速带来一定的用户量和排名



缺点：

随时因为搜索引擎算法的改变而面临惩罚

被搜索引擎发现就会被K掉，且恢复期至少需要半年





###  **SEO-收录** 



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





###  **SEO-排序** 



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

```
<img src="/i/eg_tulip.jpg" alt="上海鲜花港-郁金香"/>
```



###  **SEO-展现** 



1、title

* 内容符合页面信息。使用上用户常搜索的关键词，同时避免关键词堆积

* 字数切忌过多。PC百度最多展示30个汉字。



2、description

* 在搜索结果中通常作为摘要展现，会影响用户的点击。

* 控制字数，最多不超过70字

* 完整、有效的将页面信息概括性的写出





### metadata

In web development, metadata provides additional details about a webpage. Metadata is not visible to the users visiting the page. Instead, it works behind the scenes, embedded within the page's HTML, usually within the `<head>` element. This hidden information is crucial for search engines and other systems that need to understand your webpage's content better.

在web开发中，元数据提供了关于网页的额外细节。元数据对于访问页面的用户是不可见的。相反，它在幕后工作，嵌入在页面的HTML中，通常在<head>元素中。这些隐藏的信息对于搜索引擎和其他需要更好地理解你网页内容的系统来说是至关重要的。

Metadata plays a significant role in enhancing a webpage's SEO, making it more accessible and understandable for search engines and social media platforms

元数据在增强网页搜索引擎优化方面发挥着重要作用，使其更容易被搜索引擎和社交媒体平台访问和理解



###   robots.txt 

[https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/robots-txt](https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/robots-txt)

robots.txt声明网站中哪些目录不让搜索引擎收录，**执行顺序从上到下**，robots.txt写法，添加sitemap链接。sitemap是谷歌创造出来的，搜索引擎会优先读取sitemap.xml文件，如果没有就逐个抓取URL。 





#### 基本语法

```
User-agent:定义禁止搜索引擎名字。百度(Baiduspide)、谷歌(Googlebot)、360(360Spider)等。*号代表全部搜索引擎
Disallow:不允许收录
Allow:允许收录
#:注释
```



#### 全站屏蔽所有蜘蛛

网站改版之前，为了避免蜘蛛抓取错误或临时的链接，可以采用全站屏蔽蜘蛛的操作，在根目录建立robots.txt，添加如下内容： 

```
User-agent:*
Disallow:/
```





#### 屏蔽某个目录、文件

案例：屏蔽所有蜘蛛抓取根目录下的inc文件夹及里面的所有内容，根目录下的wap目录下的index.html文件。robots.txt文件添加如下内容：

```
User-agent:*
Disallow:/inc/
Disallow:/wap/index.html
```



#### 屏蔽某个目录，但抓取目录下的文件或文件夹

案例：屏蔽所有蜘蛛抓取根目录下的wap文件夹，但抓取里面后缀名为html的文件

```
User-agent:*
Disallow:/wap/
Allow:/wap/*.html
```



#### 屏蔽动态URL

网站有些动态页面可能会和静态页面相同，造成重复收录。

 用robots屏蔽动态URL 

```
User-agent:*
Disallow:/*?*
```

仅允许访问“.html”为后缀的URL 

```
User-agent:*
Allow:.html$
Disallow:/
```



#### sitemap索引在robots.txt的位置

 sitamap索引的位置最好放在robots.txt的**最下面**，蜘蛛先遵循前面的原则，再按照网站地图爬取。

```
Sitemap: http://www.vi586.com/sitemap.xml
Sitemap: http://www.vi586.com/sitemap.html
```



### XML Sitemaps

[https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/xml-sitemaps](https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/xml-sitemapss)





### Special Meta Tags

[https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/metatags](https://nextjs.org/learn-pages-router/seo/crawling-and-indexing/metatags)





### AMP

 a technology that enables developers to create web pages that load faster on mobile devices – at the cost of building and maintaining them over time.

该技术使开发人员能够创建在移动设备上加载速度更快的网页，但代价是随着时间的推移构建和维护它们。





# 补充



## Google Search Console

[https://www.youtube.com/watch?v=Pe_-TkdbXN0](https://www.youtube.com/watch?v=Pe_-TkdbXN0)





## 富媒体搜索

富媒体搜索结果是指我们在各种 Google 平台（例如 Google 搜索）上提供的超越了普通蓝色链接的用户体验。这类搜索结果可能包括轮换展示内容、图片以及其他非文字元素。

[https://search.google.com/test/rich-results](https://search.google.com/test/rich-results)
