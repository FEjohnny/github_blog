---
title: vue如何引入js库，vue插件编写，canvas相关
date: 2018-04-15 10:02:21
tags: [javascript, vue, canvas]
---

# 1.vue框架引入js库的正确方式:

### a.全局变量法：

window._ = require('xxx.js');
即将对象绑定到window上，如果服务端渲染，就没有window对象，会报错。

<!--more-->

### b.处处引入法：

import _ from 'xxx'；
在需要的地方就引入，不够简洁，打包可能出现重复代码。

### c.将库变成vue原型对象的属性：

import _ from 'xxx';
Object.definePrototype(vue.prototype,'$_',{value:_});
由于所有的组件都会继承Vue原型对象上的方法，因此这些方法在任何组件文件中都能通过this.$_访问到。

### d.写成插件：

如果你在项目的很多地方都用了某个库，或者你希望全局可用，你可以构建自己的Vue 插件。插件能化繁为简，能让你像下面这样很简单地引入自己想要的库：

```
import MyLibraryPlugin from 'my-library-plugin';
Vue.use(MyLibraryPlugin);
```
就像Vue Route，Vuex等插件一样，我们的库仅仅需要两行，就能在任何地方使用了。

如何编写插件：

首先，创建一个文件。本例中，我将引入一个Axios库的插件。我们就把这个文件命名为axios.js 吧。最关键的地方在于，我们需要暴露一个将Vue构造器作为第一个参数的install 方法。

```
axios.js
import axios from 'axios';
export default {
  install: function(Vue，name="$http") {
    Object.defineProperty(Vue.prototype, name, { value: axios });
  }
}
```

接着我们只需要Vue实例的use方法就能将这个库引入整个项目了。我们像下面代码一样简单引入：

```
import AxiosPlugin from './axios.js';
Vue.use(AxiosPlugin);
new Vue({
  created() {
    console.log(this.$http ? 'Axios works!' : 'Uh oh..');
  }
})
```

# 2.canvas:

### a. canvas的元素大小与绘图表面大小：

这样设置的是元素大小和绘图表面大小，如果用css样式去设置canvas的大小，实际是设置的canvas元素大小，而绘图表面大小还是默认值（300,150），当canvas元素大小不符合其绘图表面的大小时，浏览器会对绘图表面进行缩放，使其符合元素的大小。

### b. web GL：

全名Web Graphics Library，一种3D绘图协议，这种绘图技术标准允许把javascript与OpenGL ES 2.0结合在一起，通过增加OpenGL ES 2.0的一个JavaScript绑定，WebGL可以为HTML5 Canvas提供硬件3D加速渲染，这样Web开发人员就可以借助系统显卡来在浏览器里更流畅地展示3D场景和模型了，还能创建复杂的导航和数据视觉化。

### c. CanvasRenderingContext2D对象所含属性方法，这些方法用来绘制图形：

    canvas： 指向该绘图环境所属的canvas对象。context.canvas

    fillstyle：指定该绘图环境再后续的图形填充操作中所使用的颜色、渐变色或图案。context.fillstyle="#000000"。

    gloabalAlpha：全局透明度设定，取值0~1，浏览器会将每个像素的alpha值与该值相乘。

    gloabalCompsiteOperation：该值告诉浏览器将某个物体绘制在其他物体之上时，所采取的绘制方式。可取值如下图，


gloabalCompsiteOperation可取值

    lineCap：该值告诉浏览器如何绘制线段的端点，可取值：butt（默认），round，square。

    lineWidth：该值决定了在canvas之中绘制线段的屏幕像素宽度。默认1.0

    lineJoin：告诉浏览器在两条线段相交时如何绘制焦点，可取值：bevel，round，miter（默认）。

    miterLimit：告诉浏览器如何绘制miter形式的线段焦点。

    shadowBlur：该值决定了浏览器如何延伸阴影效果（高斯模糊）.,默认0。

    shadowColor：阴影颜色，通常采用半透明色。

    shadowOffsetX/shadowOffsetY：单位像素，指定阴影效果的水平/垂直偏移量

    strokeStyle：指定了对路径进行描边时所用的绘制风格，可谓色值、渐变色或图案。

    textAlign/textBaseline：决定了fillText()或strokeText()方法进行绘制时，所画文本的水平/垂直对齐方式。 

    measureText：在画布输出文本之前检测文本宽度。

### d.canvas状态保存与恢复：

context.save()和context.restore()，用于保存和恢复当前canvas绘图环境的所有属性。save方法将当前的绘图环境压入堆栈顶部，restore方法则从堆栈顶部弹出一组状态信息来恢复绘制环境的状态，因此可以嵌套式地调用save（）/restore（）。

### e.canvas、svg与webGL：

    Canvas 是需要自己画点的白板； 

    SVG 是给数据就可以绘制点、线、图形的，基于 XML 的标记语言； 

    WebGL 是基于 Canvas 的 3D 框架。 

    Canvas 适用于位图，高数据量高绘制频率（帧率）的场景，如动画、游戏； 

    SVG 适用于矢量图，低数据量低绘制频率的场景，如图形、图表； 

    WebGL 主要用来做 3D 展示、动画、游戏。