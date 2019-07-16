---
title: 关于如何计算第n个丑数的一些思考
date: 2018-07-15 11:12:34
tags: [letcode, javascript]
---

&emsp;&emsp;如题：计算第N的丑数的值是多少。

&emsp;&emsp;丑数的定义：只包含因子2,3,5的正整数被称作丑数，比如4,10,12都是丑数，而7,23,111则不是丑数，另外1也是丑数。

&emsp;&emsp;说明：前10个丑数分别是：1， 2， 3， 4， 5， 6， 8， 9， 10， 12。

&emsp;&emsp;例如： 输入n=10，输出12。

<!--more-->

&emsp;&emsp;这道题是我在leetcode上遇到的，首先我的第一反应就是：直接计算从1开始，判断每个数字是否是丑数，找到一个丑数，N就减1，直到N等于0的时候，最后找到的那个丑数，就是我们需要的结果啦。看代码：

```
function fnthUglyNumber(n) {
    var num = 1, result;
    while (n > 0) {
        var temp = num;
        while (temp % 2 === 0) {
            temp = temp / 2;
        }
        while (temp % 5 === 0) {
            temp = temp / 5;
        }
        while (temp % 3 === 0) {
            temp = temp / 3;
        }
        if (temp === 1) {
            result = num;
            n --;
        }
        num ++;
    }
    return result;
}
```
&emsp;&emsp;结果提交解答的时候，提示：超出时间限制。很明显，这种计算方式，当N偏大的时候，将会非常耗时。我测试了一下，当N等于1407的时候大概计算耗时为：8~9秒。所以，这种傻瓜式的计算，肯定是不会通过的啦。

&emsp;&emsp;很明显，这道题肯定是有什么规律的。具体怎么找寻这个规律来编写代码呢？我又想到一点：那就是所有的丑数一定是前面较小的丑数相乘得来，丑数乘以丑数，结果一定是个丑数。来，看下这种方案的代码实现：

```
function fnthUglyNumber(n) {
    var all = [1],
        base = [2,3,5], 
        min, 
        baseLen = base.length;
    while (n > 1) {
        var allLen = all.length;
        min = all[allLen-1] * base[baseLen-1];
        // 每次循环，计算出下一个丑数，push到列表中
     for(var i = 0; i < allLen; i++) {
            for(var j = 0; j < baseLen; j++){
                var res = all[i] * base[j];
                if (res > all[allLen -1] && res < min) {
                    min = res;
                }
            }
        }
        all.push(min);
        n--;
    }
    return all[all.length-1];
}
```

&emsp;&emsp;OK，提交之后通过了程序的验证。再次测试输入1407的时候，几乎可以在瞬间计算出结果。