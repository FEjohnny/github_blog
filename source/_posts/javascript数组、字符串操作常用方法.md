---
title: javascript数组、字符串操作常用方法
date: 2017-03-08 17:16:37
tags: [javascript, 数组]
---

# 字符串操作

    1.字符串转换：19.toString()或者19+“”。

    2.字符串分割（返回数组形式）：split（",",length），接收连个参数：分割符，返回的数组长度。

    3.获取字符串长度：str.length。

    4.查询字符串：

        str.indexOf("abc")：从头开始，找到则返回对应下表，有多个匹配则返回最后一个的下标，可接收第二个可选参数，表示开始查询的位置。

        str.lastIndexOf('abc')：从末尾开始查找，找到则返回对应下表，有多个匹配则返回最后一个的下标，可接收第二个可选参数，表示开始查询的位置。
<!--more-->

    5.字符串替换：str.replace("love","hate")：默认只替换第一次查询到的，要想全局替换，可改加上/g，str.replace("love/g","hate")。

    6.查找给定位置的字符：

          str.charAt(2)：从0小标开始查找,返回找到的字符。

          str.charCodeAt(2):从0小标开始查找,返回找到的字符的表码值。

    7.字符串连接：

        ‘+’：‘asd’+'dksdj'。

        concat函数：‘asd’ . concat ('dksdj');可接收多个字符串，’如：‘a’ . concat ( 'b' , 'c' , 'd' ）。

    8.字符串切割和提取：

        str.slice(1,4)：表示截取从第一个到第三个的字符串，‘abcd’.slice(1,4) = 'bcd' 。     

        str.substring(1,4)：结果和slice相同。

        str.substr(1.4)：第一个参数表示开始位置，第二个参数表示截取长度，'abcde'.substr(1,4)='bcde'。

    9.字符串大小写转换：

        str.toLowerCase()转换为小写，str.toUpperCase()转换为大写。

    10.字符串匹配：

        math()：'abcd'.math(/b/)->返回['b']。再者var str='abcd'.math(/b/); str.index=1; str.input='abcd'。

        exec()：/b/.exec('abcd')->返回['b']。再者var str=/b/.exec('abcd'); str.index=1; str.input='abcd'。

    以上连个函数，如果匹配失败，则返回null。

    11.字符串比较：

        str.localeCompare(str1)：以本地特定的顺序进行比较，结果类似于str - str1，大于则返回1，小于返回-1，相等返回0。



# 数组操作：

    1.如何创建数组：new Array()，new Array(2)已知长度时，new Array(1,2, 3)已知数组元素时，var arr=[1,2,3]使用数组字面量表示法创建。

    2.数组操作方法：

        push()：arr.push()，往数组后面添加元素，可接收多个参数。

        pop()：arr.pop()，返回数组的最后一个元素，没有参数。

        unshift()：arr.unshift(),往数组前面添加元素，可接收多个参数。

        shift()：arr.shift()，返回数组的第一个元素，没有参数。

    3.检测验证数组：

        arr instanceof Array。

        Array.isArray(arr)。//该方法只使用与高版本的浏览器：IE9+、Firefox4+/Chrome

    4.slice(start,end)：arr.slice(0,5)返回一个新的数组,第一个参数必须，第二个不传则默认到最后一个。

    5.concat(item)：arr.concat(item)，把item添加到arr后面，返回一个新的数组

    6.splice()：向/从数组中添加/删除元素，然后返回被删除的项目（该方法会改变原始数组）。

        arr.splice(index,number,item1,item2...itemx),前两个参数必须。

        [1,2,3,4,5].splice(2,1,6);//[3]，原数组变为[1,2,6,4,5]

    7.concat()：arr.concat(item1,item2,...itemx)，item传入值或者数组都行，该方法相当于合并数组，返回一个新的数组，不会改变原来的数组。