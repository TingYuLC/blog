---
title: Hybrid交互方式
categories: JavaScript
tags: [hybrid]
date: "2019-12-31"
---

# Hybrid交互方式

### 一.APP开发模式(<font color="red">这里的APP是Application的意思</font>).

| 模式 | Native App       | Web App          | Hybrid App                                | ReactNative、Weex、Ionic、Flutter |
| ---- | ---------------- | ---------------- | ----------------------------------------- | --------------------------------- |
| 优点 | 体验好           | 体验差           | 体验中                                    | 体验好，开发成本低                |
| 缺点 | 开发和发布成本高 | 开发和发布成本低 | 开发和发布成本中,初次加载或更新有白屏效果 | 对开发人员技术要求高              |

其中Hybrid App又分为两种：

**1.以H5为主，Native为辅。**例如Cordova,Native的作用单纯就是提供一个APP外壳和暴露App独有功能给H5。

**2.以Native为主，H5为辅。**

### 二.Hybrid的交互方式.

#### (1)url scheme.

<font color="red">1.H5将参数通过url发送给Native,并将回调函数保留在Window对象上.</font>

<font color="red">2.Native通过拦截url获取H5的参数，Native进行相应处理后将调用Window对象上的回调函数.</font>

优点：不存在兼容性问题，简单易用，基本功能需求都可以满足.

缺点：代码冗余，不支持return，参数过多时耗时长，ifame.src有长度隐患.

#### (2)Native暴露对象，H5调用.

<font color="red">Native暴露对象给H5，H5直接调用.</font>

优点：支持return.

缺点：不支持回调，在异步函数无法return的情况下无法知道结果，存在兼容性和安全性问题.

#### (3)JSBridge.

<font color="red">1.Native将对象暴露给H5，H5直接调用.</font>

<font color="red">2.H5将对象绑定在Window上，Native直接调用.</font>

优点：不需要通过url scheme去发送参数，不存在兼容性和安全问题.

缺点：不支持return，前期搭建复杂，排除问题相对麻烦.

### 三.例子:Jockey.js的H5实现(url scheme方式).

