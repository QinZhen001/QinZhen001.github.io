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





# 基础





## MediaStream

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream)

**`MediaStream`** 接口是一个媒体内容的流.。一个流包含几个*轨道*，比如视频和音频轨道。

```ts
new MediaStream()
new MediaStream(stream)
new MediaStream(tracks)
```

举个例子：

```tsx
// audioMediaStreamTrack 为 MediaStreamTrack 类型
new MediaStream([audioMediaStreamTrack]
```





### addTrack

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream/addTrac](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream/addTrac)





## MediaSource

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaSource](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaSource)

**`MediaSource`** 是 [Media Source Extensions API](https://developer.mozilla.org/zh-CN/docs/Web/API/Media_Source_Extensions_API) 表示媒体资源 [`HTMLMediaElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement) 对象的接口。`MediaSource` 对象可以附着在 [`HTMLMediaElement`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLMediaElement) 在客户端进行播放。







## MediaRecorder

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaRecorder](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaRecorder)



### start 

开始将媒体记录到一个或多个 Blob 对象中。

```ts
start(timeslice) // 记录到每个 Blob 中的毫秒数。
```





### stop 

  用于停止媒体捕获。



### state

返回当前 MediaRecorder 对象的当前状态。

inactive/recording/paused



### ondataavailable







## MediaStreamTrack

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStreamTrack](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStreamTrack)

**`MediaStreamTrack`** 接口在 User Agent 中表示一段媒体源，比如音轨或视频轨。

```tsx
navigator.mediaDevices.getUserMedia({ audio: true, video: true })
  .then(function(stream) {
    const audioTrack = stream.getAudioTracks()[0];
    const videoTrack = stream.getVideoTracks()[0];
    
    console.log('音频轨道:', audioTrack);
    console.log('视频轨道:', videoTrack);
  })
  .catch(function(error) {
    console.error('获取媒体流失败:', error);
  });
```



## AudioData

> 兼容性差（Firefox 和 Safari 不支持）

[AudioData](https://developer.mozilla.org/en-US/docs/Web/API/AudioData)

表示未编码的音频数据。

The **`AudioData`** interface of the [WebCodecs API](https://developer.mozilla.org/en-US/docs/Web/API/WebCodecs_API) represents an audio sample.





## AudioNode

[https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode)

**`AudioNode`** 接口是一个处理音频的通用模块，比如：

- 音频源（如，HTML audio/video 元素，[`OscillatorNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/OscillatorNode)，等等）
- 音频地址
- 中间处理模块（如，类似 [`BiquadFilterNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/BiquadFilterNode) 或 [`ConvolverNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/ConvolverNode) 这样的滤波器）
- 音量控制器（如 [`GainNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/GainNode))。

每个 `AudioNode` 都有输入和输出，多个音频节点连接在一起构成一个*处理图*。这个图包含在一个 [`AudioContext`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext) 中，每个音频节点只能属于一个音频上下文。





### MediaStreamAudioSourceNode

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStreamAudioSourceNode](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStreamAudioSourceNode)

`MediaStreamAudioSourceNode` 接口代表一个音频接口，是[WebRTC](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API) [`MediaStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaStream) (比如一个摄像头或者麦克风) 的一部分。是个表现为音频源的[`AudioNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode)。

```tsx
// 获取音频输入设备的媒体流
navigator.mediaDevices.getUserMedia({ audio: true })
  .then(function(stream) {
    // 创建音频上下文
    const audioContext = new AudioContext();   
    // 创建MediaStreamAudioSourceNode
    const sourceNode = audioContext.createMediaStreamSource(stream);
    // 创建其他节点进行处理或播放
    const gainNode = audioContext.createGain();
    const destinationNode = audioContext.destination;
    // 连接节点
    sourceNode.connect(gainNode);
    gainNode.connect(destinationNode);  
    // 控制音量
    gainNode.gain.value = 0.5;
  })
  .catch(function(error) {
    console.error('获取媒体流失败: ', error);
  });
```





### MediaElementAudioSourceNode

