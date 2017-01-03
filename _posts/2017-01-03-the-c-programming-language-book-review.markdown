---
layout:     post
title:      "The C Programming Language - Second Edition" Book Review
subtitle:   "《C程序设计语言第二版》阅读记录"
date:       2017-01-03
author:     "owtotwo"
header-img: "img/hill.jpg"
tags:
    - Reading
    - C
---

> "Ok, let's begin."

## 前言
`created on 2017-01-03`

忽然心血来潮想挖个小坑，约280页的 _The C Programming Language_ 全书阅读浅评～
复习马原压力大，容易让人想减压，挖坑是一个很好的方式，有效地转移了主要矛盾，符合马克思主义的现实指导思想。
为什么我会选这本书呢，根本原因有三个：  
1. 非常薄，比C primer plus薄到不知哪里去了。
2. C语言应用的广泛性，也同时因为有Unix这个强大的后盾，基本再过20年也不会被彻底淘汰的。
3. C语言是较“低级”的编程语言，特别是作为毕竟原始的系统编程语言，C语言毫无疑问是精简而底层的。（较接近汇编）

希望能在有生之年把这个坑填完…

---

## 正文

### Preface / Preface to the First Edition / Introduction / Contents

大意是说1978年从这本书的初版开始在计算机科学领域发生了很大的变化，C也有了其标准，即ANSI C，是一份为跨平台
而定制的标准。（Lua的出现也是建立在ANSI C优秀的跨平台表现的基础上的）

序言中顺便提到了本书第二版新增的一些内容（如标准库等），作为C语言之父，K&R（是两个人）的对C语言的相关叙述应
该算是很权威的，所以这本书的阅读价值也是有的，最起码在语言设计的思想上应该是表达得最准确而简洁的。

第一版的序言没有什么好说的，毕竟是八十年代的东西。

引言是对标准C语言的概述以及对书中内容编排的说明。

接下来是很重点的目录分析！（其实有点像引言中的最后部分，不过这里是按照我个人的阅读感觉描述）

1. [**A Tutorial Introduction**](#a-tutorial-introduction)  
    感觉就是一C语言的quick-start，估计信息量很大。
2. [**Types Operators and Expressions**](#types-operators-and-expressions)  
    应该算是一个语言的组成元素了，倾向于Syntax。
3. [**Control Flow**](#control-flow)  
    控制流是命令式编程(Imperative programming)中很重要的几种语句的支持（运算、循环、条件、无条件）
4. [**Functions and Program Structure**](#functions-and-program-structure)  
    这部分就毕竟考验基本功了，对函数和各种变量常量等的[Scope][1]的说明，比较期待这章。
5. [**Pointers and Arrays**](#pointers-and-arrays)  
    看看K&R是怎么一章把指针讲清楚的！
6. [**Structures**](#structures)  
    结构体联合体什么的，单独一章还是很有必要的，毕竟数据结构的构成都靠这东西。
7. [**Input and Output**](#input-and-output)  
    很多坑啊……文件的处理至今仍没完全搞清楚
8. [**The UNIX System Interface**](#the-unix-system-interface)  
    这部分很有兴趣！因为接触得毕竟少。
9. [**Reference Manual**](#reference-manual)  
    当语言标准看就好。
10. [**Standard Library**](#standard-library)  
    考验背诵的时候到了，顺便学学用setjmp写异常处理的宏。
11. [**Summary of Changes**](#summary-of-changes)
    俩版本的不同。

诶我发现好像没单独讲宏的章节啊，我想看看K&R是怎么花式写宏的哇。

### A Tutorial Introduction

### Types Operators and Expressions

### Control Flow

### Functions and Program Structure

### Pointers and Arrays

### Structures

### Input and Output

### The UNIX System Interface

### Reference Manual

### Standard Library

### Summary of Changes


[1]: https://en.wikipedia.org/wiki/Scope_(computer_science)

---
## 后记
应该是不会有的了
