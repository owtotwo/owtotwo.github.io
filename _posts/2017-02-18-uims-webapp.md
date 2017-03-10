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
*   注册用户信息
*   登录管理系统
*   更新用户信息

## 实现
从无到有一步步地将整个webapp搭建起来吧！

1.  利用npm创建一个项目：

    打开工作目录，输入：

    `> mkdir uims && cd uims && npm init`

    按提示输入相应的信息。(entry point填app.js，因为是用Express)

2.  创建需要用到的目录：

    `> mkdir routes public views public/css public/img`

    其中routes存放路由（根据不同的URL进行分发），public存放静态文件，views存放jade文件（将被渲染
    成html的视图文件）。

3.  安装所需库：

    `> npm install express --save`

    通过npm这个包管理软件安装express4。（--save表示将dependencies添加到package.json文件中）

4.  先用尽量少的代码将app跑起来：

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

5.  首先进行错误处理

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

6.  打印日志

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

7.  错误页面使用模板渲染

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

    jade的语法请参考[segmentfault上的文章](https://segmentfault.com/a/1190000000357534)，
    现在jade已经更名为pug了。

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
        res.render('error', { title: err.message });
    });

    // ...
    ```

8.  注册页面

    错误处理算是搞定了，那么就进入正题。三个页面先写哪个呢？考虑到如果先写登录页或者信息页，那么
    就可能没有测试的数据了，所以我们优先写注册页。

    首先编写一个注册页的模板文件views/register.jade：

    ``` jade
    // register.jade
    doctype html
    head
      title= title
    body
      h1 Register
      form(method='post', action='/login')
        div
          label Username: 
          input(type='text', name='username', placeholder='Username')
        div
          label Password:
          input(type='password', name='password', placeholder='Password')
        div
          label Password Again:
          input(type='password', name='password again', placeholder='Password Again')
        div
          label Age:
          input(type='text', name='age', placeholder='Age')
        button(type='submit') Confirm
    ```

    暂时用户信息只有年龄作为例子，之后再逐步添加另外的信息。

    这里我们会发现有和error.jade模板重复的部分（即开头的doctype和head），我们可以提取公共部分。

    在views文件夹下新建公共模板layout.jade：

    ``` jade
    // layout.jade
    doctype html
    head
      title= title
    body
      block content
    ```

    同时修改另外两个jade文件：

    ``` jade
    // error.jade
    extends layout
    block content
      // 错误信息，即变量res.locals.message
      h1= message
      // 错误状态，即变量res.locals.error.status
      h2= error.status
      // 错误栈，即变量res.locals.error.stack
      pre #{error.stack}
    ```

    ``` jade
    // register.jade
    extends layout
    block content
      h1 Register
      form(method='post', action='/register')
        div
          label Username: 
          input(type='text', name='username', placeholder='Username')
        div
          label Password:
          input(type='password', name='password', placeholder='Password')
        div
          label Password Again:
          input(type='password', name='password again', placeholder='Password Again')
        div
          label Age:
          input(type='text', name='age', placeholder='Age')
        button(type='submit') Confirm
    ```

    既然注册页模板完成了，下面就是要在访问 http://localhost:3000/register 的时候渲染它并
    返回注册页。这时候就要用到路由来解析`/register`。

    在routes文件夹中添加路由文件register.js：

    ``` javascript
    // register.js

    var express = require('express');
    // 创建处理/register的路由本体
    var router = express.Router();

    // 在/register路径下解析根目录（即此路径本身）
    router.get('/', (req, res, next) => {
        res.render('register', { title: 'Register' });
    });

    // 作为package供app.js使用
    module.exports = router;
    ```

    并且在app.js中添加此路由：

    ``` javascript
    // app.js

    // ...
    var path = require('path');
    // 引入处理/register路径的路由
    var register = require('./routes/register');

    // ...
    app.get('/', (req, res, next) => res.send('Hello UIMS!'));

    // 为处理/register路径添加路由
    app.use('/register', register);

    // ...
    ```

    这时候打开 http://localhost:3000/register 就会看到注册页面了，非常丑，先不管了能用就好。

9.  处理注册请求

    点一下Confirm按钮就能在命令行处看到有个`POST /register`的请求，我们接下来就要处理这个。

    `GET /register`是返回注册页面给用户，`POST /register`是根据用户的request body的信息
    （也就是用户提交的信息如用户名密码还有年龄等）来在后台创建一个用户并存入到数据库中。这一步的
    内容有点点复杂，因为开始涉及到了数据库。

    首先我们来处理POST请求，在register.js中修改成链式调用来对同一个路径处理get和post：

    ``` javascript
    // register.js

    // ...

    // 在/register路径下解析根目录（即此路径本身）
    router.route('/')
        .get((req, res, next) => {
            res.render('register', { title: 'Register' });
        })
        .post((req, res, next) => {
            res.send('您发出了Post请求');
        });

    // 作为package供app.js使用
    module.exports = router;
    ```

    先运行下看看效果。

    然后我们得知道用户的request body里的信息，这就需要parse以下request了，我们需要用到第三方
    中间件body-parser：

    `> npm install body-parser --save`

    然后使用它：

    ``` javascript
    // app.js

    // ...
    var register = require('./routes/register');
    // 引入body-parser来解析post请求
    var bodyParser = require('body-parser');

    // ...
    app.use(logger('dev'));
    // 使用其中的URL-Parser
    app.use(bodyParser.urlencoded({ extended: false }));

    // ...
    ```

    当请求头里的Content-Type为`application/x-www-form-urlencoded`时就会进行parsing，
    一般的表单提交都是这种格式。（而ajax提交json的时候则需要用到body-parser中的json部分）

    这时候我们就可以在req.body中查看用户提交的内容了。


10. 创建用户并存入数据库

    既然已经收到了请求并且拿到了解析出来的用户的请求内容，那么我们就可以进行创建用户并存入数据库
    的操作了。

    首先在你的电脑上安装mongoDB。（非企业版的是免费的，具体下载安装请参考官网）

    然后安装mongodb的驱动库mongoose，使用方法请参考[mongoose官网](http://mongoosejs.com/)。
    （这东西贼好用）

    `> npm install mongoose --save`

    在根目录下创建一个models文件夹用于存放用户类模型，并在其中创建一个User.js：

    ``` javascript
    // User.js

    // 引入mongoose来驱动mongoDB
    var mongoose = require('mongoose');

    // 连接到数据库，具体host和端口请参考自己系统中的mongoDB服务进程信息。
    // 若数据库不存在则会自动创建一个新数据库
    mongoose.connect('mongodb://127.0.0.1:27017/uims');
    
    // 判断连接成功与否
    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
        console.log('Success to Connect the MongoDB...');
    });

    // 创建用户类模型
    var User = mongoose.model('User', {
        username: String,
        password: String,
        age: Number
    });

    module.exports = User;
    ```

    此时就可以使用这个类模型来创建用户并储存下来了，修改register.js如下：

    ``` javascript
    // register.js

    var express = require('express');
    // 创建处理/register的路由本体
    var router = express.Router();
    // 使用用户类模型
    var User = require('../models/User');

    // 在/register路径下解析根目录（即此路径本身）
    router.route('/')
        .get((req, res, next) => {
            res.render('register', { title: 'Register' });
        })
        .post((req, res, next) => {
            console.log('Requset to register a user: \n' + 
                        '  Username: %s\n' + 
                        '  Password: %s\n' + 
                        '  Age:      %d\n',
                        req.body.username, 
                        req.body.password, 
                        req.body.age);
            // 密码确认
            if (req.body['password'] !== req.body['password again']) {
                res.send('两次输入的密码不一致');
            }
            // 创建新用户
            var user = new User({
                username: req.body.username,
                password: req.body.password,
                age: req.body.age
            });
            // 保存新用户到数据库中
            user.save((err) => {
                if (err) {
                    console.log('Failed to save the new user `%s` for `%s`', 
                                user.username, err.message, err);
                    res.send('注册失败');
                } else {
                    console.log('Save the new user `%s`', user.username);
                    //注册成功则重定向至登录页
                    res.redirect('/login');
                }
            });
        });

    // 作为package供app.js使用
    module.exports = router;
    ```

    至此我们完成了注册的部分。

11. 实现登录及cookie功能

    登录功能实质就是判断用户输入是否与数据库中的数据匹配。

    首先实现一个登录页面接收用户的输入：

    ``` jade
    // login.jade
    extends layout
    block content
      h1 Login
      form(method='post', action='/login')
        div
          label Username: 
          input(type='text', name='username', placeholder='Username')
        div
          label Password: 
          input(type='password', name='password', placeholder='Password')
        button(type='submit') Confirm
    ```

    跟注册界面差不多。我们给它在routes文件夹中添加一个路由文件login.js以便我们可以处理对
    `/login`的请求：

    ``` javascript
    // login.js

    var express = require('express');
    // 创建处理/login的路由本体
    var router = express.Router();
    // 使用用户类模型
    var User = require('../models/User');

    // 在/login路径下解析根目录（即此路径本身）
    router.route('/')
        .get((req, res, next) => {
            res.render('login', { title: 'Login' });
        })
        .post((req, res, next) => {
            console.log('Requset to login a user: \n' + 
                        '  Username: %s\n' + 
                        '  Password: %s\n',
                        req.body.username, 
                        req.body.password);
            User.findOne({ username: req.body.username }, (err, user) => {
                if (err) {
                    console.log(err);
                    res.send('数据库查询出错，可能不存在此用户');
                    return;
                }
                if (req.body.password !== user.password) {
                    console.log('密码错误');
                    res.send('密码错误');
                    return;
                }
                console.log('登录成功');
                // 成功登录则重定向至主页
                res.redirect('/');
            });
        });

    // 作为package供app.js使用
    module.exports = router;
    ```

    当然，我们得在app.js文件里引入此路由到本app中：

    ``` javascript 
    // app.js

    // ...
    var register = require('./routes/register');
    // 引入处理/login路径的路由
    var login = require('./routes/login');

    // ...
    app.use('/register', register);
    // 为处理/login路径添加路由
    app.use('/login', login);

    // ...
    ```

    好了现在我们可以成功登录并跳转至首页了。但是浏览器并没有记住我们的登录状态为已登录，这时候就
    需要用到cookie了。

    首先安装cookie-parser中间件：

    `> npm install cookie-parser --save`

    这东西有什么用呢？本来呢express是有自带的req.cookies和req.signedCookies的，但是需要这
    个中间件来解析request的header获得它们，所以我们就用它吧：

    ``` javascript
    // app.js

    // ...
    app.use(bodyParser.urlencoded({ extended: false }));
    // 使用cookie-parser中间件，里面的参数是为cookie签名的密钥
    app.use(cookieParser('uims_secret'));

    // ...
    ```

    然后我们需要在登录成功的时候为用户设置一个签名的cookie来作为通行证：

    ``` javascript
    // login.js

    // ...
    console.log('登录成功');
    // 为用户设置一个cookie作为通行证，signed表示为此cookie签名（使用uims_secret)
    res.cookie('username', user.username);
    res.cookie('alive', user.username, { signed: true });
    // ...
    ```

    现在我们登录后可以在F12控制台看到我们的request带有含alive和username字段的cookie了，我
    们将使用它来授权其他页面（其实就只有用户信息页一个）的访问，也就是登录后的操作。

    因为express是通过一个个中间件串联起来的框架，有先后之分，所以我们只需要在需要授权的页面的
    路由前放置一个自定义的中间件就可以拦截没有带含alive和username的cookie的请求：

    ``` javascript
    // app.js

    // ...
    app.use('/login', login);

    // 拦截未登录用户，未登录的请求直接重定向至登录页面
    // 注：此中间件后面的路由只有登录后才能访问
    app.use((req, res, next) => {
        if (!req.isAuthorized)
            res.redirect('/login');
        else
            next();
    });

    // ...
    ```

    而req.isAuthorized的值我们需要对所有请求都进行处理，并通过其携带的cookie中的alive（使用
    了`uims_secret`这个字符串签名了的username，故cookie解密后在signedCookies里的alive值
    就是username的值）来为其赋值（编写中间件记得调用next）：

    ``` javascript
    // app.js

    // ...
    app.use(cookieParser('uims_secret'));

    // 为所有请求添加req.isAuthorized参数，通过判断是否携带含alive和username字段的cookie
    app.use((req, res, next) => {
        if (req.signedCookies.alive && req.signedCookies.alive == req.cookies.username) {
            req.isAuthorized = true;
        } else {
            req.isAuthorized = false;
        }
        next();
    });

    // ...
    ```

    我们为`/logout`写一个简单的路由，用于已登录的用户登出（实质为清除cookie），即此请求要求登录
    后进行：

    ``` javascript
    // app.js

    // ...

    // 已登录的用户请求/logout时即为它清除响应的cookies
    app.get('/logout', (req, res) => {
        res.clearCookie('username');
        res.clearCookie('alive');
        // 登出后重定向至登录页面
        res.redirect('/login');
    });

    // 假若上面的中间件（路由等）中没有结束，那就无条件地进入到这个中间件，除错误处理中间件以外，其
    // ...
    ```

    同时访问`/`主页也必须已登录，否则重定向至登录页面，故将：

    ``` javascript
    // app.js

    // 分析根路径的url的get请求，箭头函数请学习ES6，下同
    app.get('/', (req, res, next) => res.send('Hello UIMS!'));
    ```

    移至拦截未登录请求的中间件后面。

    而假如是已登录状态来访问/login，就应该直接重定向至主页，故在login.js里添加一小段：

    ``` javascript
    // login.js

    // ...

    // 在/login路径下解析根目录（即此路径本身）
    router.route('/')
        .get((req, res, next) => {
            // 如果已经登录那么就直接重定向至首页
            if (req.isAuthorized)
                res.redirect('/');
            else
                res.render('login', { title: 'Login' });
        })
    // ...
    ```

    至此我们完成了登录功能

`待续`