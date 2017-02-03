---
layout:     post
title:      "Timed Reminder"
subtitle:   "捣鼓一下定时提醒功能"
date:       2017-02-03
author:     "owtotwo"
header-img: "img/wood.jpg"
tags:
    - Ubuntu
    - Whim
---

忽然需要个类似于手机的定时任务的提醒功能，一开始是想着自己写一个的，但是造轮子似乎不太好，意义不大。

然后当然就是跑去Ubuntu Software看看有没有这种小软件了，发现有一个叫Alarm-Clock-Applet的东西。

配合Ubuntu下的`notify-send`命令，用了一下感觉还可以，但是提醒的时候和`notify-send`的气泡撞上
了，感觉怪怪的，然后就想着既然有`notify-send`这个东西，那么只需要一个定时执行的软件就OK了。

接着就是crontab出场了。

`crontab -e`来编辑当前用户（可用`crontab -u <user> ...`来指定用户）的任务内容。（首次编辑需
要选择默认编辑器，选vim或者nano都是可以的。

具体用法直接看编辑时的注释，简单的格式描述如下：

```
 +---------------- minute (0 - 59)
 |  +------------- hour (0 - 23)
 |  |  +---------- day of month (1 - 31)
 |  |  |  +------- month (1 - 12)
 |  |  |  |  +---- day of week (0 - 6) (Sunday=0 or 7)
 |  |  |  |  |
 *  *  *  *  *  command to be executed 
```

举个例子：

```
1 2 3 4 5 notify-send haha
```

意思是在某年4月3号是星期五的时候的02:01am执行命令`notify-send haha`也就是有个气泡提示“haha”。

```
00 19 * * 1 notify-send hey man
```

意思是每个星期一的晚上七点整用气泡显示标题为hey内容为man的提示。

然而事实上你试一下会发现并没有成功，这应该跟GUI有关（如Gnome和Unity），命令需要在前面加上
`export DISPLAY=:0.0 &&`，所以正确的内容如下：

```
00 19 * * 1 export DISPLAY=:0.0 && notify-send hey man
```

好了就这样。