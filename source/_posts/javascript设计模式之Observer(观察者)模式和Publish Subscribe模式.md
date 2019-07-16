---
title: javascript设计模式之Observer(观察者)模式和Publish/Subscribe模式
date: 2018-09-02 2018-07-08 23:36:20
tags: [设计模式, javascript]
---

# 一、Observer(观察者)

&emsp;&emsp; 在Observer(观察者)模式中，一个对象（subject）维持一系列依赖于它（观察者）的对象；当一个目标需要告诉观察者发生了什么有趣的的事情时，它会向观察者广播一个通知（可以包括与通知主题相关的特定数据）；当我们不再希望某个特定的观察者获得其注册目标发出的改变通知时，该目标可以将它从观察者列表中移除。

<!--more-->

### 1.定义

四人组（ErichGamma、Richard Helm、Ralph Johnson、John Vlissides）所著书籍《设计模式：可复用面向对象软件的基础》中提供的Oberver模式的定义是：一个或多个观察者对目标对状态感兴趣，它们通过将自己依附在目标对象上以便注册所感兴趣的内容。目标状态发生改变，就会发送一个通知消息，调用每个观察者的更新方法，当观察者不再对目标状态感兴趣时，它们可以简单地将自己从中分离。

Oberver Pattern

![](/images/28.jpeg)

### 2.Observer模式基本组件

```
1.Subject(目标）：维护一些列的观察者，方便添加或删除观察者。

2.Observer(观察者)：为那些在目标状态发生改变时需要获得通知的对象提供一个更新接口。

3.ConcreteSubject(具体目标)：状态发生改变时，存储ConcreteSubject的状态。

4.ConcreteObserver(具体观察者)：存储一个指向ConcreteSubject的引用，实现Observer的更新接口，以使自身状态与目标的状态保存一致。
```

### 3.实现简单的Obeserver模式

```
// 实现subject类
function Subject() {
    this.observerLists = [];
}
Subject.prototype.Add = function (observer) {
    this.observerLists.push(observer);
}
Subject.prototype.Remove = function (index) {
    if(index > -1 && index < this.observerLists.length){
        this.observerLists.splice(index,1);
    }
}
Subject.prototype.Notify = function (context) {
    for(var i = 0; i < this.observerLists.length; i++){
        this.observerLists[i].update(context);
    }
}
//实现Observer类
function Observer() {
    this.update = function (context) {
       // ...处理更新事件，一般在具体观察者中实现该方法
    }
}
// 具体目标类
 var concreteSubject = new Subject();
// 具体观察者
var ob1 = new Observer();
var ob2 = new Observer();
var ob3 = new Observer();
// 目标类维护一系列观察者
concreteSubject.Add(ob1);
concreteSubject.Add(ob2);
concreteSubject.Add(ob3);
// 通知更新
concreteSubject.Notify('我更新了');
```

### 4.Observer模式使用样例

```
<button id="addNewObserver">add new observer checkboxbutton>

<input type="checkbox" id="mainCheckbox">

<div id="observerContainer">div>
```

```
//定义扩展对象方法
function extend(obj,extention) {
    for (var key in obj){
        extention[key] = obj[key];
    }
}
// 引用dom元素
var controlCheckbox = document.getElementById(('mainCheckbox')),
    addBtn = document.getElementById('addNewObserver'),
    container = document.getElementById('observerContainer');
// 具体目标，利用Subject扩展controlCheckbox
extend(new Subject(),controlCheckbox);
// 点击checkbox会触发通知到观察者上
controlCheckbox['onclick'] = new Function('controlCheckbox.Notify(controlCheckbox.checked)');
// 添加新的观察者
addBtn['onclick'] = addNewObserver;
// 具体观察者
function addNewObserver() {
    //创建需要新添加到checkbox
    var check = document.createElement('input');
    check.type = 'checkbox';
    //利用Observer类扩展check
    extend(new Observer(), check);
    //重新自定义更新行为
    check.update = function (value) {
        this.checked = value;
    }
    // 为主subject到观察者列表添加新的观察者
    controlCheckbox.Add(check);
    // 将观察者附加到容器中
    container.appendChild(check);
}
```

# 二、Publish/Subscribe模式

&emsp;&emsp;Publish/Subscribe模式非常适用于javascript生态系统，主要在于ECMScript核心的事件驱动，在浏览器环境下更是如此，因为DOM将事件作为脚本编程的主要交互API。

### 1.Observer模式与Publish/Subscribe模式的区别

&emsp;&emsp;Observer模式要求希望接受到主题通知到观察者必须订阅内容到改变事件；Publish/Subscribe模式使用来了一个主题/事件通道，这个通道介于希望接收到通知(订阅者)到对象和激活事件到对象(发布者)之间，该事件系统允许代码定义应用程序的特定事件，这些事件可以传递自定义参数，其目地是避免发布者和定义者之间产生依赖关系，它允许任何定义者执行适当的事件处理程序来注册和接受发布者发出的通知。

观察者模式与发布订阅模式的对比

![](/images/29.jpeg)

### 2.Publish/Subscribe模式的优缺点

&emsp;&emsp;使用Publish/Subscribe模式主要优点是可以保持程序的低耦合。而它的缺点也主要是源于它的主要优点，通过将发布者与订阅者解耦之后，有时会很难保证程序的特定部分是否按照我们期望的运行。比如，发布者可能会假设：一个或多个订阅者在监听它们，倘若假如订阅者需要记录或者输出一些应用相关的错误的时候，如果订阅者执行日志崩溃，由于系统的解耦特性，发布者就不会看到这一点。它还有一个缺点是：订阅者非常无视发布者的存在，并对变换发布者产生的成本视而不见，由于订阅者和发布者之间的动态关系，很难跟踪依赖更新。

### 3.Publish/Subscribe模式的实现

```
var pubsub = {};
(function (q) {
    var topics = {},
          subUid = -1;
    // 通过特定的名称和回调函数订阅事件
  q.subscribe = function (topic, func) {
        if (!topics[topic]) {
            topics[topic] = [];
        }
        var token = (++subUid).toString();
        topics[topic].push({
            token: token,
            func: func
        });
        return token;
    }
    // 基于订阅的标记，取消订阅
  q.unsubscribe = function (token) {
        for (var key in topics) {
            if (topics[key]) {
                topics[key].forEach(function (item, index) {
                    if (item.token === token) {
                        topics[key].splice(index, 1);
                        return token;
                    }
                })
            }
        }
        return this;
    }
    // 发布或者广播事件
  q.publish = function (topic, args) {
        if (!topics[topic]) {
            return false;
        }
        var subscribers = topics[topic],
            len = subscribers ? subscribers.length : 0;
        while (len--) {
            subscribers[len].func(topic, args);
        }
        return this;
    }
})(pubsub)
```

使用样例：

```
// 记录通过订阅着收到的主题和数据
var msgLogger = function (topics, data) {
    alert(topics+":" + data);
}
// 订阅对象
var subscription = pubsub.subscribe("inbox/newMsg",msgLogger);
// 发布通知
pubsub.publish("inbox/newMsg","hello world!");
pubsub.publish("inbox/newMsg",['test','message','!']);
pubsub.publish("inbox/newMsg",{
    sender: 'hell@google.com',
    body: 'hey again'
});
// 取消订阅
pubsub.unsubscribe(subscription);
pubsub.publish("inbox/newMsg","you should not see me!");
```