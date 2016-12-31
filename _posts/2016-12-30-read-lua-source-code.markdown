---
layout:     post
title:      "Lua5.3源码阅读计划"
subtitle:   "Reading for fun!"
date:       2016-12-31
author:     "owtotwo"
header-img: "img/wave.jpg"
tags:
    - Lua
    - Compiler
---

> "Huh... it will never be done."

## 前言
暂时略过

## 正文
`created on 2016-12-30`

谷歌发现reddit上有这个[lua源码阅读建议][1]，虽然是5.1版本的。

> Recommended reading order:  
>
> - lmathlib.c, lstrlib.c: get familiar with the external C API. Don't bother with the pattern matcher though. Just the easy functions.
>
> - lapi.c: Check how the API is implemented internally. Only skim this to get a feeling for the code. Cross-reference to lua.h and luaconf.h as needed.
>
> - lobject.h: tagged values and object representation. skim through this first. you'll want to keep a window with this file open all the time.
>
> - lstate.h: state objects. ditto.
>
> - lopcodes.h: bytecode instruction format and opcode definitions. easy.
>
> - lvm.c: scroll down to luaV_execute, the main interpreter loop. see how all of the instructions are implemented. skip the details for now. reread later.
>
> - ldo.c: calls, stacks, exceptions, coroutines. tough read.
>
> - lstring.c: string interning. cute, huh?
>
> - ltable.c: hash tables and arrays. tricky code.
>
> - ltm.c: metamethod handling, reread all of lvm.c now.
>
> - You may want to reread lapi.c now.
>
> - ldebug.c: surprise waiting for you. abstract interpretation is used to find object names for tracebacks. does bytecode verification, too.
>
> - lparser.c, lcode.c: recursive descent parser, targetting a register-based VM. start from chunk() and work your way through. read the expression parser and the code generator parts last.
>
> - lgc.c: incremental garbage collector. take your time.
>
> - Read all the other files as you see references to them. Don't let your stack get too deep though.
>
> If you're done before X-Mas and understood all of it, you're good. The information density of the code is rather high.

源码的话当然是选择最新版本来阅读了，原因有两个：一是5.3版本区分了浮点数(float)和整数(int)，GC采用增量式
垃圾回收算法，虚拟机基于寄存器而使得instructions的数量大大减少；二是我之前写的lua都是用的5.3版本，也就
顺其自然地看这个版本了。虽然相比于很早之前的版本代码量增加了约一倍，也就是20k行左右的标准C，但整体上应该变
化不会非常大。

这里提供了很多的源码阅读相关建议和链接： http://lua-users.org/wiki/LuaSource  
Lua5.3源码在线阅读： http://www.lua.org/source/5.3/  

--- 
`update on 2016-12-31`  

看了云风的Blog发现有 _The Implementation of Lua 5.0_ 的译文，也就顺便中英对照着看了。先阅读了前六节
（共八节），发现还是有不少看不懂的地方。  

-   第一节是介绍Lua5.0的特点，如基于寄存器的虚拟机、散列表用作数组时的新优化算法、闭包的特殊实现和协程的加
    入等。

-   第二节是提出Lua的设计理念和目标，如简洁高效、可移植（ANSI C）、可嵌入并易于嵌入（即实现需小巧高效）。

-   第三节讲解了Lua的值的内部表示，即其八种基本类型在C中的实现结构。Lua将值表示成带标志的联合结构(tagged 
    unions)，这里的联合更像是组合，用(tag, value)表示。其中value储存值或相应的引用（指针）。特别的，字
    符串因常被用于比较等操作，所以lua会对内部化(internalized)了的字符串(string)进行hash并保存下hash
    值，此后可通过对比hash值来快速判断两字符串是否相等。

-   第四节讲了Lua的表(table)。因为表作为lua唯一的表示数据结构的方式（数组通过表的整数索引来支持），意义重
    大。因为这种混合型结构，Lua5.0中，对被用作数组的表使用了一种特殊的优化方式，即对于整数索引，将不保存此
    键（即索引），而是将值存入一个真正的数组（C的原生数组）中。并且此数组的内存空间大小将由一种特殊的算法来
    确定（较复杂，具体需看文章）。另外散列表那一part的实现暂时看不太懂（本节最后一段）。

-   第五节讲了函数和闭包的实现。在编译期会生成函数原型，在运行期会创建闭包（即函数原型的引用、环境的引用、
    upvalue引用的数组）。upvalue即外部局部变量，引用[Quora中一个答案][2]的描述： “The local 
    variable that has been closed over by that function 'jumps up' into the new scope 
    which is why it's called an upvalue.”。另外本节最后一段的flat闭包不太能理解。  

    鉴于译文的翻译有点迷，难以理解，所以我在这里翻译一下第五节的倒数第二段（关键段落）：  

    **原文**： Mutable state is shared correctly among closures by creating at most one 
    upvalue per variable and reusing it as needed. To ensure this uniqueness, Lua
    keeps a linked list with all open upvalues (that is, those that still point to the
    stack) of a stack (the pending vars list in Figure 4). When Lua creates a new
    closure, it goes through all its outer local variables. For each one, if it can find
    an open upvalue in the list, it reuses that upvalue. Otherwise, Lua creates a new
    upvalue and links it in the list. Notice that the list search typically probes only
    a few nodes, because the list contains at most one entry for each local variable
    that is used by a nested function. Once a closed upvalue is no longer referred by
    any closure, it is eventually garbage collected.

    **译文**：对每个变量通过创建至多一个upvalue（按需重用）来保证它们在不同的闭包间正确地被共享。为了保证
    其唯一性，Lua维护一个链表来储存所有打开状态(open)的upvalues（即仍指向栈中变量的upvalues）。当Lua创
    建一个新的闭包时，Lua将遍历所有其外部局部变量。对于其中的每一个，如果能在链表中找到相应的打开状态的
    upvalue，就使用这个upvalue。否则，Lua将会创建一个新的upvalue并且将其插入到链表中。注意其中的链表搜
    索过程一般只用检查少数几个节点，因为链表为每个被嵌套函数使用到的局部变量包含了至多一个入口（即
    upvalue）。一旦一个关闭状态(closed)的upvalue不再被任何闭包引用时，其储存空间最终就会被GC回收。  

-   第六节讲的是线程和协程（其实就是协程，与一般程序的线程(thread)不是一个东西）。

`待续...`

[1]: https://www.reddit.com/r/programming/comments/63hth/ask_reddit_which_oss_codebases_out_there_are_so/c02pxbp/
[2]: https://www.quora.com/What-are-upvalues-in-Lua

## 后记
暂时略过