[Jockey.js](https://github.com/tcoulter/jockeyjs/blob/master/JockeyJS/js/jockey.js)

```
//
//  JockeyJS
//
//  Copyright (c) 2013, Tim Coulter
//
//  Permission is hereby granted, free of charge, to any person obtaining
//  a copy of this software and associated documentation files (the
//  "Software"), to deal in the Software without restriction, including
//  without limitation the rights to use, copy, modify, merge, publish,
//  distribute, sublicense, and/or sell copies of the Software, and to
//  permit persons to whom the Software is furnished to do so, subject to
//  the following conditions:
//
//  The above copyright notice and this permission notice shall be
//  included in all copies or substantial portions of the Software.
//
//  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
//  EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
//  MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
//  NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
//  LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
//  OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
//  WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
/* eslint-disable */
;(function () {
    if (window.Jockey) return
    // Non-accessible variable to send to the app, to ensure events only
    // come from the desired host.
    var host = window.location.host;

    var Dispatcher = {
        callbacks: {},

        send: function(envelope, complete) {
            var dispatcher = this;

            this.callbacks[envelope.id] = function() {
                complete.apply(this, arguments);

                delete dispatcher.callbacks[envelope.id];
            };

            this.dispatchMessage("event", envelope);
        },

        sendCallback: function(messageId) {
            var envelope = Jockey.createEnvelope(messageId);

            this.dispatchMessage("callback", envelope);
        },

        triggerCallback: function(id) {
            var dispatcher = this;

            var args = Array.prototype.slice.call(arguments);
            args.shift();


            // Alerts within JS callbacks will sometimes freeze the iOS app.
            // Let's wrap the callback in a timeout to prevent this.
            setTimeout(function() {
                var callback = dispatcher.callbacks[id];
                callback.apply(this, args);
            }, 0);
        },

        // `type` can either be "event" or "callback"
        dispatchMessage: function(type, envelope) {
            // We send the message by navigating the browser to a special URL.
            // The iOS library will catch the navigation, prevent the UIWebView
            // from continuing, and use the data in the URL to execute code
            // within the iOS app.

            var src = "jockey://" + type + "/" + envelope.id + "?" + encodeURIComponent(JSON.stringify(envelope));
            var iframe = document.createElement("iframe");
            iframe.setAttribute("src", src);
            document.documentElement.appendChild(iframe);
            iframe.parentNode.removeChild(iframe);
            iframe = null;
        }
    };

    var Jockey = {
        listeners: {},

        dispatcher: null,

        messageCount: 0,

        on: function(type, fn) {
            if (!this.listeners.hasOwnProperty(type) || !this.listeners[type] instanceof Array) {
                this.listeners[type] = [];
            }

            this.listeners[type].push(fn);
        },

        off: function(type) {
            if (!this.listeners.hasOwnProperty(type) || !this.listeners[type] instanceof Array) {
                this.listeners[type] = [];
            }

            this.listeners[type] = [];
        },

        send: function(type, payload, complete) {
            if (payload instanceof Function) {
                complete = payload;
                payload = null;
            }

            payload = payload || {};
            complete = complete || function() {};

            var envelope = this.createEnvelope(this.messageCount, type, payload);

            this.dispatcher.send(envelope, complete);

            this.messageCount += 1;
        },

        // Called by the native application when events are sent to JS from the app.
        // Will execute every function, FIFO order, that was attached to this event type.
        trigger: function(type, messageId, json) {
            var self = this;

            var listenerList = this.listeners[type] || [];

            var executedCount = 0;

            var complete = function() {
                executedCount += 1;

                if (executedCount >= listenerList.length) {
                    self.dispatcher.sendCallback(messageId);
                }
            };

            for (var index = 0; index < listenerList.length; index++) {
                var listener = listenerList[index];

                // If it's a "sync" listener, we'll call the complete() function
                // after it has finished. If it's async, we expect it to call complete().
                if (listener.length <= 1) {
                    listener(json);
                    complete();
                } else {
                    listener(json, complete);
                }
            }

        },

        // Called by the native application in response to an event sent to it.
        // This will trigger the callback passed to the send() function for
        // a given message.
        triggerCallback: function(id) {
            this.dispatcher.triggerCallback.apply(this.dispatcher, arguments);
        },

        createEnvelope: function(id, type, payload) {
            return {
                id: id,
                type: type,
                host: host,
                payload: payload
            };
        }
    };

    // i.e., on a Desktop browser.
    var nullDispatcher = {
        send: function(envelope, complete) { complete(); },
        triggerCallback: function() {},
        sendCallback: function() {}
    };

    // Dispatcher detection. Currently only supports iOS.
    // Looking for equivalent Android implementation.
    var i = 0,
        j = 0,
        iOS = false,
        isPC = false,
        pcDevice = ['Win', 'Mac'],
        iDevice = ['iPad', 'iPhone', 'iPod'];

    for (; i < iDevice.length; i++) {
        if (navigator.platform.indexOf(iDevice[i]) >= 0) {
            iOS = true;
            break;
        }
    }

    for (; j < pcDevice.length; j++) {
        if (navigator.platform.indexOf(pcDevice[j]) >= 0) {
            isPC = true;
            break;
        }
    }

    // Detect UIWebview. In Mobile Safari proper, jockey urls cause a popup to
    // be shown that says "Safari cannot open page because the URL is invalid."
    // From here: http://stackoverflow.com/questions/4460205/detect-ipad-iphone-webview-via-javascript

    var UIWebView = /(iPhone|iPod|iPad).*AppleWebKit(?!.*Safari)/i.test(navigator.userAgent);
    var isAndroid = navigator.userAgent.toLowerCase().indexOf("android") > -1;

    if ((iOS && UIWebView) || (isAndroid && !isPC)) {
        Jockey.dispatcher = Dispatcher;
    } else {
        Jockey.dispatcher = nullDispatcher;
    }

    window.Jockey = Jockey;
})();

export default Jockey;
```

