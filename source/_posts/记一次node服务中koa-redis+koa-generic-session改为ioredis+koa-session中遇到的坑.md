---
title: 记一次node服务中koa-redis+koa-generic-session改为ioredis+koa-session中遇到的坑
date: 2018-11-05 00:47:35
tags: node
---

&emsp;&emsp;在node项目中，我们之前koa-generic-session模块来存储用户信息和登录状态等信息，并且用koa-redis模块来进行登陆状态的持久化，将登陆信息存储在redis中。最近在工作中收到一个前端组的任务，需要对node项目中koa-redis + koa-generic-session的模块进行替换，全部改为使用ioredis+koa-session。
一开始，我本以为是个很简单的任务，找一个空闲的时间，就开始做这个模块替换的事儿了。在github上找到了ioredis和koa-session，随便读了一下文档，撸起袖子就开始干；然而，事情并没有我想象的那么简单。
<!--more-->

# 坑一：
&emsp;&emsp;直接将两个模块替换之后，系统运行会抛出一个错误：大致意思就是说store对象缺少destroy方法。诶？奇怪了，明明都是按照官方文档做的，怎么会说没有destroy方法呢？去翻了一下两个模块的源码，果然里面没有destroy这个方法存在啊，到底哪里出了问题啊。百思不解，就去仔细阅读了两个模块的说明文档，果然发现了一个好东西。

![](/images/6.jpg)

&emsp;&emsp;意思就是说，使用koa-session模块，session默认是存储在cookie中的，如果你想要把session存到redis或其他数据库中的话，你配置的store属性必须要有（自己实现）get，set，destory方法，而且都必须是异步函数。之前我是直接new ioredis(options)来使用的，这下真想大白，好，那就自己加上这几个方法，把ioredis重新封装一下然后再使用就可以了呗。

```
const Redis = require('ioredis');
class RedisStore{
    constructor(config) {
        this.redis = new Redis(config);
    }
    async get(key, maxAge, options) {
        let data = await this.redis.get(key);
        return JSON.parse(data);
    }
    async set(key, sess, maxAge, options) {
        try {
            // Use redis set EX to automatically drop expired sessions
            await this.redis.set(key, JSON.stringify(sess), 'EX', (maxAge / 1000) | 0);
        } catch (e) {
            console.log('redis set error:', e);
        }
        return key;
    }
    async destroy(key) {
        return await this.redis.del(key);
    }
}
module.exports = RedisStore;
```
&emsp;&emsp;成功填完坑一。

# 坑二：
&emsp;&emsp;完成上面的工作之后，组件也基本上就算是完成替换了，系统也能正常跑起来。但是自己在测试升级发布的时候，发现一个问题，那就是替换完这两个模块发布之后，之前的登陆态会全部丢失，咱们调试过程中可能无所谓，但是生产环境上面的用户可不能不考虑他们的感受啊。总不能发布之后，让他们全部都重新登陆吧（其实这也是一个可选的方案，选择夜深人静系统使用率比较低的时候发布）。不过，为了更好的用户体验，还是考虑一下能不能升级之后，兼容之前的登陆态。
&emsp;&emsp;考虑一下，我们的登陆信息都是存在redis中的，而redis中都是简单的键值对存储的内容，升级之后就取不到登陆信息了。只有一个原因，key值不对，所以在redis中找不到之前的登陆信息。为了验证这个猜想的正确性，我先是跑了一边测试流程，果然升级之后，之前登陆用户得到的key是不同的，然后看了一下模块中的源码。结论就是--koa-generic-session生成key的规则是uid2(24)，而在redis中的存取都会加上自己配置的前缀prefix；而koa-session中生成key的规则是prefix+时间戳+uid2(24)，取的时候不做任何处理。
这下要做兼容也就比较容易了，在取值的地方，给老的登陆信息主动加上前缀之前配置的前缀就可以了。
```
async get(key, maxAge, options) {
    if(key && key.indexOf('aaa:sess:') < 0) {
        key = 'aaa:sess:' + key;
    }
    let data = await this.redis.get(key);
    return JSON.parse(data);
}
```
&emsp;&emsp;看似一个很简单的任务，却也花了我不少时间，记录一下。
 