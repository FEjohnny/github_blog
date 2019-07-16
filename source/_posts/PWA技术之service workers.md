---
title: PWA技术之service workers
date: 2018-06-09 15:41:04
tags: [service workers, PWA, javascript]
---

# 一、基本概念      

&emsp;&emsp;Service Workers是PWA的关键。我觉得它最主要的一个功能就是：使你可以不通过网络连接来访问资源。它是一个运行于后台的js脚本，只需短短几行代码，即可完成拦截网络请求，处理推送消息和并行执行任务等任务。如果当前客户端不支持service workers，它们只是简单的回退，应用程序就作为一个普通的应用程序来运行，这也就是Progressive（渐进增强）的由来；也就是指，你完全可以先创建一个在任何地方运行的应用，然后为支持高级功能的设备增强体验。

&emsp;&emsp;如何理解service workers？来看Google 的 Jeff Posnick 是如何描述他们的：“将你的网络请求想象成飞机起飞。Service Worker 是路由请求的空中交通管制员。它可以通过网络加载，或甚至通过缓存加载。（甚至可以重定向加载）”。也就是说，Service Workers 能够拦截进出的 HTTP 请求，从而完全控制你的网站。

<!--more-->

![service workders](/images/18.png)

### service workers用javascript编写，但它与普通浏览器js脚本文件有所不同：

+ 1.service workers运行在自己（worker）的全局上下文中，应用的js运行在不同的线程上，所以它不会被阻塞，而且它被设计为完全异步，因此你无法使用诸如同步 XHR 和 localStorage 之类的功能。

+ 2.不绑定到具体网页。

+ 3.无法修改页面元素，无法访问dom。

+ 4.只能使用https

### service workers的生命周期

![service workders](/images/19.png)

# 二、安装service workers

&emsp;&emsp;如何使用service workers？示例：

index.html

```
<html>
<head>
    <meta charset="UTF-8">
    <title>Hello Caching World!title>
    <link href="https://fonts.googleapis.com/css?family=Lato" rel="stylesheet">
    <style>
        #body {
            color: red;
            font-family: 'Lato', sans-serif;
        }
    style>
head>

<body>
<h1>Hello Caching World!h1>

<script async src="/js/script.js">script>
<script>
    if ('serviceWorker' in navigator) {
        navigator.serviceWorker.register('service-worker.js?v=2').then(function (registration) {
            console.log('ServiceWorker registration successful with scope: ', registration.scope);
        }).catch(function (err) {
            console.log('ServiceWorker registration failed: ', err);
        });
    }
<script>
<body>
<html>
```

service-worker.js

```
var cacheName = 'helloWorld'; //缓存的名称
self.addEventListener('install', event => {
    event.waitUntil(
        caches.open(cacheName)
            .then(cache => cache.addAll([
            '/js/script.js',
            '/images/worders-life.png'
        ]))
    );
});

//监听网络请求
self.addEventListener('fetch', function (event) {
    event.respondWith(
        caches.match(event.request,{ //优先匹配缓存
            ignoreSearch: true,  //查询url时，忽略附带的查询参数
            ignoreMethod:false,  //是否忽略请求方法，如果忽略则post请求可能会匹配到get请求
            ignoreVary: false, //ignoreVary 选项会忽略已缓存响应中的 vary 首部
        })
        .then(function (response) {
            if (response) {
                return response;
            }
         //克隆请求，因为请求是一个流，只能消耗一次，而我们已经通过缓存消耗了一次
         var requestToCache = event.request.clone();
            return fetch(requestToCache).then(//尝试按预期一样发起原始的 HTTP 请求
                 function (response) {
                    if (!response || response.status !== 200) {
                            return response;
                  }
                 //你可能会疑惑我们为什么需要再次克隆响应，请记住响应是一个流，
                 //它只能消耗一次。因为我们想要浏览器和缓存都能够消耗响应，
                // 所以我们需要克隆它，这样就有了两个流。
                var responseToCache = response.clone();
                    caches.open(cacheName).then(function (cache){
                        cache.put(requestToCache, responseToCache);
                   });
                return response;
           });
        })
    );
});
```

```
self.addEventListener('install', function(event) {
    event.waitUntil(self.skipWaiting());
});
```

```
self.addEventListener('activate', function(event) {
    event.waitUntil(self.clients.claim());
});
```

&emsp;&emsp;kipWaiting() 函数强制等待中的 Service Worker 被激活 。self.skipWaiting() 函数还可以与 self.clients.claim() 一起使用，以确保底层 Service Worker 的更新立即生效。

# 三、service workers版本更新

&emsp;&emsp;service-worker.js文件控制着整站的页面资源和请求的缓存，如果缓存策略更新，也就是service-worker.js文件本身需要更新的时候怎么办。首先，我们可以通过为service-worker.js文件添加版本号控制，来强制客户端更新service-worker.js文件。如果 service-worker.js内容有更新，当访问网站页面时浏览器获取了新的文件，逐字节比对 service-worker.js 文件发现不同时它会认为有更新，于是会安装新的文件并触发 install 事件。

&emsp;&emsp;但是此时已经处于激活状态的旧的 Service Worker 还在运行，新的 Service Worker 完成安装后会进入 waiting 状态。直到所有已打开的页面都关闭，旧的 Service Worker 自动停止，新的 Service Worker 才会在接下来重新打开的页面里生效。为了解决这个问题，在版本更新的时候，所以页面都能及时自动更新，可以通过下面的方法来实现：

```
// 安装阶段跳过等待，直接进入 active
self.addEventListener('install', function (event) {
    event.waitUntil(self.skipWaiting());
});
self.addEventListener('activate', function (event) {
    event.waitUntil(
        Promise.all([
            caches.open(catchName).then(function (catche) {
                // 通过 cache 缓存对象的 addAll 方法添加 precache 缓存
         catche.addAll([
                    '/',
                    '/css/index.css',
                    '/js/index.js',
                    '/img/logo.png',
                    '/data/data.json',
                ])
            }),
            // 更新客户端
       self.clients.claim(),
            // 清理旧版本
       caches.keys().then(function (cacheList) {
                return Promise.all(
                    cacheList.map(function (oldcacheName) {
                        if (oldcacheName !== catchName) {
                            return caches.delete(oldcacheName);
                        }
                    })
                );
            })
        ])
    );
});
```

&emsp;&emsp;使用上面的方式的话，我们就不能在service workers安装阶段去缓存我们需要的资源，但是可以通过在fetch事件中去做资源的缓存。除了上面的更新方式之外，还可以通过registration.update()方法在页面上实现手动更新。

```
var version = '1.0.1';
navigator.serviceWorker.register('/service-worker.js').then(function (reg) {
    if (localStorage.getItem('sw_version') !== version) {
        reg.update().then(function () {
            localStorage.setItem('sw_version', version)
        });
    }
});
```

&emsp;&emsp;Service Worker 的特殊之处除了由浏览器触发更新之外，还应用了特殊的缓存策略： 如果该文件已 24 小时没有更新，当 Update 触发时会强制更新。这意味着最坏情况下 Service Worker 会每天更新一次 。