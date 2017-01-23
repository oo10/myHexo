title: __proto__与prototype
date: 2016-10-15 13:58:41
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
　　** 详解JavaScript中的__proto__与prototype.
<!-- more -->

先看一段代码
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div><div class="line">4</div><div class="line">5</div></pre></td><td class="code"><pre><div class="line">Function instanceof Object // true </div><div class="line">Object instanceof Function // true </div><div class="line">Function instanceof Function //true </div><div class="line">Object instanceof Object // true </div><div class="line">Number instanceof Number //false</div></pre></td></tr></table></figure>

<a id="more"></a>

再来看一段
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div><div class="line">4</div><div class="line">5</div><div class="line">6</div><div class="line">7</div><div class="line">8</div><div class="line">9</div><div class="line">10</div><div class="line">11</div><div class="line">12</div><div class="line">13</div><div class="line">14</div><div class="line">15</div><div class="line">16</div><div class="line">17</div></pre></td><td class="code"><pre><div class="line">var obj = &#123; a : 1 &#125;; </div><div class="line">console.log(obj.__proto__ === Object.prototype); // true </div><div class="line"></div><div class="line">var str = new String(&apos;123&apos;); </div><div class="line">console.log(str.__proto__ === String.prototype); // true </div><div class="line"></div><div class="line"></div><div class="line">function Point()&#123;&#125;; </div><div class="line">var Circle = Object.create(Point); </div><div class="line">console.log(Circle.__proto__ === Point); // true </div><div class="line">console.log(Circle.__proto__ === Point.prototype); // false </div><div class="line"></div><div class="line">var p = new Point(); </div><div class="line">console.log(Point.__proto__); // function() </div><div class="line">console.log(Point.prototype); // Point &#123;&#125; </div><div class="line">console.log(p.__proto__); // Point &#123;&#125; </div><div class="line">console.log(p.prototype); // undefined</div></pre></td></tr></table></figure>

很明显，__proto__与prototype看起来似乎很相似，但是实际上是不同的。
先了解一下各自是什么，再进一步探讨。

### [](#prototype-显示原型 "prototype 显示原型")prototype 显示原型

每一个函数在创建之后都会拥有一个名为prototype的属性，这个属性指向函数的原型对象。(通过Function.prototype.bind方法构造出来的函数是个例外，它没有prototype属性。)

### [](#proto-隐式原型 "__proto__ 隐式原型")__proto__ 隐式原型

JavaScript中任意对象都有一个内置属性[[prototype]]，在ES5之前没有标准的方法访问这个内置属性，但是大多数浏览器都支持通过**proto**来访问。ES5中有了对于这个内置属性标准的Get方法Object.getPrototypeOf().
Note: Object.prototype 这个对象是个例外，它的__proto__值为null 

### [](#二者关系 "二者关系")<span id="two_rel">二者关系</span>

隐式原型指向创建这个对象的函数(constructor)的prototype
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div></pre></td><td class="code"><pre><div class="line">function test()&#123;&#125;;</div><div class="line">var a = new test();</div><div class="line">console.log(a.__proto__ === test.prototype) ;// true</div></pre></td></tr></table></figure>

### [](#梳理 "梳理")梳理

JavaScript里万物皆对象，方法（Function）是一种特殊的对象。
由上述的定义可知，任意对象都有都有__proto__,而prototype只有函数创建之后自己才有,通过该函数创建的对象是没有的。
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div><div class="line">4</div></pre></td><td class="code"><pre><div class="line">function test()&#123;&#125;;</div><div class="line">var a = new test();</div><div class="line">console.log(test.prototype); // test&#123;&#125;</div><div class="line">console.log(a.prototype); // undefined</div></pre></td></tr></table></figure>

而Object.prototype这个特殊的对象的__proto__位于原型链金字塔的顶端,为null，如下图
![图形化原型链](/ProtoType/1.png "图形化原型链")
这里值得一提的是JavaScript的原型链中原型对象prototype之间是通过__proto__联系起来的
![__proto__](/ProtoType/3.png "__proto__")
同时原型对象prototype中都有个预定义的constructor属性，用来引用它的函数对象。这是一种循环引用

<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div></pre></td><td class="code"><pre><div class="line">person.prototype.constructor === person //true</div><div class="line">Function.prototype.constructor === Function //true</div><div class="line">Object.prototype.constructor === Object //true</div></pre></td></tr></table></figure>

#### [](#这一点在本文后面的图中也有显示，Click "这一点在本文后面的图中也有显示，Click")这一点在本文后面的图中也有显示，[Click](#last_pic)

### [](#有两点需要注意： "有两点需要注意：")有两点需要注意：
> （1）注意Object.constructor===Function；//true 本身Object就是Function函数构造出来的
> （2）如何查找一个对象的constructor，就是在该对象的原型链上寻找碰到的第一个constructor属性所指向的对象

再看上面一截代码中的部分
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div></pre></td><td class="code"><pre><div class="line">function Point()&#123;&#125;; </div><div class="line">var Circle = Object.create(Point); </div><div class="line">console.log(Circle.__proto__ === Point); // true</div></pre></td></tr></table></figure>

Object.create(),这是ES5中新增的方法，在这之前这被称为原型式继承,我们可以理解为 new Object(),这样我们就很好理解结果true了，实际上，上述代码可以等价理解为
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div></pre></td><td class="code"><pre><div class="line">function Point()&#123;&#125;;</div><div class="line">var Circle = new Point();</div><div class="line">console.log(Circle.__proto__ === Point); // true</div></pre></td></tr></table></figure>

等价的原因在上面[**二者关系**](#two_rel)有提到。
也可以从constructor来理解
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div></pre></td><td class="code"><pre><div class="line">console.log(Circle.constructor); //function Point()</div><div class="line">console.log(Circle.constructor.prototype ===  Circle.__prototype) // true</div></pre></td></tr></table></figure>

### [](#instanceof "instanceof")instanceof

instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。

#### [](#语法 "语法")语法
<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div></pre></td><td class="code"><pre><div class="line">object instanceof constructor</div></pre></td></tr></table></figure>

#### [](#参数 "参数")参数

object
    要检测的对象.

constructor
    某个构造函数

#### [](#描述 "描述")描述

instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上。

<figure class="highlight plain"><table><tr><td class="gutter"><pre><div class="line">1</div><div class="line">2</div><div class="line">3</div><div class="line">4</div></pre></td><td class="code"><pre><div class="line">//设 L instanceof R </div><div class="line">//通过判断</div><div class="line"> L.__proto__.__proto__ ..... === R.prototype ？</div><div class="line">//最终返回true or false</div></pre></td></tr></table></figure>

也就是沿着L的__proto__一直寻找到原型链末端，直到等于R.prototype为止,如果一直到Object.prototyoe都没找到，则返回false。就此，我们结合下面这幅图再剖析一下文章开头部分的代码段就很容易理解了
<span id="last_pic"></span>
![__proto__与prototype](/ProtoType/2.jpg "__proto__与prototype")

### [](#参照知乎知乎–js中proto和prototype的区别和关系？整理得出，加入部分个人见解，有错之处望各位大牛斧正 "参照知乎知乎–js中proto和prototype的区别和关系？整理得出，加入部分个人见解，有错之处望各位大牛斧正")参照知乎[知乎–js中**proto**和prototype的区别和关系？](http://www.zhihu.com/question/34183746)整理得出，加入部分个人见解，有错之处望各位大牛斧正