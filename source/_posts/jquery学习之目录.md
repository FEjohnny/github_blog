---
title: jquery学习之目录
date: 2017-05-11 10:51:11
tags: [javascript, jquery]
---

# 一、核心

jQuery核心函数

```
jquery([selector,[context]])，selector用来查找的字符串，context为待查找的DOM集或者jquery对象。是jquery的核心功能函数。

jquery(html,[ownerDocument])，提供原始的html标记字符串，动态创建jquery对象包装的DOM元素，同时设置属性、事件等。

jquery(callback)，$(document).ready()的简写。$(function(){}) / jQuery(function($){})
```
<!--more-->


jQuery访问对象

```
each()，以每一个匹配的元素作为上下文来执行一个函数。

size(),jquery对象元素的个数，实现核心为length属性。

length，jquery对象元素的个数。

selector，返回传给jQuery()的原始选择器。

context，返回传给jQuery()的原始DOM内容，即传给jQuery()的第二个参数，如果没有指定context，默认指向当前的文档（document）。

get([index])，取得匹配元素的第index个元素，(可结合reverse()将数组反向，$(selector).get().reverse())。等价于$(selector)[index]。

index([selector|element]),搜索匹配的元素，并返回相应元素的索引值。

```

数据缓存

```
expando:表示可扩展对象的动态属性。

data(key,val)/removeData(key):在元素上存放/读取数据(任何格式），返回jQuery对象。

jQuery.data(element,[key],[value]):data()的底层方法。
```

队列控制

```
queue(element,[queueName]):显示或操作在匹配元素上执行的函数队列。

dequeue([queueName]):移除元素队列最前端的一个队列函数并执行它。

clearQueue([queueName]):清空对象上尚未执行的所有队列；如果不带参，则默认清空的是动画队列；和stop(true)类似，但stop()只能清空动画队列，而这个可以清空所有通过queue()创建的队列。
```

插件机制

```
jQuery.fn.extend(object):扩展jQuery元素集来提供新的方法，用来扩充jQeury对象。

jQuery.extend(object):扩展jQuery对象本身，用来在jQuery命名空间上增加新的函数。
```

多库共存

```
jQeury.noConflict():多库共存，用来将函数变量$让渡给第一个实现它的库；也可创建一个新的别名，在接下来的库中使用jQuery对象，var j = jQuery.noConflict();j(selector)。
```

# 二、属性

属性

```
attr():设置或返回被选元素集中第一个元素的属性值；removeAttr():从每一个匹配元素中删除一个属性；prop():置或返回被选元素集中第一个元素的属性值；removeProp():删除由prop()方法设置的属性集。
```

css类

```
addClass():为元素集添加一个或多个class；removeClass():为元素集移除一个或多个或全部class；toggleClass():元素集中有就删除，无则添加class。
```

HTML代码/文本/值

```
html(val|fn):获取第一个匹配元素的html内容或者设置所选元素的html内容，此方法不能用于xml文档，可用于xhtml文档。

text(val|fn):取得/设置所有匹配元素的内容，取得的结果是所有匹配元素包含的文本内容组合起来的文本。
```

# 三、css

css

```
css():访问元素的指定样式属性或者设置元素的样式属性。
```

位置

```
offset():返回元素在当前视口中的偏移量对象，包含两个参数：top，left，只对可见元素有效。

position():返回元素相对于父元素的偏移量对象，包含top，left两个参数，只对可见元素有效。

scrollTop():获取/设置匹配元素相对于滚动条顶部的偏移。对隐藏元素仍有效。

scrollLeft():获取/设置匹配元素相对于滚动条左侧的偏移。对隐藏元素仍有效。
```

尺寸

```
width() , height():内容高宽度；innerHeigh() , innerWidth()：包含补白，不含边框； outerHeight() , outerWidth()：包含补白和边框。
```

# 四、选择器

