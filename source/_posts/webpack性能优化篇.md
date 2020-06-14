---
title: webpack性能优化篇，二次构建提速90%
date: 2020-06-14 17:12:34
tags: [webpack, 性能优化]
---

&emsp;&emsp;关于webpack的优化方案有很多，不管官方的还是非官方的。总结起来大概有如下几种优化方式：
1. 优化模块搜索、模块解析时间（loader配置，resolve.module配置，resolve.alias配置，resolve.extensions 配置，resolve.mainFields 配置，module.noParse 配置）

<!--more-->

# 背景
1. 客服工作台前端代码使用vue(v2.6) + webpack(v4.39.1)编写和构建，随着业务规模的增长，代码量变得越来越大，仅工作台一个模块下面就有**82**个vue(单文件组件)文件，**24749**行代码。每次构建需要**17秒**左右（自己的imac，性能还算好的了），17秒对于首次构建还可以接受；但实际开发中，二次构建的次数要远远远远大于首次构建的次数，对于开发调试时每次修改代码要等上17秒才能在浏览器中看到修改后代码的运行效果，**感觉就是在浪费生命-。-**。
2. 一个因为二次构建速度引发的事故

由此可见，提升代码二次构建速度，将直接影响代码调试效率。

# 构建速度检测和分析
优化的第一步是首先找出webpack构建都把时间花在了哪里。通过使用[speed-measure-webpack-plugin](https://www.npmjs.com/package/speed-measure-webpack-plugin "speed-measure-webpack-plugin")，测量出webpack调试时构建期间的时间消耗情况。
speed-measure-webpack-plugin使用方法：
```
const config = require('./webpack.base.config.js');
const SpeedMeasurePlugin = require("speed-measure-webpack-plugin");
const smp = new SpeedMeasurePlugin();
smp.wrap(config); // 打包速度测量
```
测试结果如下：
![图片](/images/36.png)
整体构建耗时在17秒左右，这是首次构建时间。二次构建时间也在15秒左右；也就是说，我修改一行前端代码，需要等15秒左右才能在浏览器中的页面上看到修改后的代码执行效果，开发体验极差，开发效率极低。

# 优化方案
关于webpack的优化方案有很多，不管官方的还是非官方的。总结起来大概有如下几种优化方式：
1. 优化模块搜索、模块解析时间（loader配置，resolve.module配置，resolve.alias配置，resolve.extensions 配置，resolve.mainFields 配置，module.noParse 配置）
2. 优化代码压缩时间（terser-webpack-plugin）
3. 开启多进程打包（thread-loader）
4. 合理利用缓存（cache-loader，HardSourceWebpackPlugin ）

### 工作台使用方案
在经过各种尝试之后，发现很多优化措施对工作台构建速度提升都不是很明显。唯有在使用**[hard-source-webpack-plugin](https://www.npmjs.com/package/hard-source-webpack-plugin "hard-source-webpack-plugin")**优化后，二次构建耗时控制在了2秒左右，提速90%左右。考虑到实际开发中，二次构建的次数远远远远远远要大于首次构建的速度，强烈安利这个插件。附使用方法：
```
const HardSourceWebpackPlugin = require('hard-source-webpack-plugin-fixed-hashbug');
config.plugins.unshift(
    new HardSourceWebpackPlugin({
        cacheDirectory: 'node_modules/.cache/hard-source/[confighash]',
        configHash: function(webpackConfig) {
            return require('node-object-hash')({sort: false}).hash(webpackConfig);
        },
        environmentHash: {
            root: process.cwd(),
            directories: [],
            files: ['package-lock.json'],
        },
        info: {
            mode: 'none',
            level: 'debug',
        },
        cachePrune: {
            maxAge: 2 * 24 * 60 * 60 * 1000,
            sizeThreshold: 50 * 1024 * 1024
        },
    })
);
```

### 其他方案


# 此次优化过程中遇到的问题及解决方案

### 1. 添加speed-measure-webpack-plugin插件后，二次构建报错
![图片](/images/37.png)
目前还没有找到比较好解决方案，github有人提对应的issue，还没解决（我就用来测首次编译速度就够用了）。

### 2. hard-source-webpack-plugin使用报错。
```
Could not freeze xxxxxxxxxx: Cannot read property 'hash' of undefined
```
**解决方案**：使用hard-source-webpack-plugin-fixed-hashbug插件代替

### 3. speed-measure-webpack-plugin与hard-source-webpack-plugin一起使用会报错。
![图片](/images/38.png)
**解决方案：**待解决（分开使用就OK）