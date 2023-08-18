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

[WebRTC 点对点通信原理](http://www.yyyweb.com/5480.html)



音视频通信的流程有五步：采集、编码、通信、解码、渲染。

[WebRTC 介绍](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Session_lifetime)

[WebRTC 协议介绍](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Protocols)

[信令与视频通话](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling)



## **媒体协商**

不同浏览器对于音视频的编解码能力是不同的。比如: Peer-A 端支持 H264、VP8 等多种编码格式,而 Peer-B 端支持 H264、VP9 等格式。为了保证双方都可以正确的编解码，最简单的办法即取它们所都支持格式的交集-H264。在 WebRTC 中，有一个专门的协议，称为**Session Description Protocol(SDP)**,可以用于描述上述这类信息。因此参与音视频通讯的双方想要了解对方支持的媒体格式，必须要交换 SDP 信息。而交换 SDP 的过程，通常称之为**媒体协商**。





## **网络协商**

参与音视频实时通信的双方要了解彼此的网络情况，这样才有可能找到一条相互通讯的链路。理想的网络情况是每个浏览器的电脑都有自己的私有公网 IP 地址，这样的话就可以直接进行点对点连接。但实际上出于网络安全和 IPV4 地址不够的考虑，我们的电脑与电脑之间或大或小都是在某个局域网内，需要**NAT(Network Address Translation, 网络地址转换)**。在 WebRTC 中我们使用 ICE 机制建立网络连接





## [信令服务器](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#信令服务器)

两个设备之间建立 WebRTC 连接需要一个**信令服务器**来实现双方通过网络进行连接。信令服务器的作用是作为一个中间人帮助双方在尽可能少的暴露隐私的情况下建立连接。



## **ICE**

**ICE (Interactive Connecctivity Establishment, 交互式连接建立)**，ICE 不是一种协议，而是整合了 STUN 和 TURN 两种协议的框架。其中**STUN(Sesssion Traversal Utilities for NAT, NAT 会话穿越应用程序)**，它允许位于 NAT（或多重 NAT）后的客户端找出自己对应的公网 IP 地址和端口，也就是俗称的“打洞”。但是，如果 NAT 类型是对称型的话，那么就无法打洞成功。这时候 TURN 就派上用场了，**TURN**(Traversal USing Replays around NAT)是 STUN/RFC5389 的一个拓展协议在其基础上添加了 Replay(中继)功能，简单来说其目的就是解决对称 NAT 无法穿越的问题，在 STUN 分配公网 IP 失败后，可以通过 TURN 服务器请求公网 IP 地址作为中继地址。



在 WebRTC 中有三种类型的 ICE 候选者，它们分别是：

- 主机候选者
- 反射候选者
- 中继候选者

**主机候选者**，表示的是本地局域网内的 IP 地址及端口。它是三个候选者中优先级最高的，也就是说在 WebRTC 底层，首先会尝试本地局域网内建立连接。

**反射候选者**，表示的是获取 NAT 内主机的外网 IP 地址和端口。其优先级低于 主机候选者。也就是说当 WebRTC 尝试本地连接不通时，会尝试通过反射候选者获得的 IP 地址和端口进行连接。

**中继候选者**，表示的是中继服务器的 IP 地址与端口，即通过服务器中转媒体数据。当 WebRTC 客户端通信双方无法穿越 P2P NAT 时，为了保证双方可以正常通讯，此时只能通过服务器中转来保证服务质量了。



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





## UDP

WebRTC底层使用UDP协议主要有以下几个原因：

1. 实时性：UDP协议对实时性要求较高，适用于需要快速传输数据的实时应用场景，如音视频通话、实时游戏等。与TCP协议相比，UDP协议无需建立连接和保持状态，可以更快地传输数据。
2. 低延迟：UDP协议没有TCP协议中的拥塞控制、流量控制、重传等机制，在数据传输过程中不对数据包进行确认和重传，因此可以减少传输延迟，提高实时传输的效率。
3. 带宽利用率：UDP协议的无连接特性使得传输效率更高。在视频通话等实时应用中，有时丢失一些数据包对用户体验的影响较小，而追求高带宽利用率更为重要。
4. NAT穿透：UDP协议更容易实现NAT穿透，可以更好地解决防火墙和网络地址转换的限制，使得WebRTC可以在不同的网络环境中进行通信。

需要注意的是，WebRTC在传输层上并不直接使用UDP协议，而是基于UDP协议实现了自己的传输协议（DTLS/SRTP），以提供更好的安全性和可靠性。





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









# RTMP

RTMP（Real-Time Messaging Protocol，实时消息传输协议）是一种用于音频、视频和数据传输的协议。它由Adobe Systems开发，并且在Adobe Flash Player和Adobe AIR等播放器中使用。RTMP协议可以将音频、视频和数据从Flash播放器传输到流媒体服务器，然后再将其传送给其他用户。

RTMP协议是一种基于TCP的协议，通过TCP的可靠连接来传输数据。它支持实时的音视频传输，并且可以在不同的传输速率下自动调整视频和音频的质量，以适应不同的网络环境。

RTMP协议包括两个部分：RTMP和RTMPT。

- RTMP（Real-Time Messaging Protocol）是RTMP协议的常用版本，它使用TCP连接进行数据传输，并且支持实时的音频、视频和数据传输。
- RTMPT（Real-Time Messaging Protocol over HTTP Tunneling）是通过HTTP隧道使用RTMP协议的版本。它将RTMP的数据包封装在HTTP请求中，可以通过HTTP端口进行传输，因此可以绕过防火墙和代理服务器的限制。

RTMP协议广泛应用于流媒体领域，例如用于视频直播、音视频会议、在线游戏等。它具有低延迟、高可靠性和良好的扩展性等特点，适用于多种不同的网络环境。



# 补充



## 音频

[https://zhuanlan.zhihu.com/p/34295106](https://zhuanlan.zhihu.com/p/34295106)

声音是一种波。计算机只能处理离散的信号，通过收集足够多的离散的信号，来不断逼近波形，这个过程我们叫做采样。



### **采样频率(Sample Rate)**

每秒采集声音的数量，它用赫兹(Hz)来表示。



### **声音通道(Channel)**

每个通道存储的声音会从其中的一个喇叭出来就好了，不过可以通过算法的模拟来让没有那么多喇叭也能出来类似的效果。



### PCM 数据

PCM 数据是脉冲编码调制（Pulse Code Modulation）的缩写，是一种数字音频编码方式。在 PCM 中，音频信号会被采样成一系列的数字化值，每个值对应着采样时的声音强度。这些数字化值通常是用二进制数表示，可以存储在计算机中或者传输到其他设备或系统中。PCM 数据是一种常见的音频格式，包括 CD 音乐、数字音频文件等，也是许多数字信号处理方案的基础。



### **比特率(bps [bits per second])**

每秒钟要播放多少 bit 的数据。公式一目了然：

```text
比特率 = 采样率 × 采样深度 × 通道。
```





### 音频裁剪

[js实现音频PCM数据合并、拼接、裁剪、调节音量等功能](https://juejin.cn/post/7095537979724136456#heading-1)

```tsx
/**
* 音频裁剪
* @param audioBuffer 待裁剪的数据
* @param duration 音频总时长
* @param startOffset 裁剪偏移时间,单位s
*/
clipAudio(audioBuffer,duration,startOffset = 0){
	return new Promise((resolve,reject) => {
		// 获取音频通道数量
		const channels = audioBuffer,.numberOfChannels;
		// 获取采样率
		const rate = audioBuffer.sampleRate;
		
		// 计算截取后需要的采样数量
		const endOffset = rate * duration;
		const frameCount = endOffset - 0;
		
		// 创建新的audioBuffer数据
		const newAudioBuffer = new AudioContext().createBuffer(channels,frameCount,rate);
		
		// 创建Float32的空间,作为copy数据的载体
		const anotherArray = new Float32Array(frameCount);
		
		// 裁剪后放置的起始位置
		const offset = 0;
		
		// 遍历通道,将每个通道的数据分别copy到对应的newAudioBuffer的通道
		for(let channel = 0; channel < channels;channel++){
      // 将样本从 AudioBuffer 的指定通道复制到目标数组中
			audioBuffer.copyFromChannel(anotherArray, channel, rate * startOffset);
      // 将样本从源数组复制到AudioBuffer的指定通道。
			newAudioBuffer.copyToChannel(anotherArray, channel, offset);
		}
		// 完成裁剪
		resolve(newAudioBuffer);
	})
	
}
```







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





### **多音轨和多声道的区别**

多音轨和多声道是两个完全不同的概念：

* 多音轨指的是在放置音乐元素的位置，可以笼统对应混音设备上的具体轨道。主要是用于音乐制作中。
* 多声道是指录制采样或者播放时的音源数量，用来营造声音位置效果，声道越多声音的立体感和现场感越强。人们的听觉是通过两只耳朵对声源的反馈，除了对音色，音高的感知以外，还可以通过感知声音的传播速度差异等来确定音源的位置，多声道就是通过不同声道的细微差异而让声音给人们多一些空间感。





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



## http下无法调用摄像头和麦克风

[https://blog.csdn.net/qq_33878858/article/details/114382864](https://blog.csdn.net/qq_33878858/article/details/114382864)

在浏览器地址栏中输入“chrome://flags/#unsafely-treat-insecure-origin-as-secure”

将该选项置为Enabled
