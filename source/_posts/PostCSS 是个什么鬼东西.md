title: PostCSS是个什么鬼东西？
date: 2017-2-11 11:21:00
description:
categories:
- CSS
tags:
- CSS
toc: true
author:
comments:
original:
permalink:
---
最近有点解到关于PostCSS的一些东西，所以这篇文章将按一个思考和学习的角度来理解一下 PostCSS 到底是一个什么东西。

<!-- more -->

## 一、提出不懂的地方

很多时候第一次在网上查询 PostCSS 概念的时候，大家都解释成一个后处理器的概念，其实个人觉得这些概念不重要，更为重要的有以下几点：

1. 它本质上是一个什么东西？
2. 它能解决我们什么问题？
3. 它是通过什么方式来解决我们的问题？
4. 它解决我们的问题是为什么？
5. 怎么实现与 SASS、LESS、Stylus 相同的功能（因为它们被经常拿来比较）
6. 它由哪些东西组成？
7. 既然是程序可以用的，那么它的API呢？

Q: 这个时候，你应该会问：为什么要将组成和API放到最后呢？

A: 那是因为我们在认识一个不太清楚的东西的时候，第一次肯定是一个直观的认识：它到底有什么用？而不会说，一来就去深入的研究它。不过这里本质还是要先说一下的，先留个印象。

## 二、个个击破

### 1. 它本质上是一个什么东西？

* PostCSS 可以直观的理解为：它就是一个`平台、平台、平台`，重要的事情来三遍比较爽，哈哈！

> 为什么说它是一个平台呢？因为我们直接用它，感觉不能干什么事情，但是如果让一些插件在它上面跑，那么将会很强大。

* PostCSS 提供了一个解析器，它能够将 CSS 解析成抽象语法树（AST）。

上面两条看完后，我们可以理解为下面这个模型。

