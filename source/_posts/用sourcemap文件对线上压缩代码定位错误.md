---
title: 用sourcemap文件对线上压缩代码定位错误
date: 2018-10-14 18:09:02
tags: webpack
---

# 前言：

&emsp;&emsp;在前面的文章中，有介绍source map文件的功能以及其不同配置选项的作用。webpack devTool配置选项控制是否生成，以及如何生成 source map。在开发环境中，当bundle文件执行出错当时候，浏览器如果打开了Enable JavaScript source maps功能（截图是chrome该功能当开关）
<!--more-->

![](/images/1.jpg)

&emsp;&emsp;chrome会去加载map文件（注意这种外联的.map文件只会在F12开启时进行下载），帮我们在控制台显示转换之后的错误信息，比如错误文件，行列信息等，可以很方便地定位到导致错误到代码位置，

![](/images/2.jpg)

&emsp;&emsp;点击index.js就可以直接跳转到错误文件到位置（还是打包压缩之前的文件和位置哦，是不是很贴心）。

&emsp;&emsp;但是，如果线上运行的代码报出了一个错误，无论是window全局环境的错误，还是框架（vue，angular等）层面拦截的全局错误，在监控日志里面拿到的错误信息，都是编译、压缩、合并之后的代码信息，通常一行就会有几万甚至十几万行，许多变量还会被改写成像a,b,c这种，这对于我们去定位出错代码的位置就会变得十分困难。看如下的错误信息，可能会觉得又点儿头大。
```
ReferenceError: m is not defined
at statusService.getStatus (main.min.js:29:233694)
...
....
.......
at injectionArgs (main.min.js:1:24243)
```
&emsp;&emsp;这个时候，我们可能就会需要使用打包产生的.map文件去把我们拿到的错误信息还原成对应源码的信息（主要是文件名，行列信息）。如何实现这个功能，这也就是本篇文章的主题。

# map文件的构成：

&emsp;&emsp;mappings是记录位置信息对字符串，它记录了转换前和转换后是如何一一对应对，这也就是我们能通过map文件去溯源的主要原因之一。看下mappings都记录了写啥：

![](/images/3.jpg)

&emsp;&emsp;这个mappings实际上它是一个很长的字符串，主要可以分为3层：
```
第一层是行对应，以分号（;）表示，每个分号对应转换后源码的一行。所以，第一个分号前的内容，就对应源码的第一行，以此类推。
第二层是位置对应，以逗号（,）表示，每个逗号对应转换后源码的一个位置。所以，第一个逗号前的内容，就对应该行源码的第一个位置，以此类推。
第三层是位置转换，以VLQ编码表示，代表该位置对应的转换前的源码位置。
```

&emsp;&emsp;那什么又是VLQ编码呢？请看这篇文章了解下。
&emsp;&emsp;举个列子：
```
gBACZ,2DAAY;aACb;YACD,OAAO,EAAE;
```
&emsp;&emsp;这段字符串就表示，转换后的源码分成三行，第一行有两个位置，第二行有一个位置，第三行有两个位置。
&emsp;&emsp;位置对应的原理是每个位置使用五位，表示五个字段。从左边算起，它们分别表示：
```
- 第一位，表示这个位置在（转换后的代码的）的第几列。
- 第二位，表示这个位置属于sources属性中的哪一个文件。
- 第三位，表示这个位置属于转换前代码的第几行。
- 第四位，表示这个位置属于转换前代码的第几列
- 第五位，表示这个位置属于names属性中的哪一个变量。（第五位不是必须，可能没有对应的变量）
```

# 使用source-map包处理映射源（mappings）
 &emsp;&emsp;处理源映射，可以使用官方推荐的 source-map 库。为了避免将map文件暴漏出去，我使用node读取map文件来处理映射。直接附上该功能的主要处理mappings映射的源码：
 ```
var fs = require('fs');
var sourceMap = require('source-map');

function fixPath(filepath) {
    return filepath.replace(/\.[\.\/]+/g, "");
}

/**
 * 读取map文件查找源码信息
 * @param mapFile 要读取的map文件
 * @param line 线上错误代码的行
 * @param column 线上错误代码的列
 * @returns {Promise}
 */
module.exports = function(mapFile, line, column) {
    return new Promise((resolve, reject) => {
        fs.readFile(mapFile, async function(err, data) {
            if(err) {
                console.log(err);
                reject(err);
            }

            var fileContent = data.toString(),
                fileObj = JSON.parse(fileContent),
                sources = fileObj.sources;

            let sourcesPathMap = {};
            sources.map(item => {
                sourcesPathMap[fixPath(item)] = item;
            });
            var consumer = new sourceMap.SourceMapConsumer(fileContent);
            var lookup = {
                line: parseInt(line),
                column: parseInt(column)
            };
            var result = consumer.originalPositionFor(lookup);
            var originSource = sourcesPathMap[result.source],
                sourcesContent = fileObj.sourcesContent[sources.indexOf(originSource)];
            result.sourcesContent = sourcesContent;
            resolve(result);
        });
    });
}
 ```

 # 最后：

&emsp;&emsp;有了这个转换功能之后，在处理线上错误的时候，我们就可以将其转换之后，然后再进行上报，我们拿到的就是错误信息对应源码的位置啦。

![](/images/4.jpg?center)


 