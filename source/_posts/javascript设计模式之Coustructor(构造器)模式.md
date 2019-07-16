---
title: javascript设计模式之Coustructor(构造器)模式
date: 2018-09-02 17:11:45
tags: [设计模式, javascript]
---

&emsp;&emsp; 在经典面向对象编程语言中，Coustructor是一种在内存已分配给该对象的情况下，用于初始化新创建对象的特殊方法。而在javascript中，几乎所有东西都是对象，通常我们最感兴趣的的object构造器；object构造器用于创建特定类型的对象，同时可以构造器接手参数以在第一次创建对西那个时设置成员属性和方法值。

<!--more-->

constructor(构造器)模式

![](/images/27.jpg)

# 一、对象创建

javascript中创建对象的两种常用方法：

```
var newObject = {};   //采用自变量方式创建一个新的空对象
```

```
var newObect = new Object();   //object构造器的简洁方式
```

将键值赋给对象的四种方式：

```
ES3
newObject.someKey = 'some value';  // 点号赋值
newObject[someKey] = 'some value'; // 中括号赋值
```

```
ES5
Object.defineproperty(newObject, 'someKey', {  // 定义一个key值
       value: 'some value',  // value值
       writable: true,   // 是否可写
       enumerable: true,  // 是否可枚举
       configurable: true,  // 是否可配置
});
Object.definePropertyies(newObject, { // 同时定义的多个key值
       "someKey": {
              value: 'some value',
              writable: true
       },
       "anotherKey": {
              value: 'some value',
              writable: true
       },
}
```

# 二、基本Coustructor（构造器）

&emsp;&emsp; 正如我们前面所看到的，javascript不支持类的概念，但它确实支持与对象一起用的特殊Coustructor函数。通过在构造器前面加new关键字，告诉javascript使用构造器实例化一个新对象，并且对象成员由该函数定义。在构造器内，关键字this指向该对象的引用。看下面这个列子：

```
function Person(name, age) {
       this.name = name;
       this.age = age;
       this.speak = functino() {
              console.log('my name is: ' + this.name); 
      }
}
var me = new Person('chenjun', 17); 
var you = new Person('Guest', 18);
me.speak();
you.speak();
```

&emsp;&emsp; 上面是一个简单构造器版本，但它确实存在一些问题。一个是它使继承变得困难，另一个是里面但speak方法，每new一个Person的实例的时候，都会新建一个speak方法，而它如果能在所有实例对象直接共享才是最好的。

# 三、带原型的Coustructor（构造器）

javascript中又一个名为prototype的属性（注意，它是构造器的专有属性哦）。调用javascript构造器创建一个新对象后，新对象就会具有构造器原型的所有属性。通过这种方式，可以创建多个Person的实例对象，并访问相同的原型。请看

```
function Person(name, age) {
       this.name = name;
       this.age = age;
}
Person.speak = functino() {
       console.log('my name is: ' + this.name); 
}
var me = new Person('chenjun', 17); 
var you = new Person('Guest', 18);
me.speak();
you.speak();
```

 现在speak的单一实例就能在多个Person的实例对象直接共享。
