title: JS 中的进制转换
date: 2017-4-17 16:22:00
description:
categories:
- JavaScript
tags:
- JavaScript
toc: true
author:
comments:
original:
permalink:
---
在 JS 中用到二进制或者16进制的机会并不多，但如果用涉及到与 server 进行 socket 通信，是需要将我们所熟悉的十进制转换成相应的二进制或者16进制。

<!-- more -->



Number.prototype.toString(radix)

JavaScript 提供了很好的方法给我们可以很方便的进行进制转换，其中 radix 是 2-36 之间的基数，即允许我们转换成相应进制的值，如果没有这个值，该方法将默认换数字为字符串。

现在我们试下几个简单的例子

// 错误的写法
10.toString()

// 正确的写法
10..toString()
这里需要注意，当我们直接用数字而非变量时，是不可以直接调用方法 toString。这样会报以下错误：

Uncaught SyntaxError: Invalid or unexpected token
下面我们将列举转换成二进制、16进制的例子

// 二进制
10..toString(2)     => "1010"

// 8进制
10..toString(8)     => "12"

// 16进制
10..toString(16)    => "a"
当我们想要将 8进制或者 16进制转换成10进制或者二进制，不可以直接使用方法 toString，
由于上面的例子并非补全8进制或者16进制的格式，因而当我们想要将其转换成十进制时，需先补全格式，再进行转换，如以下例子：

// 8进制转换为10进制
012.toString(10)     => 10

// 16进制转换为10进制
0x000a.toString(10)  => 10
JS 中的进制转换还是很方便的，对于8进制或者其他进制的数，直接使用时，系统会默认转换为10进制的数字。比如以下例子：

// 8进制相加
012 + 013 = 21

// 16进制相加
0x000a + 0x000b = 21

// 8进制和16进制相加
012 + 0x000b = 21
JS 与 C 或者 C++不同的，它主要以十进制进行运算，对于弱类型语言来讲，进制的重要性虽不那么高，但当我们于 server 进行通信时，需要重点关注一下。