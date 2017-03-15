---
layout:     post
title:      "Rust Introduction"
subtitle:   "安利一波Rust"
date:       2017-03-15
author:     "owtotwo"
header-img: "img/hello-blog.jpg"
tags:
    - Tag
---

> Rust大法好！

`created on 2017-03-15`

官网：https://www.rust-lang.org/zh-CN/

我们首先跑上去中文官网扫一眼，一眼看到了特点那儿：

* 零开销抽象：这东西一句话很难描述，强行解释的话我会描述成“对同一个抽象的不同具体实现开销一致并且不会有额外的开销”。知乎上也有一个我比较认可的描述：“只用只需要用的，不用不需要的(@周贤)”。另外可以参考[zero-cost-abstractions](https://ruudvanasseldonk.com/2016/11/30/zero-cost-abstractions)这篇文章，写得很不错。可能你会听说ruby的抽象程度很高而导致了它的效率会相对较慢（对于底层而言也就是同一个抽象行为运行更多的机器指令），那么你可以理解为rust在编译器帮你做了表达式化简而使得rust呈现出just do what it should do的效果。

* 转移语义：如果用过C++11的move（或者说了解过右值引用）那么就很好理解了，同一个东西只有一份实体，这对于抽象来说很有意义，避免不必要的拷贝开销。

* 保证内存安全：也就是这点让很多人喜欢上了rust（同时也不再使用C++…），你再也不用看到segmentation fault了。

* 没有数据竞争的线程：如果没有写过多线程的代码的话可能不太有感觉，但是在这个多核时代，并行的重要性得以体现，函数式再次火了起来，相信rust的这个特性也会让rust在接下来几年（2017年后）里坚强地活着。

* trait泛型：这个我目前还没搞懂，不敢乱说，大概就是用trait这个语言特性来实现泛型。

* 模式匹配：学过函数式的应该不必多说了，我感觉自己对模式匹配理解还不够深。

* 类型推断：不是C++11那种auto的type deduction而是[type inference](https://en.wikipedia.org/wiki/Type_inference)

* 极小运行时：让用rust写嵌入式的程序成为可能！

* 高效的C绑定：毕竟积累不够深，能用已有的别人的积累也是极好的，C有很多成熟的库，那么rust也可以借此拿来用用。

点了官网的`安装 Rust x.y.z`（此时是1.15.1），通过rustup来给macOS安装好了rust（记得跑一下source ~/.cargo/env)，同时可以用rustup管理各种nightly版本，毕竟更新得很勤快嘛！

学习资料的话当然是官方推荐的[Rust 程序设计语言](https://kaisery.gitbooks.io/rust-book-chinese/content/)啦。

扔个hello world就跑！

``` rust
fn main() {
    println!("Hello World!");
}
```

`没了喵`