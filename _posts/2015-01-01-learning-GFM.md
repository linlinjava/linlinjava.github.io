---
layout: page
title: GitHub Markdown[翻译]
category: markdown
description: markdown syntax
---

本文翻译自:

* <https://help.github.com/articles/markdown-basics/>
* <https://help.github.com/articles/github-flavored-markdown/>
* <https://help.github.com/articles/writing-on-github/>

[TOC]

# GitHub Markdown

## 1. Markdown基础知识

[Markdown][markdown]允许你采用一种方便阅读和方便书写的纯文本格式书写, 而纯文本则会转化成正确的HTML格式用于在GitHub上浏览.

[markdown]: http://daringfireball.net/projects/markdown/syntax#precode

### 段落

Markdown中的段落只是一行或多行文本之后跟着一个或多个空白行.

	On July 2, an alien mothership entered Earth's orbit and deployed several dozen saucer-shaped "destroyer" spacecraft, each 15 miles (24 km) wide.

	On July 3, the Black Knights, a squadron of Marine Corps F/A-18 Hornets, participated in an assault on a destroyer near the city of Los Angeles.

### 标题

你可以通过在你的标题文本之前添加一个或多个`#`符号来创建一个标题. 你所使用`#`的数量将决定标题的尺寸.

	# The largest heading (an <h1> tag)
	## The second largest heading (an <h2> tag)
	…
	###### The 6th largest heading (an <h6> tag)

### 引用

你可以通过一个`>`来指示引用.

	In the words of Abraham Lincoln:

	> Pardon my french

### 文本样式

你可以是文字**加粗**或*斜体*.

	*This text will be italic*
	**This text will be bold**

无论是粗体还是斜体能可以采用一个`*`或一个`_`来围绕文本用于样式. 这使得你如果有需要可以联合起来既粗体有斜体.

	**Everyone _must_ attend the meeting at 5 o'clock today.**

### 无序列表

你可以通过在列表前面添加一个`*`或者一个`-`来产生一个无序列表.

	* Item
	* Item
	* Item

	- Item
	- Item
	- Item

### 有序列表

你可以通过在列表前面添加一个数字来产生一个有序列表.

1. Item 1
2. Item 2
3. Item 3

### 嵌套列表

你可以通过这两张方式缩进列表项来产生一个嵌套列表.

	1. Item 1
	  1. A corollary to the above item.
	  2. Yet another point to consider.
	2. Item 2
	  * A corollary that does not need to be ordered.
	    * This is indented four spaces, because it's two spaces further than the item above.
	    * You might want to consider making a new list.
	3. Item 3

### Code内联式

使用单引号(` ` `)把文本格式化成一个特殊的monospace格式. 在引号内的任何文本都如文本本身所示, 不存在其他特殊的格式.

	Here's an idea: why don't we take `SuperiorProject` and turn it into `**Reasonable**Project`.

### Code多线式

你可以使用三个引号(` ``` `)格式化文本, 使文本格式正如其自身块所示.

Check out this neat program I wrote:

	```
	x = 0
	x = 2 + 2
	what is x
	```

### 链接

你可以将用方括号(`[]`)包围链接文本并且用圆括号(`()`)包围链接来产生一个内联链接.

