title: 周会分享- 前端黑魔法
date: 2016-11-21
description: 
categories:
- HTML
tags:
- 周会分享
toc: true
author:
comments:
original:
permalink: 
---
　　** 实习期间周会分享内容

<!-- more -->

![](https://img.alicdn.com/imgextra/i1/1876943437/TB2fja2el9J.eBjy0FoXXXyvpXa_!!1876943437.png_620x10000.jpg)

---
## 链接嵌套


我们都知道在前端开发当中，超链接`<a>`标签嵌套`<a>`标签会出问题：浏览器解析的时候，会自动闭合标签，将它们解析成不嵌套的。

比如:
```html
<a href="#url1">When the crisis was over,  
  <a href="#url2">Mr.Y</a>    
    left the region immediately.</a>
```

浏览器解析后的HTML会变成这样:
```html
<a href="#url1">When the crisis was over,</a>
  <a href="#url2">Mr.Y</a>        
    left the region immediately.
```
![](https://img.alicdn.com/imgextra/i1/1876943437/TB2_uOpemiJ.eBjSspiXXbqAFXa_!!1876943437.png)

而实际上我们需要的是:
```html
<a href="#url1">When the crisis was over,</a>  
<a href="#url2">Mr. Jones</a> 
<a href="#url1">left the region immediately.</a>
```
曾经为了达到此的效果，只能这样老实的写HTML代码，然而是存在一定的黑魔法的。
那就是给`<a>`标签内部的`<a>`标签外添加一个`<object>`标签。如下所示：

```html
<a href="#url1">When the crisis was over,  
  <object><a href="#url2">Mr.Y</a></object>    
    left the region immediately.</a>
```

浏览器解析后的HTML:
```html
<a href="#url1">When the crisis was over,</a>
<a href="#url2">Mr.Y</a>
<a href="#url1">left the region immediately.</a>
```

效果:

![](https://img.alicdn.com/imgextra/i2/1876943437/TB2qd5teg9J.eBjSsppXXXAAVXa_!!1876943437.png)

---
## 图片自适应


官方对 `object-fit` 和 `object-position` 的解释:
>The object-fit CSS property specifies how the contents of a replaced element should be fitted to the box established by its used height and width.

>The object-position property determines the alignment of the replaced element inside its box.
这里的object实际上指的是replaced element,也就是说这俩属性只针对替换元素有作用，比如img图片、HTML5 video等...
什么是替换元素???比如img标签，它本身没有实际的内容，通过src属性来判断显示具体的内容进行替换。

```css
img {
    width: 120px;
    height: 180px;
    border: 1px solid;
    background-color: #eee;
}

```
我们来看效果，原始图片:

![](https://img.alicdn.com/imgextra/i1/1876943437/TB2H984eQWM.eBjSZFhXXbdWpXa_!!1876943437.png)

应用了各个属性之后的图片，可以对照示意感受下，什么是内容拉伸(fill)，什么是内容全部都显示(contain)，什么是容器没有留白(cover)，什么是该多大就多大(none)。


![](https://img.alicdn.com/imgextra/i4/1876943437/TB2GzR_eNeK.eBjSZFlXXaywXXa_!!1876943437.png)

`scale-down` 由于具有动态特性，所以要专门展示下。实际的替换内容表现是 `none` 和 `contain` 最终尺寸小的那个属性值的表现。
例如，假设我们的容器可以resize拉伸，改变其尺寸，则当容器尺寸比400*400小的时候，也就是容器比替换内容（这里的图片）实际尺寸小的时候，效果跟contain一致，因为此时contain的实际展示尺寸比none小。如下所示：

![mahua](https://img.alicdn.com/imgextra/i1/1876943437/TB2Ll7FdM9J.eBjSsppXXXAAVXa_!!1876943437.png)

但是，当容器尺寸拉伸到比图片实际尺寸还大的时候，则效果跟none一致，因为此时none的实际展示尺寸比contain小。如下所示：

![](https://img.alicdn.com/imgextra/i3/1876943437/TB2fcIJdH5K.eBjy0FnXXaZzVXa_!!1876943437.png)

* fill: 中文释义“填充”。默认值。替换内容拉伸填满整个content box, 不保证保持原有的比例。
* contain: 中文释义“包含”。保持原有尺寸比例。保证替换内容尺寸一定可以在容器里面放得下。因此，此参数可能会在容器内留下空白。
* cover: 中文释义“覆盖”。保持原有尺寸比例。保证替换内容尺寸一定大于容器尺寸，宽度和高度至少有一个和容器一致。因此，此参数可能会让替换内容（如图片）部分区域不可见。
* none: 中文释义“无”。保持原有尺寸比例。同时保持替换内容原始尺寸大小。
* scale-down: 中文释义“降低”。就好像依次设置了none或contain, 最终呈现的是尺寸比较小的那个。

![](https://img.alicdn.com/imgextra/i3/1876943437/TB2V7SmemuJ.eBjy0FgXXXBBXXa_!!1876943437.png)

--- 
## CSS Shapes
布局一般都是遵循按行和列等线性原则，Web网站的布局到今天为止很大程度上受到这些原则的影响。
虽然 [CSS Grid](https://css-tricks.com/snippets/css/complete-guide-grid/) 的出现让布局变得更好，更灵活，但相对于印刷媒体而言，Web布局总体上还是受到很好的限制，特别是内容流上。杂志和报纸一直有很好的办法来安排内容。比如文本绕着非矩形排列。

CSS Shapes使网页设计师能更好的实现自己的创意，除了简单的长方形和正方形，还可以实现任何几何图形布局。改规范提供了两个新的CSS属性： `shape-outside` 和 `shape-margin` 。浏览器支持这些规范是有一定道理的，尽管这些属性目前仅可以在Chrome、Opera和Safari看到效果，而且还需要带上浏览器的私有属性 `-webkit` ,因此使用的是`-webkit-shape-outside` 和 `-webkit-shape-margin` 。


![](https://img.alicdn.com/imgextra/i4/1876943437/TB2djpjffOM.eBjSZFqXXculVXa_!!1876943437.jpg)


### 创建一个图形

创建一个圆是CSS创建一个图形（不是矩形）最简单的。使用一个div来制作这个圆，并且在这个圆的边上放置几个段落p。


```html
<div class="circle"></div>
<p>Lorem ipsum…</p>
<p>Lorem ipsum…</p>
<p>Lorem ipsum…</p>
```

写一点基本样式，给`.circle` 元素设置一个相同的 `width` 和 `height` ，使用 `border-radius` 将 div 变成一个圆，并且设置一个 `float` ，让段落围着这个元素。

```css
.circle { 
    width: 250px;
    height: 250px;
    background-color: #40a977;
    border-radius: 50%;
    float: left;
}
```
除了段落现在围绕着该元素 `.circle` 之外，文本还是没有变成曲线绕着圆形。那是由于 `border-radius` 属性实际上并无法改变一个元素形状，它依旧是一个矩形。

通过浏览器开发者工具（DevTools）的审查元素可以发现，事实上 `.circle` 仍然是一个盒子。即使这个 div 的外观看上去是一个圆，但实际上它没有元素看到的实际形状。

![](https://img.alicdn.com/imgextra/i4/1876943437/TB2ZJ8pfgCN.eBjSZFoXXXj0FXa_!!1876943437.jpg)

为了让段落呈现成一个圆形，实际上我们就需要通过 `shape-outside` 属性改变元素的实际形状。在本例中，将给 `shape-outside` 设置一个 `circle()` 属性值。

```css
.circle {
    width: 250px;
    height: 250px;
    background-color: #40a977;
    border-radius: 50%;
    float: left;
    -webkit-shape-outside: circle();
    shape-outside: circle();
}
```
这个时候，再使用开发者工具审查元素，不难发现元素正确渲染成一个圆。

![](https://img.alicdn.com/imgextra/i2/1876943437/TB2HsJlfXOP.eBjSZFHXXXQnpXa_!!1876943437.jpg)


### 自定义圆

`circle()` 函数可以定义圆的半径和圆心位置： `circle(r at x y)` 。默认情况，半径值来源于元素容器大小，如果元素宽度是 `300px` ，那么圆的半径就是其宽度的一半 `150px` 。

同样，x和y坐标也是相对于元素尺寸。默认的坐标是 `50% 50%` ，也就是元素的正中间。

![](https://img.alicdn.com/imgextra/i2/1876943437/TB2WY5nehmJ.eBjy0FhXXbBdFXa_!!1876943437.jpg)

当你想保持元素实际的大小，形状和位置，而又想调整形状，这两个值相当有用。以下面的示例中，将圆的半径调整为 `60px` ，圆心坐标调整为 `30% 70%` ,相当于把圆调整到元素的左下角。

![](https://img.alicdn.com/imgextra/i3/1876943437/TB2KfewemqJ.eBjy1zbXXbx8FXa_!!1876943437.jpg)

写法上需要注意的是：
```
circle( 60px at 30% 70% )  // 正确写法
circle( 60px )  // 无效写法
circle( at 30% 70% )  // 无效写法
```

### 图形盒模型

CSS Shapes继承了元素相同的盒模型，但应用元素以外的范围。比如说，允许我们把元素的 `border-box` 设置为 `padding-box` 。改变图形盒型只需要给shape-outside属性上添加盒模型的关键字：`content-box、margin-box、border-box` 或 `padding-box` 。

```css
.circle {
    width: 250px;
    height: 250px;
    background-color: #40a977;
    border-radius: 50%;
    float: left;
    padding: 10px;
    border: 20px solid #ccc;
    margin: 30px;
    -webkit-shape-outside: circle() padding-box;
    shape-outside: circle() padding-box;
}
```

![](https://img.alicdn.com/imgextra/i1/1876943437/TB2E7eheb1K.eBjSsphXXcJOXXa_!!1876943437.jpg)

### 椭圆:ellipse()函数

```css
.ellipse {
  width: 100px;
  height: 200px;
  background-color: #A4F4B0;
  border-radius: 50%;

  shape-outside: ellipse();
  float: left;
}
```
![](https://img.alicdn.com/imgextra/i2/1876943437/TB22wqvehmI.eBjy0FlXXbgkVXa_!!1876943437.jpg)

椭圆就像是一个圆坐下或者被压扁了而已。因此 `ellipse()` 函数的正式语法是:

```
ellipse( [<shape-radius>{2}]? [at <position>]? )

```

### 矩形:inset()函数

```css
.inset {
  width: 200px;
  height: 160px;
  background-color: #A4F4B0;
  border-radius: 50px;

  shape-outside: inset(0px round 50px);
  float: left;
}
```
![](https://img.alicdn.com/imgextra/i4/1876943437/TB2w_xrfmiK.eBjSZFsXXbxZpXa_!!1876943437.jpg)

`shape-arg` 接受变量值，按照我们给 `margin` 或者 `padding` 的缩写的方式，以上、右、下、左的顺序，我们可以传入1个、2个或四个值。inset被应用到从元素边缘到中心。
`inset()` 函数的正式的语法:
```
inset( <shape-arg>{1,4} [round <border-radius>]? )

```

### 多边形：plygon()函数

```css
.polygon {
  width: 200px;
  height: 200px;
  clip-path: polygon(0 0, 0 200px, 200px 100px);
  background-color: #A4F4B0;

  shape-outside: polygon(0 0, 0 200px, 200px 100px);
  float:left;
}
```
![](https://img.alicdn.com/imgextra/i2/1876943437/TB2.4amebaI.eBjSszdXXaB6XXa_!!1876943437.jpg)

`polygon()` 函数允许你通过使用坐标值对作为参数来声明自定的形状，正式的语法是:


```
polygon( [<fill-rule>,]? [<shape-arg> <shape-arg>]# )
```
每对坐标都是你期望的形状的顶点，至少有三个点才有效。在示例代码中，使用 [clip-path](http://www.w3cplus.com/css3/creating-responsive-shapes-with-clip-path.html) 裁掉元素在多边形外的一部分来创建一个多边形，所有你可以看到文本很好的环绕在周围。

![](https://img.alicdn.com/imgextra/i2/1876943437/TB2_vVvfmmK.eBjSZPfXXce2pXa_!!1876943437.png)




---
## 选中连续的几个元素
```css
ol li:nth-child(n+7):nth-child(-n+14) {
    background: lightpink;
}

/** Or Safari Way **/

ol li:nth-child(-n+14):nth-child(n+7) {
    background: lightpink;
}
```


---
## css自定义属性（css变量）

* 为风格统一而使用颜色变量
* 一致的组件属性（布局，定位等）
* 避免代码冗余
* 动态性，可以在运行时更改
* 可以方便的从JS中读/写
* 可继承，可组合，同时具有作用域

### 第一个CSS变量
令人感到惊讶的是，你们可能已经了解或使用过一个CSS变量（可以看做是第一个）—— `currentColor`，它并不出名，但仍然可用，并且在所有浏览器工作。
```css
:root {
    color: red;
}
div {
    /* border-color is red */
    border: 1px solid currentColor;
}
```

###CSS变量语法
#### 定义

用这样的方式来声明一个变量：`--variable-name: variable-value` ;（变量名是大小写敏感的）。变量的值可以是颜色、字符串、多个值的组合等:

```css
:root{
    --main-color: #4d4e53;
    --main-bg: rgb(255, 255, 255);
    --logo-border-color: rebeccapurple;
    --header-height: 68px;
    --content-padding: 10px 20px;
    --base-line-height: 1.428571429;
    --transition-duration: .35s;
    --external-link: "external link";
    --margin-top: calc(2vh + 20px);
}
```
可能语法看起来有些丑陋，却是有原因的。比如不能使用像$var这样的变量语法，因为已经被其他的CSS预处理程序使用了。

#### 用法

以这样的方式来使用一个变量: `some-css-value: var(--variable-name [, declaration-value])` ;
```css
p {
    margin: var(--p-margin, 0 0 10px);
}
```

如果`--p-margin`没有被指定，则使用值：`0 0 10px`。这样的设计在使用时非常灵活--你可以使用一些来自某个框架的变量（通常大部分变量定义在这里），同时当你想要移除它的时候，可以保持现有功能正常工作。

#### 作用域

自定义属性遵循[CSS级联规则](https://drafts.csswg.org/css-cascade-4/#cascade)

使用`:root` 作用域来定义全局变量：
```css
:root{
    --global-var: 'global';
}
```

如果想让某个变量只在部分元素/组件下可见，只需要在特定的元素下定义该变量：
```html
<div class="block">
  My block is
  <div class="block__highlight">awesome</div>
</div>
```
```css
.block {
  --block-font-size: 1rem;
  font-size: var(--block-font-size);
}
.block__highlight {
  --block-highlight-font-size: 1.5rem;
  font-size: var(--block-highlight-font-size);
}
```

媒体查询、伪类等同样也可以提供作用域：
```css
@media screen and (min-width: 1025px) {
  :root {
    --screen-category: 'desktop';
  }
}
```
```html
body {
  --bg: #f00;
  background-color: var(--bg);
  transition: background-color 1s;
}
```
```css
body:hover {
  --bg: #ff0;
}
```

#### 变量组合

变量可以和其他变量组合使用，`--variable-name: var(--another-variable-name)` ;

```css
.block {
    --block-text: 'This is my block';
    --block-highlight-text: var(--block-text)' with highlight';
}

.block:before {
    content: var(--block-text);
}

.block__highlight:before {
    content: var(--block-highlight-text); /*This is my block with highlight*/
}
```

声明新变量的值不能直接由一个已定义的变量计算而来，但我们可以使用CSS calc()来代替:
```css
.block {
    --block-font-size: 1rem;
}

.block__highlight {
    /* DOESN'T WORK */
    --block-highlight-font-size: var(--block-font-size)*1.5;
    font-size: var(--block-highlight-font-size);

    /* WORKS */
    font-size: calc(var(--block-font-size)*1.5);
}
```

#### 值的计算（calc()）

不能简单的这样使用变量：
```css
/* 错误 */
padding: var(--spacer)px
```
但借助calc()即可以实现上面功能以及其他的计算
```css
margin: 0 0 calc(var(--base-line-height, 0) * 1rem);
```

#### 在JS中使用原生属性

使用CSS样式声明接口,可以在JS中方便的读/写自定义属性( `getPropertyValue` ,  `setProperty` ):

```js
// READ
const rootStyles = getComputedStyle(document.documentElement);
const varValue = rootStyles.getPropertyValue('--screen-category').trim();

// WRITE
document.documentElement.style.setProperty('--screen-category', value);
```
```js
// GET
alert(
    getComputedStyle(document.documentElement).getPropertyValue('--screen-category').trim();
);

// SET
document.documentElement.style.setProperty('--screen-category', 'custom');

// or reassign from an another prop
document.documentElement.style.setProperty(
    '--screen-category', 'var(--default-screen-category, '%DEFAULT VALUE IF VAR IS NOT SET%')'
);
```

### 例子

[Theme switcher...](https://blog.hospodarets.com/demos/css-custom-props-theme-switcher/)

[Generate colors...](https://blog.hospodarets.com/demos/css-colors-from-custom-props/)


![](https://img.alicdn.com/imgextra/i3/1876943437/TB2yNVgaI2DjeFjSspnXXb20XXa_!!1876943437.png)



---
## 逐帧动画

动画特效在Web的使用中越来越频繁，而且要求也越来越高，希望看到的效果是平滑细腻的。比如鼠标悬浮效果，希望描述一个运动的幻觉。而要做到这样的效果，我们就需要在一个元素上使用一系列的图片。比如Twitter上的点赞动画。

![](https://img.alicdn.com/imgextra/i1/1876943437/TB2SiejebeI.eBjSspkXXaXqVXa_!!1876943437.gif)

类似于小人书，将其运用到Web动画中来是一样的道理，需要一张雪碧图，这图上放置了动画所效果对应的图片，当然，图片越多，效果越平滑细腻。常把这样的图称之为雪碧图。

![](https://img.alicdn.com/imgextra/i2/1876943437/TB2wk4qfiGO.eBjSZFjXXcU9FXa_!!1876943437.png)

有图还不够，这里最主要的是使用 `animation-timing-function` 中的 `steps()` 功能。对于大多数timing function而言（比如 `cubic-bezier` ），过度的开始和结束都会很平滑。但是steps方法不同，它把过度切分成很多步。每一步的区别很明显。

![](https://img.alicdn.com/imgextra/i2/1876943437/TB2YRRDflaM.eBjSZFMXXcypVXa_!!1876943437.png)

```css
.fave { 
    width: 70px;  
    height: 70px; 
    background-size: 50em 2em; 
    background: url(images/fave.svg) no-repeat;  background-position: 0 0; 
} 

.fave:hover { 
    background-position: -50em 0; 
    transition: background 1s steps(55);  
}
```

驱动背景图片精灵生成动画只是steps的一种使用方法。任何使用一些列特定步骤的动画都可以用steps生成，比如做一个时钟。

使用CSS的Sprites图配合 `animation-timing-function` 或者 `transition-timing-function` 的 `steps()` 做的动画起来越多，效果越来越复杂。我们也常将这样的效果运用到自己的实际业务当中。

比如 [手淘年货节舞龙](http://www.w3cplus.com/animation/dragon-dance-opening-animation.html) 动画。

![](https://img.alicdn.com/imgextra/i2/1876943437/TB2Qiaxeb1K.eBjSszbXXcTHpXa_!!1876943437.gif)



#### 本文部分内容参考、整理于以下网站博文:

w3cplus：http://www.w3cplus.com/

zhangxinxu: http://www.zhangxinxu.com/

smashing：https://www.smashingmagazine.com/2016/03/dirty-tricks-dark-corners-front-end-slides-pdf/

hackernoon：https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f#.gpz7sj5ny


#### 周会PPT地址: 

https://pan.baidu.com/s/1nvNgIQP

