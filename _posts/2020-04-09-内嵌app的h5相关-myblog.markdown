---
layout:     post
title:      "内嵌app的h5相关"
date:       2020-04-09 17:20:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>
> 


# 正文



app和h5交互通常通过WebViewJavascriptBridge



[WebViewJavascriptBridge相关知识]( https://segmentfault.com/a/1190000008948594 )





### WebViewJavascriptBridge



[ https://github.com/marcuswestin/WebViewJavascriptBridge ]( https://github.com/marcuswestin/WebViewJavascriptBridge )



An iOS/OSX bridge for sending messages between Obj-C and JavaScript in WKWebViews, UIWebViews & WebViews.



 一个iOS / OSX桥，用于在WKWebViews，UIWebViews和WebViews中的Obj-C和JavaScript之间发送消息。 





-------







Copy and paste `setupWebViewJavascriptBridge` into your JS:



```js
function setupWebViewJavascriptBridge(callback) {
	if (window.WebViewJavascriptBridge) { return callback(WebViewJavascriptBridge); }
	if (window.WVJBCallbacks) { return window.WVJBCallbacks.push(callback); }
	window.WVJBCallbacks = [callback];
	var WVJBIframe = document.createElement('iframe');
	WVJBIframe.style.display = 'none';
	WVJBIframe.src = 'https://__bridge_loaded__';
	document.documentElement.appendChild(WVJBIframe);
	setTimeout(function() { document.documentElement.removeChild(WVJBIframe) }, 0)
}
```

  

 实现一个到`https://__bridge_loaded__`的跳转，从而达到初始化javascript环境的bridge的作用。 



Finally, call `setupWebViewJavascriptBridge` and then use the bridge to register handlers and call ObjC handlers:



```js
setupWebViewJavascriptBridge(function(bridge) {
	
	// xxx 就是app提供给h5的方法

	bridge.registerHandler('xxx', function(data, responseCallback) {
		console.log("JS Echo called with:", data)
		responseCallback(data)
	})

})
```



----





### WebViewJavascriptBridge_JS.js解析





上面我们讲到了注入javascript方法到webview中。具体的代码就是`WebViewJavascriptBridge_JS.js`这个文件中的方法。我们通过分析这个文件的代码可以知道javascript环境的bridge是如何初始化的。



```js
;(function() {
    //如果已经初始化了，则返回。
    if (window.WebViewJavascriptBridge) {
        return;
    }
    if (!window.onerror) {
        window.onerror = function(msg, url, line) {
            console.log("WebViewJavascriptBridge: ERROR:" + msg + "@" + url + ":" + line);
        }
    }
    //初始化一些属性。
    var messagingIframe;
    //用于存储消息列表
    var sendMessageQueue = [];
    //用于存储消息
    var messageHandlers = {};
    //通过下面两个协议组合来确定是否是特定的消息，然后拦击。
    var CUSTOM_PROTOCOL_SCHEME = 'https';
    var QUEUE_HAS_MESSAGE = '__wvjb_queue_message__';
    //oc调用js的回调
    var responseCallbacks = {};
    //消息对应的id
    var uniqueId = 1;
    //是否设置消息超时
    var dispatchMessagesWithTimeoutSafety = true;
    //web端注册一个消息方法
    function registerHandler(handlerName, handler) {
        messageHandlers[handlerName] = handler;
    }
    //web端调用一个OC注册的消息
    function callHandler(handlerName, data, responseCallback) {
        if (arguments.length == 2 && typeof data == 'function') {
            responseCallback = data;
            data = null;
        }
        _doSend({ handlerName: handlerName, data: data }, responseCallback);
    }
    function disableJavscriptAlertBoxSafetyTimeout() {
        dispatchMessagesWithTimeoutSafety = false;
    }
        //把消息转换成JSON字符串返回
    function _fetchQueue() {
        var messageQueueString = JSON.stringify(sendMessageQueue);
        sendMessageQueue = [];
        return messageQueueString;
    }
    //OC调用JS的入口方法
    function _handleMessageFromObjC(messageJSON) {
        _dispatchMessageFromObjC(messageJSON);
    }

    //初始化桥接对象，OC可以通过WebViewJavascriptBridge来调用JS里面的各种方法。
    window.WebViewJavascriptBridge = {
        registerHandler: registerHandler,
        callHandler: callHandler,
        disableJavscriptAlertBoxSafetyTimeout: disableJavscriptAlertBoxSafetyTimeout,
        _fetchQueue: _fetchQueue,
        _handleMessageFromObjC: _handleMessageFromObjC
    };


    //处理从OC返回的消息。
    function _dispatchMessageFromObjC(messageJSON) {
        if (dispatchMessagesWithTimeoutSafety) {
            setTimeout(_doDispatchMessageFromObjC);
        } else {
            _doDispatchMessageFromObjC();
        }

        function _doDispatchMessageFromObjC() {
            var message = JSON.parse(messageJSON);
            var messageHandler;
            var responseCallback;
            //回调
            if (message.responseId) {
                responseCallback = responseCallbacks[message.responseId];
                if (!responseCallback) {
                    return;
                }
                responseCallback(message.responseData);
                delete responseCallbacks[message.responseId];
            } else {//主动调用
                if (message.callbackId) {
                    var callbackResponseId = message.callbackId;
                    responseCallback = function(responseData) {
                        _doSend({ handlerName: message.handlerName, responseId: callbackResponseId, responseData: responseData });
                    };
                }
                //获取JS注册的函数
                var handler = messageHandlers[message.handlerName];
                if (!handler) {
                    console.log("WebViewJavascriptBridge: WARNING: no handler for message from ObjC:", message);
                } else {
                    //调用JS中的对应函数处理
                    handler(message.data, responseCallback);
                }
            }
        }
    }
    //把消息从JS发送到OC，执行具体的发送操作。
    function _doSend(message, responseCallback) {
        if (responseCallback) {
            var callbackId = 'cb_' + (uniqueId++) + '_' + new Date().getTime();
            //存储消息的回调ID
            responseCallbacks[callbackId] = responseCallback;
            //把消息对应的回调ID和消息一起发送，以供消息返回以后使用。
            message['callbackId'] = callbackId;
        }
        //把消息放入消息列表
        sendMessageQueue.push(message);
        //下面这句话会出发JS对OC的调用
        //让webview执行跳转操作，从而可以在
        //webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler 中拦截到JS发给OC的消息
        messagingIframe.src = CUSTOM_PROTOCOL_SCHEME + '://' + QUEUE_HAS_MESSAGE;
    }


    messagingIframe = document.createElement('iframe');
    messagingIframe.style.display = 'none';
    //messagingIframe.body.style.backgroundColor="#0000ff";
    messagingIframe.src = CUSTOM_PROTOCOL_SCHEME + '://' + QUEUE_HAS_MESSAGE;
    document.documentElement.appendChild(messagingIframe);


    //注册_disableJavascriptAlertBoxSafetyTimeout方法，让OC可以关闭回调超时，默认是开启的。
    registerHandler("_disableJavascriptAlertBoxSafetyTimeout", disableJavscriptAlertBoxSafetyTimeout);
    //执行_callWVJBCallbacks方法
    setTimeout(_callWVJBCallbacks, 0);

    //初始化WEB中注册的方法。这个方法会把WEB中的hander注册到bridge中。
    //下面的代码其实就是执行WEB中的callback函数。
    function _callWVJBCallbacks() {
        var callbacks = window.WVJBCallbacks;
        delete window.WVJBCallbacks;
        for (var i = 0; i < callbacks.length; i++) {
            callbacks[i](WebViewJavascriptBridge);
        }
    }
})();
```





- 首先我们发现会初始化一个WebViewJavascriptBridge对象。并且这个对象是赋值给window对象，这里window对象可以理解为webview。所以说我们后面在OC环境中如果要调用js方法，就可以通过`window.WebViewJavascriptBridge`在加上具体方法来调用。
- WebViewJavascriptBridge对象中有javascript环境注入的提供给OC调用的方法registerHandler，javascript调用OC环境方法的callHandler。
- _fetchQueue这个方法的作用就是把javascript环境的方法序列化成JSON字符串，然后传入OC环境再转换。
- _handleMessageFromObjC就是处理OC发给javascript环境的方法。





在这个文件中也初始化了一个iframe实现webview的url跳转功能，从而激发webview代理方法的调用。



```js
    messagingIframe = document.createElement('iframe');
    messagingIframe.style.display = 'none';
    //messagingIframe.body.style.backgroundColor="#0000ff";
    messagingIframe.src = CUSTOM_PROTOCOL_SCHEME + '://' + QUEUE_HAS_MESSAGE;
    document.documentElement.appendChild(messagingIframe);

```



上面的src就是`https://__wvjb_queue_message__/`。这个是javascript发送的OC的第一条消息，目的和上面OC环境的startupMessageQueue一样，就是在javascript环境初始化完成以后，把javascript要发送给OC的消息立即发送出去。

然后我们看文件的最后面有如下代码。这段代码的作用就是立即执行ExampleApp.html中的callback方法。callback中传入的bridge参数就是我们这里初始化的window.WebViewJavascriptBridge对象。





```js
    //执行_callWVJBCallbacks方法
    setTimeout(_callWVJBCallbacks, 0);

    //初始化WEB中注册的方法。这个方法会把WEB中的hander注册到bridge中。
    //下面的代码其实就是执行WEB中的callback函数。
    function _callWVJBCallbacks() {
        var callbacks = window.WVJBCallbacks;
        delete window.WVJBCallbacks;
        for (var i = 0; i < callbacks.length; i++) {
            callbacks[i](WebViewJavascriptBridge);
        }
    }
```





直到这里，OC环境和javascript环境的bridege都建立完毕。OC和javascript环境都有一个bridge对象，这个对象都保存着注册的每个方法和回调，并且维护着各自的消息队列、回调id、requestId等一系列信息。





### OC发消息给WEB





OC要调用javascript环境的方法，其实就是调用`ExampleApp.html`中的`bridge.registerHandler`注册的方法。









## 总结



其实现在想想，原理很简单。



- 分别在OC环境和javascript环境都保存一个bridge对象，里面维持着requestId,callbackId,以及每个id对应的具体实现。
- OC通过javascript环境的`window.WebViewJavascriptBridge`对象来找到具体的方法，然后执行。
- javascript通过改变iframe的src来出发webview的代理方法`webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler`从而实现把javascript消息发送给OC这个功能。



















