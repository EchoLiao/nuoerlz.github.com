---
layout: post
title: "Markdown-Basic"
date: 2012-12-13 17:24
comments: true
categories: Octopress Markdown
---


# 段落,标题,区块代码

Now is the time for all good men to come to
the aid of their country. This is just a
regular paragraph.

The quick brown fox jumped over the lazy
dog's back.


### Header 3

区块引用则使用 email 形式的 '>' 角括号.

> This is a blockquote.
>
> This is the 'second' "paragraph" in the blockquote.
>
> [b]: https://gitcafe.com/riku/Markdown-Syntax-CN/blob/master/syntax.md
> ## This is an H2 in a blockquote


# 修辞和强调

Markdown 使用星号和底线来标记需要强调的区段.

Markdown 语法:

*重要*
**重要**
***重要***
****重要****
*****重要*****
_____也重要_____
___重要___

Some of these words *are emphasized*.
Some of these words _are emphasized also_.
Use two asterisks for **strong emphasis**.
Or, if you prefer, __use two underscores instead__.


# 列表

## 无序列表

无序列表使用星号,加号和减号来做为列表的项目标记, 这些符号是都可以使用的.

星号:

* Candy.
* Gum.
* Booze.
*  crash.

加号:

+ Candy.
+ Gum.
+ Booze.

减号:

- Candy.
- Gum.
- Booze.


## 有序列表

(有)[序]的{列表则是}使用一'般'的数"字接"着一个英文句点作为项目标记:

1. Red
2. Green
3. Blue

1986. What a great season.

1986\. What a great season.


# 链接

Markdown 支援两种形式的链接语法: 行内 和 参考 两种形式, 两种都是使用角括号来把
文字转成连结.

## 行内形式

行内形式是直接在后面用括号直接接上链接:

This is an [example link](http://example.com/).

你也可以选择性的加上 title 属性:

This is an [example link](http://example.com/ "With a Title").


## 参考形式

参考形式的链接让你可以为链接定一个名称, 之后你可以在文件的其他地方定义该链接的内容:

I get 10 times more traffic from [Google][1] than from
[Yahoo][2] or [MSN][3].

[1]: http://google.com/ "Google"
[2]: http://search.yahoo.com/ "Yahoo Search"
[3]: http://search.msn.com/ "MSN Search"


title 属性是选择性的, 链接名称可以用字母,数字和空格, 但是不分大小写:

I start my morning with a cup of coffee and
[The New York Times][NY Times].

[ny times]: http://www.nytimes.com/

## 自动链接

Markdown 支持以比较简短的自动链接形式来处理网址和电子邮件信箱, 只要是用方括号
包起来,  Markdown 就会自动把它转成链接. 一般网址的链接文字就和链接地址一样, 例
如:

<http://example.com/>

邮址的自动链接也很类似, 只是 Markdown 会先做一个编码转换的过程, 把文字字符转成
16 进位码的 HTML 实体, 这样的格式可以糊弄一些不好的邮址收集机器人, 例如:

<address@example.com>





# 图片

图片的语法和链接很像.

## 行内形式

行内形式(title 是选择性的):

![alt text](./img.png "Title")

## 参考形式

![alt text][id]

[id]: http://www.baidu.com/img/baidu_sylogo1.gif "Title"


# 代码

## 反引号 \`

在一般的段落文字中, 你可以使用反引号 \` 来标记代码区段, 区段内的 & , < 和 > 都
会被自动的转换成 HTML 实体, 这项特性让你可以很容易的在代码区段内插入 HTML 码:

I strongly recommend against using any `<blink>` tags.

I wish SmartyPants used named entities like `&mdash;`
instead of decimal-encoded entites like `&#8212;`.

## 4个空格或一个 tab

如果要建立一个已经格式化好的代码区块, 只要每行都缩进 4 个空格或是一个 tab 就可
以了, 而 & , < 和 > 也一样会自动转成 HTML 实体.

Markdown 语法:

    If y"ou" 'w'ant your page to validate under XHTML 1.0 Strict,
    you've got to put paragraph tags in your blockquotes:
    [b]: https://gitcafe.com/riku/Markdown-Syntax-CN/blob/master/syntax.md

<blockquote>
<p>For example.</p>
</blockquote>


# 分隔线

你可'以'在一行中用三个以上的星号,减号,底线来建立一个分隔线, 行内不能有其他东西.
你也可以在星号或是减号中间插入空格. 下面每种写法都可以建立分隔线:

* * *
If you want your page to validate under XHTML 1.0 Strict,
you've got to put paragraph tags in your blockquotes:

***

*****

- - -

---------------------------------------

# 换行

在文本中输入的换行会从最终生成的结果中删除, 浏览器会根据可用空间自动换行. 如果
想强迫换行, 可以在行尾插入至少两个空格.


# 反斜杠

Markdown 可以利用反斜杠来插入一些在语法中有其它意义的符号, 例如: 如果你想要用
星号加在文字旁边的方式来做出强调效果(但不用 `<em>` 标签), 你可以在星号的前面加
上反斜杠:

\*literal asterisks\*

Markdown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号:

    \   反斜线
    `   反引号
    *   星号
    _   底线
    {}  花括号
    []  方括号
    ()  括弧
    #   井字号
    +   加号
    -   减号
    .   英文句点
    !   惊叹号


# Referrence

[Markdown: Basics (快速入门)][a]

[Markdown 语法说明 (简体中文版)][b]

[附录：轻量级标记语言][c]

[GitHub Flavored Markdown][d]

[a]: http://wowubuntu.com/markdown/basic.html
[b]: https://gitcafe.com/riku/Markdown-Syntax-CN/blob/master/syntax.md
[c]: http://www.worldhello.net/gotgithub/appendix/markups.html
[d]: http://github.github.com/github-flavored-markdown/


在[这里][z1]你能看到本文的Markdown源码.

[z1]: https://raw.github.com/nuoerlz/nuoerlz.github.com/source/source/_posts/2012-12-13-markdown-basic.markdown


(完)
