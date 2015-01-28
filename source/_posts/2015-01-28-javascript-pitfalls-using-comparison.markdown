---
layout: post
title: "Truthy和Falsy：在JavaScript中进行比较时可能会遇到的陷阱"
date: 2015-01-28 17:15:31 +0800
comments: true
categories:
- JavaScript
---

和大多数的计算机语言一样，JavaScript支持Boolean数据类型。但需要多说一句的是，JavaScript有自己的内在Boolean值，truthy和falsy。处理truthy和falsy值可能会遇到一些陷阱，尤其是和变量进行比较时。理解这些古怪的规则可以在debug时给我们很大的帮助。

![Truthy&Falsy](/images/2015/js-truthy-falsy.png)

<!-- more -->

###Truthy和Falsy值

下列值是falsy：

- `false`
- `0`
- `""`
- `null`
- `undefined`
- `NaN`

其他所有的值都是truthy，包括`"0"`，`"false"`，空的function，空的array，空的object。

###比较运算符

比较运算符：`==`

判断value是否相等，忽略type。

比较运算符：`===`

判断value和type是否相等。如果可以的话尽可能使用该运算符，使用`==`可能会导致某些逻辑错误。

###进行比较时可能会遇到的陷阱

**false**，**0**和**""（空string）**在比较时被当作相等的。因此尽可能使用`===`。

```js
typeof(false);
"boolean"
typeof(0);
"number"
typeof("");
"string"

0 == false;
true
0 === false;
false
0 === true;
false

"" == false;
true
"" === false;
false
"" === true;
false

0 == "";
true
0 === "";
false
```

**null**和**undefined**在比较时被当作相等，和其他值不相等。如果一个变量没有被声明或被定义（例如一个argument被指向一个不会返回任何值的function，一个对象没有被分配值），那么这个变量会被赋予一个特殊值：undefined。因此尽可能使用`===`。

```js
typeof(null)
"object"
typeof(undefined)
"undefined" //as a string

null == false;
false
null === false;
false
null == true;
false
null === true;
false

undefined == false;
false
undefined === false;
false
undefined == true;
false
undefined === true;
false

null == undefined;
true
null === undefined;
false
```

**NaN**是一个特殊的数字值，意思是Not-a-Number，是算数运算产生invalid结果导致的。和任何值都不相等。尽可能使用**isNaN()**判断是否为**NaN**。

```js
var x = 0/0;

x;
NaN

typeof(NaN);
"number"

isNaN(NaN);
true
```

**注意**：NaN不等于它自己！

```js

NaN == NaN;
false
NaN === NaN;
false
```
~~啊，吃我一记大坑~~ 知道这些陷阱可以有效提高工作效率。

**参考文献**

[Truthy and Falsy: When All is Not Equal in JavaScript](http://www.sitepoint.com/javascript-truthy-falsy/)
