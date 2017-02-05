---
layout:     post
title:      "\"Structure and Interpretation of Computer Programs - Second Edition\" Learning Record"
subtitle:   "《计算机程序的构造和解释》学习记录"
date:       2017-01-17
author:     "owtotwo"
header-img: "img/wood.jpg"
tags:
    - Computer Science
    - Reading
    - SCIP
    - Functional Programming
    - Scheme
---

> This book is dedicated, in respect and admiration, to the spirit that lives in the 
computer.
>  
> "I think that it's extraordinarily important that we in computer science keep 
fun in computing. When it started out, it was an awful lot of fun. Of course, the 
paying customers got shafted every now and then, and after a while we began to 
take their complaints seriously. We began to feel as if we really were 
responsible for the successful, error-free perfect use of these machines. I don't 
think we are. I think we're responsible for stretching them, setting them off in 
new directions, and keeping fun in the house. I hope the field of computer 
science never loses its sense of fun. Above all, I hope we don't become 
missionaries. Don't feel as if you're Bible salesmen. The world has too many of 
those already. What you know about computing other people will learn. Don't feel 
as if the key to successful computing is only in your hands. What's in your hands,
I think and hope, is intelligence: the ability to see the machine as more than 
when you were first led up to it, that you can make it more."
>   
> Alan J. Perlis (April 1, 1922-February 7, 1990)

## 前言

`created on 2017-01-17`

有别于上一篇的博文（["The C Programming Language - Second Edition" Book Review][1]），本
文并非是对 _Structure and Interpretation of Computer Programs_ 一书的详细反馈及补充，而是
学习此书的过程记录。这是因为阅读前者是在已有C语言丰富的知识基础的情况下阅读的，所以只是将脑海里零碎的
知识点一一按照书中的目录结构列出，并根据网络资源及相关标准文案精准化。而阅读后者是在仅有部分计算机科学
领域的基础知识的前提下进行的，主要目的是为了初步学习函数式编程的相关思想以及抽象化程序设计的理念，同时
亲身感受一下这本被大多数编程者奉为神作的书的魅力。

虽然在一年内基本是不可能填完这个坑的，也许第一章也不能完成，但是坑还是要挖的。

## 正文

### 第一章 构造过程抽象

#### 第一节 程序设计的基本元素

*   练习1.1：

    `10 12 8 3 6 a b 19 #f 4 16 6 16`

*   练习1.2：

    ``` Lisp
    ; 缩进换行不好看就不换了
    (/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5)))))
       (* 3 (- 6 2) (- 2 7)))
    ```

*   练习1.3：

    ``` Lisp
    ; 返回三个数中较大的两个数之和
    ; 即三个数之和减去最小的那个数
    (define (sum-of-greatest x y z)
        (- (+ x y z)
           (min x y z)))
    ```

    这题翻译有点问题，原题意应该是求三个数中较大两个数的平方和。

    最初版：

    ``` Lisp
    (define (largest-two-square-sum x y z)
        (square-sum (first-largest x y z)
                    (second-largest x y z)))
    
    (define (square-sum x y)
        (+ (square x) (square y)))

    (define (square x) (* x x))

    (define (first-largest x y z)
        (larger (larger x y) z))
    
    (define (larger x y)
        (if (> x y) x y))

    ; 这个写得好丑啊
    (define (second-largest x y z)
        (cond ((= x (first-largest x y z)) (larger y z))
              ((= y (first-largest x y z)) (larger x z))
              ((= z (first-largest x y z)) (larger x y))))
    ```

    修改版：
    
    ``` Lisp
    (define (largest-two-square-sum x y z)
        (cond ((= x (min x y z)) (square-sum y z))
              ((= y (min x y z)) (square-sum x z))
              ((= z (min x y z)) (square-sum x y))))
    
    (define (square-sum x y)
        (+ (square x) (square y)))

    (define (square x) (* x x))
    ```

    网上的花式解法：
    ``` Lisp
    (define (sum-of-squares x y z)  
        (+ (square (max x y)) (square (max (min x y) z))))

    (define (square x) (* x x))
    ```

    真是费脑……

