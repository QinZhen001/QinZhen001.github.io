---
layout:     post
title:      "webrtc音视频相关"
date:       2020-06-12 19:16:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>



# web rtc

[https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)

[https://github.com/QinZhen001/front-end-demo/blob/master/src/pages/other/web-rtc/index.tsx](https://github.com/QinZhen001/front-end-demo/blob/master/src/pages/other/web-rtc/index.tsx)

音视频通信的流程有五步：采集、编码、通信、解码、渲染。

[WebRTC 介绍](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Session_lifetime)

[WebRTC 协议介绍](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Protocols)

[信令与视频通话](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling)



## [信令服务器](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#信令服务器)

两个设备之间建立 WebRTC 连接需要一个**信令服务器**来实现双方通过网络进行连接。信令服务器的作用是作为一个中间人帮助双方在尽可能少的暴露隐私的情况下建立连接。



## connectivity

[https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Connectivity](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Connectivity)



## SDP

[https://developer.mozilla.org/zh-CN/docs/Glossary/SDP](https://developer.mozilla.org/zh-CN/docs/Glossary/SDP)

**SDP** (Session Description [Protocol](https://developer.mozilla.org/zh-CN/docs/Glossary/Protocol)) 是一个描述[peer-to-peer (en-US)](https://developer.mozilla.org/en-US/docs/Glossary/P2P) 连接的标准。SDP 包含音视频的：编解码 ([codec](https://developer.mozilla.org/zh-CN/docs/Glossary/Codec)),源地址，和时间信息。





## AudioBuffer

[https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBuffer](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBuffer)

[https://github.com/mdn/webaudio-examples/blob/main/audio-buffer/index.html](https://github.com/mdn/webaudio-examples/blob/main/audio-buffer/index.html)

AudioBuffer 接口表示存在内存里的一段短小的音频资源，利用[`AudioContext.decodeAudioData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/decodeAudioData)方法从一个音频文件构建，或者利用 [`AudioContext.createBuffer()`](https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/createBuffer)从原始数据构建。把音频放入 AudioBuffer 后，可以传入到一个 [`AudioBufferSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBufferSourceNode)进行播放。





## MediaStream

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream)

**`MediaStream`** 接口是一个媒体内容的流.。一个流包含几个*轨道*，比如视频和音频轨道。



## MediaStreamTrack

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStreamTrack](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStreamTrack)

**`MediaStreamTrack`** 接口在 User Agent 中表示一段媒体源，比如音轨或视频。

Each `MediaStreamTrack` may have one or more channels. The channel represents the smallest unit of a media stream, such as an audio signal associated with a given speaker, like left or right in a stereo audio track.





## AudioBufferSourceNode

**`AudioBufferSourceNode`** 接口继承自 [`AudioScheduledSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioScheduledSourceNode)，表现为一个音频源，它包含了一些写在内存中的音频数据，通常储存在一个 ArrayBuffer 对象中。



## RTCPeerConnection

**`RTCPeerConnection`** 接口代表一个由本地计算机到远端的 WebRTC 连接。该接口提供了创建，保持，监控，关闭连接的方法的实现。









# video.js



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













# mpv

[https://mpv.io/](https://mpv.io/)

a free, open source, and cross-platform media player





## switch audio track

[https://github.com/mpv-player/mpv/issues/3444](https://github.com/mpv-player/mpv/issues/3444)

**快捷键:  Shirt + 3** 





# ffmpeg

浏览器环境需要 [ffmpeg.wasm](https://github.com/ffmpegwasm/ffmpeg.wasm)

[https://ffmpegwasm.netlify.app/#demo](https://ffmpegwasm.netlify.app/#demo)



```tsx
export interface FFmpeg {
    /*
     * Load ffmpeg.wasm-core script.
     * In browser environment, the ffmpeg.wasm-core script is fetch from
     * CDN and can be assign to a local path by assigning `corePath`.
     * In node environment, we use dynamic require and the default `corePath`
     * is `$ffmpeg/core`.
     *
     * Typically the load() func might take few seconds to minutes to complete,
     * better to do it as early as possible.
     *
     */
    load(): Promise<void>;
    /*
     * Determine whether the Core is loaded.
     */
    isLoaded(): boolean;
    /*
     * Run ffmpeg command.
     * This is the major function in ffmpeg.wasm, you can just imagine it
     * as ffmpeg native cli and what you need to pass is the same.
     *
     * For example, you can convert native command below:
     *
     * ```
     * $ ffmpeg -i video.avi -c:v libx264 video.mp4
     * ```
     *
     * To
     *
     * ```
     * await ffmpeg.run('-i', 'video.avi', '-c:v', 'libx264', 'video.mp4');
     * ```
     *
     */
    run(...args: string[]): Promise<void>;
    /*
     * Run FS operations.
     * For input/output file of ffmpeg.wasm, it is required to save them to MEMFS
     * first so that ffmpeg.wasm is able to consume them. Here we rely on the FS
     * methods provided by Emscripten.
     *
     * Common methods to use are:
     * ffmpeg.FS('writeFile', 'video.avi', new Uint8Array(...)): writeFile writes
     * data to MEMFS. You need to use Uint8Array for binary data.
     * ffmpeg.FS('readFile', 'video.mp4'): readFile from MEMFS.
     * ffmpeg.FS('unlink', 'video.map'): delete file from MEMFS.
     *
     * For more info, check https://emscripten.org/docs/api_reference/Filesystem-API.html
     *
     */
    FS<Method extends FSMethodNames>(method: Method, ...args: FSMethodArgs[Method]): FSMethodReturn[Method];
    setProgress(progress: ProgressCallback): void;
    setLogger(log: LogCallback): void;
    setLogging(logging: boolean): void;
    exit(): void;
}
```







## 提取音轨

```bash
ffmepg -i /Users/qz/Documents/company_project/test-audio/test/music.mp4 -c:a libmp3lame -b:a 320K -map 0:1 music-second.mp3 -map 0:0 music.first.mp3
```

例子：

```tsx
import { createFFmpeg, fetchFile } from '@ffmpeg/ffmpeg';

const file = await fetchFile(mp4Src)
    console.log('input file', file)
    ffmpeg.FS('writeFile', 'input.mp4', file);
    await ffmpeg.run('-i', 'input.mp4', '-c:a', "libmp3lame", "-map", "0:1", "music-second.mp3", "-map", "0:0", "music-first.mp3");
    Mp3musicSecond = ffmpeg.FS('readFile', 'music-second.mp3');
    Mp3musicFirst = ffmpeg.FS('readFile', 'music-first.mp3');
    const url1 = URL.createObjectURL(new Blob([Mp3musicFirst.buffer], { type: 'video/mp3' }));
    const url2 = URL.createObjectURL(new Blob([Mp3musicSecond.buffer], { type: 'video/mp3' }));

    audioSourceRef1.current.src = url1
    audioSourceRef2.current.src = url2

    audioRef1.current.load()
    audioRef2.current.load()
```



验证完成：

* 提取出来数据 ArrayBuffer  => Blob  =>  ObjectURL  =>  audio tag => 播放成功
* 提取出来数据 ArrayBuffer  =>  new AudioContext =>  decodeAudioData  => AudioBuffer =>  AgoraRTC.createBufferSourceAudioTrack  =>  audioTrack play 和 startProcessAudioBuffer => 播放成功



## 合并音轨

[http://www.ffmpeg.org/ffmpeg-filters.html#amix](http://www.ffmpeg.org/ffmpeg-filters.html#amixs)







# 补充



## flv、hls、dash

| 协议      | 传输方式 | 视频封装格式 | 数据分段 | 延时 | H5播放                                |
| --------- | -------- | ------------ | -------- | ---- | ------------------------------------- |
| http-flv  | http流   | flv          | 连续流   | 低   | flv.js                                |
| rtmp      | tcp流    | flv tag      | 连续流   | 低   | 不支持                                |
| hls       | http     | Ts文件       | 切片文件 | 高   | hls.js                                |
| mpeg-dash | http     | mp4 3gp webm | 切片文件 | 高   | 文件列表是mp4webm的dash，可以直接播放 |

**FLV (Flash Video) 是 Adobe 公司推出的另一种视频格式，是一种在网络上传输的流媒体数据存储容器格式。**其格式相对简单轻量，不需要很大的媒体头部信息。整个 FLV 由 The FLV Header, The FLV Body 以及其它 Tag 组成。因此加载速度极快。采用 FLV 格式封装的文件后缀为 .flv。

**而我们所说的 HTTP-FLV 即将流媒体数据封装成 FLV 格式，然后通过 HTTP 协议传输给客户端。**

**HLS (HTTP Live Streaming) 则是苹果公司基于 HTTP 的流媒体传输协议。**主要应用于 iOS 设备，包含(iPhone, iPad, iPod touch) 以及 Mac OSX 提供音视频直播服务和录制内容（点播）等服务。

相对于常见的流媒体协议，HLS 最大的不同在于它并不是一下请求完整的数据流。它会在服务器端将流媒体数据切割成连续的时长较短的 ts 小文件，并通过 M3U8 索引文件按序访问 ts 文件。客户端只要不停的按序播放从服务器获取到的文件，从而实现播放音视频。

**RTMP 协议为流媒体而设计，在推流中用的比较多，同时大多 CDN 厂商支持RTMP 协议。**



## webaudio-examples

[https://github.com/mdn/webaudio-examples](https://github.com/mdn/webaudio-examples)

[https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API)







## 科普

[「完全理解」video 标签到底能播放什么](https://juejin.cn/post/7018373086072766472)



### 封装与解包

H.264 和 AAC，可以被封装成 MP4 或 FLV。但是 Chrome 只能播放前者而不支持后者，细想其实很没道理。就好比两个东西被装在 A 盒子里的时候能拆开用，但是装在 B 盒子里就不行了

因为本质上重要的是**东西**（H.264、H.265），而不是**盒子**（MP4、FLV）

把编码封装成文件的步骤叫做**封装**（remux），文件拆解成编码的步骤叫**解包**（demux）

封装，抽象的说其实就是把**东西**按**不同排列**码到一块





# 问题



## 设置referrer解决403

抖音视频的url直接在浏览器中打开是没有问题的,直接打开本地的.html文件也是可以正常播放视频的,但访问服务器上的请求过来的页面就无法播放视频，浏览器里按F12查看network发现video标签里的src请求视频资源时报**403 Forbiddn** 错误。



经过对比发现错误的请求头中多了个**Referer**



估计请求的是服务器 referer 做了判断，不是正常的referer就拒绝了，可以模拟他们的 referer 请求试下，测试发现, 通过 https 站点打开的页面, 可以正常打开视频链接，在 https 下, 发送的请求是不会带有 Referer 的header 的, 这个时候是可以正常加载视频的. 所以, 我们在 页面的 head 标签内, 增加一行代码,指定浏览器任何情况下都不发送Referer,这样就可以正常加载资源了。

```xml
<meta name="referrer" content="no-referrer">
```

## autoplay

[https://webkit.org/blog/6784/new-video-policies-for-ios/](https://webkit.org/blog/6784/new-video-policies-for-ios/)

`<video autoplay>` elements will now honor the autoplayattribute, for elements which meet the following conditions: 

*   `<video>` elements will be allowed to `autoplay` without a user gesture if their source media contains no audio tracks.

- `<video muted>` elements will also be allowed to autoplay without a user gesture.
- If a `<video>` element gains an audio track or becomes un-muted without a user gesture, playback will pause.
- `<video autoplay>` elements will only begin playing when visible on-screen such as when they are scrolled into the viewport, made visible through CSS, and inserted into the DOM.
- `<video autoplay>` elements will pause if they become non-visible, such as by being scrolled out of the viewport.

没有音轨的video可以自动播放

有音轨但是muted静音的video可以自动播放