![](https://segmentfault.com/img/bVqy8j)

所以说，PostCSS 它需要一个插件系统才能够发挥作用。我们可以通过“插件”来传递AST，然后再把AST转换成一个串，最后再输出到目标文件中去。当然，这里是有API可以用，这里先不讲，免得晕了。

### 2. 它能解决我们什么问题？它是通过什么方式来解决我们的问题？

上面的图很清晰，但是我还是不知道是个什么东西！所以接下来温和点，直接从代码层面来感官的认识一下。

* 它能够为 CSS 提供额外的功能；
* 通过在 PostCSS 这个平台上，我们能够开发一些插件，来处理我们的CSS，比如热门的：autoprefixer
* 我们能够使用JavaScript来开发插件（这点对前端来说很重要）

好吧，看到一个熟悉的单词了：autoprefixer，这里我们就让它来当栗子吧，可能更容易理解一点。

首先，我们需要做一些准备，安装好需要的东西。


```
// postcss 的命令行工具
sudo npm install  -g postcss-cli
// autoprefixer 插件
sudo npm install -g autoprefixer
```

第一次用命令行能让你更直观去理解它哈，所以请要有一颗折腾的心。

```
// 1. 先看下这个命令有哪些参数可以用
postcss --help

Usage: /usr/local/bin/postcss -use plugin [--config|-c config.json] [--output|-o
output.css] [input.css]

选项：
  -c, --config       JSON file with plugin configuration
  -u, --use          postcss plugin name (can be used multiple times)
  -o, --output       Output file (stdout if not provided)
  -d, --dir          Output directory
  -r, --replace      Replace input file(s) with generated output       [boolean]
  -s, --syntax       Alternative input syntax parser
  -p, --parser       Alternative CSS parser
  -t, --stringifier  Alternative output stringifier
  -w, --watch        auto-recompile when detecting source changes
  -v, --version      显示版本号                                        [boolean]
  -h, --help         显示帮助信息                                      [boolean]

示例：
  postcss --use autoprefixer -c             Use autoprefixer as a postcss plugin
  options.json -o screen.css screen.css
  postcss --use autoprefixer                Pass plugin parameters in
  --autoprefixer.browsers "> 5%" -o         plugin.option notation
  screen.css screen.css
  postcss -u postcss-cachify -u             Use multiple plugins and multiple
  autoprefixer -d build *.css               input files

Please specify at least one plugin name.
```

PS: 我贴出来是方便大家在看的时候不用电脑……^_^

好吧，先看一下文件目录，这里我只说一下比较好写的方式，就是将一些参数配置到配置文件中去。

![](https://segmentfault.com/img/bVqy8m)

```
// config.json: 所有的配置
// p.json: 仅有 autoprefixer 插件的配置

// config.json 的内容
{
    "use": ["autoprefixer"],
    "input": "src/index.css",
    "output": "index.css",
    "autoprefixer": {
        "browsers": "> 5%"
    }
}

// p.json 的内容
{
    "autoprefixer": {
        "browsers": "> 5%"
    }
}
```

接下来我们在终端里面输入：

```
// 最简洁的方式
postcss -c config.json
// 稍微复杂一点的方式，这里要用 -i 参数，help里面没有，我是从config.json里面的配置猜出来的，官方的那个写法出不来
postcss -u autoprefixer -c p.json -i src/index.css -o index.css
// 最复杂的方式
// 还是不写比较好。。。
```

跟平时想到的效果一样：

```
// src/index.css 中的源码
`* {
    transition: all .1s;
}`
// 转换过后的代码 index.css
`* {
    -webkit-transition: all .1s;
        transition: all .1s;
}`
```

好吧，现在肯定就对 PostCSS 有一个感官的认识了，接下来就是需要自己动手去用一下 cssnext 这个插件了～看会发生什么，这里就不写了，也挺好用的，不过应该还是草案状态。

我们开发不可能用命令行吧，所以这里再接着介绍代码编写，然后用 node 去执行文件的方式。直接上代码吧。

```
// 1. 先安装一下需要的库
npm install postcss --save-dev
npm install autoprefixer --save-dev

// 2. 其实应该先看看 postcss 的 package.json 文件，来看看包含了些什么，留个印象

// 3. p.js 中的代码
var postcss = require('postcss');
var autoprefixer = require('autoprefixer');
var fs = require('fs');

var css = '* { transition: all .1s; }';

postcss([autoprefixer])
    .process(css)
    .then(function(result) {
        // 这一行是学习的时候需要的，看一下到底对象里面包含什么
        console.log(result);
        if (result.css) {
            fs.writeFileSync('index.css', result.css);
        }
        if (result.map) {
            fs.writeFileSync('index.css.map', result.map);
        }
    });

// 4. 执行 p.js
node p
```

好吧，最后的结果和之前用命令行的方式一样，只不过过程不同。这样下来应该对 PostCSS 有了更多的感觉了吧。还没完，不用慌～我们还需要提出一个问题，我都有 SASS 等预处理器了，还拿它来不是又给前端届添乱么？因为这2年东西确实太多了～

> 记住一句话：存在即合理

既然合理，那么我们就看看它有什么优势呗～

### 3. 它解决我们的问题是为什么？优势何在？

比如，我们用 SASS 来处理 box-shadow 的前缀，我们需要这样写：

```css
/* CSS3 box-shadow */
@mixin box-shadow($top, $left, $blur, $size, $color, $inset: false) {
    @if $inset {
        -webkit-box-shadow: inset $top $left $blur $size $color;
        box-shadow: inset $top $left $blur $size $color;
    } @else {
        -webkit-box-shadow: $top $left $blur $size $color;
        box-shadow: $top $left $blur $size $color;
    }
}
```

使用 PostCSS 我们只需要按标准的 CSS 来写就行了，因为最后 autoprefixer 会帮我们做添加这个事情～

```css
box-shadow: 0 0 3px 5px rgba(222, 222, 222, .3);
```

所以，这里就出现了一个经常大家说的未来编码的问题。实际上，PostCSS 改变的是一种开发模式。

* SASS等工具：源代码 -> 生产环境 CSS
* PostCSS：源代码 -> 标准 CSS -> 生产环境 CSS

这样能体会出优势吧，但是目前大家都是 SASS + PostCSS 这样的开发模式，其实我认为是不错的，取长补短嘛，当然，在 PostCSS 平台上都是可以做到的，只是目前这个过渡期，这样更好，更工程化。接下来我就介绍一些方法来纯粹是用 PostCSS。

### 4. 怎么实现与 SASS、LESS、Stylus 相同的功能

其实这一节我都不需要写了～列一下插件就行了，因为插件才是实现，PostCSS 只是提供了一个平台。

其实可以去官方看看：[插件系统](https://github.com/postcss/postcss)

这里列几个便于理解的插件

* postcss-each
* postcss-for
* postcss-mixins
* postcss-extend

从名字就能看出来了吧～应该很好理解。


### 5. 它由哪些东西组成？

其实从官方介绍来看，只包含以下内容：

1. CSS Parser
2. CSS 节点树 API
3. source map 生成器
4. 生成节点树串

英文不太好 ＝＝ ，就这 4 部分吧，从第一个图其实也能够看出来。

其中的 I/O 体现在什么地方呢？好吧，很容易想到，主要体现在：

* Input: **插件程式** 和 **CSS Parser**
* Output: 生成节点树串

CSS Parser 可以理解为一个内部过程，而插件程式主要体现在：

```javascript
postcss([ autoprefixer ])
```

最后生成的节点树串体现在：

```javascript
postcss().process().then(function (result) {
    // 就是这里了
    console.log(result.css);
});

// 现在我贴一下上面 result 对象的一个输出结果
// 这里我多引入了一个 cssnano 插件
// 改变的代码就这点，为了更全的看 result
var opts = {
    from: 'src/index.css',
    to: 'index.css',
    // 配置 map
    map: { inline: false }
};
postcss([ autoprefixer, cssnano() ]).process(css, opts)

Result {
    processor: Processor {
        // 处理器的版本号
        version: '5.0.10',
        // 加载的一堆插件
        plugins: [
            [Object], [Object], [Object], [Object], [Object],
            [Object], [Object], [Object], [Object], [Object],
            [Object], [Object], [Object], [Object], [Object],
            [Object], [Object], [Object], [Object], [Object],
            [Object], [Object], [Object], [Object], [Object],
            [Object], [Object]
        ]
    },
    messages: [],
    root: Root {
        raws: {
            semicolon: false,
            after: ''
        },
        type: 'root',
        nodes: [
            [Object]
        ],
        source: {
            input: [Object],
            start: [Object]
        },
        _autoprefixerDisabled: false,
        _autoprefixerPrefix: false,
        rawCache: {
            colon: ':',
            indent: '',
            beforeDecl: '',
            beforeRule: '',
            beforeOpen: '',
            beforeClose: '',
            beforeComment: '',
            after: '',
            emptyBody: '',
            commentLeft: '',
            commentRight: ''
        }
    },
    // 我们代码中配置 opts 变量
    opts: {
        from: 'src/index.css',
        to: 'index.css'
    },
    // 这就是重新生成的 节点树串
    // 这里有自动补全和高效压缩的效果
    css: '*{-webkit-transition:all .1s;transition:all .1s}',
    // map的文件的配置
    map:
        SourceMapGenerator {
            _file: 'index.css',
            _sourceRoot: null,
            _skipValidation: false,
            _sources: ArraySet { _array: [Object], _set: [Object] },
            _names: ArraySet { _array: [], _set: {} },
            _mappings: MappingList { _array: [Object], _sorted: true, _last: [Object] },
            _sourcesContents: { '$src/index.css': '* { transition: all .1s; }' } },
    // 这里应该是链式要用的吧，暂时不深究
    lastPlugin: {
        [Function]
        postcssPlugin: 'cssnano-reset-stylecache',
            postcssVersion: '5.0.10'
    }
}
```

其实吧，这样有点抽象的，还是来看熟悉的 API 吧。

这里出现了 sourcemap，说明 PostCSS 中的转换功能是它必备的，但是必备并不等于：**源代码与目标代码不能完全一致**。

![](https://segmentfault.com/img/bVqy8H)

这里吐槽一下 Chrome 的 sourcemap 功能，一坨屎！下面看看 firefox 里面的效果吧。


这里 firefox 里面就自动映射了**源文件**，非常不错！


### 6. 既然是程序可以用的，那么它的API呢？

其实官方有 API 的详细解释，我看了一下，一看就明白了，就不再花时间介绍了，大家可以去看看，这样会知道，原来如此～

PS: 大家可以先看看 Node Common 和 Node相关的，然后再看 plugin

[官方API](https://github.com/postcss/postcss/blob/master/docs/api.md)

这里看一个 DEMO，主要做 rem 和 px 单位之间的互换，加入 processors 就可以用了，很方便：

```javascript
var custom = function(css, opts){
    css.eachDecl(function(decl){
        decl.value = decl.value.replace(/\d+rem/, function(str){
            return 16 * parseFloat(str) + "px";
        });
    });
};
```

开发插件可以看一下 [官方插件指南](https://github.com/postcss/postcss/blob/master/docs/guidelines/plugin.md)

更细致的地方，之后有时间的时候再写写 ^_^ 一说技术就停不下来了～

大家在问？我怎么在工程上应用它呢？好吧，使用 gulp, grunt, webpack 都是可以的，我觉得都理解了 PostCSS ，使用这些就很简单了，一查资料，拷贝一份配置就可以开始用了～就这样吧，下次再结合 react 来介绍一下一个叫: postcss-js 的插件，看上去还不错，还没深入用，用到的时候再分享吧。

其实我也是初学者，只是用了自己的学习方法来梳理成文章，下面都是我看过的文章，部分是引用的。这里就不全部举例了，看的文章有点多。。。

### 参考的文章

* 最权威的初认识 https://github.com/postcss/postcss
* 一个不错的东西 rework https://github.com/reworkcss
* 理解 AST 等插件解析技术 http://rapheal.sinaapp.com/category/js/uglify%E6%BA%90%E7%A0%81/
* 大漠的系列文章，应用型很强，推荐 http://www.w3cplus.com/blog/tags/517.html
* 一个年龄差不多的开发者的解释 http://acgtofe.com/posts/2015/05/modular-transforming-with-postcss/
* 一篇稍微理论化解释的文章 http://www.oschina.net/translate/its-time-for-everyone-to-learn-about-postcss?cmp
* 一个开发模式简单的优劣比较 http://caibaojian.com/css-processor.html
* 原文链接 https://segmentfault.com/a/1190000003909268