```
选择器： 
    #id, 
    element, 
    .class, 
    *, 
    [selector1,selector2,selectorN]。

层级：
    $("ancestor descendant"):ancestor所有的后台元素；
    $("parent > child):parent的所有子元素；
    $("pre + next"):匹配所有紧接在pre元素后的next元素；
    $("pre ~ siblings"):pre之后的所有siblings元素。

基本：  
    :first获取匹配的第一个元素；
    :last获取匹配的最后一个元素；
    :not(selector)去除所有给定选择器匹配的元素；
    :even匹配所有索引值为偶数的元素；
    :odd匹配所有索引值为基数的元素；
    :eq(index)匹配指定索引值的元素；
    :gt(index)匹配所有大于给定索引值的元素；
    :lt(index)匹配所有小于给定索引值的元素；
    $(":head")匹配所有h1,h2,h3...之类的标题元素；
    :animated匹配所有正在执行动画效果的元素；
    :focus匹配所有获得焦点的元素。

内容：
    :contains(text)匹配包含给定文本的元素；
    :empty匹配所有不包含子元素或者文本为空的元素；
    :has(selector)匹配含有选择器所匹配的元素；
    :parent匹配含有子元素或者文本的元素。

可见性：
    :hidden查找所有不可见元素或者type为hidden的元素；  
    :visible 匹配所有可见元素。

属性：
    [attribute]:匹配给定属性的元素； 
    [attribute=value]:匹配指定属性值是某个特定值的元素；
    [attribute!=value]:匹配不含指定属性或者属性值不等于指定值的元素；
    [attribute^=value]:匹配给定的属性是以某些值开始的元素；
    [attribute$=value]:匹配给定的属性是以某些值结尾的元素；
    [attribute*=value]:匹配给定的属性包含某些值的元素；
    [selector1][selector2][selectorN]:复合属性选择器，同时满足多个条件使用。

子元素： 
    :nth-child()匹配其父元素下的第n个子元素或者基偶元素；
    :first-child匹配每个父元素的第一个子元素（ps：:first只匹配一个元素）；
    :last-child匹配最后一个子元素（ps：:last只匹配第一个元素）；
    ：only-child如果某个元素是父元素中唯一的子元素，那将会被匹配。

表单：
    :input匹配所有input，textarea，select，button元素；
    :text匹配所有的单行文本框；
    :password匹配所有的密码框；
    :radio匹配所有单选按钮；
    :checkbox匹配所有复选框；
    :submit匹配所有提交按钮；
    :image匹配所有图像区域；
    :reset匹配所有重置按钮；
    :button匹配所有按钮元素；
    :file匹配所有文件区域；
    :hidden匹配所有不可见元素，包括type为hidden的元素。

表单对象属性：
    :enabled匹配所有可用元素；
    :disabled匹配所有不可用元素；
    :checked匹配所有被选中的元素（复选框，单选框等，不包括select中的option）；
    :selected匹配所有选中的option。
```

# 五、文档处理

```
内部插入：
    append(content|fn)向每个匹配的元素内部追加内容；
    appendTo(content)把匹配的元素追加到另一个指定的元素中（$('p'）.appendTo($('div')）；
    prepend(content|fn)向每个匹配元素内部前置追加内容；
    prependTo(content)把所有匹配的元素前置插入到另一个指定的元素集合中。

外部插入：
    after(content|fn)/after(content|fn)在每个匹配的元素之后/前插入内容；
    insertAfter(content)/insertBefore(content)把所有匹配的元素插入到另一个指定的元素集合后/前面。

包裹：
    wrap(html|ele|fn)把每一个匹配的元素用其他元素的结构化标签包裹起来；
    unwrap()移除元素的父元素；
    wrapAll(html|fn)把所有匹配元素用一个结构化标签包裹起来；
    wrapInner(html|ele|fn)将每一个匹配的元素的子内容用一个html结构包裹起来。

替换：
    replaceWith(content|fn)将每一个匹配元素替换为指定的html或者DOM内容；
    replaceAll(selector)用匹配的元素替换掉selector元素。

删除：
    empty()删除所有匹配元素的子节点；
    remove([expr])从DOM中删除所有匹配元素；
    detach([expr])从DOM中删除所有匹配元素（所有绑定的事件、附加的数据等都会保留，而remove不会）。

复制：
    clone(Even)匹配选中的DOM元素并选中这些克隆的副本（参数表示是否克隆事件处理函数，1.5以上默认false）。
```

# 六、筛选

