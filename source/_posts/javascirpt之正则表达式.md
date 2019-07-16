---
title: javascirpt之正则表达式
date: 2017-03-05 02:52:14
tags: [javascript, 正则]
---

RegExp对象表示正则表达式，它是对字符串执行模式匹配的工具；

正则表达式的基本语法如下2种：

1. 直接量语法
  /pattern/attributes;
  /pattern/attributes;

2. 创建RegExp对象的语法
  new RegExp(pattern,attributes);
  参数：参数pattern是一个字符串，指定了正则表达式的模式；参数attributes是一个可选的参数，包含属性 g，i，m，分别使用与全局匹配，不区分大小写匹配，多行匹配；
  @return 返回值：一个新的RegExp对象，具有指定的模式和标志；
<!--more-->

# 一.支持正则表达式的String对象的方法

1. search方法；该方法用于检索字符串中指定的子字符串，或检索与正 则表达式相匹配的字符串。

  基本语法：stringObject.search(regexp);

  @param 参数regexp可以需要在stringObject中检索的字符串，也可以 是需要检索的RegExp对象。

  @return(返回值) stringObject中第一个与regexp对象相匹配的子串的起 始位置。如果没有找到任何匹配的子串，则返回-1；

  注意：search()方法不执行全局匹配，它将忽略标志g，同时它也没有regexp对象的lastIndex的属性，且总是从字符串开始位置进行查找，总是返回的是stringObject匹配的第一个位置。

2. match：该方法用于在字符串内检索指定的值，或找到一个或者多个正则表达式的匹配。该方法类似于indexOf()或者lastIndexOf(); 但是它返回的是指定的值，而不是字符串的位置；

  基本语法： stringObject.match(searchValue) 或者stringObject.match(regexp)

  @param(参数) 

  searchValue 需要检索字符串的值；

  regexp: 需要匹配模式的RegExp对象；

  @return(返回值) 存放匹配成功的数组; 它可以全局匹配模式，全局匹配的话，它返回的是一个数组。如果没有找到任何的一个匹配，那么它将返回的是null；返回的数组内有三个元素，第一个元素的存放的是匹配的文本，还有二个对象属性；index属性表明的是匹配文本的起始字符在stringObject中的位置；input属性声明的是对stringObject对象的引用；

3. replace()方法：该方法用于在字符串中使用一些字符替换另一些字符，或者替换一个与正则表达式匹配的子字符串；

  基本语法：stringObject.replace(regexp/substr,replacement);

  @param(参数) 

  regexp/substr; 字符串或者需要替换模式的RegExp对象。

  replacement：一个字符串的值，被替换的文本或者生成替换文本的函数。

  @return(返回值)  返回替换后的新字符串

  注意：字符串的stringObject的replace()方法执行的是查找和替换操作，替换的模式有2种，既可以是字符串，也可以是正则匹配模式，如果是正则匹配模式的话，那么它可以加修饰符g,代表全局替换，否则的话，它只替换第一个匹配的字符串；

  replacement 既可以是字符串，也可以是函数，如果它是字符串的话，那么匹配的将与字符串替换，replacement中的$有具体的含义，如下：

  1,1,2,3....3....99 含义是：与regexp中的第1到第99个子表达式相匹配的文本。

  $& 的含义是：与RegExp相匹配的子字符串。

  lastMatch或RegExp["$_"]的含义是：返回任何正则表达式搜索过程中的最后匹配的字符。

  lastParen或 RegExp["$+"]的含义是：返回任何正则表达式查找过程中最后括号的子匹配。

  leftContext或RegExp["$`"]的含义是：返回被查找的字符串从字符串开始的位置到最后匹配之前的位置之间的字符。

  rightContext或RegExp["$'"]的含义是：返回被搜索的字符串中从最后一个匹配位置开始到字符串结尾之间的字符。

4. split()方法:该方法把一个字符串分割成字符串数组。

  基本语法如：stringObject.split(separator,howmany);

  @param(参数) 

  1. separator[必填项]，字符串或正则表达式，该参数指定的地方分割stringObject; 

  2. howmany[可选] 该参数指定返回的数组的最大长度，如果设置了该参数，返回的子字符串不会多于这个参数指定的数组。如果没有设置该参数的话，整个字符串都会被分割，不考虑他的长度。

  @return(返回值) 一个字符串数组。该数组通过在separator指定的边界处将字符串stringObject分割成子字符串。

# 二、RegExp对象方法

1. test()方法：该方法用于检测一个字符串是否匹配某个模式；

  基本语法：RegExpObject.test(str);

  @param(参数) str是需要检测的字符串；

  @return (返回值) 如果字符串str中含有与RegExpObject匹配的文本的话，返回true，否则返回false；

了解正则中的普通字符，字母，数字，汉字，下划线及一些没有特殊定义的标点符号，都属于普通字符，正则中的普通字符，在匹配字符串的时候，匹配与之相同的字符即可~ 比如如下代码：

```
var str = "abcde";

console.log(str.match(/a/)); // ["a", index: 0, input: "abcde"]
```

如上代码，字符串abcde匹配a的时候，匹配成功，索引位置从0开始；

了解正则中的方括号[]的含义

方括号包含一系列字符，能够匹配其中任意一个字符, 如[abc]可以匹配abc中任意一个字符，使用[^abc]包含的字符abc，则能够匹配abc字符之外的任何一个字符，只能是一个字符。如下的含义：

```
[abc]:  查找在方括号中的任意一个字符；

[^abc]: 查找不在方括号中的任意一个字符；

[0-9]: 查找0-9中的任意一个数字；

[a-z]: 查找从小写a到z中的任意一个字符；

(red|blue|green); 查找小括号中的任意一项，小括号中的 | 是或者的意思；

列举1：表达式[bcd][bcd] 匹配 "abcde"时候，匹配成功，内容是bc，匹配到的位置开始于1，结束与3；如下代码：

```

```
var str = "abcde";

console.log(str.match(/[bcd][bcd]/)); // ["bc", index: 1, input: "abcde"]
```

理解javascript中的元字符

    ·（点）：查找任意的单个字符，除换行符除外。

    \w：任意一个字符或数字或下划线，等价于a_zA_Z0_9,_ 中任何一个。            \W：查找非单词的字符，等价于[^a_zA_Z0_9]。

    \d： 匹配一个数字字符，等价于[0_9]。

    \D：匹配一个非数字字符，等价于[^0_9]。

    \s：匹配任何空白字符，等价于[\t\n\f\r\v]。

    \S：匹配任何非空白字符，等价于[^\t\n\f\r\v]。

    \b：匹配一个单词边界，也就是指单词和空格间的位置，比如’er\b’可以匹配”never”中的”er”,但是不能匹配”verb”中的”er

    \B：匹配非单词边界,’er\B’能匹配’verb’中的’er’,但不能匹配’never’中的’er’


    \0：查找NULL字符。

    \t：匹配一个制表符。