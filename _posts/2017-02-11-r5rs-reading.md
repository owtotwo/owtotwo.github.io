---
layout:     post
title:      "\"Revised5 Report on the Algorithmic Language Scheme\" Reading"
subtitle:   "R5RS阅读记录"
date:       2017-02-11
author:     "owtotwo"
header-img: "img/wave.jpg"
tags:
    - Computer Science
    - Scheme
    - R5RS
    - Programming Language
    - Language Standard
---

> Programming languages should be designed not by piling feature on top of 
feature, but by removing the weaknesses and restrictions that make additional 
features appear necessary.

# 前言

`created on 2017-02-11`

又莫名其妙开了一个坑，说大不算大，但是内容还是比较多的。

这个坑起源于Racket这个东东，因为做得很不错，文档写得很好，因为就写Scheme写上瘾了。虽然说还是处
于未入门状态，但是对于如此简洁的语法（S-Expression）还有优雅的函数式特性，搞搞这东西还是很爽的。

由于前面略读了一遍 [_The Little Schemer_][1] 感觉受到了严重的打击，所以被迫跑来看Scheme标
准文档压压惊。

# 粗览

国内已经有译者（王咏刚老师）在十多年前做了试译，有免费的电子版发布在网上。

也同时感谢下原作的编者 RICHARD KELSEY, WILLIAM CLINGER, 和 JONATHAN REES。

全文只有不到50页，我相信我可以在有生之年读完！

概述段开头的第一句应该是Scheme的核心设计理念了：

> Programming languages should be designed not by piling feature on top of 
feature, but by removing the weaknesses and restrictions that make additional 
features appear necessary.

> 程序设计语言的设计不应该是特征的堆砌，而应消除那些需要依赖于多余特征的弱点和局限。

感觉黑了一把D语言（其实好像还误伤了很多语言）。

> By relying entirely on procedure calls to express iteration, Scheme emphasized 
the fact that tailrecursive procedure calls are essentially goto’s that pass
arguments.

> Scheme 完全依赖过程调用来表示迭代，并以此强调，尾递归过程调用在本质上就是传递参数的 goto。

这句话非常重要。在Scheme里你无法像C语言一类的面向过程的语言一样通过循环语句来表达迭代（迭代与递
归的定义及区别可以阅读SICP的1.2节部分），这是很多从Python或者Java等语言转向函数式语言的程序员
所感到不适应的地方。

文中提到的Escape procedure一词暂时无法得知是什么意思。

Hygienic Macro很让人期待呀。

## 第一章 Scheme概论

*   类型与值相关联而非与变量相关联。

*   过程中创建的对象拥有无限的生存期(Extent)。

*   Scheme的实现需支持严格的尾递归。

*   实参表达式会在过程获得控制权之前被求值，与惰性求值(Lazy-evalution)不同（按需求值）。

*   整数是有理数，有理数是实数，实数是复数。

*   实现的错误处理分为“与定义不符的错误”和“符合定义但因实现约束造成的错误”。

*   若标准中对某表达式的值未定义(Unspecified)，则应由实现决定将其定义为某对象。

*   竟然有Mutation procedure，惊了。说好的函数式呢？

*   大小写不敏感，不喜欢这个…

*   非数值起始字符作第一个字符的都是标识符。

*   标准要求的空白字符(Whitespace)只有空格和换行，实现可以选择加上制表符等。

*   译文中有一句话比较绕：

    > 注释对 Scheme 来说是不可见的，但该行的结尾是一个可见的空白符，这可以防止注释出现在标识符
    或数值的中间。

    我们看看原文：

    > Comments are invisible to Scheme, but the end of the line is visible as 
    whitespace. This prevents a comment from appearing in the middle of an 
    identifier or number.

    这个就比较清楚了，我们来举个例子：

    ``` Scheme
    (define near-the-comment; I am a comment.
    beginning-of-line)
    ```

    假如注释的末尾换行不可见（即和注释一同被无视掉），就会变成：

    ``` Scheme
    (define near-the-commentbeginning-of-line) ; Syntax Error!
    ```

*   空表`()`在Scheme中不是一个有效的表达式。

*   并不知道`set!`有啥用…赋值不太清真。

*   `(and)` => `#t` ; `(or)` => `#f`.

*   我发现后面看得无所适从，决定先缓缓。

# 细读

`怎么可能有嘛`

[1]: http://www.schemers.org/Documents/Standards/R5RS/r5rs.pdf