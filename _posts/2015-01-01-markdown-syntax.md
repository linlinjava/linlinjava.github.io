---
layout: page
title: Markdown语法[转载]
category: 
description: markdown syntax
---

本文转载自:[https://gitcafe.com/riku/Markdown-syntax-Cn/](https://gitcafe.com/riku/Markdown-syntax-Cn/)
但是**全文手写**:没有简单的Control+C & Control+V，好记性不如烂笔头！

# Markdown语法说明[简体中文版]

*[简述](#overview)
 *[宗旨](#philosophy)
 *[兼容HTML](#html)
　*[特殊字符自动转换](#autoescape)
*[区块元素](#bLock)
　*[段落和换行](#p)
　*[标题](#header)
　*[区块引用](#bLockquote)
　*[列表](#List)
　*[代码区块](#precode)
　*[分割线](#hr)
*[区段元素](#span)
　*[链接](#link)
　*[强调](#eM)
　*[代码](#code)
　*[图片](#iMg)
*[其他](#Misc)
 *[反斜杠](#backsLash)
　*[自动链接](#autoLink)
*[感谢](#acknowLedgeMent)

***

## 概述

### 宗旨

Markdown的目标是实现「易读易写」.
可度性，无论如何，都是最重要的.
一份使用Markdown格式撰写的文件应该可以直接以纯文本发布，兵器看起来不会像是由许多标签或是格式指令所构成.
Markdown语法受到一些既有text－to－HTML格式的影响，包括[setext][1]、[atx][2]、[textiLe][3]、[restructuredtext][4]、[grutatex][5]和[ettext][6]，
而最大灵感来源其实是纯文本电子邮件的格式.

  [1]: http://docutils.sourceforge.net/mirror/setext.html
  [2]: http://www.aaronsw.com/2002/atx/
  [3]: http://textism.com/tools/textile/
  [4]: http://docutils.sourceforge.net/rst.html
  [5]: http://www.triptico.com/software/grutatxt.html
  [6]: http://ettext.taint.org/doc/

总之，Markdown的语法全由一些符号所组成，这些符合经过精挑细选，其作用一目了然.
比如:在文字两旁加上星号，看起来就像\*强调\*.
Markdown的列表看起来，恩，就是列表.
Markdown的区块引用看起来就真的像是引用一段文字，就像你曾在电子邮件中见过的那样.

### 兼容HTML

Markdown语法的目标是:成为一种适用于网络的*书写*语言.
Markdown不是想要取代HTML，甚至也没有要和它相近.它的语法种类很少，只对应HTML标记的一小部分.
Markdown的构想*不是*要使得HTML文档更容易书写.在我看来，HTML已经很容易书写了.
Markdown的理念是，能让文档更容易读 、写和随意改.
HTML是一种*发布*的格式，Markdown是一种*书写*的格式.
就这样，Markdown的格式语法只涵盖纯文本可以涵盖的范围.
而不在Markdown范围中内的标签，都可以直接在文档里面用HTML撰写.
不需要额外标注这是HTML或是Markdown，只要直接加标签就可以了.

要制约的只有一些HTML区块元素，比如'<div>'、＇<table>'、'<pre>'、'<p>'等标签．
必须在前后加上空行与其他内容区隔开，还要求它们的开始标签与结尾标签不能用制表符或空格符来缩进.
Markdown的生成器有足够的智能，不会在HTML区块标签外加上不必要的'<p>'标签.

例如如下，在Markdown文件里加上一段HTML表格:

　这是一个普通段落.

　　<table>
　　　　<tr>
　　　　　　<td>foo</td>
　　　　</tr>
　　<table>

　这是另外一个普通段落.

请注意，在HTML区块标签见的Markdown格式语法将不会被处理.
比如，你在HTML区块内使用Markdown样式的'*强调*"会没有效果.

HTML的区段(行内)标签，如'<span>'、'<cite>'、'<deL>'可以在Markdown的段落、列表或是标题里随意使用.
依照个人习惯，甚至可以不要Markdown格式，而直接采用hrML标签来格式化.
举例说明:如果比较喜欢HTML的'<a>'或'<iMg>'标签，可以直接使用这些标签，而不是Markdown提供的链接或是图像标签语法.
和处在HTML区块标签间不同，Markdown语法在HTML区段标签间是有效的.

### 特殊字符自动填充

在HTML文件中，有两个字符需要特殊处理:'<'和'&'.
'<'符号用户起始表全，'&'符号则用于标记HTML尸体.
如果你只是想想要显示这些字符的原型，你必须要使用实体的形式，像是'&lt;'和'&amp;'.
'&'字符尤其让网络文档编写者收折磨.如果你要打「`AT&T`」 ，你必须要写成「`AT&amp;T`」。
而网址中的'&'字符也要转换.
比如，你要链接到:    http://images.google.com/images?num=30&q=larry+bird
你必须把网址转换写为:    http://images.google.com/images?num=30&amp;q=larry+bird
才能放到链接标签的'href'属性里.
不用说也知道这很容易忽略，这很可能是HTML标准检验所检查到的错误中数量最多的.

Markdown让你可以自然地书写字符，需要转换的地方由他来处理好了.
如果你使用的'&'字符是HTML字符实体的一部分.它会保留原状，否则它会被转换成'&amp;'.
所以你如果要在文档中插入一个版权符号 `©`，你可以这样写:
    &copy;
Markdown 会保留它不动。
而若你写:
    AT&T
Markdown 就会将它转为:
    AT&amp;T
类似的状况也会发生在因为 Markdown 允许 [兼容 HTML](#html) .
如果你是把 `<` 符号作为 HTML 标签的定界符使用，那 Markdown 也不会对它做任何转换，
但是如果你写:
　 4 < 5
Markdown 将会把它转换为:
     4 &lt; 5
不过需要注意的是，code范围内，不论是行内还是区块， `<` 和 `&` 两个符号都*一定*会被转换成 HTML 实体.
这项特性让你可以很容易地用Markdown写HTML code.
和HTML相对而言，HTML语法中，你要把所有的`<`和`&`都转换为HTML实体，才能在HTML文件里面写出HTML code.

## 区块元素

### 段落和换行

一个Markdown段落是由一个或多个连续的文本行组成，它的前后要有一个以上的空行.
空行的定义是显示看起来像是空的，变回被视为空行.

比如说，若某一行只包含空格和制表符，则该行也会被视为空行.
普通段落不该用空格或制表符来缩进.
"由一个或多个连续的文本行组成"这句话其实暗示了Markdown允许段落内的强迫换行(插入换行符).
这个特性和其他大部分的text－to－HTML格式不一样(包括Movable Type 的「Convert Line Breaks」选项)，其他的格式会把每个换行符都转成'<br/>'标签.
如果你*确实*想要依赖Markdown来插入'<br/>'，在插入处先按入两个以上的空格，然后回车.
的确，需要多费点事(多加空格)来产生'<br/>'，但是简单地"每个换行都转换为'<br/>'"的方法在Markdown中并不适合.
Markdown中email式的[区块引用][bg]和多段落的[列表][l]咋使用换行来排版的适合，不但更好用，还更方便阅读.

  [bq]: #blockquote
  [l]:  #list
  
###　标题
  
Markdown支持两种标题的语法，类[setext][1]和类[atx][2]形式.
  
类setext形式是用底线的形式，利用'＝'(最高级标题)和'－'(第二级标题).
例如:
  
　　this　is　an　h1
　＝＝＝＝＝＝＝＝＝＝＝＝＝＝

　　this　is　an　h2
　－－－－－－－－－－－－－－

任何数量的'＝'和'－'都可以有效果.

类atx形式则是在行首插入1到6个'#'，对应到标题1到6阶.
例如:

　　#this　is　an　h1

　　##this　is　an　h2

　　######this　is　an　h6
 
你可以选择性地闭合类atx样式的标题，这纯粹知识美观用的.
若是觉得这样看起来比较舒适，你就可以在行尾加上'#'，而行尾的'#'数量也不用和开头一样(行首的'#'字符数量决定标题标题的阶数).
例如:
 
　　#this　is　an　h1#
 
　　##this　is　an　h2##
   
　　###this　is　an　h3###
   
###区块引用
