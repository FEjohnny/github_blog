---
title: webpack之devtool的几种sourcemaps模式用法含义
date: 2018-09-20 22:24:23
tags: webpack
---

# 为什么需要source maps？

&emsp;&emsp;One of the easiest performance wins you can gain for your website is to combine and compress your JavaScript and CSS files. But what happens when you need to debug the code within those compressed files? It can be a nightmare. Fear not however, there is a solution on the horizon and it goes by the name of source maps.
<!--more-->

&emsp;&emsp;A source map provides a way of mapping code within a compressed file back to it’s original position in a source file. This means that – with the help of a bit of software – you can easily debug your applications even after your assets have been optimized. The Chrome and Firefox developer tools both ship with built-in support for source maps.

# source maps如何工作？
&emsp;&emsp;This option controls if and how source maps are generated. webpack config:
&emsp;&emsp;devtool: string || false,

|devtool|build|rebuild|production|quality|
|:-:|:-:|:-:|:-:|:-:|:-:|
|(none)|+++|+++|yes|bundled code|
|eval|+++|+++|no|generated code|
|cheap-eval-source-map|+|++|no|transformed code (lines only)|
|cheap-module-eval-source-map|o|++|no|original source (lines only)|
|eval-source-map|--|+|no|original source|
|cheap-source-map|+|o|yes|transformed code (lines only)|
|cheap-module-source-map|o|-|yes|original source (lines only)|
|inline-cheap-source-map|+|o|no|transformed code (lines only)|
|inline-cheap-module-source-map|o|-|no|original source (lines only)|
|source-map|--|--|yes|original source|
|inline-source-map|--|--|no|original source|
|hidden-source-map|--|--|yes|original source|
|nosources-source-map|--|--|yes|without source content|

```
+++ super fast, ++ fast, + pretty fast, o medium, - pretty slow, -- slow
```

&emsp;&emsp;Some of these values are suited for development and some for production. For development you typically want fast Source Maps at the cost of bundle size, but for production you want separate Source Maps that are accurate and support minimizing.

# 各种Qualities的含义

bundled code - -一大块的代码，无模块分离。

generated code - - 模块互相分离，并用模块名注释。还可以看到webpack生成的代码。
```
Example: Instead of import {test} from "module"; test(); you see something like var module__WEBPACK_IMPORTED_MODULE_1__ = __webpack_require__(42); module__WEBPACK_IMPORTED_MODULE_1__.a();.
```

transformed code -- 模块互相分离，并用模块名注释。webpack转换之前可以看到代码，但是是在loaders加载之后的代码。
```
Example: Instead of import {test} from "module"; class A extends test {} you see something like import {test} from "module"; var A = function(_test) { ... }(test);
```

original source - 模块互相分离，并用模块名注释。You see the code before transpilation, as you authored it. This depends on Loader support.

&emsp;&emsp;without source content - Contents for the sources are not included in the Source Maps. Browsers usually try to load the source from the webserver or filesystem. You have to make sure to set output.devtoolModuleFilenameTemplate correctly to match source urls.

&emsp;&emsp;(lines only) - Source Maps are simplified to a single mapping per line. This usually means a single mapping per statement (assuming you author is this way). This prevents you from debugging execution on statement level and from settings breakpoints on columns of a line. Combining with minimizing is not possible as minimizers usually only emit a single line.

# Development 

&emsp;&emsp;The following options are ideal for development:

&emsp;&emsp;eval - Each module is executed with eval() and //@ sourceURL. This is pretty fast. The main disadvantage is that it doesn't display line numbers correctly since it gets mapped to transpiled code instead of the original code (No Source Maps from Loaders).

&emsp;&emsp;eval-source-map - Each module is executed with eval() and a SourceMap is added as a DataUrl to the eval(). Initially it is slow, but it provides fast rebuild speed and yields real files. Line numbers are correctly mapped since it gets mapped to the original code. It yields the best quality SourceMaps for development.

&emsp;&emsp;cheap-eval-source-map - Similar to eval-source-map, each module is executed with eval(). It is "cheap" because it doesn't have column mappings, it only maps line numbers. It ignores SourceMaps from Loaders and only display transpiled code similar to the eval devtool.

&emsp;&emsp;cheap-module-eval-source-map - Similar to cheap-eval-source-map, however, in this case Source Maps from Loaders are processed for better results. However Loader Source Maps are simplified to a single mapping per line.

# Special cases

&emsp;&emsp;The following options are not ideal for development nor production. They are needed for some special cases, i. e. for some 3rd party tools.

inline-source-map - A SourceMap is added as a DataUrl to the bundle.

cheap-source-map - A SourceMap without column-mappings ignoring loader Source Maps.

inline-cheap-source-map - Similar to cheap-source-map but SourceMap is added as a DataUrl to the bundle.

cheap-module-source-map - A SourceMap without column-mappings that simplifies loader Source Maps to a single mapping per line.

inline-cheap-module-source-map - Similar to cheap-module-source-map but SourceMap is added as a DataUrl to the bundle.

# Production 

&emsp;&emsp;These options are typically used in production:

&emsp;&emsp;(none) (Omit the devtool option) - No SourceMap is emitted. This is a good option to start with.

&emsp;&emsp;source-map - A full SourceMap is emitted as a separate file. It adds a reference comment to the bundle so development tools know where to find it.

> You should configure your server to disallow access to the Source Map file for normal users!

&emsp;&emsp;hidden-source-map - Same as source-map, but doesn't add a reference comment to the bundle. Useful if you only want SourceMaps to map error stack traces from error reports, but don't want to expose your SourceMap for the browser development tools.

> You should not deploy the Source Map file to the webserver. Instead only use it for error report tooling.

&emsp;&emsp;nosources-source-map - A SourceMap is created without the sourcesContent in it. It can be used to map stack traces on the client without exposing all of the source code. You can deploy the Source Map file to the webserver

&emsp;&emsp;It still exposes filenames and structure for decompiling, but it doesn't expose the original code.

&emsp;&emsp;When using the uglifyjs-webpack-plugin you must provide the sourceMap: true option to enable SourceMap support.