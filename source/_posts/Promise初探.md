---
title: Promise初探
date: 2018-05-20 19:08:42
tags: [javascript, promise]
---

# 1.Promise概念
&emsp;&emsp;Promise是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。
<!--more-->

# 2.Promise构造函数

```
var promise = new Promise(function(resolve, reject) {
setTimeout(resolve, 100, 'foo');
});
console.log(promise);
```

# 3.Promise基础用法：

定义一个方法，返回一个promise对象。

```
function getId(){
    return new Promise(function (resolve,reject) {
        setTimeout(function () {
            if(true){
                resolve(100);
            } 
            else {
                reject('err');
            }
        },1000);
    });
}
```

### 用法1：then（resolveCallback，rejectCallback）

```
getId().then(function (id) {
    console.log(id);
}, function (reason) {
    console.log(reason);
})
```

### 用法2：catch()来处理失败

```
getId().then(function (id) {
    console.log(id);
}).catch(function (reason) {
    console.log(reason);
});
```

### 链式调用:getNameById也是一个异步方法，通过id异步获取名字

```
getId().then(getNameById).then(function (name) {
    console.log(name);
}).catch(function (err) {
    console.log(err);
});
```

# 4.Promise状态

&emsp;&emsp;pomise有三种状态，分别是pending（初始状态），fulfilled（操作成功），rejected（操作失败）。他们之间的关系如下图：

![](/images/20.png)

# 5.Promise的方法

&emsp;&emsp;Promise.all([p1,p2,p3...]):返回一个新的promise对象，它的参数可以不是一个数组，但是必须具有iterator接口。如果所有的promise对象状态都变成了fulfilled，那么这个新的promise对象的状态才会变成fulfilled，返回这个传入的集合对象的返回值按照原顺序组成一个集合返回；如果这个集合中有一个promise对象的状态变成rejecte，那么新对象的状态立刻变成rejected，并返回第一个失败的promise对象的错误信息。

&emsp;&emsp;Promise.race([p1,p2,p3...]):返回一个新的promise对象，只要iterator中有一个promise对象的状态先改变，就会将其值传递给外层promise对象继续执行下面的操作。

&emsp;&emsp;Promise.reject(reason)：返回一个用reason拒绝的Promise。

```
Promise.reject("Testing static reject").then(function(reason) {
// 未被调用
}, function(reason) {
console.log(reason); // "测试静态拒绝"
});
```

&emsp;&emsp;Promise.resolve(value)：一个状态由value决定的promise对象；如果该值是promise对象，则直接返回该对象；如果该值是thenable(即，带有then方法的对象)，返回的Promise对象的最终状态由then方法执行决定；否则的话(该value为空，基本类型或者不带then方法的对象),返回的Promise对象状态为fulfilled，并且将该value传递给对应的then方法。通常而言，如果你不知道一个值是否是Promise对象，使用Promise.resolve(value) 来返回一个Promise对象,这样就能将该value以Promise对象形式使用。

# 6.Promise的原形

&emsp;&emsp;Promise.prototype.constructor:构造函数。

&emsp;&emsp;Promise.prototype.catch(noRejected):添加一个失败回调到当前到promise实例对象上。

&emsp;&emsp;Promise.prototype.then(onFulFIlled,onRejected):添加一个成功和失败回调到当前promise实例对象上。

&emsp;&emsp;Promise.prototype.finally(onFinally):通过它添加调回调会在promise运行完后被执行，无论运行到结果状态是失败还是成功。