[https://developer.mozilla.org/zh-CN/docs/Web/API/MediaElementAudioSourceNode](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaElementAudioSourceNode)

举个例子：

```html
<audio controls src="./黄霄雲 - 带我去很远地方.mp3" ></audio>
```

```ts
const audioElement = document.querySelector('audio');
audioElement.addEventListener('play', () => {
    // 创建Audio Context
    const audioContext = new AudioContext();
    // 创建音频源, 之前提到的创建Audio Node第二种方法
    const source = new MediaElementAudioSourceNode(audioContext, {
        mediaElement: audioElement // 传入Audio节点
    })
})
```







### GainNode

> 音量🔊控制节点，一般放到最后

[https://developer.mozilla.org/zh-CN/docs/Web/API/GainNode](https://developer.mozilla.org/zh-CN/docs/Web/API/GainNode)

**`GainNode`** 接口表示音量的变化。它是一个 [`AudioNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode) 音频处理模块，在输出前使用给定*增益*应用到输入。一个 `GainNode` 始终只有一个输入和一个输出，两者拥有同样数量的声道。



#### gain

[`GainNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/GainNode) 接口的 `gain` 属性是一个 [a-rate](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioParam#a-rate) [`AudioParam`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioParam)，表示要应用的增益量。



### AnalyserNode

**`AnalyserNode`** 接口表示了一个可以提供实时频域和时域分析信息的节点。它是一个不对音频流作任何改动的 [`AudioNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode)，同时允许你获取和处理它生成的数据，从而创建音频可视化。

`AnalyzerNode` 只有一个输入和输出，即使未连接到输出它也能正常工作。



#### fftSize

[https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode/fftSize](https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode/fftSize)

fftSize 是 Fast Fourier Transform（FFT，快速傅里叶变换）的大小。FFT 是一种用于将时域信号（例如音频）转换为频域信号的算法。fftSize 决定了频域分析的精度和频谱的分辨率。

fftSize 的值必须是 2 的幂，并且在范围为 32 至 32768 之间。较大的 fftSize 值会提供更高的频谱分辨率，但也会增加计算量和内存消耗。较小的 fftSize 值会提供更快的分析速度，但可能会导致频谱分辨率较低。

对于 AnalyserNode，默认的 fftSize 值是 2048。可以使用 AnalyserNode.fftSize 属性来获取或设置 fftSize 的值。

需要注意的是，fftSize 的值会影响 AnalyserNode.getFloatFrequencyData() 和 AnalyserNode.getByteFrequencyData() 这两个方法返回的频谱数据的长度。频谱数据的长度等于 fftSize 的一半，也就是说 fftSize 为 2048 时，这两个方法返回的频谱数据长度为 1024。



## AudioContext

> extend BaseAudioContext

[https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext)

`AudioContext`接口表示由链接在一起的音频模块构建的音频处理图，每个模块由一个[`AudioNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode)表示。

音频上下文控制它包含的节点的创建和音频处理或解码的执行。

**在做任何其他操作之前，你需要创建一个`AudioContext`对象，因为所有事情都是在上下文中发生的。**

建议创建一个`AudioContext`对象并复用它，而不是每次初始化一个新的`AudioContext`对象，并且可以对多个不同的音频源和管道同时使用一个`AudioContext`对象。





### decodeAudioData

[https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/decodeAudioData](https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/decodeAudioData)

[`AudioContext`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext)接口的 `decodeAudioData()` 方法可用于异步解码音频文件中的 [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)。`ArrayBuffer` 数据可以通过 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 和 [`FileReader`](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader) 来获取。AudioBuffer 是通过 AudioContext 采样率进行解码的，然后通过回调返回结果。

这是从音频轨道创建用于 web audio API 音频源的首选方法

```tsx
decodeAudioData(audioData: ArrayBuffer)  =>  return AudioBuffer
```





### createBuffer

>  在 BaseAudioContext 上 

音频环境[`AudioContext`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext) 接口的 `createBuffer() 方法用于新建一个空`白的 [`AudioBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBuffer) 对象，以便用于填充数据，通过 [`AudioBufferSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBufferSourceNode) 播放。

例子

```ts
var audioCtx = new AudioContext();
var buffer = audioCtx.createBuffer(2, 22050, 44100);

// 2 numOfChannels 一个定义了 buffer 中包含的声频通道数量的整数。
// 22050 length 一个代表 buffer 中的样本帧数的整数。
// 44100 sampleRate 线性音频样本的采样率
```

采样率是指每一秒包含的关键帧的个数吗？

不完全正确。采样率是指每秒采样的次数，而关键帧是视频编码中的概念，表示一个完整的帧信息，与采样率不直接相关。关键帧在视频编码中起到重要的作用，可以作为随机访问点以及帧内预测的基准。在视频中，一般会有一些关键帧和其他非关键帧（例如预测帧或者参考帧）交替出现。



### destination

[`AudioContext`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext)的 `destination` 属性返回一个 [`AudioDestinationNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioDestinationNode)，表示 context 中所有音频的最终目标节点，一般是音频渲染设备，比如扬声器。





### currentTime

> 以秒为单位

[https://developer.mozilla.org/en-US/docs/Web/API/BaseAudioContext/currentTime](https://developer.mozilla.org/en-US/docs/Web/API/BaseAudioContext/currentTime)

BaseAudioContext 接口的 currentTime 只读属性返回一个双精度值，表示不断增加的硬件时间戳

可用于安排音频播放、可视化时间线等。它从 0 开始。



### createAnalyser

创建一个[`AnalyserNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AnalyserNode)，它可以用来暴露音频时间和频率数据，以及创建数据可视化。





### createMediaStreamSource

[https://developer.mozilla.org/en-US/docs/Web/API/AudioContext/createMediaStreamSource](https://developer.mozilla.org/en-US/docs/Web/API/AudioContext/createMediaStreamSource)

AudioContext 接口的 createMediaStreamSource() 方法用于创建一个新的 MediaStreamAudioSourceNode 对象，给定一个媒体流（例如，来自 MediaDevices.getUserMedia 实例），然后可以播放和操作其中的音频。

```tsx
createMediaStreamSource(stream)
```



### createMediaElementSource

> 返回 MediaElementAudioSourceNode

[https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext/createMediaElementSource](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext/createMediaElementSource)

[`AudioContext`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioContext) 接口的 `createMediaElementSource()` 方法用于创建一个新的 [`MediaElementAudioSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/MediaElementAudioSourceNode) 对象，输入某个存在的 HTML audio or video  元素，对应的音频即可被播放或者修改。

```ts
var audioCtx = new AudioContext();
var source = audioCtx.createMediaElementSource(myMediaElement);
// myMediaElement => 某个被期待被录入音频处理图修改的 HTMLMediaElement 对象。
```







### createBufferSource

[https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/createBufferSource](https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/createBufferSource)

`createBufferSource()` 方法用于创建一个新的[`AudioBufferSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBufferSourceNode)接口，该接口可以通过[`AudioBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBuffer) 对象来播放音频数据。



### createGain

[https://developer.mozilla.org/en-US/docs/Web/API/BaseAudioContext/createGain](https://developer.mozilla.org/en-US/docs/Web/API/BaseAudioContext/createGain)

The `createGain()` method of the [`BaseAudioContext`](https://developer.mozilla.org/en-US/docs/Web/API/BaseAudioContext) interface creates a [`GainNode`](https://developer.mozilla.org/en-US/docs/Web/API/GainNode), which can be used to control the overall gain (or volume) of the audio graph.





## AudioWorkletNode

[https://developer.mozilla.org/zh-CN/docs/Web/API/AudioWorkletNode](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioWorkletNode)

[一文拿下Web端基于AudioWorkletNode录制音频](https://juejin.cn/post/7310787455112413219)

**`AudioWorkletNode`** 接口代表了用户定义的[`AudioNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioNode)的基类，该基类可以与其他节点一起连接到音频路由图。其

```tsx
    const audioContext = new AudioContext()
    await audioContext.audioWorklet.addModule("pcm-processor.js")   
		const audioWorkletNode = new AudioWorkletNode(audioContext, "pcm-processor")
    const audioMediaStreamTrack = this.audioTrack.getMediaStreamTrack()
    this.mediaStreamAudioSourceNode = audioContext.createMediaStreamSource(new MediaStream([audioMediaStreamTrack]))
    this.mediaStreamAudioSourceNode.connect(audioWorkletNode)
    
    audioWorkletNode.port.onmessage = (event) => {
    	// ...
    }
```



## AudioBufferSourceNode

[https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBufferSourceNode](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBufferSourceNode)

**`AudioBufferSourceNode`** 接口继承自 [`AudioScheduledSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioScheduledSourceNode)，表现为一个音频源，它包含了一些写在内存中的音频数据，通常储存在一个 ArrayBuffer 对象中。

一个 [`AudioBufferSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBufferSourceNode) 只能被播放一次，也就是说，每次调用 [`start()`](https://developer.mozilla.org/en-US/docs/Web/API/AudioScheduledSourceNode/start) 之后，如果还想再播放一遍同样的声音，那么就需要再创建一个 `AudioBufferSourceNode`

庆幸的是，创建该节点的代价并不大，并且想要多次播放声音的话，实际上 `AudioBuffer` 也可以被重用。



## AudioBuffer

[https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBuffer](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBuffer)

[https://github.com/mdn/webaudio-examples/blob/main/audio-buffer/index.html](https://github.com/mdn/webaudio-examples/blob/main/audio-buffer/index.html)

AudioBuffer 接口表示存在内存里的一段短小的音频资源，利用[`AudioContext.decodeAudioData()`](https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/decodeAudioData)方法从一个音频文件构建，或者利用 [`AudioContext.createBuffer()`](https://developer.mozilla.org/zh-CN/docs/Web/API/BaseAudioContext/createBuffer)从原始数据构建。

把音频放入 AudioBuffer 后，可以传入到一个 [`AudioBufferSourceNode`](https://developer.mozilla.org/zh-CN/docs/Web/API/AudioBufferSourceNode)进行播放。







## WebCodecs API

[https://developer.mozilla.org/zh-CN/docs/Web/API/WebCodecs_API](https://developer.mozilla.org/zh-CN/docs/Web/API/WebCodecs_API)

**WebCodecs API** 为 web 开发者提供了对视频流的单个帧和音频数据块的底层访问能力。这对于那些需要完全控制媒体处理方式的 web 应用程序非常有用。例如，视频或音频编辑器，以及视频会议。



## **WebVTT**

包含实际字幕数据的文件是遵循指定格式的简单文本文件，在这种情况下是[Web视频文本轨道](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FHTML%2FWebVTT)（WebVTT）格式。该**WebVTT插入规范仍在开发中**，但它的**主要部分是稳定的**，所以我们今天可以使用它。

**Web 视频文本轨格式**（**WebVTT**）是一种使用 [track](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/track)元素显示定时文本轨道（例如字幕或者标题）的格式。WebVTT 文件的主要用途是将文本叠加到 [video](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/video) 中。WebVTT 是一种基于文本的格式，必须使用 [UTF-8](https://developer.mozilla.org/zh-CN/docs/Glossary/UTF-8) 进行编码。在可以使用空格的地方，也可以使用制表符。这里也有一个小的 API 可用于表示和管理这些轨道以及在正确时间执行文本回放所需的数据。

https://www.zhangxinxu.com/sp/webvtt.html

https://developer.mozilla.org/zh-CN/docs/Web/API/WebVTT_API

[Simple SubRip to WebVTT converter](https://atelier.u-sub.net/srt2vtt/)

**::cue伪元件是用于在VTT轨道的媒体中使用字幕和其他线索。只有少数CSS属性可以应用于文本提示：**

- color
- opacity
- visibility
- text-decoration
- text-shadow
- background
- outline
- font
- line-height
- white-space

WebVTT还支持一些HTML标签进行样式控制，常见的有声音 **v** 标签，颜色 **c** 标签，加粗**b**标签，倾斜**i**标签，下划线**u**标签，还有**ruby**和**lang**标签等。

```css
//设置字幕的样式
video::cue{
    background-color:transparent;
    color:white;
    font-size:20px;
    line-height: 100px;
}

// 设置单行字幕的样式 
video::cue(v[voice=aa]){
    color:green;
}

video::cue(v[voice=bb]){
    color:rgb(0, 26, 128);
}
```

一个 WebVTT 文件（`.vtt`）包含一行或者多行的时间提示性内容（cue）

I looked into it. In fact, the solution to this problem is to create a css:

```
::cue(.yellow) {color: yellow;}
::cue(.bg_blue) {background-color: blue;}
```



### VTTCue

[https://dev.opera.com/articles/zh-cn/an-introduction-to-webvtt-and-track/](https://dev.opera.com/articles/zh-cn/an-introduction-to-webvtt-and-track/)

VTTCue

```css
    video::cue {
      font-size: 1rem;
      color: yellow;
    }

		// 查找特定c标签
    video::cue(c.asd) {
      color: blue
    }
```

```
 const cue = new VTTCue(0, 15, "<c.asd>Cue text</c>: 11111");
```







# Web RTC

[https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API](https://developer.mozilla.org/en-US/docs/Web/API/WebRTC_API)

[https://github.com/QinZhen001/front-end-demo/blob/master/src/pages/other/web-rtc/index.tsx](https://github.com/QinZhen001/front-end-demo/blob/master/src/pages/other/web-rtc/index.tsx)

[WebRTC 介绍](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Session_lifetime)

[WebRTC 协议介绍](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Protocols)

[信令与视频通话](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling)

[音视频流媒体原理以及基础入门知识](https://zhuanlan.zhihu.com/p/538790451)

[快速入门 WebRTC：屏幕和摄像头的录制、回放、下载](https://mp.weixin.qq.com/s/u6MkezGj1o9h5-ACEkFcRQ)

音视频通信的流程有五步：采集、编码、通信、解码、渲染。

WebRTC（Web Real-Time Communication）是一种基于Web技术的实时通信协议，它主要包括三个主要组成部分：RTCPeerConnection、RTCDataChannel和MediaStream。

RTCPeerConnection是WebRTC的核心组件，它允许浏览器之间建立点对点（Peer-to-Peer）连接，并通过建立网络连接传输音频、视频和数据。RTCPeerConnection使用底层的协议来处理网络传输，例如ICE（Interactive Connectivity Establishment）协议用于处理网络地址的交换和NAT穿越，DTLS（Datagram Transport Layer Security）用于保护传输的数据。

RTCDataChannel是一个可靠的、双向的、有序的连接，可以用于在两个浏览器之间传输任意类型的数据。它使用底层的协议来处理数据传输，例如SCTP（Stream Control Transmission Protocol）用于传输和监控数据流。

MediaStream允许浏览器捕获和处理音频和视频流，并将其发送到其他浏览器。它使用底层的协议来处理音视频流的传输，例如RTP（Real-time Transport Protocol）**(属于网络协议层中的传输层)** 用于实时传输音视频数据。

除了上述的底层协议，WebRTC还使用其他协议来支持不同功能的实现，例如SDP（Session Description Protocol）用于描述会话信息，STUN（Session Traversal Utilities for NAT）用于处理NAT穿越等。

**总之，WebRTC底层协议主要涉及ICE、DTLS、SCTP、RTP等，这些协议共同实现了WebRTC的实时通信功能。**





## **媒体协商**

不同浏览器对于音视频的编解码能力是不同的。比如: Peer-A 端支持 H264、VP8 等多种编码格式,而 Peer-B 端支持 H264、VP9 等格式。为了保证双方都可以正确的编解码，最简单的办法即取它们所都支持格式的交集-H264。在 WebRTC 中，有一个专门的协议，称为**Session Description Protocol(SDP)**,可以用于描述上述这类信息。因此参与音视频通讯的双方想要了解对方支持的媒体格式，必须要交换 SDP 信息。而交换 SDP 的过程，通常称之为**媒体协商**。



## **网络协商**

参与音视频实时通信的双方要了解彼此的网络情况，这样才有可能找到一条相互通讯的链路。理想的网络情况是每个浏览器的电脑都有自己的私有公网 IP 地址，这样的话就可以直接进行点对点连接。但实际上出于网络安全和 IPV4 地址不够的考虑，我们的电脑与电脑之间或大或小都是在某个局域网内，需要**NAT(Network Address Translation, 网络地址转换)**。在 WebRTC 中我们使用 ICE 机制建立网络连接



## ICE

在WebRTC中，ICE指的是“Interactive Connectivity Establishment”。它是一种用于在网络中建立P2P（点对点）连接的框架，确保媒体和数据可以可靠地流动。ICE的主要功能是穿越NAT（网络地址转换）和防火墙，以便两个终端可以找到彼此并建立直接连接。

ICE的工作流程包括以下几个步骤：

1. **候选收集**：每个端点收集可用的网络地址（候选），这些可以是本地地址、STUN（Session Traversal Utilities for NAT）服务器提供的公共地址，或通过TURN（Traversal Using Relays around NAT）服务器获得的地址。
2. **候选配对**：一旦候选被收集，ICE会尝试配对各自的候选，以确定哪一个组合能成功建立连接。
3. **连接测试**：通过选定的候选进行连接测试，以确保双方能够顺利通信。
4. **最终连接**：一旦确定了可以成功的候选对，ICE会使用这些地址来建立和维持P2P连接。

通过ICE，WebRTC能够克服网络中的各种障碍，确保音频、视频和数据的可靠传输。



## [信令服务器](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Signaling_and_video_calling#信令服务器)

两个设备之间建立 WebRTC 连接需要一个**信令服务器**来实现双方通过网络进行连接。信令服务器的作用是作为一个中间人帮助双方在尽可能少的暴露隐私的情况下建立连接。



## STUN 和 TURN

STUN（Session Traversal Utilities for NAT）和 TURN（Traversal Using Relays around NAT）是实现网络地址转换（NAT）穿透的重要协议，它们在 WebRTC 中用于实现点对点（P2P）连接。

### STUN 协议

概述

STUN 协议主要用于帮助客户端发现公共 IP 地址和端口，以便能够与外部网络中的其他设备建立连接。STUN 通过客户端发送请求到 STUN 服务器，服务器再返回客户端的公共地址信息。

功能

- **IP 地址和端口发现**：STUN 允许 NAT 后面的设备获取其公共 IP 地址和端口，从而将这些信息提供给其他节点进行连接。
- **NAT 类型检测**：STUN 还可以帮助确定 NAT 的类型（如全锥形 NAT、受限锥形 NAT、端口受限锥形 NAT 或 Symmetric NAT），这一信息对连接策略的制定至关重要。

使用场景

- 在 WebRTC 中，STUN 服务器通常用于快速找到对等方的公共地址，以便尽量建立直接的 P2P 连接。

示例工作流程

1. 客户端向 STUN 服务器发送请求。
2. STUN 服务器接收到请求后，获取请求的源 IP 和端口，并将其作为响应发送回客户端。
3. 客户端可以将这个公共 IP 和端口信息发送给想要连接的其他方。



### TURN 协议

概述

TURN 协议是 STUN 的补充，专门设计用于在无法直接建立 P2P 连接的情况下提供中继服务。它可以在复杂的 NAT 环境下保证通信。

功能

- **中继通信**：当 P2P 连接无法直接建立时，TURN 允许客户端将数据通过 TURN 服务器中继，从而实现通信。
- **带宽保障**：TURN 服务器可以承载数据流量，确保在面对复杂网络条件时仍能进行通信。

使用场景

- 当使用 STUN 服务器无法建立对等连接（例如，受限制的 NAT 或防火墙配置），TURN 服务器作为可靠的中转站是选择。

示例工作流程

1. 客户端与 TURN 服务器建立连接。
2. 客户端请求 TURN 服务器分配一个中继地址。
3. TURN 服务器分配一个 IP 地址和端口给客户端，并告知其如何转发数据。
4. 客户端将所有音视频数据发送到 TURN 服务器，服务器再将数据转发给目标客户端。

STUN 与 TURN 的比较

| 特性         | STUN                      | TURN                            |
| :----------- | :------------------------ | :------------------------------ |
| 主要功能     | NAT穿透，获取公共IP和端口 | 提供中继服务以确保通信          |
| 直接连接能力 | 尝试建立 P2P 连接         | 仅在 P2P 连接失败时使用         |
| 性能         | 较低的延迟和带宽消耗      | 较高的延迟和带宽消耗            |
| 适用环境     | 简单 NAT 环境             | 复杂 NAT 环境或防火墙阻碍的情况 |

### 结论

STUN 和 TURN 协议是 WebRTC 实现实时通信的基础，STUN 主要用于快速建立 P2P 连接，而 TURN 则作为后盾，当 P2P 连接不可行时，提供可靠的数据中继。这两者的组合确保了 WebRTC 能够在各种网络环境中高效运行。



## **ICE**

**ICE (Interactive Connecctivity Establishment, 交互式连接建立)**，ICE 不是一种协议，而是整合了 STUN 和 TURN 两种协议的框架。其中**STUN(Sesssion Traversal Utilities for NAT, NAT 会话穿越应用程序)**，它允许位于 NAT（或多重 NAT）后的客户端找出自己对应的公网 IP 地址和端口，也就是俗称的“打洞”。但是，如果 NAT 类型是对称型的话，那么就无法打洞成功。这时候 TURN 就派上用场了，**TURN**(Traversal USing Replays around NAT)是 STUN/RFC5389 的一个拓展协议在其基础上添加了 Replay(中继)功能，简单来说其目的就是解决对称 NAT 无法穿越的问题，在 STUN 分配公网 IP 失败后，可以通过 TURN 服务器请求公网 IP 地址作为中继地址。

在 WebRTC 中有三种类型的 ICE 候选者，它们分别是：

- 主机候选者
- 反射候选者
- 中继候选者

**主机候选者**，表示的是本地局域网内的 IP 地址及端口。它是三个候选者中优先级最高的，也就是说在 WebRTC 底层，首先会尝试本地局域网内建立连接。

**反射候选者**，表示的是获取 NAT 内主机的外网 IP 地址和端口。其优先级低于 主机候选者。也就是说当 WebRTC 尝试本地连接不通时，会尝试通过反射候选者获得的 IP 地址和端口进行连接。

**中继候选者**，表示的是中继服务器的 IP 地址与端口，即通过服务器中转媒体数据。当 WebRTC 客户端通信双方无法穿越 P2P NAT 时，为了保证双方可以正常通讯，此时只能通过服务器中转来保证服务质量了。

ICE 组件

ICE 由以下几个主要部分组成：

- **候选地址（Candidate）**：候选地址是可以用于建立连接的网络地址，包括：
  - **主机候选（Host candidates）**：直接来自终端设备的 IP 地址和端口。
  - **反向代理候选（Server reflexive candidates）**：使用 STUN 服务器获取的公共 IP 地址和端口。
  - **中继候选（Relay candidates）**：通过 TURN 服务器获取的地址，适用于 NAT 环境复杂的情况。
- **ICE Agent**：ICE 的主要执行逻辑，负责收集候选地址、处理连接建立过程，并管理候选优先级。

总结：

**通过 ICE，WebRTC 能够高效地处理 NAT 问题，确保可靠的 P2P 连接建立和音视频流传输。这一机制使得 WebRTC 在各种网络环境下具备了更强的适应能力。**



## connectivity

[https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Connectivity](https://developer.mozilla.org/zh-CN/docs/Web/API/WebRTC_API/Connectivity)





## NAT

NAT（Network Address Translation，网络地址转换）是一种网络协议，用于将私有 IP 地址（内部网络地址）转换为公共 IP 地址（外部网络地址）和反向转换。NAT 的主要目的是通过允许多台设备共享一个公共 IP 地址来节省有限的 IPv4 地址空间，同时提供基本的安全性。

NAT 的工作原理

NAT 工作在路由器或防火墙上，当内网设备发送数据包到外网时，NAT 会执行以下操作：

1. **转换源地址**：在数据包离开内部网络时，NAT 设备会将数据包的源地址（内部私有 IP 地址）替换为其自身的公共 IP 地址。
2. **维护映射表**：NAT 设备会维护一个地址映射表，将内部 IP 地址和端口与公共 IP 地址和端口相关联。这确保后续数据包能正确路由回原始设备。
3. **转换目标地址**：当外部数据包返回时，NAT 会查找映射表，将目标地址的公共 IP 地址转换回相应的内部私有 IP 地址，并将数据包发往正确的内部设备。

NAT 的类型

NAT 有几种主要类型，分别适用于不同的网络场景：

1. **静态 NAT**：
   - 一对一映射，将特定的内部 IP 地址映射到特定的公共 IP 地址。通常用于需要从外部网络保持可访问性的设备（如服务器）。
2. **动态 NAT**：
   - 使用一组公共 IP 地址动态映射内部 IP 地址。当内部设备需要访问外部网络时，NAT 设备在共享的 IP 数组中为其分配一个公共 IP 地址。
3. **端口地址转换 (PAT)** 或称为 **NAT Overload**：
   - 多个内部设备共享一个公共 IP 地址，通过不同的端口号进行区分。这是最常用的 NAT 形式，因为它允许大量内网设备通过一个公共 IP 地址访问互联网。

NAT 的优点

- **节省 IP 地址**：NAT 允许多个设备使用同一个公共 IP 地址，帮助缓解 IPv4 地址短缺的问题。
- **安全性**：NAT 隐藏了内部网络结构，使得外部攻击者更难直接访问内部设备。

NAT 的缺点

- **带来延迟**：NAT 的地址转换可能会导致少量延迟，影响实时应用（如 VoIP 和视频会议）的性能。
- **复杂的连接管理**：有些应用（尤其是点对点应用，如 WebRTC）需要进行 NAT 穿透，以便在 NAT 后的设备之间建立连接，增加了额外的复杂性。
- **某些协议的限制**：某些协议（如 FTP 和 SIP）可能在 NAT 环境下无法正常工作，需要额外的配置和支持（例如，使用 STUN 或 TURN 协议进行 NAT 穿透）。

结论

NAT 是现代网络架构中的重要组成部分，通过转换私有和公共 IP 地址，实现了设备之间的通信，同时提供了安全性和 IP 地址的高效利用。但它也带来了诸如延迟和复杂性等挑战。针对特定网络需求，选择合适的 NAT 类型和相关解决方案至关重要。



## SDP

[https://developer.mozilla.org/zh-CN/docs/Glossary/SDP](https://developer.mozilla.org/zh-CN/docs/Glossary/SDP)

**SDP** (Session Description [Protocol](https://developer.mozilla.org/zh-CN/docs/Glossary/Protocol)) 是一个描述[peer-to-peer (en-US)](https://developer.mozilla.org/en-US/docs/Glossary/P2P) 连接的标准。SDP 包含音视频的：编解码 ([codec](https://developer.mozilla.org/zh-CN/docs/Glossary/Codec)),源地址，和时间信息。





## RTCPeerConnection

**`RTCPeerConnection`** 接口代表一个由本地计算机到远端的 WebRTC 连接。该接口提供了创建，保持，监控，关闭连接的方法的实现。





## UDP

**WebRTC 通过 UDP (属于网络协议层中的传输层) 传输 RTP 数据包。**WebRTC底层使用UDP协议主要有以下几个原因：

1. 实时性：UDP协议对实时性要求较高，适用于需要快速传输数据的实时应用场景，如音视频通话、实时游戏等。与TCP协议相比，UDP协议无需建立连接和保持状态，可以更快地传输数据。
2. 低延迟：UDP协议没有TCP协议中的拥塞控制、流量控制、重传等机制，在数据传输过程中不对数据包进行确认和重传，因此可以减少传输延迟，提高实时传输的效率。
3. 带宽利用率：UDP协议的无连接特性使得传输效率更高。在视频通话等实时应用中，有时丢失一些数据包对用户体验的影响较小，而追求高带宽利用率更为重要。
4. NAT穿透：UDP协议更容易实现NAT穿透，可以更好地解决防火墙和网络地址转换的限制，使得WebRTC可以在不同的网络环境中进行通信。

需要注意的是，WebRTC在传输层上并不直接使用UDP协议，而是基于UDP协议实现了自己的传输协议（DTLS/SRTP），以提供更好的安全性和可靠性。





## Janus

[https://janus.conf.meetecho.com/docs/](https://janus.conf.meetecho.com/docs/)

General purpose WebRTC server







# video.js

[https://videojs.com/getting-started](https://videojs.com/getting-started)

Video.js is an extendable framework/library around the native video element. 





## Tech

[https://juejin.cn/post/6968628379213889550](https://juejin.cn/post/6968628379213889550)

Tech 是 videojs 中处理视频播放的技术

videojs 核心源码中提供了 Html5 作为默认 Tech，用于播放浏览器默认支持的视频类型。同时支持扩展 Tech，用于支持浏览器无法直接播放的视频类型，例如 flv、hls、dash 等。



## screenfull

videojs的源码，发现可以用screenfull.js这个库直接实现全屏，非常方便。

```ts
// 无需自己做浏览器兼容
import screenfull from "screenfull";

if (screenfull.isEnabled) {
    screenfull.request(video);
}
```





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



## HLS

HLS（HTTP Live Streaming）

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





## 音频采集

```bash
ffmpeg -f avfoundation -i :0 out.wav
```





## copy 模式

在FFmpeg中，复制（copy）模式是指直接复制输入文件的音频或视频流，不进行任何编码或解码操作。这种模式可以快速地将输入文件复制到输出文件中，而不会改变编码格式或压缩质量。使用复制模式可以节省时间和系统资源，并且可以保留原始的音视频质量。

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





# ffplay

ffplay是FFmpeg项目中的一个简单的媒体播放器，可以用于播放各种音视频文件。它可以在命令行中使用，并提供一些基本的播放控制选项和特效设置。ffplay基于FFmpeg的多媒体框架构建，支持多种音视频格式，并可以通过命令行选项进行自定义设置。



# 协议





## RTP，RTCP

实时传输协议（Real-time Transport Protocol），RTP协议常用于流媒体系统（配合RTCP协议），视频会议和一键通系统（配合H.323或SIP），使它成为IP电话产业的技术基础。RTP协议和RTP控制协议RTCP一起使用，而且它是建立在UDP协议上的。

实时传输控制协议（Real-time Transport Control Protocol或RTP Control Protocol或简写RTCP）是实时传输协议的一个姐妹协议。RTCP为RTP媒体流提供信道外控制。RTCP本身并不传输数据，但和RTP一起协作将多媒体数据打包和发送。RTCP定期在流多媒体会话参加者之间传输控制数据。RTCP的主要功能是为RTP所提供的服务质量提供反馈。

RTSP+RTP经常用于IPTV领域。因为其采用UDP传输视音频，支持组播，效率较高。但其缺点是网络不好的情况下可能会丢包，影响视频观看质量。

**RTP协议的延迟主要取决于网络传输的延迟和数据包的处理时间等因素，一般在几十到几百毫秒之间。**

**RTP 底层是基于UDP 的，UDP只是负责传输数据包，RTP提供时间标志戳及其他技术来保证流媒体在实时传输时的时间正确性。**

**WebRTC（Web实时通信）是基于RTP（实时传输协议）实现的**

**RTP是传输层协议**



## RTSP

**RTSP是传输层（RTP是传输层）之上的应用层协议**

RTSP，全称为Real Time Streaming Protocol，即实时流传输协议。它是一种用于控制实时多媒体数据传输的应用层协议。RTSP协议允许客户端通过控制服务器来播放、暂停、调节音量、跳跃等操作，与流媒体服务器之间进行交互。RTSP常用于流媒体播放器、IP摄像机等设备上，用于实现实时音视频的传输和控制。

RTSP 和 RTMP 的区别：

1. 目的和使用场景：RTMP主要用于实时传输媒体数据，如音频和视频，它适用于一对一或一对多的直播和点播场景。RTSP主要用于控制网络流媒体服务器，它主要用于需要进行流式传输和控制的多媒体应用。
2. 协议结构：RTMP是基于TCP的应用层协议，它通过建立TCP连接，并在该连接上传输媒体数据和控制信息。RTSP是一个用于实时传输控制的应用层协议，它使用TCP或UDP来传输控制消息，而媒体数据则使用RTP或者RTCP。
3. 支持性和平台兼容性：RTMP在多种操作系统和平台上都有广泛的支持，包括Windows、Mac、Linux等。RTSP也有广泛的支持，但它可能不同程度地依赖于不同的操作系统和流媒体服务器。
4. 编码支持：RTMP支持多种编码类型，包括H.264、AAC等，可以在较低的带宽下提供高质量的传输和播放。RTSP也支持多种编码类型，但具体的编码支持取决于流媒体服务器和客户端的能力。

总的来说，RTMP适用于实时的直播和点播场景，而RTSP适用于需要进行流式传输和控制的多媒体应用。两种协议各有优劣，选择使用哪一种取决于具体的应用需求和平台支持。





## RTMP

RTMP（Real-Time Messaging Protocol，实时消息传输协议）是一种用于音频、视频和数据传输的协议。它由Adobe Systems开发，并且在Adobe Flash Player和Adobe AIR等播放器中使用。RTMP协议可以将音频、视频和数据从Flash播放器传输到流媒体服务器，然后再将其传送给其他用户。

**RTMP协议是一种基于TCP的协议，通过TCP的可靠连接来传输数据。它支持实时的音视频传输，并且可以在不同的传输速率下自动调整视频和音频的质量，以适应不同的网络环境。**

RTMP协议包括两个部分：RTMP和RTMPT。

- RTMP（Real-Time Messaging Protocol）是RTMP协议的常用版本，它使用TCP连接进行数据传输，并且支持实时的音频、视频和数据传输。
- RTMPT（Real-Time Messaging Protocol over HTTP Tunneling）是通过HTTP隧道使用RTMP协议的版本。它将RTMP的数据包封装在HTTP请求中，可以通过HTTP端口进行传输，因此可以绕过防火墙和代理服务器的限制。

RTMP协议广泛应用于流媒体领域，例如用于视频直播、音视频会议、在线游戏等。它具有低延迟、高可靠性和良好的扩展性等特点，适用于多种不同的网络环境。





它有三种变种：

* 工作在TCP之上的明文协议，使用端口1935；
* RTMPT封装在HTTP请求之中，可穿越防火墙；
* RTMPS类似RTMPT，但使用的是HTTPS连接；

总结： RTMP协议基于TCP来实现，每个时刻的数据，收到后立刻转发，**一般延迟在1-3s左右**





## HLS

HTTP Live Streaming（HLS）是苹果公司(Apple Inc.)实现的基于HTTP的流媒体传输协议，可实现流媒体的直播和点播。HLS点播，基本上就是常见的分段HTTP点播，不同在于，它的分段非常小。基本原理就是将视频或流切分成小片（TS）， 并建立索引（M3U8）HTTP Live Streaming（HLS）是苹果公司(Apple Inc.)实现的基于HTTP的流媒体传输协议，可实现流媒体的直播和点播。HLS点播，基本上就是常见的分段HTTP点播，不同在于，它的分段非常小。基本原理就是将视频或流切分成小片（TS）， 并建立索引（M3U8）.

**相对于常见的流媒体直播协议，例如RTMP协议、RTSP协议、MMS协议等，HLS直播最大的不同在于，直播客户端获取到的，并不是一个完整的数据流。**

HLS协议在服务器端将直播数据流存储为连续的、很短时长的媒体文件（MPEG-TS格式），而客户端则不断的下载并播放这些小文件，因为服务器端总是会将最新的直播数据生成新的小文件，这样客户端只要不停的按顺序播放从服务器获取到的文件，就实现了直播。

由此可见，基本上可以认为，HLS是以点播的技术方式来实现直播。

**总结： HLS协议基于HTTP短连接来实现，集合一段时间数据，生成ts切片文件，然后更新m3u8(HTTP Live Streaming直播的索引文件)，一般延迟会大于10s**



## **HTTP-FLV**

HTTP-FLV基于HTTP长连接，通RTMP一样，每个时刻的数据，收到后立刻转发，只不过使用的是HTTP协议，一般延迟在1-3s



## **CDN**

CDN架构设计比较复杂。不同的CDN厂商，也在对其架构进行不断的优化，所以架构不能统一而论。这里只是对一些基本的架构进行简单的剖析。

CDN主要包含：源站、缓存服务器、智能DNS、客户端等几个主要组成部分。

源站：是指发布内容的原始站点。添加、删除和更改网站的文件，都是在源站上进行的；另外缓存服务器所抓取的对象也全部来自于源站。对于直播来说，源站为主播客户端。

缓存服务器：是直接提供给用户访问的站点资源，由一台或数台服务器组成；当用户发起访问时，他的访问请求被智能DNS定位到离他较近的缓存服务器。如果用户所请求的内容刚好在缓存里面，则直接把内容返还给用户；如果访问所需的内容没有被缓存，则缓存服务器向邻近的缓存服务器或直接向源站抓取内容，然后再返还给用户。

**智能DNS：是整个CDN技术的核心，它主要根据用户的来源，以及当前缓存服务器的负载情况等，将其访问请求指向离用户比较近且负载较小的缓存服务器。通过智能DNS解析，让用户访问同服务商下、负载较小的服务器，可以消除网络访问慢的问题，达到加速作用。**

客户端：即发起访问的普通用户。对于直播来说，就是观众客户端。





# 补充







## 音频

[https://zhuanlan.zhihu.com/p/34295106](https://zhuanlan.zhihu.com/p/34295106)

声音是一种波。计算机只能处理离散的信号，通过收集足够多的离散的信号，来不断逼近波形，这个过程我们叫做采样。



### 音频处理(AudioGraph)

浏览器中的音频处理的术语称为AudioGraph，其实就是一个【中间件模式】，你需要创建一个source节点和一个destination节点，然后在它们之间可以连接许许多多不同类型的节点，source节点既可以来自流媒体对象，也可以自己填充生成，destination可以连接默认的扬声器端点，也可以连接到媒体录制APIMediaRecorder来直接将pcm数据转换为指定媒体编码格式的数据。
中间节点的类型有很多种，可实现的功能也非常丰富，包括增益、滤波、混响、声道的合并分离以及音频可视化分析等等非常多功能





### **采样频率(Sample Rate)**

每秒采集声音的数量，它用赫兹(Hz)来表示。

**定义了每秒从连续信号中提取并组成离散信号的采样个数，它用赫兹（Hz）来表示。**采样率是指将模拟信号转换成数字信号时的采样频率，也就是单位时间内采样多少点。一个采样点数据有多少个比特。比特率是指每秒传送的比特(bit)数。单位为 bps(Bit Per Second)，比特率越高，传送的数据越大，音质越好.



### **声音通道(Channel)**

每个通道存储的声音会从其中的一个喇叭出来就好了，不过可以通过算法的模拟来让没有那么多喇叭也能出来类似的效果。

声道数：声道数是音频传输的重要指标，现在主要有单声道和双声道之分。双声道又称为立体声，在硬件中要占两条线路，音质、音色好， 但立体声数字化后所占空间比单声道多一倍。



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



### WAV

WAV可以在mac 和 网页上进行播放。

请注意，不是所有的浏览器都支持播放WAV文件。在某些情况下，您可能需要将WAV文件转换为其他格式（例如MP3或OGG），以确保在各种浏览器中的兼容性。

判断浏览器是否支持播放WAV文件：

```tsx
var audio = new Audio("path_to_wav_file.wav");
if (audio.canPlayType("audio/wav") !== "") {
  // 浏览器支持播放WAV文件
} else {
  // 浏览器不支持播放WAV文件
}
```





### WAV 和 MP3 

WAV文件和MP3是两种常见的音频文件格式。

* WAV（Waveform Audio File Format）是一种无损的音频文件格式，它的文件体积相对较大，因为它保存音频数据的完整信息，包括采样率、采样位数等。WAV文件通常用于存储无损音频数据，如音乐制作、CD音轨等。由于它的音质较高，很多专业音频工作者和音乐制作人都会选择使用WAV文件进行录制和编辑。
* MP3（MPEG-1 Audio Layer III）是一种有损的音频文件格式，它通过压缩音频数据来减小文件体积，因此相对于WAV文件，MP3文件体积更小。在压缩过程中，MP3会丢弃一些人耳较难察觉的音频数据，从而达到较高的压缩比。由于MP3文件相对较小，易于传输和存储，因此成为了互联网上非常流行的音频格式，经常在音乐下载、流媒体等场景中使用。

需要注意的是，由于MP3是有损压缩格式，相对于WAV来说会损失一些音质。因此，如果对音质要求较高的情况下，可以选择WAV文件作为首选，而如果追求文件体积小并且对音质要求相对较低，可以选择MP3文件。



### webaudio-examples

[https://github.com/mdn/webaudio-examples](https://github.com/mdn/webaudio-examples)

[https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Audio_API)





### webrtc-adapter

[https://www.npmjs.com/package/webrtc-adapter](https://www.npmjs.com/package/webrtc-adapter)

adapter.js is a shim to insulate apps from spec changes and prefix differences in WebRTC. The prefix differences are mostly gone these days but differences in behaviour between browsers remain.

js是一种将应用程序与WebRTC中的规范变化和前缀差异隔离开来的机制。这些天，前缀的差异已经消失了，但浏览器之间的行为差异仍然存在。





## 视频



### M3U8

M3U8 文件是一种播放列表文件，用于描述一系列媒体段（通常是 TS 文件）的位置和顺序。它是 M3U 文件格式的扩展版本，使用 UTF-8 编码。

application/vnd.apple.mpegURL 是一种MPEG-DASH（Dynamic Adaptive Streaming over HTTP）视频流的容器格式，也即.m3u8文件。

这种格式通常用于流媒体服务中，允许动态自适应流媒体传输，以适应观众的网络状况和设备能力。.m3u8 是一个文本文件，其中包含媒体片段的URL，可以用于播放HLS（HTTP Live Streaming）视频流。HLS是苹果公司开发的一种流媒体传输协议，适用于iOS和macOS设备上的视频播放。

```tsx
browserHlsSupported(): boolean {
    const mediaElement = document.createElement('video');
    return !!mediaElement.canPlayType('application/vnd.apple.mpegURL');
  }
```

- **播放列表**：M3U8 文件包含指向实际媒体段（如 TS 文件）的 URL 或路径。
- **文本格式**：M3U8 文件是纯文本文件，可以用任何文本编辑器打开和查看。
- **动态更新**：在直播场景中，M3U8 文件可以动态更新，以包含新的媒体段。



### TS 文件

TS 文件（Transport Stream 文件）是一种媒体段文件，包含实际的音视频数据。TS 是一种容器格式，常用于存储和传输视频、音频和数据。

- **媒体段**：TS 文件包含实际的音视频数据段，通常是几秒钟的长度。
- **分段传输**：在 HLS 中，视频被分割成多个 TS 文件，这些文件可以独立下载和播放。
- **容器格式**：TS 文件可以包含多种编码格式的数据，常见的是 H.264 视频和 AAC 音频。





### flv、hls、dash

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

HLS支持AES加密，可以对视频内容进行加密保护；FLV的安全性较差，容易被破解和盗取。

HLS在传输过程中有一定的延迟，通常为几秒到十几秒；FLV的传输延迟较低，可以实现较低的实时性。

HLS使用了自适应码率技术，根据网络条件动态调整码率和缓冲区大小，以提供更好的播放体验；FLV没有自适应码率技术，网络不稳定时容易出现卡顿和缓冲。

总体来说，HLS更适用于移动设备和各种平台的流媒体传输，具有更好的兼容性和安全性；FLV在过去常用于Flash平台上的流媒体传输，现在已经逐渐被其他传输协议所取代。

**RTMP 协议为流媒体而设计，在推流中用的比较多，同时大多 CDN 厂商支持RTMP 协议。**





### MP4

MP4（MPEG-4 Part 14）是一种数字多媒体容器格式，用于存储视频、音频、字幕和其他数据。MP4 文件的扩展名通常是 `.mp4`。它可以包含多种编码格式的数据，包括但不限于：

- **视频编码格式**：H.264（也称为 AVC）、H.265（也称为 HEVC）、MPEG-4 Part 2、VP9 等。
- **音频编码格式**：AAC、MP3、ALAC、Opus 等。
- **字幕**：SRT、VTT、TTML 等。

---

**MP4 作为容器**：MP4 可以包含 H.264 编码的视频流，但也可以包含其他编码格式的视频流。

**H.264 作为编码格式**：H.264 编码的视频流可以存储在 MP4 容器中，但也可以存储在其他容器格式中，如 MKV、AVI、FLV 等。



### 卡顿率

视频卡顿率是指在播放视频时出现卡顿现象的频率。具体来说，视频卡顿率是指每秒钟连续播放的视频帧中，有多少帧出现了卡顿现象。常见的视频卡顿原因包括网络缓慢、计算机性能不足、视频文件损坏等。视频卡顿率越低，表示视频播放的流畅程度越高。





### PVC

PVC（Perceptual Video Coding）视频感知编码，是一种在保障同等画质的前提下降低带宽消耗的视频编码方式。例如，在带宽受限场景下提升视频流畅性，在移动网络场景下降低流量消耗。



### FLV 和 FLS

FLV（Flash Video）和 FLS（Flash Live Streaming）是两种与 Adobe Flash 技术相关的文件格式和传输协议。尽管它们都与 Flash 相关，但它们的用途和功能有所不同。以下是对 FLV 和 FLS 的详细解释：

FLV（Flash Video）

概述

- **文件扩展名**：.flv
- **用途**：用于存储和传输视频内容。
- **开发者**：Adobe Systems（原 Macromedia）

特点

- **视频编码**：通常使用 H.263、VP6 或 H.264 编码。
- **音频编码**：通常使用 MP3 或 AAC 编码。
- **容器格式**：FLV 是一种容器格式，能够封装视频、音频、字幕和元数据。
- **流媒体支持**：支持通过 HTTP 或 RTMP（Real-Time Messaging Protocol）进行流媒体传输。

使用场景

- **在线视频**：广泛用于视频网站，如 YouTube（早期）、Vimeo 等。
- **嵌入网页**：通过 Flash 播放器嵌入网页，实现视频播放。
- **多媒体应用**：用于多媒体应用和广告。

FLS（Flash Live Streaming）

概述

- **协议名称**：FLS 通常指 Flash Live Streaming。
- **用途**：用于实时视频流传输。

特点

- **实时传输**：FLS 是一种协议，支持实时视频和音频流的传输，通常用于直播。
- **基于 RTMP**：FLS 通常基于 RTMP 协议，用于低延迟的实时流传输。
- **服务器支持**：需要专门的流媒体服务器，如 Adobe Media Server 或开源的 Red5、Wowza 等。

使用场景

- **实时直播**：用于直播活动、网络研讨会、在线教育等实时视频流传输。
- **互动视频**：支持实时互动视频应用，如视频会议、视频聊天等。

### 对比

| 特性     | FLV                            | FLS                                   |
| -------- | ------------------------------ | ------------------------------------- |
| 类型     | 文件格式（容器格式）           | 流媒体传输协议                        |
| 用途     | 存储和传输视频内容             | 实时视频流传输                        |
| 视频编码 | H.263、VP6、H.264              | 取决于具体实现，通常为 H.264          |
| 音频编码 | MP3、AAC                       | 取决于具体实现，通常为 AAC            |
| 传输方式 | HTTP、RTMP                     | RTMP                                  |
| 使用场景 | 在线视频、嵌入网页、多媒体应用 | 实时直播、互动视频、网络研讨会        |
| 依赖     | Flash 播放器                   | 流媒体服务器（如 Adobe Media Server） |

### 总结

FLV 是一种用于存储和传输视频内容的文件格式，广泛应用于在线视频和多媒体应用。FLS 则是一种用于实时视频流传输的协议，通常基于 RTMP，用于直播和互动视频场景。尽管两者都与 Flash 技术相关，但它们在用途和实现方式上有显著区别。





### video bitrate

视频比特率（video bitrate）指的是视频编码中每秒传输的比特数。单位为bps(Bit Per Second)。比特率越高，视频的质量越好，但文件大小也越大。因此，在选择视频比特率时需要权衡视频质量和文件大小。较低的比特率会导致视频压缩和失真，而较高的比特率可以提供更清晰的图像和更高的视频质量。





### **码率**

码流(Data Rate)是指视频文件在单位时间内使用的数据流量，也叫码率或码流率，通俗一点的理解就是取样率,是视频编码中画面质量控制中最重要的部分，一般我们用的单位是kb/s或者Mb/s。一般来说同样分辨率下，视频文件的码流越大，压缩比就越小，画面质量就越高。码流越大，说明单位时间内采样率越大，数据流，精度就越高，处理出来的文件就越接近原始文件，图像质量越好，画质越清晰，要求播放设备的解码能力也越高。



### **帧率**

帧率也称为FPS(Frames Per Second)- - - 帧/秒。是指每秒钟刷新的图片的帧数，也可以理解为图形处理器每秒钟能够刷新几次。越高的帧速率可以得到更流畅、更逼真的动画。每秒钟帧数(FPS)越多，所显示的动作就会越流畅。

帧率越高，cpu消耗就高

* 秀场视频直播，一般帧率20fps
* 普通视频直播，一般帧率15fps



### **分辨率**

视频分辨率是指视频成像产品所成图像的大小或尺寸。常见的视像分辨率有352×288，176×144，640×480，1024×768。在成像的两组数字中，前者为图片长度，后者为图片的宽度，两者相乘得出的是图片的像素，长宽比一般为4：3.

* 480P : 640 x 480 个像素点
* 720P : 1280 x 720 个像素点
* 1080P : 1920 x 1080 个像素点



#### 帧率、码率与分辨率之间关系

码率和帧率没有半毛钱关系









## 科普

[「完全理解」video 标签到底能播放什么](https://juejin.cn/post/7018373086072766472)



### SEI

SEI（Supplemental Enhancement Information）是一种嵌入在 H.264 和 H.265 视频流中的元数据，用于提供额外的信息以增强解码器的功能和性能。SEI 信息可以包含时间戳、显示信息、用户数据、场景切换信息和质量信息等，帮助解码器更好地处理和显示视频内容。



### SODB、RBSP 和 EBSP

[https://lazybing.github.io/blog/2017/06/22/sodb-rbsp-ebsp/](https://lazybing.github.io/blog/2017/06/22/sodb-rbsp-ebsp/)

SODB、RBSP 和 EBSP 是与视频编码和传输相关的术语，特别是在 H.264/AVC 和 H.265/HEVC 等视频编码标准中。这些术语涉及编码视频数据的不同级别和结构。

SODB (String Of Data Bits)

SODB 是指数据位串（String Of Data Bits），它是编码数据的最基本形式，不包括任何填充位或附加信息。SODB 是最原始的编码数据，直接从编码器输出。

RBSP (Raw Byte Sequence Payload)

RBSP 是指原始字节序列有效载荷（Raw Byte Sequence Payload）。它是在 SODB 基础上添加了一些填充位，以确保数据对齐到字节边界。RBSP 是一个包含了编码数据和必要的填充位的字节序列。

EBSP (Encapsulated Byte Sequence Payload)

EBSP 是指封装字节序列有效载荷（Encapsulated Byte Sequence Payload）。在 RBSP 的基础上，EBSP 添加了防止起始码前缀（start code prefix）的防止机制（即字节填充），以便在传输或存储过程中避免与起始码混淆。EBSP 是最终用于传输或存储的编码数据格式。

关系和转换

1. **SODB 到 RBSP**：在 SODB 的基础上添加填充位，使数据对齐到字节边界，形成 RBSP。
2. **RBSP 到 EBSP**：在 RBSP 的基础上添加防止起始码前缀的机制，形成 EBSP。

### 示例

假设我们有一个 SODB，如下所示：

```
SODB: 1101001110100011 (16 bits)
```

为了形成 RBSP，我们可能需要添加一些填充位，使其对齐到字节边界：

```
RBSP: 11010011 10100011 (分成两个字节)
```

接下来，为了形成 EBSP，我们需要确保没有字节序列与起始码前缀冲突（例如，避免出现 `0x000001`）。假设在这个例子中不需要额外的防止机制，那么：

```
EBSP: 11010011 10100011 (与 RBSP 相同)
```

然而，如果我们需要防止起始码前缀，我们可能会插入一些额外的字节来避免冲突。

总结

- **SODB** 是最原始的编码数据位串。
- **RBSP** 是在 SODB 基础上添加填充位后的字节序列。
- **EBSP** 是在 RBSP 基础上添加防止起始码前缀机制后的封装字节序列。

这些概念在视频编码标准中非常重要，用于确保视频数据在编码、传输和解码过程中保持一致性和可靠性。



### 封装与解包

H.264 和 AAC，可以被封装成 MP4 或 FLV。但是 Chrome 只能播放前者而不支持后者，细想其实很没道理。就好比两个东西被装在 A 盒子里的时候能拆开用，但是装在 B 盒子里就不行了

因为本质上重要的是**东西**（H.264、H.265），而不是**盒子**（MP4、FLV）

把编码封装成文件的步骤叫做**封装**（remux），文件拆解成编码的步骤叫**解包**（demux）

封装，抽象的说其实就是把**东西**按**不同排列**码到一块



### Remux 和 Demux

"Remux" 和 "Demux" 是与多媒体文件处理相关的术语，主要涉及到文件的封装和解封装。它们在视频编辑、转换和传输中起着重要作用。

**Demux** 是“Demultiplexing”的缩写，意为“解复用”。它指的是从一个多路复用的媒体文件中分离出不同的流（如视频流、音频流、字幕流等）的过程。

具体过程

1. **输入文件**：一个多路复用的媒体文件，如 MP4、MKV、TS 等。
2. **分离流**：将视频流、音频流、字幕流等分离成独立的文件或数据流。
3. **输出**：得到独立的流文件，如 .h264（视频流）、.aac（音频流）、.srt（字幕流）等。

示例应用

- **视频编辑**：需要单独处理视频和音频流。
- **音频提取**：从视频文件中提取音频。
- **格式转换**：将多路复用的文件转换为其他格式。

---

**Remux** 是“Remultiplexing”的缩写，意为“重新复用”。它指的是将多个独立的流（如视频流、音频流、字幕流等）重新组合成一个多路复用的媒体文件的过程。

具体过程

1. **输入流**：独立的视频流、音频流、字幕流等。
2. **组合流**：将这些独立的流组合成一个多路复用的文件。
3. **输出文件**：得到一个多路复用的媒体文件，如 MP4、MKV、TS 等。

示例应用

- **视频制作**：将编辑好的视频和音频流重新组合成一个文件。
- **格式转换**：将独立的流文件组合成一个特定格式的媒体文件。
- **文件优化**：重新封装以获得更好的兼容性或更小的文件大小。

区别总结

- **Demux（解复用）**：从一个多路复用的媒体文件中分离出独立的流。
  - **用例**：从 MP4 文件中提取视频和音频流。
- **Remux（重新复用）**：将独立的流重新组合成一个多路复用的媒体文件。
  - **用例**：将独立的视频和音频流重新组合成一个 MKV 文件。

实际工具

- **Demux 工具**：FFmpeg、MKVToolNix、tsMuxer 等。
- **Remux 工具**：FFmpeg、MKVToolNix、tsMuxer 等。

通过使用这些工具，用户可以方便地进行媒体文件的解复用和重新复用，以满足不同的需求。



### 解码器和编码器

解码器（Decoder）和编码器（Encoder）是一对相反的操作。

编码器是将原始数据转换为另一种表示形式的设备或算法。它通过将输入数据映射到一个较低维度的特征空间来捕捉数据的重要特征。编码器常用于数据压缩、特征提取和降维等任务。

解码器是编码器的逆过程。它将经过编码的数据转换回原始表示形式。解码器根据编码器生成的特征向量或码字来重构原始数据。解码器常用于数据恢复、文本生成、图像重建和语音合成等任务。

编码器和解码器通常结合使用，构成了自动编码器（Autoencoder）模型。自动编码器由一个编码器和一个解码器组成，通过训练数据的重构误差来学习数据的压缩表示和重建能力。自动编码器常用于无监督学习、特征学习和生成模型等任务。



#### 硬编解码

通过硬件实现编解码，减轻CPU计算的负担，如GPU等



#### 软编解码

如 H264、H265、MPEG-4等编解码算法，更消耗CPU



### **多音轨和多声道的区别**

多音轨和多声道是两个完全不同的概念：

* 多音轨指的是在放置音乐元素的位置，可以笼统对应混音设备上的具体轨道。主要是用于音乐制作中。
* 多声道是指录制采样或者播放时的音源数量，用来营造声音位置效果，声道越多声音的立体感和现场感越强。人们的听觉是通过两只耳朵对声源的反馈，除了对音色，音高的感知以外，还可以通过感知声音的传播速度差异等来确定音源的位置，多声道就是通过不同声道的细微差异而让声音给人们多一些空间感。



### SFU架构

SFU（Selective Forwarding Unit）架构是一种常用于实时通信（例如视频会议、直播等）的数据传输架构。在SFU架构中，每个参与通信的终端设备（如用户的电脑、手机等）都与服务器建立连接，并将实时传输的数据（如音频、视频等）发送到服务器。服务器根据数据的特性和终端设备的需求，选择性地将特定的数据转发给其他参与者，而不是将所有数据复制发送给所有终端设备。这样可以减少网络带宽的使用，提高数据传输的效率和质量。

在SFU架构中，服务器起到了中心节点的角色，负责中转和转发数据。它可以对数据进行处理、编解码、混音等操作，以适应不同终端设备的需求和网络环境的限制。终端设备之间则通过服务器进行数据传输，从而实现实时通信。

与传统的点对点通信相比，SFU架构具有一些优势。首先，SFU可以有效地减少网络带宽的消耗。因为服务器只选择性地转发特定的数据，而不是将所有数据复制发送给所有终端设备，可以节省带宽资源。其次，SFU可以提供更好的数据传输质量。服务器可以对数据进行处理、优化和转码等操作，以适应不同的终端设备和网络环境，可以提供更好的音视频质量和更稳定的通信效果。同时，SFU还可以支持更大规模的通信，因为服务器可以同时处理和转发多个终端设备的数据。

**总之，SFU架构通过服务器中转和选择性转发数据，可以提高实时通信的效率、质量和可扩展性，广泛应用于视频会议、直播等实时通信场景中。**





### SRS流媒体服务器

[https://ossrs.net/lts/zh-cn/docs/v5/doc/introduction](https://ossrs.net/lts/zh-cn/docs/v5/doc/introduction)

**SRS服务器的作用是实现流媒体的推送、转码和播放功能。**

SRS（Simple-RTMP-Server）是一个基于RTMP协议的流媒体服务器，它可以接收RTMP协议的推流，并将其转码为多种格式，包括HTTP-FLV、HLS等，以便在不同的终端设备上进行播放。SRS服务器可以实现实时的音视频数据传输，支持高并发、低延迟的流媒体传输服务。

SRS服务器的主要功能如下：

1. 接收RTMP推流：SRS服务器能够接收RTMP协议的音视频推流，将其转发到后续流程进行处理。
2. 转码和转封装：SRS服务器支持对RTMP流进行实时转码和转封装，将其转换为多种格式，方便在不同的终端上播放。
3. 多协议支持：SRS服务器可以将RTMP流转换为多种协议，如HTTP-FLV、HLS等，以便在不同的设备和平台上进行播放。
4. 支持HTTP回源：SRS服务器可以从HTTP服务器回源获取流媒体文件，以实现稳定的流媒体传输服务。
5. 支持弹幕：SRS服务器支持实时弹幕推送功能，可以与弹幕服务进行集成，实现实时的弹幕效果。
6. 支持多路流媒体处理：SRS服务器可以同时处理多路流媒体数据，支持高并发的流媒体传输服务。

**总之，SRS服务器是一个功能强大的流媒体服务器，能够实现实时的推流、转码和播放功能，为用户提供高质量的流媒体传输服务。**



### GOP

GOP是视频编解码技术中的一种格式，全称为“Group of Pictures”，即一组图片。在视频压缩编码中，视频帧按照一定的顺序分为多个GOP，每个GOP由多个图像组成。GOP的设置可以影响视频的压缩率和质量。常见的视频编码标准如H.264、MPEG-2等也采用了GOP。



### I帧、P帧、B帧、IDR帧

* I帧:帧内编码帧
* P帧:前向预测编码帧

* B帧:双向预测内插编码帧。

* IDR（Instantaneous Decoding Refresh）--即时解码刷新。

---

P帧是I帧后面相隔1~2帧的编码帧;

P帧采用运动补偿的方法传送它与前面的I或P帧的差值及运动矢量(预测误差);

I和IDR帧都是使用帧内预测的。它们都是同一个东西而已,在编码和解码中为了方便，要首个I帧和其他I帧区别开，所以才把第一个首个I帧叫IDR，这样就方便控制编码和解码流程。

一般平均来说，I的压缩率是7（跟JPG差不多），P是20，B可以达到50，可见使用B帧能节省大量空间

**一个 I 帧可以不依赖其他帧就解码出一幅完整的图像，而 P 帧、B 帧不行。P 帧需要依赖视频流中排在它前面的帧才能解码出图像。B 帧则需要依赖视频流中排在它前面或后面的帧才能解码出图像。**





### DTS 和 PTS

在视频和音频处理领域，DTS（Decoding Time Stamp）和 PTS（Presentation Time Stamp）是两个重要的时间戳概念，它们用于同步和控制媒体流的解码和显示。以下是它们的详细区别和作用：

DTS（Decoding Time Stamp）

DTS（解码时间戳）指示一个视频或音频帧应该被解码的时间。

作用

- **解码顺序**：DTS 用于确定解码器何时应该解码某个帧。特别是在 B 帧（双向预测帧）存在的情况下，帧的解码顺序和显示顺序可能不同。
- **缓冲管理**：DTS 帮助解码器管理缓冲区，确保帧在正确的时间被解码。

示例

假设视频流中有三个帧：I 帧、B 帧和 P 帧。解码顺序可能是 I 帧 -> P 帧 -> B 帧，但显示顺序是 I 帧 -> B 帧 -> P 帧。在这种情况下，DTS 用于告诉解码器按 I -> P -> B 的顺序解码。

PTS（Presentation Time Stamp）

PTS（显示时间戳）指示一个视频或音频帧应该被呈现（显示或播放）的时间。

作用

- **播放顺序**：PTS 用于确定帧的播放顺序，确保视频和音频按正确的时间顺序播放。
- **音视频同步**：PTS 是音频和视频同步的关键，用于确保音频和视频在播放时保持同步。

示例

继续上面的例子，PTS 用于告诉播放器按 I -> B -> P 的顺序显示帧，以确保正确的播放顺序。

| 特性         | DTS（Decoding Time Stamp）    | PTS（Presentation Time Stamp） |
| ------------ | ----------------------------- | ------------------------------ |
| **作用**     | 确定帧的解码时间              | 确定帧的显示时间               |
| **使用场景** | 帧的解码顺序和缓冲管理        | 帧的播放顺序和音视频同步       |
| **适用对象** | 解码器                        | 播放器                         |
| **典型应用** | 处理 B 帧等需要重新排序的情况 | 确保音视频同步播放             |





### 弱网优化

* 播放器Buffer
* 丢帧策略 (优先丢P帧，其次I帧，最后音频)
* 自适应码率算法
* 双向链路优化





### 音视频同步

音视频同步（A/V Sync）是指在播放多媒体内容时，确保音频和视频能够正确地协调和同步播放。这是一个复杂的过程，涉及多个阶段，包括捕获、编码、传输、解码和播放。以下是一些关键技术和方法，用于实现音视频同步：

时间戳（Timestamps）

PTS（Presentation Time Stamp）

- **作用**：PTS 用于标记每个音频和视频帧的显示时间。
- **同步机制**：播放器使用 PTS 来确保音频和视频帧在正确的时间被播放。通过比较当前播放时间和帧的 PTS，播放器可以决定是否应该播放、延迟或跳过帧。

缓冲区（Buffers）

- **音频缓冲区**：用于存储解码后的音频数据，以便平滑播放。
- **视频缓冲区**：用于存储解码后的视频帧，以便平滑播放。
- **同步机制**：播放器会根据音频和视频的 PTS 调整缓冲区的内容，确保音视频同步播放。例如，如果视频帧的 PTS 比当前时间早，而音频帧的 PTS 比当前时间晚，播放器可能会延迟视频播放以等待音频。

时钟（Clocks）

- **系统时钟**：通常由操作系统提供，用于跟踪当前的播放时间。
- **音频时钟**：由音频硬件提供，用于精确控制音频播放时间。
- **同步机制**：播放器使用系统时钟和音频时钟来协调音频和视频的播放时间。通常，音频时钟被认为是更精确的时钟源，视频播放时间会根据音频时钟进行调整。

AV 同步算法

- **音频主导**：以音频播放时间为主，调整视频播放时间以匹配音频。这种方法利用音频时钟的高精度，减少音视频不同步的可能性。
- **视频主导**：以视频播放时间为主，调整音频播放时间以匹配视频。这种方法适用于视频播放要求更高的场景。
- **双向调整**：同时调整音频和视频的播放时间，以达到最佳的同步效果。

延迟和补偿

- **音频延迟**：有时音频解码和播放可能会有延迟，播放器需要补偿这种延迟，以保持同步。
- **视频延迟**：类似地，视频解码和播放也可能会有延迟，需要进行补偿。
- **同步机制**：通过调整缓冲区的大小和内容，播放器可以补偿解码和播放过程中的延迟，确保音视频同步。

网络传输中的同步

在流媒体传输中，音视频同步更加复杂，因为网络延迟和抖动可能影响同步性。常用的方法包括：

- **RTP（Real-time Transport Protocol）**：用于实时传输音视频数据，支持时间戳和序列号，帮助接收端进行同步。
- **RTCP（RTP Control Protocol）**：用于监控和控制 RTP 流，提供反馈信息，帮助维持同步。
- **缓冲策略**：通过增加缓冲区大小，平滑网络抖动，确保音视频同步。

实际应用中的同步

媒体播放器

- **VLC、MPC-HC 等播放器**：这些播放器使用上述同步机制，通过时间戳、缓冲区和时钟协调音视频播放。

流媒体服务

- **YouTube、Netflix 等**：这些服务使用 RTP/RTCP 等协议和自定义同步算法，确保在不同网络条件下音视频的同步播放。

总结

音视频同步是通过多种技术和方法实现的，包括使用时间戳（PTS）、缓冲区、系统时钟和音频时钟、同步算法、延迟补偿以及网络传输协议。通过这些手段，播放器和流媒体服务可以确保音频和视频在播放过程中保持同步，提供良好的用户体验。



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
