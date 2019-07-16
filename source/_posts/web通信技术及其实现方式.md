---
title: web通信技术及其实现方式
date: 2018-08-06 01:50:49
tags: javascript
---

&emsp;&emsp;传统的Web数据更新，必须要刷新网页才能显示更新的内容。这是浏览器采用的是B/S架构，而B/S架构是基于HTTP协议的。HTTP协议的工作模式就是客户端向服务器发送一个请求，服务器收到请求后返回响应。所以这种工作模式是基于请求显示数据的。这样的工作方式有其自身的好处，但是也会导致很多问题。在Web应用越来越火的今天，经常会遇到需要服务器主动发送数据到客户端的需求，比如事件推送、Web聊天等。这些需求使用传统的Web数据更新工作模式是无法实现的，因此就需要一项新的技术：Web实时通信技术。
<!--more-->

# 一、短轮询

## 基本原理

客户端发送请求，服务端立刻返回，客户端处理完毕后继续发送下一个请求。

![](/images/7.jpg)

## 实现方式

客户端

```
// 轮询方法
let timer = setTimeout(() => {
    getMessage();
},1000);
// 获取消息方法
function getMessage() {
    let xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function () {
        if(xhr.readyState === 4 && (xhr.status === 200 || xhr.status === 304) ){
            document.getElementById('number').innerText = xhr.responseText;
            clearTimeout(timer);
            timer = setTimeout(() => {
                getMessage();
            },1000);
        }
    }
    xhr.open('get','/message');
    xhr.send('');
}
```

服务端

```
const express = require('express');
let app = new express();
let count = 0;

setInterval(() => {
    count++;
},5000); // 每个五秒，改变一下返回值

app.listen(3000,() => {
    console.log('listen port 3000');
});
app.get('/',function (req, res) {
    res.sendfile('./index.html');
});
app.get('/message',function (req, res) {
    res.end(count.toString());
});
```

&emsp;&emsp;客户端向服务端发起一个请求，获取并处理好信息之后，间隔一秒再次发起该请求，如此循环，就可以达到轮询的目的。这里注意一下，我们在用定时器的时候，并没有使用setInterval，而使用的setTimeout；原因很简单，如果使用setInterval，服务器如果还没有响应当前请求的话，可能会出现重复发起该请求的情况。

&emsp;&emsp;缺点：在使用短轮询的时候，我们每间隔一秒就向服务器请求新数据，但是服务器的数据是每个5秒（demo是5秒，实际情况下什么可能都有，就是不一定是间隔一秒）才会产生一个新数据，这样会造成很多不必要的网络传输和资源浪费。

# 二、长轮询

## 基本原理

客户端发送请求，服务端会阻塞请求不会立刻返回，直到有数据或超时才返回给客户端，客户端处理完毕后继续发送下一个请求。长轮询就可以弥补短轮询短那个致命缺点。

![](/images/8.jpg)

## 实现方式

客服端代码

```
// 轮询方法
function getMessage() {
    let xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function () {
        if(xhr.readyState === 4 && (xhr.status === 200 || xhr.status === 304) ){
            document.getElementById('number').innerText = xhr.responseText;
            getMessage();
        }
    }
    xhr.open('get','/message');
    xhr.send('');
}
getMessage();
```

服务端代码

```
const express = require('express');
let app = new express();
let count = 0;
let lastCount = -1;

setInterval(() => {
    count++;
},5000); // 每个五秒，改变一下返回值

app.listen(3000,() => {
    console.log('listen port 3000');
});
app.get('/',function (req, res) {
    res.sendfile('./view/test1.html');
});
app.get('/message',function (req, res) {
    const timer = setInterval(()=>{  
        if(lastCount !== count){  // 数据发生改变的时候，才响应该请求
            clearInterval(timer);
            lastCount = count;
            res.end(count.toString());
        }
    },1000);
});
```

&emsp;&emsp;缺点：这种方式可以避免不必要的网络传输，但是hold住的请求，对服务器也是一种资源对浪费。

# 三、http-stream

## 基本原理

&emsp;&emsp;客户端在一次请求中保持和服务端连接不断开，然后服务端源源不断传送数据给客户端，就好比数据流一样，并不是一次性将数据全部发给客户端。它与轮询方式的区别在于整个通信过程客户端只发送一次请求，然后服务端保持与客户端的长连接，并利用这个连接在回送数据给客户端。

![](/images/9.jpg)

## 实现方式一

&emsp;&emsp;xhr-stream：这种方式的思想是构造一个XHR对象，通过监听它的onreadystatechange事件，当它的readyState为3的时候，获取它的responseText然后进行处理，readyState为3表示数据传送中，整个通信过程还没有结束，所以它还在不断获取服务端发送过来的数据，直到readyState为4的时候才表示数据发送完毕，一次通信过程结束。在这个过程中，服务端传给客户端的数据是分多次以stream的形式发送给客户端，客户端也是通过stream形式来获取的，所以称作http-streaming数据流方式。

&emsp;&emsp;示列：

客户端代码：

