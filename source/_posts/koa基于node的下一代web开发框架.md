---
title: koa基于node的下一代web开发框架
date: 2018-07-29 21:05:28
tags: [PWA, javascript]
---

&emsp;&emsp;今天早上室友飞回家了，自己一个人在家待了一天，下午睡了个午觉醒来都晚上7点了，醒来都那一瞬间就感觉世界只剩自己一个人了一样，哈哈哈哈，看来我真是一个受不得寂寞的人啊。不知道独在异乡奋斗的你是否也会时常感到孤独寂寞呢？如果是的话，赶紧来和我一起学习koa吧 =.=，愿每一个行走在奋斗路上的你都能够被岁月善待。

<!--more-->

&emsp;&emsp;最近换了新的工作环境，接手的项目是基于koa+angularjs的web项目，所以自己也提前来看看koa的文档。这就是一篇入门教程吧。

# 1.Koa是什么

&emsp;&emsp;koa 是由 Express 原班人马打造的，致力于成为一个更小、更富有表现力、更健壮的 Web 框架。 使用 koa 编写 web 应用，通过组合不同的 generator，可以免除重复繁琐的回调函数嵌套， 并极大地提升错误处理的效率。koa 不在内核方法中绑定任何中间件， 它仅仅提供了一个轻量优雅的函数库，使得编写 Web 应用变得得心应手。

# 2.安装

&emsp;&emsp;Koa要求node到版本不得低于7.6.0，如果需要升级node到话，你可能会用到下面到命令：

```
sudo npm cache clean -f    清除node缓存
sudo npm install n -g    安装node版本管理工具
sudo n 8.11.3 / sudo n stable     使用node版本管理工具升级node到指定版本或者最新版
```

&emsp;&emsp;升级完成之后，执行初始化一个node项目

```
mkdir koa-test    新建一个项目目录
cd koa-test    进入该目录
npm init    初始化npm，会生成一个package.json文件
npm intall koa    安装koa
```

&emsp;&emsp;现在我们应该能够看到package.json中的dependencies项中已经添加了koa的最新版，node_modules目录也已经下载了koa的依赖包。至此，koa安装已经算是完成了。

# 3.使用

&emsp;&emsp;只需要七行代码，就能让我们的koa跑起来了。新建一个app.js入口文件，写下如下代码：

```
const Koa = require('koa');
const app = new Koa();
app.use(async ctx => {
    ctx.body = 'Hello World';
});
app.listen(3000,()=>{
    console.log('now you can visite localhost:3000');
});
```

&emsp;&emsp;现在你去访问localhost:3000的时候，应该就能看到Hello World在向你挥手了，是不是很简单。

# 4.Koa的一些特点

&emsp;&emsp;Koa 就是一种简单好用的 Web 框架。它的特点是优雅、简洁、表达力强、自由度高。本身代码只有1000多行，所有功能都通过插件实现，很符合 Unix 哲学。

&emsp;&emsp;后续会更新更多关于node以及Koa框架的文章。。。