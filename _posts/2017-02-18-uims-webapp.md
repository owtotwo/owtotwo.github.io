---
layout:     post
title:      "User Information Management System"
subtitle:   "用Express + Bootstrap + MongoDB + Jade实现简单用户管理系统"
date:       2017-02-18
author:     "owtotwo"
header-img: "img/water.jpg"
tags:
    - Computer Science
    - Web
    - JaveScript
    - NodeJS
    - Express
    - Bootstrap
    - MongoDB
    - Jade
    - Whim
---

> Maybe AngularJS or CoffeeScript in the future?

`created on 2017-02-18`

最近被迫要写Web，所以就把上学期的Web课最后一次没做的作业拿出来写写。

大概目标就是写一个储存用户信息的系统，用户可以登录进系统（其实就一个页面）来对自己的信息进行修改。

主要着眼点在后端，前端暂且就随便一点。

其实后端也很随便。

## 需求
* 注册用户信息
* 登录管理系统
* 更新用户信息

## 实现
从无到有一步步地将整个webapp搭建起来吧！

1. 利用npm创建一个项目：

打开工作目录，输入：

`> mkdir uims && cd uims && npm init`

按提示输入相应的信息。(entry point填app.js，因为是用Express)

2. 创建需要用到的目录：

`> mkdir routes public views public/css public/img`

其中routes存放路由（根据不同的URL进行分发），public存放静态文件，views存放jade文件（将被渲染
成html的视图文件）。

3. 安装所需库：

`> npm install express mongoose --save`

通过npm这个包管理软件安装express4和mongodb的驱动mongoose。（--save表示将dependencies添加
到package.json文件中）

4. 先用尽量少的代码将app跑起来：

在工作目录的根目录下创建并编辑文件app.js：

``` javascript
// app.js

// 引入必要的express库
var express = require('express');

// 创建app
app = express();

// 分析根路径的url的get请求，箭头函数请学习ES6，下同
app.get('/', (req, res, next) => res.send('Hello UIMS!'));

// 监听端口3000并输入提示日志
app.listen(3000, () => console.log('listening at http://localhost:3000/'));
```

运行一下看看：

`> node app.js`

打开http://localhost:3000/即可看到输出`Hello UIMS!`。

express的核心概念是”中间件(middleware)”。用不严谨的说法就是express的整个运作过程是通过两个
核心参数`req`（请求）和`res`（响应），从第一个中间件传递到最后一个中间件（或者传递到一半就结束）
然后根据`res`的内容来返回相应的内容给请求者。每个中间件都会选择性地（路由）根据`req`携带的信息
来对`res`进行加工，或者丰富`req`自身的信息，像流水线一样。

而中间件的使用就是通过`app.METHOD()`或者`app.use()`来实现的，中间件就是一个函数，并且每个中
间件使用的顺序很重要。

5. 首先进行错误处理

无论是编写什么程序，错误处理都是必不可少的，首要考虑。

这里要做的就是处理404的页面，意思就是如果所有的路由都没有结束（如res.send()），那么就会进入到
这个处理Not Found的中间件中。

``` javascript
// app.js

// ...

// 假若上面的中间件（路由等）中没有结束，那就无条件地进入到这个中间件，除错误处理中间件以外，其
// 他的中间件都是三个参数。
app.use((req, res, next) => {
    // 创建一个错误并向后传递
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
});

// 错误处理中间件
app.use((err, req, res, next) => {
    // 状态码500也就是服务器炸了的意思，双竖线是短路操作
    res.status(err.status || 500);
    // 发送消息'error'
    res.send('error');
});

// 监听端口3000并输入提示日志
app.listen(3000, () => console.log('listening at http://localhost:3000/'));
```

这时候运行并访问 http://localhost:3000/ 除根目录以外的路径，就会看到'error'。

6. 打印日志

但是作为服务器，在命令行处啥都没显示，感觉不太好，起码也提醒一下用户访问了啥状态是啥吧。

所以我们先着手将log输出出来，这时候就要利用第三方的中间件morgan了（虽然这个中间件是express团
队维护的）。

`> npm install morgan --save`

并且修改app.js：

``` javascript
// app.js

// 引入必要的express库
var express = require('express');
// 引入morgan作为logger
var logger = require('morgan');

// 创建app
app = express();

// 在app的最前面使用打log的中间件并设置log的格式为'dev'，其他格式请看官方文档
app.use(logger('dev'));

// ...
```

这时候运行下并访问 http://localhost:3000/ 就会看到命令行处有log出来啦。

7. 错误页面使用模板渲染

当访问其他页面的时候只显示一个error好像有点简陋，起码也得说声是什么错了吧，这时候就需要在不同的
错误下显示不同的错误页面，这时候模板引擎jade就可以上场了。

用过flask的或者用python写过web的同学可能有使用过jinjia2，jade大同小异，不过更优雅，使用缩进
来分清层次。

在views目录下创建一个错误页面的模板文件error.jade：

``` jade
// error.jade
doctype html
html
  head
    title= title
  body
    h1= message         // 错误信息，即变量res.locals.message
    h2= error.status    // 错误状态，即变量res.locals.error.status
    pre #{error.stack}  // 错误栈，即变量res.locals.error.stack
```

那怎么使用它呢？

首先是先设置模板引擎为jade：

``` javascript
// app.js

// 引入必要的express库
var express = require('express');
// 引入morgan作为logger
var logger = require('morgan');
// 引入nodejs自带的path处理库
var path = require('path');

// 创建app
app = express();

// 设置app的settings的views值（模板文件存放的路径）以供模板引擎使用
app.set('views', path.join(__dirname, 'views'));
// 设置模板引擎为jade
app.set('view engine', 'jade');

// 在app的最前面使用打log的中间件并设置log的格式为'dev'，其他格式请看官方文档
app.use(logger('dev'));

// ...
```

然后将`res.send('error')`变成渲染模板并响应。

``` javascript
// app.js

// ...

// 错误处理中间件
app.use((err, req, res, next) => {
    // 设置生命周期为res的生命周期的局部变量，以供模板error.jade使用
    res.locals.message = err.message;
    // 同上，并且当运行环境为开发环境时才设置局部变量error
    res.locals.error = req.app.get('env') === 'development' ? err : {};
    // 设置响应状态，状态码500也就是服务器炸了的意思，双竖线是短路操作
    res.status(err.status || 500);
    // 渲染模板error.jade并响应
    res.render('error');
});

// ...
```

`待续`