例如, 为了生成一个超链接www.github.com, 并且链接文字是"Visit GitHub!", 那么你需要用Markdown这样写: 

	[Visit GitHub!](https://www.github.com).

## 2. GFM

GitHub在`issues`,`comments`,和`pull requests`中使用"GitHub Flavored Markdown"或GFM. 与标准Markdown(SM)相比,在一些地方会存在不同,而且增加了一些额外功能.

如果你不是很熟悉Markdown,可以先看看[Markdown Basics][basic].如果你想要了解更多`issues`,`comments`,和`pull requests`相关的特性,例如工作表(task lists),可以阅读[Writing on GitHub][writing].

### 单词间的下划线

SM将把下划线(`_`)斜体, 而GFM则是忽略单词间的下划线.

例如:
	> wow_great_stuff
	> do_this_and_do_that_and_another_thing

这样可以使得具有下划线的代码和命名可以正常渲染. 为了强调一个单词的一部分,可以使用星号(`*`).

### URL自动链接

GFM会自动链接标准URL.所以如果你想要链接一个URL(不是链接文字),你可以简单地输入URL, 而它会被转换成指向URL的一个超链接.

	http://example.com

将成为

http://example.com

### 删除线

GFM增加了一个语法来产生删除文本,而这个在SM中是没有的.

	~~mistaken text.~~

将成为

~~mistaken text.~~

### 栅栏式代码块(Fenced code blocks)

SM会将每行开头包含四个空格的文本转换成代码块, 而GFM也支持栅栏式代码块. 只需要用` ``` `(如下所示)包围你的代码, 你就可以不需要再缩进四个空格. 

注意: 和缩进式代码块不一样, 栅栏式代码块可以不需要之前的空白行,但是我们建议在它们之前放置一个空白行,使得原始的Markdown更容易阅读.

	Here's an example:

	```
	function test() {
  	console.log("notice the blank line before this function?");
	}
	```

请记住, 列表内,你必须缩进非栅栏式代码块*8个*空格来正确地渲染.

### 语法高亮

代码块能够进一步增加语法高亮. 在你的栅栏块里增加一个可选的语言标识符, 接着我们就可以进行代码高亮了.

例如, 为了Ruby代码语法高亮:

	```ruby
	require 'redcarpet'
	markdown = Redcarpet.new("Hello World!")
	puts markdown.to_html
	```

我们使用[Linguist][linguist]来进行语言检测和语法高亮. 你可以阅读[语言YAML文件][languages]来找出哪些关键字是正确的．

[linguist]: https://github.com/github/linguist
[languages]: https://github.com/github/linguist/blob/master/lib/linguist/languages.yml

### 表格

你可以用连字符`－`(用于第一行)来组装和分隔一些单词来创建列表, 然后用一个管道字符`|`来分隔每一列:

	First Header  | Second Header
	------------- | -------------
	Content Cell  | Content Cell
	Content Cell  | Content Cell

为了美观的目的,你也可以在两端添加二外的管道字符:

	| First Header  | Second Header |
	| ------------- | ------------- |
	| Content Cell  | Content Cell  |
	| Content Cell  | Content Cell  |

请注意, 在上面的破折号并不需要完全匹配标题文本的长度:

	| Name | Description          |
	| ------------- | ----------- |
	| Help      | Display the help window.|
	| Close     | Closes a window     |

你也可以包含内联的Markdown, 例如链接, 粗体, 斜体, 或删除线:

	| Name | Description          |
	| ------------- | ----------- |
	| Help      | ~~Display the~~ help window.|
	| Close     | _Closes_ a window     |

最后,通过标题行内的冒号来定义文本是左对齐, 右对齐或居中对齐:

	| Left-Aligned  | Center Aligned  | Right Aligned |
	| :------------ |:---------------:| -----:|
	| col 3 is      | some wordy text | $1600 |
	| col 2 is      | centered        |   $12 |
	| zebra stripes | are neat        |    $1 |

最左边的一个冒号表明一个左对齐的列; 最右边的一个冒号表明一个右对齐的列; 而两边都有冒号表明一个居中对齐的列.

### HTML

你可以在你的`READMEs`,`issues`和`pull requests`中使用HTML的子集.

一个包含所有我们支持的标签和属性的列表可以在[gitugithub/markup repository][markup]找到.

[markup]: https://github.com/github/markup

## 3. 精通Markdown

Markdown是一种轻量且容易使用的语法, 用于GitHub平台所书写的所有类型.

### 你将会学习到

* Markdown格式是如何使协同编辑变得容易的.
* Markdown是如何和传统的格式化方式不同的.
* 如何使用Markdown格式化文本.
* 如何利用GitHub的自动Markdown渲染.
* 如何应用GitHub独有的Markdown扩展

### 什么是Markdown?

Markdown是一种在网络上组织文本的方式. 你可以控制文档的显示; 文字格式化成粗体或斜体, 添加图片和创建列表也是Markdown可以做到的一部分事情. 更多的是, Markdown仅仅只是包含了一些如`#`或`*`之类非字母字符的普通文本.

你可以在GitHub的绝大多数地方使用Markdown:

* Gists
* 在Issues和Pull Requests里的评论
* `.md`和`.markdown`后缀的文件

### 示例

https://guides.github.com/features/mastering-markdown/#examples

### 语法指南

以下是Markdown语法的简述, 你可以在GitHub.com或你自己的文本文件中使用.

#### 标题

	# This is an <h1> tag
	## This is an <h2> tag
	###### This is an <h6> tag

#### 重点

	*This text will be italic*
	_This will also be italic_

	\**This text will be bold**
	\__This will also be bold__

	*You **can** combine them*

#### 无序列表

	* Item 1
	* Item 2
	  * Item 2a
	  * Item 2b

#### 有序列表

	1. Item 1
	2. Item 2
	3. Item 3
	   * Item 3a
	   * Item 3b

#### 图片

	\![GitHub Logo](/images/logo.png)
	Format: \![Alt Text](url)

#### 链接

	http://github.com - automatic!
	\[GitHub](http://github.com)

#### 引用

	As Kanye West said:

	> We're living the future so
	> the present is our past.

#### 内联代码

	I think you should use an
	\`<addr>` element here instead.

### GitHub Flavored Markdown

GitHub.com使用自身版本的Markdown语法提供一些额外的有用功能, 这些可以使得在GitHub.com上更加容易写作.

注意:GitHub Flavored Markdown的一些功能只能在Issues和Pull Requests的描述和评论中所使用. 这些包括@mentions, SHA-1哈希引用, Issues引用和Pull Requests引用.

#### 语言高亮

以下是你使用GFM进行语法高亮的一个示例:

	```javascript
	function fancyAlert(arg) {
	  if(arg) {
	    $.facebox({div:'#foo'})
	  }
	}
	```

你也可以仅仅简单地把你的代码缩进四个空格:

	    function fancyAlert(arg) {
	      if(arg) {
	        $.facebox({div:'#foo'})
	      }
	    }

以下是一个Python代码没有语法高亮的示例:

def foo():
    if not bar:
        return True

#### 任务列表

	- [x] @mentions, #refs, [links](), \**formatting**, and <del>tags</del> supported
	- [x] list syntax required (any unordered or ordered list supported)
	- [x] this is a complete item
	- [ ] this is an incomplete item

#### 表格

你可以用一个连字符`-`(用于第一行)来集合和分隔单词, 同时用一个管道字符`|`来隔离每一列,从而创建表格:

	First Header | Second Header
	------------ | -------------
	Content from cell 1 | Content from cell 2
	Content in the first column | Content in the second column

First Header | Second Header
------------ | -------------
Content from cell 1 | Content from cell 2
Content in the first column | Content in the second column

#### SHA引用

任何一个commit的SHA-1哈希值都会在GitHub上自动转化成一个指向commit的链接.

	16c999e8c71134401a78d4d46435517b2271d6ac
	mojombo@16c999e8c71134401a78d4d46435517b2271d6ac
	mojombo/github-flavored-markdown@16c999e8c71134401a78d4d46435517b2271d6ac

#### repository的Issue引用

任何一个指向Issue或Pull Request的数字都会自动转化成一个链接.

	#1
	mojombo#1
	mojombo/github-flavored-markdown#1

#### @mentions

输入`@`, 之后再输入一个用户名, 将会提醒那个人过来并查看评论. 这个称作"@mention", 因为你正在*提到*某人. 你也可以@mention一个组织的团队.

#### 自动链接URL

任何一个URL(如`http://www.github.com/`)都会自动转化成一个可以点击的链接.

#### 删除线

任何一个包含在两个波浪字符之间的单词(如`~~this~~`)将会被划掉.

#### Emoji

GitHub支持Emoji!:sparkles::camel::boom:

请检验[Emoji Cheat Sheet](http://www.emoji-cheat-sheet.com/)来查看我们所支持的图片集合.
