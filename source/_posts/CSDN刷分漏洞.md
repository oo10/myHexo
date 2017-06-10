title: CSDN刷分漏洞
date: 2016-10-21 13:58:41
description: 
author:
comments:
original:
permalink: 
---
### [](#这篇博客的由来 "这篇博客的由来")这篇博客的由来

偶尔有一次机会点开了CSDN排行第一的博主的博客，看到了他的一篇文章!<a id="more"></a>[message](http://img.blog.csdn.net/20160920123920368)

当时看到这篇文章第一想法是跪拜的，前辈不愧是前辈，不愧是CSDN排名第一。

看到前辈后面说这个漏洞已经被封了，但是我又有点心痒难耐，想了想琢磨了一下发现似乎可以在文章访问量上动手脚，测试发现CSDN对文章访问计数是一个IP地址短时间只能访问一次。于是乎想了想能不能从从http报头上动手脚？所以就有了这篇博客~~~

## [](#声明 "声明")声明

本博客只是为了证明CSDN确实有这个漏洞，由于博主已经被CSDN客服爸爸发了黄牌警告。代码方面就不会全部公布了，只是提供一个思路，同时也希望CSDN客服爸爸尽快修复这个漏洞。
再次强调！博主真不是故意的，测试脚本的时候挂服务器上后就去玩游戏了，忘记关了，后来被CSDN客服爸爸黄牌警告才想起。。。。。好在CSDN客服爸爸宽宏大量，原谅了我。我只是单纯想向前辈致敬，结果玩成这样了。。。。



![这里写图片描述](http://img.blog.csdn.net/20160920124901518)![这里写图片描述](http://img.blog.csdn.net/20160920124918643)

## [](#思路 "思路")思路

首先利用爬虫把自己每篇文章的url给扫描下来，然后利用Php中的curl往里面填入伪造的IP地址.

<figure class="highlight plain">

| 

<pre>

<div class="line">1</div>

<div class="line">2</div>

<div class="line">3</div>

<div class="line">4</div>

<div class="line">5</div>

<div class="line">6</div>

<div class="line">7</div>

<div class="line">8</div>

<div class="line">9</div>

</pre>

 | 

<pre>

<div class="line">$ip = $_GET['ip'] ? $_GET['ip'] : '1.1.1.1';</div>

<div class="line">$ipArr = explode(".",$ip);</div>

<div class="line">$ipArr[3]=rand(1,255);</div>

<div class="line">$ipArr[2]=rand(1,255);</div>

<div class="line">$ipArr[1]=rand(1,255);</div>

<div class="line">$ipArr[0]=rand(1,255);</div>

<div class="line">$ip = implode(".", $ipArr);</div>

<div class="line">$headers['CLIENT-IP'] = $ip;</div>

<div class="line">$headers['X-FORWARDED-FOR'] = $ip;</div>

</pre>

 |

</figure>

伪造的用户代理

<figure class="highlight plain">

| 

<pre>

<div class="line">1</div>

<div class="line">2</div>

</pre>

 | 

<pre>

<div class="line">$user_agent = 'Safari Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.73.11 (KHTML, like Gecko) Version/7.0.1 Safari/5';</div>

<div class="line">curl_setopt($ch, CURLOPT_USERAGENT, $user_agent);</div>

</pre>

 |

</figure>

伪造的来路，百度爸爸躺枪

<figure class="highlight plain">

| 

<pre>

<div class="line">1</div>

</pre>

 | 

<pre>

<div class="line">curl_setopt($ch, CURLOPT_REFERER, "http://www.baidu.com/");   //构造来路</div>

</pre>

 |

</figure>

核心思想主要就是这些，有点底子的人也知道剩下的该怎么做了吧？(呸呸呸！！各位别瞎搞，技术分享而已，让大家知道有这么个东西就行了。)然后把伪造的信息发过去再访问页面就蒙混过关了~

## [](#结束语 "结束语")结束语

再次强调！此篇文章为技术分享性质，同时让大家知道有这么个东西即可，切勿以此为恶！
同时希望CSDN越办越好，同时尽快修复这个BUG吧！