*   练习1.4：

    ``` Lisp
    (define (a-plus-abs-b a b)
        ((if (> b 0) + -) a b))
    ; 如果b为正数则用a加b否则a减b，实现了a与b的绝对值相加的过程
    ; 这里突出了运算符（这里为'+'和'-'）也可以作为过程的运算对象（非第一个参数）
    ; 而不仅仅是运算过程（第一个参数）
    ```

*   练习1.5：

    ``` Lisp
    (define (p) (p))    ; 只要调用过程p或展开p则会无限递归
    (define (test x y)  ; 根据假定条件（if过程首先求值谓词部分）
        (if (= x 0)     ; 若以应用序求值则会先求值0然后求值(p)，即得死循环
            0           ; 若以正则序求值则会先展开为(if (= 0 0) 0 (p))得真值
            y))         ;   返回0，而p部分不求值
    (test 0 (p))        ; 假定条件应该就是为了防止(p)展开而设定的
    ```

    貌似第四章会详细讲解正则序和应用序，但估计看不到那么后。

*   顺手写个斐波那契数列：

    ``` Lisp
    (define (fib n)
        (if (< n 3) 1 (+ (fib (- n 1) (fib (- n 2))))))
    ```

    用mit-scheme跑了一下发现很慢，最多大概就(fib 35)左右，(fib 40)不知道等到什么时候才跑得完。

    ``` Lisp
    ; tail-recursive
    (define (fib-aux n sum1 sum2)
        (if (< n 3) sum1 (fib-aux (- n 1) (+ sum1 sum2) sum1)))
    (define (fib n) (fib-aux n 1 1))
    ```

    这个有尾递归优化([Tail_call][2])所以跟循环一样快，大意就是将临时变量作为参数传递。

*   练习1.6：

    无限递归sqrt-iter，即死循环。因为new-if是一个常规过程，而Scheme是应用序求值，即先算出各参数
    表达式的值再进行过程运算，所以会产生无限递归。

*   练习1.7：

    对于很小的数的平方根而言，0.001可能是一个很大的误差；而对于很大的数的平方根而言，guess的值的
    平方很可能溢出而使得运算出错。如：

    ``` Lisp
    (good-enough? 0.01 0.0002) ; #t
    (good-enough? 4938271605 9876543210) ; maybe overflow for 32bit int
    ```

    改进：

    ``` Lisp
    (define (sqrt x) (sqrt-iter 1.0 x))

    (define (sqrt-iter guess x)
        (if (good-enough? guess x)
            guess
            (sqrt-iter (improve guess x) x)))

    (define (good-enough? guess x)
        (> (rate (square guess) x) (- 1 0.0001)))  ; error in 0.0001

    (define (improve guess x)
        (average guess (/ x guess)))

    (define (square x) (* x x))

    (define (average x y) (/ (+ x y) 2))

    ; return rate which is less than 1
    (define (rate x y)
        (if (> (abs x) (abs y)) (/ y x) (/ x y)))
    ```

    对于很大很小的数都能工作。

*   练习1.8：

    ``` Lisp
    ; 牛顿迭代法求立方根
    (define (cbrt x) (cbrt-iter 1.0 x))

    (define (cbrt-iter guess x)
        (if (good-enough? guess x)
            guess
            (cbrt-iter (improve guess x) x)))

    (define (good-enough? guess x)
        (> (rate (cube guess) x) (- 1 0.0001)))  ; error in 0.0001

    (define (improve guess x)
        (/ (+ (/ x (square guess)) (* 2 guess)) 3))

    (define (square x) (* x x))
    (define (cube x) (* x x x))

    ; return rate which is less than 1
    (define (rate x y)
        (if (> (abs x) (abs y)) (/ y x) (/ x y)))
    ```

