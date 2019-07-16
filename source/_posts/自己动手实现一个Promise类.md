---
title: 自己动手实现一个Promise类
date: 2018-05-27 19:32:35
tags: [javascript, promise]
---

&emsp;&emsp;首先，我们来看一下Promise的基础用法：
```
//定义一个函数，获取用户id
function getUserId() {
return new Promise(function (resolve) {
    //用setTimeout模拟http异步请求
setTimeout(function () {
        if(true){
            resolve(1);
        }
    },1000);
});
}
```
<!--more-->

```
//调用getUserId函数，并通过then方法进行成功回调函数绑定
getUserId().then(function (id) {
console.log('responce id is：'+id);
});
```

然后，根据这个用法，我们可以这样去写一个Promise类，下面是一个Promise的基础实现：

```
function Promise(func) {
var _callback = '';
this.resolve = function(value){
        _callback(value);
}
this.then = function(callback){
    _callback = callback;
}
func(this.resolve);
}
```

但是这里存在两个问题：1.如果在resolve方法执行的时候，callback还没有注册，或者还没有注册完(有很多），这个时候上面的是否还适用?  2.Promise有一个支持链式调用的特性，刚刚的实现会不会有问题? 答案肯定都是否定的，所以我们对Promise类进行修改如下：

```
function MyPromise(func) {
    var _callbacks = [],
         _value = null,
        _status='pending';//引入promise状态变量
  this.resolve = function(value){
        _status = 'fulfilled';
        _value = value;
        _callbacks.forEach(function(callback){
            callback(value);
        });
    }
    this.then = function(callback){
        if(_status === 'pending'){
            _callbacks.push(callback);
            return this; //支持链式调用
     }
         callback(_value); //如果状态已经改变，直接执行回调
     return this;
    }
    func(this.resolve);
}
```

接下来，如果then方法里传入的方法又返回一个Promise对象呢，像下面这样：

```
//根据刚刚获取对id，去异步获取name
function getUserName(id) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            if(true){
                resolve('corlin');
            }
        },1000);
    });
}
```

```
getUserId().then(getUserName).then(function(name) {
    console.log('responce name is：'+name);
});
```

我们对then方法进行改造：

```
function handle(callback) { //Promise内部方法
    if(_status === 'pending'){
        _callbacks.push(callback);
        return;
    }
    //这里很关键，当上一个promise当回调执行之后，
  //将其返回值作为实参，执行它后临promise的resolve方法，
  //它标志者后临的promise异步操作成功，然后这样接力下去。
  var result = callback.callback(_value);
    callback.resolve(result);
}

this.then = function(callback){
    return new Promise(function(resolve){
        handle({
            callback:callback,//当前promise的回调
       resolve:resolve,//后临promise的resolve
        });
    });
}
```

接下来是resolve方法：

```
this.resolve = function(value){
    //处理value是以promise对象，或者是带有then属性的函数
  if(value && (typeof value === 'object' || typeof value === 'function')){
        var then = value.then;
        if(typeof then === 'function'){
            then.call(value,this.resolve);//这里resolve执行作用域为callback对象，所以有resolve属性
       return ;
        }
    }

    _status = 'fulfilled';
    _value = value;
    setTimeout(function(){//将回调放倒后面执行
     _callbacks.forEach(function(callback){
            handle(callback);
        });
    },0);
}
```

最后，加上reject和catch处理：

```
function MyPromise(func) {
    var _callbacks = [],
        _value = null,
        _status='pending';//引入promise状态变量

    this.resolve = function(value){
        //处理value是以promise对象
        if(value && (typeof value === 'object' || typeof value === 'function')){
            var then = value.then;
            if(typeof then === 'function'){
                then.call(value,this.resolve,this.reject);//因为此时resolve执行作用域为callback对象，所以有resolve属性
                return ;
            }
        }

        _status = 'fulfilled';
        _value = value;
        finale();
    }

    function handle(callback) {
        if(_status === 'pending'){
            _callbacks.push(callback);
            return;
        }
        //这里很关键，当上一个promise当回调执行之后，
        //将其返回值作为实参，执行它后临promise的resolve方法，
        //它标志者后临的promise异步操作成功，然后这样接力下去。
        var cb = _status === 'fulfilled' ? callback.onFulFilled : callback.onRejected;
        if(!cb){
            cb = _status === 'fulfilled' ? callback.resolve : callback.reject;
            cb(_value);
            return;
        }
        var result = cb(_value);
        callback.resolve(result);
    }

    this.then = function(onFulFilled,onRejected){
        return new MyPromise(function(resolve,reject){
            handle({
                onFulFilled:onFulFilled,//当前promise的回调
                onRejected:onRejected,
                resolve:resolve,//后临promise的resolve
                reject:reject,
            });
        });
    }

    this.reject = function(reason){
        _status = 'rejected';
        _value = reason;
        finale();
    }

    this.catch = function(func){
        return this.then(null,func);
    }

    function finale() {
        setTimeout(function(){
            _callbacks.forEach(function(callback){
                handle(callback);
            });
        },0);
    }

    func(this.resolve,this.reject);
}
```
