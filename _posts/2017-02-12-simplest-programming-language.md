---
layout:     post
title:      "Simplest Programming Language"
subtitle:   "极简编程语言"
date:       2017-02-12
author:     "owtotwo"
header-img: "img/wave.jpg"
tags:
    - Computer Science
    - Programming Language
    - Scheme
    - Lisp
    - Whim
---

> No more simple.

# Goal #

用Scheme(R5RS)实现连整数四则运算计算器都不如的超迷你语言 _atsay_ (a simple subset of 
Scheme)的解释器。

# Definition of _atsay_ #

## Lexical structure ##

```
<token>       ::=  <number> | <identifier> | ( | )
<delimiter>   ::=  <whitespace> | ( | )
<whitespace>  ::=  <space> | <newline>
<number>      ::=  <digit> [<digit> ...]
<identifier>  ::=  +
```

## Expression ##

```
<program>     ::=  <expression>
<expression>  ::=  <literal>
              ::=  ( <expression> )
              ::=  ( + <expression> <expression> )
<literal>     ::=  <integer>
<integer>     ::=  <number>
```

## Sample ##

``` atsay
(+ (+ (1) 2) (+ 10 01))
```

## Implementation ##

``` Scheme
#lang r5rs

(define atsay:version "0.0.1")

(define atsay:in (current-input-port))   ;; stdin
(define atsay:out (current-output-port)) ;; stdout
(define atsay:err (current-error-port))  ;; stderr

;;----------- Aux ------------

(define pass '())

(define (unimplemented)
  (atsay:error "Unimplemented!"))

; read-line: [port] -> string
; read a line as a string from stdin (or port).
(define read-line
  (lambda ports
    (let ((in (if (= (length ports) 1)
                  (car ports)
                  (current-input-port))))
      (define (read-chars-until-newline)
        (let ((c (read-char in)))
          (if (char=? c #\newline)
              '()
              (cons c (read-chars-until-newline)))))
      (apply string (read-chars-until-newline)))))

(define (atsay:read-line)
  (read-line atsay:in))

(define (atsay:display s)
  (display s atsay:out))

(define (atsay:write s)
  (write s atsay:out))

(define (atsay:error e)
  (begin
    (display e atsay:err)
    (newline atsay:err)
    (scheme-report-environment -1))) ;; hope it works

;;----------------------------

; void -> void
; entry of interpreter
(define (atsay)
  (begin
    (display (string-append "Atsay " atsay:version "\n"))
    (atsay:repl)))

; void -x>
; read-eval-print-loop
(define (atsay:repl)
  (atsay:display ">> ")
  (atsay:write (atsay:scan (atsay:read-line)))
  (atsay:display "\n")
  (atsay:repl))

; string -> token-list
; lexer
(define (atsay:scan s)
  (if (string? s)
      (chars->tokens (string->list s))
      (atsay:error "atsay program should be a string")))

; char-list -> token-list
(define (chars->tokens chars)
  (reverse (make-tokens '() '() chars)))

; token-list, char-list, char-list -> token-list
(define (make-tokens tokens some rest)
  (cond
    ((and (null? rest) (null? some))
     tokens)
    ((null? rest)
     (cons (chars->token some) tokens))
    ((null? some)
     (let ((c (car rest)))
       (cond
         ((char-whitespace? c)
          (make-tokens tokens '() (cdr rest)))
         ((char=? c #\()
          (make-tokens (cons (list 'left-p #\() tokens) '() (cdr rest)))
         ((char=? c #\))
          (make-tokens (cons (list 'right-p #\)) tokens) '() (cdr rest)))
         (else
          (make-tokens tokens (cons c some) (cdr rest))))))
    (else
     (let ((c (car rest)))
       (cond
         ((char-whitespace? c)
          (make-tokens (cons (chars->token some) tokens) '() (cdr rest)))
         ((char=? c #\()
          (make-tokens (cons (list 'left-p #\()
                             (cons (chars->token some) tokens))
                       '()
                       (cdr rest)))
         ((char=? c #\))
          (make-tokens (cons (list 'right-p #\))
                             (cons (chars->token some) tokens))
                       '()
                       (cdr rest)))
         (else
          (make-tokens tokens (cons c some) (cdr rest))))))))

(define (chars->token chars)
  (if (null? chars)
      (atsay:error "cannot create a token from an empty list of char")
      (let ((value (apply string (reverse chars))))
        (let ((num (string->number value)))
          (if num
              (list 'number num)
              (list 'identifier value))))))

; ???
; ???
(define (atsay:parse tokens)
  pass)

; ???
; ???
(define (atsay:eval ast)
  (unimplemented))


(atsay)
```