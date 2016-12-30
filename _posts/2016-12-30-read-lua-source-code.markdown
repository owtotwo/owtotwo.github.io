---
layout:     post
title:      "Lua5.3源码阅读计划"
subtitle:   "Reading for fun!"
date:       2016-12-30
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

[1]: https://www.reddit.com/r/programming/comments/63hth/ask_reddit_which_oss_codebases_out_there_are_so/c02pxbp/

## 后记
暂时略过