*   练习1.9：

    ``` Lisp
    (define (+ a b)
        (if (= a 0)
            b
            (inc (+ (dec a) b))))

    (+ 4 5)
    (inc (+ 3 5))
    (inc (inc (+ 2 5)))
    (inc (inc (inc (+ 1 5))))
    (inc (inc (inc (inc (+ 0 5)))))
    (inc (inc (inc (inc 5))))
    (inc (inc (inc 6)))
    (inc (inc 7))
    (inc 8)
    9

    ; 以上计算过程是递归的，因为整个求值过程是一个推迟执行的运算
    ```

    ``` Lisp
    (define (+ a b)
        (if (= a 0)
            b
            (+ (dec a) (inc b))))
    
    (+ 4 5)
    (+ 3 6)
    (+ 2 7)
    (+ 1 8)
    (+ 0 9)
    9 
    ; 以上计算过程是迭代的，因为其状态可以用固定数目的状态变量描述
    ```

*   练习1.10：

    ``` Lisp
    ; Ackermann Function
    (define (A x y)
      (cond ((= y 0) 0)
            ((= x 0) (* 2 y))
            ((= y 1) 2)
            (else (A (- x 1)
                     (A x (- y 1))))))
    
    (A 1 10)
    (A 0 (A 1 9))
    (A 0 (A 0 (A 1 8)))
    (A 0 ... (A 0 (A 1 1)))
    (A 0 ... (A 0 2))
    (A 0 ... 4)
    (A 0 512)
    1024

    (A 2 4)
    (A 1 (A 2 3))
    (A 1 (A 1 (A 2 2)))
    (A 1 (A 1 (A 1 (A 2 1))))
    (A 1 (A 1 (A 1 2)))
    (A 1 (A 1 (A 0 (A 1 1))))
    (A 1 (A 1 (A 0 2)))
    (A 1 (A 1 4))
    (A 1 (A 0 (A 1 3)))
    (A 1 (A 0 (A 0 (A 1 2))))
    (A 1 (A 0 (A 0 (A 0 (A 1 1)))))
    (A 1 (A 0 (A 0 (A 0 2))))
    (A 1 16)
    2^16

    ; 由上可知 (A 1 m) 即 2^m

    (A 3 3)
    (A 2 (A 3 2))
    (A 2 (A 2 (A 3 1)))
    (A 2 (A 2 2))
    (A 2 (A 1 (A 2 1)))
    (A 2 (A 1 2))
    (A 2 (A 0 (A 1 1)))
    (A 2 (A 0 2))
    (A 2 4)
    2^16

    ; 没有找到啥规律…

    (define (f n) (A 0 n)) ; f(x) = 2 * x

    (define (g n) (A 1 n)) ; g(x) = 2 ^ x

    (define (h n) (A 2 n)) ; h(x) = 2 ^ 2 ^ ... ^ 2 (x in total)
    ```

*   练习1.11

    ``` Lisp
    ; 递归版
    (define (f n)
        (if (< n 3)
            n
            (+ (f (- n 1))
               (* (f (- n 2)) 2)
               (* (f (- n 3)) 3))))

    ; 迭代版
    (define (f n) (f-iter 0 1 2 n))

    (define (f-iter a b c count)
        (cond ((= count 0) a)
              ((= count 1) b)
              ((= count 2) c)
              (else (f-iter b c (+ c (* 2 b) (* 3 a)) (- count 1)))))
    ```

*   练习1.12

    ``` Lisp
    ; 杨辉三角递归版
    (define (pascal-triangle x y)
        (cond ((= y 1) 1)
              ((= y x) 1)
              (else (+ (pascal-triangle (- x 1) (- y 1))
                       (pascal-triangle (- x 1) y)))))
    ```

*   练习1.13

    ``` Lisp
    ; 数学题…先跳过吧，以前好像做过
    ; 这本书贼难
    ```


[1]: https://owtotwo.github.io/2017/01/03/the-c-programming-language-book-review/
[2]: https://en.wikipedia.org/wiki/Tail_call

## 后记
不会有的。