```
过滤：
    eq(index|-index)获取正数（0开始）第N个或者倒数（1开始）第N个；
    first()获取第一个元素；
    last()获取最后一个元素；
    hasClass(class)检查元素是否包含某个特定的class；
    filter(expr|obj|ele|fn)筛选出与指定表达式匹配的元素集合；
    is(expr|obj|ele|fn)根据选择器、dom元素或者jQuery对象来检测匹配元素集合，如果其中至少有一个符合给定的表达式就返回true；
    map(callback)将一组元素转换成其他数组（不论是否是数组元素）；
    has(expr|ele)保留包含特定后代的元素，去掉不包含特定后代的元素；
    not(expr|fn)删除与制定表达式不匹配的元素；
    slice(start,[end])选取一个匹配的子集；

查找：
    children([expr])取得一个包含匹配的元素集合中每个元素的所有子元素的集合；
    closest(expr,[context]|obj|ele)从元素本身开始向上查找，返回最先匹配到的元素；
    find(expr|obj|ele)搜索所有与指定表达式匹配的元素。这个函数是找出正在处理的元素的后代元素的好方法。
    next([expr])取得匹配元素紧邻的后面一个同辈元素；
    nextAll([expr])取得匹配元素紧邻的后面所有同辈元素；
    nextUntil(exp|ele|filter)取得匹配元素紧邻的后面所有同辈元素，直到指定的那个元素为止（不包括指定元素）；
    offsetParent()返回第一个用于定位的父元素（只对可见元素有效）；
    parent([expr])取得一个包含着所有匹配元素的唯一父元素的集合；
    parent([expr])取得所有父元素集合；
    parentsUntil([exp|ele|filter])所有父元素直到指定的元素为止；
    pre()紧邻的前一个同辈元素；preAll()所有前面的同辈元素；
    preUntil([exp|ele|filter])所有前面的同辈元素，直到指定的元素为止；
    siblings([expr])所有唯一同辈元素的元素集合。

串联：
    add(expr|ele|html|obj,[con])把与表达式匹配的元素添加到jQuery对象中；
    andSelf()把先前所选元素加入当前所选元素中（$('div') .find('p'). add ( ' class ' )最后div也会加上class类）；
    contents()查找所选元素内部所有的子节点（包括文本节点），如果元素是iframe则查找文档内容；end()回到最近的一个“破坏性”操作之前，即将匹配的元素变为前一次的状态。
```


# 第七、事件

```
页面载入：
    ready(fn)当DOM载入就绪可以查询及操纵时绑定一个要执行的函数。window.onload=$(document).ready(fn)=$(fn)

事件处理：
    on(events,[selector],[data],fun)在选定元素上绑定一个或多个事件处理函数；
    off(events,[selector],fn)在选中元素上移除一个或者多个事件的事件处理函数；
    bind(type,[data],fn)/unbind(type,[data|fn])为元素绑定/接触特定的事件处理函数；
    one(type,[data],fn)为匹配元素的特定事件绑定一个一次性的事件处理函数；
    trigger(type,[data])出发某类事件；
    triggerHandler(type,[data])出发指定事件类型上所有绑定的待处理函数。

事件委派：
    live(type,[data],fn)/die(type,[fn])为元素附加/删除一个事件处理函数，后添加进来的元素也有效；
    delegate(selector,[type],[data],fn) / undelegate()为元素添加/删除一个或多个事件处理程序，并规定当这些事件发生时的处理函数；

事件切换：
    hover([over,out])鼠标over，out触发的函数；
    toggle([speed],[easing], [fn])绑定两个或多个事件处理函数。

事件：
    blur([data],fn)失去焦点是触发；
    change([data],fn)触发元素的change事件；
    click([data],fn)点击事件；dbclick([data],fn)双击事件；
    error([data],fn) error事件；
    focus([data],fn)/获得焦点事件；
    focusin([data],fn) / focusout([data],fn)当元素或者其内部任何一个元素获得/失去焦点的时候会触发这个事件，可在父元素上检测子元素获得/失去焦点的情况；
    keydown([data],fn)键盘按下时触发；
    keypress([data],fn)与keydow类似，没插入一个字符都会触发keypress事件；
    keyup([data],fn)按键释放是触发；
    mousedown([data],fn)按下鼠标触发；
    mouseenter([data],fn)鼠标穿过元素时触发；
    mouseleave([data],fn)鼠标离开元素时触发，子元素不能触发；
    mousemove ( [data] , fn )鼠标在元素上移动时触发；
    mouseout([data],fn)鼠标从元素上离开时触发，子元素也可触发；
    mouseover([data],fn)鼠标移入被选元素时触发；
    mouseup([data],fn)鼠标释放时触发；
    resize([data],fn)当文档窗口改变大小时触发；
    scroll([data],fn)当滚动条滚动时触发；
    select([data],fn)当文本框中的文本被选中时触发；
    submit([data],fn)表单提交事件；unload([data],fn)页面卸载的时候触发，只用于window对象。
```

# 第八、效果

```
基本：
    show([speed],[easing],fn)/hide([speed],[easing],fn)显示/隐藏元素；
    toggle([speed],[easing],fn)显示与隐藏互相切换。

滑动：
    slideUp([speed],[easing],fn)/slideDown([speed],[easing],fn)向上/向下滑动隐藏/显示元素；
    slideToggle([speed],[easing],fn)。

淡入淡出：
    fadeIn([speed],[easing],fn) / fadeOut([speed],[easing],fn)淡入/淡出；
    fadeTo([[speed],opcity,[easing],[fn]])调整到指定透明度；
    fadeToggle ( [speed] , [easing] , fn)。

自定义：
    animate(params,[speed],[easing],fn)自定义动画；
    stop( [clearQueue] ,[jumpToEnd])停止元素上正在运行的动画；
    delay(duration,[queueName])设置一个延时来推迟执行队列中之后的项目）。

设置：
    jQuery.fx.off=ture/false;关闭/重新开启页面上所有动画；
    jQuery.fx.interval=100设置动画的显示帧数。

```

