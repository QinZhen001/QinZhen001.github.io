---
layout:     post
title:      "videojs相关"
date:       2021-12-24 17:57:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>



# 正文





## Tech

[https://juejin.cn/post/6968628379213889550](https://juejin.cn/post/6968628379213889550)

Tech 是 videojs 中处理视频播放的技术

videojs 核心源码中提供了 Html5 作为默认 Tech，用于播放浏览器默认支持的视频类型。同时支持扩展 Tech，用于支持浏览器无法直接播放的视频类型，例如 flv、hls、dash 等。







## middleware

[https://videojs.com/blog/feature-spotlight-middleware/](https://videojs.com/blog/feature-spotlight-middleware/)

Video.js middleware are like Express middleware but routes are based on video MIME types.



There are two important pieces to be aware of with middleware:

1. dynamic source handling
2. intercepting the player and tech interactions.



```tsx
<video controls class="video-js">
  <source src="123" type="video/my-catalog">
</video>
```

Then, you could register a middleware for that route -- the type `video/my-catalog`.

```tsx
// middleware methods get the player instance as an argument
videojs.use('video/my-catalog', function(player) {

  // middleware are expected to return an object with the methods on it.
  // It can be a plain object or an instance of something.
  return {

    // setSource allows you to tell Video.js whether you're going to be handling the source or not
    setSource(srcObj, next) {
      const id = srcObj.src;

      videojs.xhr({
        uri: '/getVideo?id=' + id
      }, function(err, res, body) {

        // pass null as the first argument to say everything is going fine and we can handle it.
        next(null, {
          src: body.sourceUrl,
          type: body.sourceType
        })
      });
    }
  };
});
```





## hls

[https://juejin.cn/post/6968628379213889550](https://juejin.cn/post/6968628379213889550)

关于 Hls 的支持，videojs 官方目前提供的最新支持是 [videojs-http-streaming](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvideojs%2Fhttp-streaming)，简称 VHS，替代过去的 [videojs-contrib-hls](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fvideojs%2Fvideojs-contrib-hls)。

Github 上也有一些第三方实现，基于 hls.js 的扩展，例如基于 `Tech.registerTech` 实现的 [videojs-hlsjs](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2FSRGSSR%2Fvideojs-hlsjs)，和基于 `registerSourceHandler` 实现的 [videojs-hlsjs-plugin](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fstreamroot%2Fvideojs-hlsjs-plugin)。

对比这两个第三方库，可以发现使用 `Tech.registerTech` 会比使用  `registerSourceHandler` 代码更简单。

**videojs 较新的版本已经默认内置了 VHS**，只有 videojs.core.js 才是剔除了 VHS 的核心代码。









# 补充 



## 显示当前时间 

在进度条中显示当前时间

进过一系列的研究 始终无法通过js操作来显示 (currentTimeDisplay设置无效) 但是可以通过css实现

[https://github.com/videojs/video.js/issues/6534](https://github.com/videojs/video.js/issues/6534)

这个issues中的思路我们可以参考 但是它改的css有点问题 我们采用我们的方式：



```tsx
<video  className="video-js" />
```

```css
.video-js .vjs-time-divider {
  display: block;
}

.video-js .vjs-control-bar .vjs-duration {
  display: block;
}

.video-js .vjs-control-bar .vjs-current-time {
  display: block;
}
```

这样就可以在进度条control-bar中显示当前时间





## 源码

[https://juejin.cn/post/6961014406842941448](https://juejin.cn/post/6961014406842941448)

