title: JavaScript中的各种模块化规范
date: 2016-12-12 14:11:20
description: 
categories:
- Skill
tags:
- JavaScript
toc: true
author:
comments:
original:
permalink: 
---
　　** 自用笔记：**JavaScript中的各种模块化规范
<!-- more -->

前端发展到今天，已经有不少模块化的方案，比如AMD、CMD、UMD、CommonJS等，当然了，还有es6带来的模块系统，这些模块化规范的核心价值都是让 JavaScript 的模块化开发变得简单和自然，今天就来看看这些规范都是啥。
 
为什么要模块化
 
在模块化这东西没出来之前，前端脚本引用大概是这样的：

![](http://img105.job1001.com/upload/adminnew/2016-08-29/1472449131-7YEI4FQ.jpg)

模块把接口暴露到全局对象下（比如window），各个模块可以通过全局对象访问各个依赖的接口，但是也存在一些问题：
 
1、挂在全局对象下容易产生冲突
2、各个脚本加载的必须严格按照依赖顺序，不然可能就玩不转
3、在一个特别大的项目中，引用的脚本就会特别多，script 标签就会特别多，并且难以维护。比如：

![](http://img105.job1001.com/upload/adminnew/2016-08-29/1472449167-VPKWRRP.jpg)

为了解决这些问题，各种模块化的方案都出来了
 
CommonJS
 
这种方式通过一个叫做require的方法，同步加载依赖，然后返导出API供其它模块使用，一个模块可以通过exports或者module.exports导出API。CommonJS规范中，一个单独的文件就是一个模块。每一个模块都是一个单独的作用域，在一个文件中定义的变量，都是私有的，对其他文件是不可见的。

![](http://img105.job1001.com/upload/adminnew/2016-08-29/1472449185-UZNZAKZ.jpg)

服务端Node.js就是用的这种方式。
 
Well
 
1、服务端模块可以很好的复用
2、这种风格的模块已经很多了，比如npm上基本上都是这种风格的module
3、简单易用
 
Less Well
 
1、加载模块是同步的，所以只有加载完成才能执行后面的操作
2、多个模块不能并行加载
 
像Node.js主要用于服务器的编程，加载的模块文件一般都已经存在本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以CommonJS规范比较适用。但如果是浏览器环境，要从服务器加载模块，这是就必须采用异步模式。所以就有了 AMD 、CMD 的解决方案。
 
AMD: 异步require
AMD === Asynchronous Module Definition
 
介于上面的说到的问题，于是浏览器端的异步版本的require应运而生

![](http://img105.job1001.com/upload/adminnew/2016-08-29/1472449208-QVXLZAM.jpg)

AMD 规范中，define 函数有一个公有属性 define.amd。
 
Well
 
1、解决了模块异步加载的问题
2、解决了多个脚本并行加载的问题
 
Less Well
 
1、代码太过臃肿，不够优雅，难以阅读和书写
2、但是似乎又是某种解决方案
 
AMD被使用的最广泛的实现方案无疑就是 require.js 了
 
CMD表示不服
 
CMD是SeaJS 在推广过程中对模块定义的规范化产出
 
CMD 规范中定义了 define 函数有一个公有属性 define.cmd。
 
CMD 模块中有两种方式提供对外的接口，一种是 exports.MyModule = ...，一种是使用 return 进行返回。
 
CMD和AMD的区别有以下几点：
 
 
1、对于依赖的模块AMD是提前执行，CMD是延迟执行。（require2.0貌似有变化）
 
2、CMD推崇依赖就近，AMD推崇依赖前置。

![](http://img105.job1001.com/upload/adminnew/2016-08-29/1472449242-G712VGJ.jpg)

虽然AMD也支持CMD写法，但依赖前置是官方文档的默认模块定义写法。
推荐一篇文章：SeaJS与RequireJS最大的区别
 
UMD: 通用模块规范
 
UMD是AMD和CommonJS两者的结合,AMD 浏览器第一的原则发展，异步加载模块。CommonJS 模块以服务器第一原则发展，同步加载模块，它的模块无需包装。
 
但是我如果想同时支持两种风格呢？于是通用模块规范（UMD）诞生了。这个模式中加入了当前存在哪种规范的判断，所以能够“通用”，它兼容了AMD和CommonJS，同时还支持老式的“全局”变量规范：

![](http://img105.job1001.com/upload/adminnew/2016-08-29/1472449264-7WVZ6L2.jpg)

 
ES6 modules: 你们都让开，我才是标准
 
ES6为JavaScript添加了一些语言结构，形成另一个模块系统。

![](http://img105.job1001.com/upload/adminnew/2016-08-29/1472449281-6YXKWU2.jpg)

 
Well
 
1、静态分析非常容易
2、未来的标准
 
Less Well
 
1、目前的浏览器大都还不兼容，要想使用这种方式的模块系统，貌似只能借助于转译工具了（比如Babel）
2、这种模式的module目前还很少
 
总结
 
本文主要是介绍了一下 AMD、CMD等规范，较为笼统，下面的扩展阅读可以更好的帮助你理解模块化以及各个规范。

转自 http://www.yl1001.com/group_article/8041472449293730.htm