# 第九、ajax

```
ajax请求：
    $.ajax(url,[setting])通过HTTP远程加载数据；
    load(url, [data] ,[callback])载入远程HTML文件代码并插入调用元素DOM中；
    $.get(url, [data], [callback], [type])通过HTTP GET远程载入信息；
    $.getJSON(url, [data], [callback])通过HTTP GET远程载入JSON数据；
    $.getScript(url, [callback])通过HTTP GET请求载入并执行一个javascript文件；
    $.post(url, [data], [callback], [type])通过HTTP POST远程载入信息。

ajax事件：
    ajaxComplete(callback)ajax请求完时执行；
    ajaxError( callback ) ajax请求发生错误时执行；
    ajaxSend( callback )ajax请求发送之前执行；
    ajaxStart( callback )ajax请求开始时执行；
    ajaxStop( callback ) ajax请求结束时执行；
    ajaxSuccess( callback ) ajax请求成功是执行。

其他：
    ajaxSetup([options]) 设置全局ajax默认项；
    serialize()序列化表单为字符串；
    serializeArray()序列化表单为JSON对象。
```

# 第十：工具

```
浏览器及特性检测：
    jQuery.suppor一组用于展示不同浏览器各自特性和bug的属性集合。

数组和对象操作：
    $.each(obj,callback)通用遍历方法，可用于遍历对象和数组；
    $.extend([deep],target,object1,objectN)用一个或者多个对象来扩展一个对象，返回被扩展的对象；
    $.grep(array,callback,[invert])过滤数组元素；
    $.makeArray(obj)将类数组对象转换为数组对象；
    $.map(arr|obj,callback)将一个数组中的元素转换到另一个数组中；
    $.inArray( value, array, [from | index ] )确定第一个参数在数组中第一次出现的位置；
    $('selector').toArray()以数组形式返回匹配元素；
    $.merge(firstArr,secondeArr)合并两个数组到第一个数组上；
    $.unique(array)删除数组中的重复元素；
    $.parseJSON(json)接受一个json字符串，返回解析后的json对象；

函数操作：
    jQuery.noop一个空函数，对插件很有用；
    jQuery.proxy( fn, context)返回一个新函数，并且这个函数始终保持了特定的作用于。

测试操作：
    $.contains(container,contained)检测第一个DOM节点是否包含另一个DOM节点；
    $.type(obj)检测对象类型，返回字符串；
    $.isArray(obj)检测是否是数组；
    $.isFunction(obj)检测是否是函数；
    $.isEmptyObject(obj)检测是否是空对象（不包含任何属性；$.isPlainObject(obj)检测是否是纯粹的对象（通过{}或者new创建）；
    $.isWindow(obj)检测是否是窗口对象（可能是Frame）；
    $.isNumberic(value)检测它的参数是否是一个数字。

字符串操作：
    $.trim(str) 去掉字符串首位空格。

URL：
    $.param(obj,[traditional])表单元素数组或者对象序列化，是 .serialize() 的核心方法。

插件编写：
    jQuery.error(message)接受一个字符串，并直接抛出一个包含这个字符串的异常。
```


# 第十一、Deffered

```
deffered.done(callback,[callback])当延迟成功是调用一个函数或函数数组。

deffered.fail(callback,[callback])当延迟失败是调用一个函数或函数数组。

deffered.state/deffered.isRejected()确定延迟对象是否已被拒绝。

deffered.isResolved() 确定延迟对象是否已得到解决。

deffered.reject(args)拒绝延迟对象，并根据给定的参数调用任何失败的回调函数。

deffered.rejectWith(context,[args])拒绝延迟对象，并根据给定的上下文和参数调用任何失败的回调函数。

deffered.resolve([args])解决延迟对象，并根据给定参数调用任何完成的回调函数。

deffered.resolveWith(context,[args])解决延迟对象，并根据给定的上下文和参数调用任何完成的回调函数。

deffered.then(doneCallbacks,failCallbacks)添加处理程序被调用时，延迟对象得到解决或者拒绝。

deffered.promise([type],[target])返回一个promise对象，用来观察当某种类型的所有行动绑定到集合，排队与否还是已完成。

deffered.pipe([doneFilter],[failFilter],[progressFilter])筛选器和/或链Deffered的使用程序方法，返回一个新的promise对象。

```