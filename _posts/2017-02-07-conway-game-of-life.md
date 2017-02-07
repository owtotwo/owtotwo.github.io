---
layout:     post
title:      "Conway's Game of Life"
subtitle:   "康威生命游戏函数式实现"
date:       2017-02-07
author:     "owtotwo"
header-img: "img/water.jpg"
tags:
    - Racket
    - Scheme
    - Functional Programming
    - Computer Science
    - Game of Life
---

> Solve problems · Make languages -- Racket

明淇大腿跟我提到有Racket可以画画图，就跟Mmathematica一样，我就兴致勃勃地跑去看看官方的
[Quick Tutorials](https://docs.racket-lang.org/quick/index.html)和
[The Racket Guide](https://docs.racket-lang.org/guide/index.html)，
简直有毒啊！

实话说文档写得还可以，但是画图什么的谷歌一搜啥都没有啊这么大堆的东西鬼知道怎么绘图啊。

然后靠着只看过两分钟Scheme语法和一小节SICP的基础写了一个极其难看的康威生命游戏的实现，反正这种东西
能跑就行了不管了。

用Racket可以跑跑（这么丑的代码竟然是我写出来的无法接受）

``` Racket
#lang slideshow

(require racket/gui/base)

; A Conway's Game of Life

(define (enum x)
  (define (enum-iter i)
    (if (= i (length x))
        '()
        (append (list (cons i (list-ref x i))) (enum-iter (+ i 1)))))
  (enum-iter 0))

(define alive #t)
(define dead  #f)
(define (alive? x) (equal? x alive))
(define (dead? x)  (equal? x dead))
(define (show-cell x)
  (if (alive? x)
      (rectangle 10 10)
      (filled-rectangle 10 10)))

(define (show-grid grid)
  (apply vc-append
         (map (lambda (x) (apply hc-append (map show-cell x))) grid)))

(define (Conway-Game row col)
  (define frame (new frame% [label "Conway-Game-of-Life"]
                            [width (* row 10)]
                            [height (* col 10)]))
  (define canvas (new canvas% [parent frame]))
  (define (grid-ref grid x y)
    (list-ref (list-ref grid x) y)) 
  (define (rand-grid)
    (define (rand-state)
      (if (= (random 1 3) 1) alive dead))
    (define (make-list len elem-gen)
      (if (= len 0)
          '()
          (append (make-list (- len 1) elem-gen) (list (elem-gen)))))
    (make-list row (lambda () (make-list col rand-state))))
  
  (define (evolve grid)
    (define (grid-get x y)
      (if (or (< x 0) (< y 0) (>= x row) (>= y col))
          '()
          (list (grid-ref grid x y))))
    (define (environment x y)
      (define (neighbours x y)
        (define (neighbour-get n)
          (cond ((= n 1) (grid-get (- x 1) (- y 1)))
                ((= n 2) (grid-get (- x 1) y))
                ((= n 3) (grid-get (- x 1) (+ y 1)))
                ((= n 4) (grid-get x (- y 1)))
                ((= n 5) (grid-get x (+ y 1)))
                ((= n 6) (grid-get (+ x 1) (- y 1)))
                ((= n 7) (grid-get (+ x 1) y))
                ((= n 8) (grid-get (+ x 1) (+ y 1)))))
        (define (iter n)
          (if (= n 0)
              '()
              (append (iter (- n 1)) (neighbour-get n))))
        (iter 8))
      (count alive? (neighbours x y)))
    (define (trans x y)
      (let ((alive-cells (environment x y)))
        (if (alive? (grid-ref grid x y))
            (cond ((< alive-cells 2) dead)
                  ((< alive-cells 4) alive)
                  (else              dead))
            (cond ((= alive-cells 3) alive)
                  (else              dead)))))
    (map
     (lambda (m)
       (let ((x (car m)))
         (map
          (lambda (n)
            (let ((y (car n)))
              (trans x y)))
          (enum (cdr m)))))
     (enum grid)))
  (define (game-loop grid count)
    (unless (= count 0)
      (begin
        (send canvas refresh-now
             (lambda (dc)
                (draw-pict (show-grid grid) dc 0 0)))
        (send frame show #t)
        (sleep 0.25)
        (game-loop (evolve grid) (- count 1)))))
  (game-loop (rand-grid) -1)) ; -1 means dead loop

(Conway-Game 20 20)
```

反正看着它跑还是挺过瘾的。