```
function getMsg() {
    var xhr = new XMLHttpRequest();
    xhr.open('get', '/message');
    xhr.onreadystatechange = function () {
        if((xhr.status === 200 || xhr.status === 304) && xhr.readyState === 4) {
            document.body.innerHTML = xhr.responseText;
        }
        if(xhr.readyState === 3) {
            document.body.innerHTML = xhr.responseText;
        }
    }
    xhr.send('');
}
setTimeout(getMsg(), 1000);
```

服务端代码：
```
const express = require('express');
const app = new express();
app.use(express.static('./'));
let num = -1;
let currNum = null;
setInterval(() => {
    num++;
    console.log(num);
}, 3000)
app.get('/', (req, res) => {
    res.sendFile('./index.html');
    res.end();
});
app.get('/message', (req, res) => {
    res.header('content-type', 'multipart/octet-stream');
    setInterval(() => {
        if(num !== currNum) {
            res.write(num.toString());
            currNum = num;
        }
        if(num > 9) {
            res.end();
        }
    },1000);
});

app.listen(8081, ()=>{
   console.log('app running at http://localhost:8081');
});
```

## 实现方式二：

&emsp;&emsp;iframe-streaming：由于低版本的IE不允许在XHR的readyState为3的时候获取其responseText属性，为了达到在IE上使用这个技术，又出现了基于iframe的数据流通信方式。具体来讲，就是在浏览器中动态载入一个iframe,让它的src属性指向请求的服务器的URL，实际上就是向服务器发送了一个http请求，然后在浏览器端创建一个处理数据的函数，在服务端通过iframe与浏览器的长连接定时输出数据给客户端，但是这个返回的数据并不是一般的数据，而是一个类似于脚本执行的方式，浏览器接收到这个数据就会将它解析成js代码并找到页面上指定的函数去执行，实际上是服务端间接使用自己的数据间接调用了客户端的代码，达到实时更新客户端的目的。

客户端：

```
<div id="data">div>
<script>
    function process(text) {
        document.getElementById('data').innerText = text;
    }
    function getMsg() {
        let ifr = document.createElement('iframe');
        ifr.src = '//localhost:3000/message';
        ifr.style.display = 'none';
        document.body.appendChild(ifr);
    }
    setTimeout(getMsg(), 1000);
script>
```

服务端：

```
const express = require('express');
let app = new express();
app.use(express.static('./'));
let num = 0;
let currNum = null;
const timer1 = setInterval(() => {
    num++;
    console.log(num);
}, 3000)
app.listen(3000,() => {
    console.log('listen port 3000');
});
app.get('/',function (req, res) {
    res.sendfile('./index.html');
});
app.get('/message',function (req, res) {
    num = 0;
    const timer = setInterval(() => {

        if(num !== currNum) {
            res.write(
                ''
            );
            currNum = num;
        }
        if(num > 9) {
            clearInterval(timer1);
            clearInterval(timer);
            res.end();
        }
    },1000);
});
```

# 四、SSE技术

&emsp;&emsp;Server-sent Events 规范是 HTML 5 规范的一个组成部分。该规范比较简单，主要由两个部分组成：第一个部分是服务器端与浏览器端之间的通讯协议，第二部分则是在浏览器端可供 JavaScript 使用的 EventSource 对象。

&emsp;&emsp;它的原理是在客户端构造一个eventSource对象，该对象具有readySate属性，分别表示如下：

&emsp;&emsp;0：正在连接到服务器；

&emsp;&emsp;1：打开了连接；

&emsp;&emsp;2：关闭了连接。

&emsp;&emsp;同时eventSource对象会保持与服务器的长连接，断开后会自动重连，如果要强制连接可以调用它的close方法。可以它的监听onmessage事件，服务端遵循SSE数据传输的格式给客户端，客户端在onmessage事件触发时就能够接收到数据，从而进行某种处理。

![](/images/10.jpg)

客户端：

```
let sse = new EventSource('//localhost:3000/message');
sse.onopen = function () {
    console.log('connect');
}
sse.onerror = function (err) {
    console.log(err);
}
sse.addEventListener('message', function(e) {
    document.body.innerHTML = e.data;
}, false);
```

服务端：

```
const express = require('express');
let app = new express();
app.use(express.static('./'));
let num = 0;
let currNum = null;
const timer1 = setInterval(() => {
    num++;
    console.log(num);
}, 3000)
app.listen(3000,() => {
    console.log('listen port 3000');
});
app.get('/',function (req, res) {
    res.sendfile('./index.html');
});
app.get('/message',function (req, res) {
    res.header('content-type', 'text/event-stream');
    const timer = setInterval(() => {
        if(num !== currNum) {
            res.write('data: ' + num.toString() + '\n\n');
            currNum = num;
        }
        if(num > 9) {
            clearInterval(timer1);
            clearInterval(timer);
            res.end();
        }
    },1000);
});
```

![](/images/11.jpg)

# 五、Websocket通信

&emsp;&emsp;websocke是一种应用层协议，客户端和服务端建立连接，随后两边可以随意发送数据。websocket协议是为了提供web应用程序和服务端全双工通信而专门制定的一种应用层协议，通常它表示为：ws://echo.websocket.org/?encoding=text HTTP/1.1，可以看到除了前面的协议名和http不同之外，它的表示地址就是传统的url地址。后面我会详细研究一下Websocket的相关细节。