---
title: PWA技术之添加到主屏
date: 2018-06-09 17:15:28
tags: [PWA, javascript]
---

# 一. 添加到主屏幕

&emsp;&emsp;允许将站点添加至主屏幕，是 PWA 提供的一项重要功能。虽然目前部分浏览器已经支持向主屏幕添加网页快捷方式以方便用户快速打开站点，但是 PWA 添加到主屏幕的不仅仅是一个网页快捷方式，它将提供更多的功能，让 PWA 具有更加原生的体验。

<!--more-->

# 二. 应用清单manifest.json文件

&emsp;&emsp;如果不给应用添加manifest.json文件，浏览器也会有一套默认的机制来为我们将应用添加到主屏。使用manifest.json文件可以定制化添加到主屏的一些属性。它提供了应用相关的有用信息 ，比如应用的名称、作者、图标和应用描述，但更特别的是，manifest.json允许开发者自定义启动画面、模板颜色，甚至是打开的 URL 。

manifest.json引入方式  ：

```
<link rel="manifest" href="manifest.json">
```

manifest.json文件内容：

```
{
    "name": "appName web app",
    "short_name": "appName",
    "start_url": "/index.html",
    "display": "standalone",
    "theme_color": "#FFDF00",
    "background_color": "#FFDF00",
    "icons": [{
        "src": "homescreen.png",
        "sizes": "192x192",
        "type": "image/png"
    },
    {
        "src": "homescreen-144.png",
        "sizes": "144x144",
        "type": "image/png"
    }]
}
```

参数描述：

    name: 用作当用户被提示安装应用时出现的文本。

    short_name: 用作当应用安装后出现在用户主屏幕上的文本。

    start_url： 用户从设备的主屏幕开启 Web 应用时所出现的第一个页面,如果是相对路径，则基础路径是manifest.json的路径。

    display：决定应用的展现方式，有四种值可以选；

        a.Fullscreen - 打开 Web 应用并占用整个可用的显示区域。

        b.Standalone - 打开 Web 应用以看起来像一个独立的原生应用。此模式下，用户代理将排除诸如 URL 栏等标准浏览器 UI 元素，但可以包括诸如状态栏和系统返回按钮的其他系统 UI 元素。

        c.Minimal-ui - 此模式类似于 fullscreen，但为终端用户提供了可访问的最小 UI 元素集合，例如，后退按钮、前进按钮、重载按钮以及查看网页地址的一些方式。

        d.Browser - 默认值，使用操作系统内置的标准浏览器来打开 Web 应用。

    theme_color： 可以对浏览器的地址栏进行着色，以符合网站的主色调。

    background_color： 和name属性一起用来显示启动页面。

    icons： Web 应用被添加到设备主屏幕时所显示的图标。

&emsp;&emsp;印度最大电子商务公司Flipkart（应用名：Flipkart Lite） 想要他们的移动用户享受不逊色于原生应用的用户体验。通过使用添加到主屏幕功能，Flipkart Lite 所有访问的60％均来自主屏幕图标启动。这项功能还意味着他们的客户转化率提升了70%。仅这两项活动就使参与人数比以前高了40%!

&emsp;&emsp;添加到主屏幕功能最棒的是几乎不用写任何代码便可实现功能，浏览器已经为你做好了这一切。但还需要知道在显示“添加到主屏幕”提示之前，需要满足几个条件:

+ 需要 manifest.json 文件
+ 清单文件需要启动 URL
+ 需要 144x144 的 PNG 图标
+ 网站正在使用通过 HTTPS 运行的 Service Worker
+ 用户需要至少浏览网站两次，并且两次访问间隔在五分钟之上

&emsp;&emsp;上述第五点或许是最为重要的，用户至少访问过网站两次并且两次之间的间隔在五分钟之上，提示才会出现，这样做的原因是为了确保这项功能不会让人反感。想像一下，如果你访问过的每个网站都使用了这项技术，并且每个页面都会提示“添加到主屏幕”？它很快就会变得令人反感，而 浏览器厂商也会发现自己在作茧自缚！

# 三. 添加到主屏高级用法

&emsp;&emsp;添加主屏的功能是由浏览器内置，这也意味着大部分功能由浏览器控制着，有时你可能会想要覆盖默认设置并提供自己的逻辑，例如对添加到主屏幕操作栏进行取消操作或推迟显示；我们可以通过代码来实现。

a.取消提示:如果出于某种原因，你不希望显示添加到主屏幕操作栏，其实是可以完全取消该操作栏的:

```
window.addEventListener('beforeinstallprompt', function(e) {
    e.preventDefault();
    return false;
});
```

b.判断使用情况:通过监听 beforeinstallprompt 事件，可以判断出用户是否决定添加你的 Web 应用到主屏幕或者直接关掉提示。

```
window.addEventListener('beforeinstallprompt', function (e) {
    // beforeinstallprompt event fired

    e.userChoice.then(function (choiceResult) {
        if (choiceResult.outcome === 'dismissed') {
            console.log('用户取消安装应用');
        }
        else {
            console.log('用户安装了应用');
        }
    });
});
```

测试中发现，ios上safari浏览器，目前还不支持manifest，但是可以通过别到方式去定义，添加桌面到各种属性。比如定义图标：

```
<link rel="apple-touch-icon-precomposed" href="homescreen.png">
```