---
layout:     blog
title:      "Incorrect time display when you Dual-boot Windows and Unix"
subtitle:   "Windows和Unix双系统的时间不同步问题"
date:       2017-02-04
author:     "owtotwo"
header-img: "img/hello-blog.jpg"
tags:
    - 操作系统
    - 双系统
---

自从装了Win10+Ubuntu16.04双系统后，两个系统的时间并不一致，也就是说打开了一个系统并调整好时间
后，再打开另一个系统就会发现时间快/慢了8个小时。这是一个遗留问题，一直没有解决。

当然，八小时这个特殊的数字会让人第一时间联想到北京在东八区，即GMT+8。那问题肯定出在时间的换算与否
上了。但因为俩系统一直是设置了自动从网络获取时间，所以等一会儿后就会自动修改回来。

但是前几天装了黑苹果，发现时间也不太对的上，那么很可能就是Unix和Windows在某些时间换算上的取舍不
一致导致了这个问题。

<!-- more -->

首先要了解的概念是时区([Time Zone][1])。学过初中地理的同学应该都是知道这东西的。那么理所当然地
产生了时间的表示方式的差异了。人们生活在同一个地球上，理应用相等的值来表示“同时”的概念，但在需求上
来说人们也需要将一天24小时与早中晚或者其他时间划分上一一对应。就好比12点该吃中午饭，晚上12点该睡
觉，那么全世界可能都是基本一致的。

因此由这一矛盾也就产生了“时间标准”与“时区”的各种时间的定义。

首先是UTC：

[Coordinated Universal Time (UTC)][2] is the primary time standard by which the 
world regulates clocks and time.

然后是GMT：

[Greenwich Mean Time (GMT)][3] is the mean solar time at the Royal Observatory in 
Greenwich, London. GMT was formerly used as the international civil time standard,
now superseded in that function by Coordinated Universal Time (UTC).

最明显的区别是：

GMT is a time zone and UTC is a time standard. UTC is an atomic time scale which 
only approximates GMT with a tolerance of 0.9 second.

因为地球是椭圆的，所以GMT这种旧式的记录方式会有偏差，现在基本都是使用UTC作为国际时间标准的了，所
以我们解决这个问题就以UTC为基准吧。

好吧其实产生上述问题的原因跟UTC和GMT无关……它们两个可以认为是相等的，问题出在**Windows将硬件时间
认做local time，而Unix认做UTC time/GMT time**。因为**系统显示的时间皆为local time**，所以
当Windows启动的时候，Windows直接取硬件时间作为时间显示，而Unix则进行换算（在东八区是+8小时）后
再显示。

鉴于一般来说都是Windows比较奇葩（好吧应该说特立独行），所以解决方法就是**将Windows的硬件时间识
别方式从local time改为UTC time/GMT time**。

在Windows的cmd（需要管理员权限）输入

```
> Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v 
RealTimeIsUniversal /t REG_DWORD /d 1
```

回车即可。(如果是Windows家庭版的那就没办法了……)

Reference:  
http://www.cnblogs.com/shareidea/p/5465306.html  
http://lifehacker.com/5742148/fix-windows-clock-issues-when-dual-booting-with-os-x  

[1]: https://en.wikipedia.org/wiki/Time_zone
[2]: https://en.wikipedia.org/wiki/Coordinated_Universal_Time
[3]: https://en.wikipedia.org/wiki/Greenwich_Mean_Time