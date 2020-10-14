---
layout:     blog
title:      "\"The C Programming Language - Second Edition\" Book Review"
subtitle:   "《C程序设计语言第二版》阅读记录"
date:       2017-01-03
author:     "owtotwo"
header-img: "img/hill.jpg"
tags:
    - Computer Science
    - Reading
    - C
---

> "Ok, let's begin."

## 前言
`created on 2017-01-03`

忽然心血来潮想挖个小坑，约280页的 _The C Programming Language_ 全书阅读浅评～
复习马原压力大，容易让人想减压，挖坑是一个很好的方式，有效地转移了主要矛盾，符合马克思主义的现实指导思想。
为什么我会选这本书呢，根本原因有三个：

1. 非常薄，比C primer plus薄到不知哪里去了。
2. C语言应用的广泛性，也同时因为有Unix这个强大的后盾，基本再过20年也不会被彻底淘汰的。
3. C语言是较“低级”的编程语言，特别是作为毕竟原始的系统编程语言，C语言毫无疑问是精简而底层的。（较接近汇编）

希望能在有生之年把这个坑填完…（填完了）

<!-- more -->

---

## 正文

### Preface / Preface to the First Edition / Introduction / Contents

大意是说1978年从这本书的初版开始在计算机科学领域发生了很大的变化，C也有了其标准，即ANSI C，是一份为跨平台
而定制的标准。（Lua的出现也是建立在ANSI C优秀的跨平台表现的基础上的）

序言中顺便提到了本书第二版新增的一些内容（如标准库等），作为C语言之父，K&R（是两个人）的对C语言的相关叙述应
该算是很权威的，所以这本书的阅读价值也是有的，最起码在语言设计的思想上应该是表达得最准确而简洁的。

第一版的序言没有什么好说的，毕竟是八十年代的东西。

引言是对标准C语言的概述以及对书中内容编排的说明。

接下来是很重点的目录分析！（其实有点像引言中的最后部分，不过这里是按照我个人的阅读感觉描述）

1. [**A Tutorial Introduction**](#a-tutorial-introduction)  
    感觉就是一C语言的quick-start，估计信息量很大。
2. [**Types Operators and Expressions**](#types-operators-and-expressions)  
    应该算是一个语言的组成元素了，倾向于Syntax。
3. [**Control Flow**](#control-flow)  
    控制流是命令式编程(Imperative programming)中很重要的几种语句的支持（运算、循环、条件、无条件）
4. [**Functions and Program Structure**](#functions-and-program-structure)  
    这部分就毕竟考验基本功了，对函数和各种变量常量等的[Scope][1]的说明，比较期待这章。
5. [**Pointers and Arrays**](#pointers-and-arrays)  
    看看K&R是怎么一章把指针讲清楚的！
6. [**Structures**](#structures)  
    结构体联合体什么的，单独一章还是很有必要的，毕竟数据结构的构成都靠这东西。
7. [**Input and Output**](#input-and-output)  
    很多坑啊……文件的处理至今仍没完全搞清楚
8. [**The UNIX System Interface**](#the-unix-system-interface)  
    这部分很有兴趣！因为接触得毕竟少。
9. [**Reference Manual**](#reference-manual)  
    当语言标准看就好。
10. [**Standard Library**](#standard-library)  
    考验背诵的时候到了，顺便学学用setjmp写异常处理的宏。
11. [**Summary of Changes**](#summary-of-changes)  
    俩版本的不同。

诶我发现好像没单独讲宏的章节啊，我想看看K&R是怎么花式写宏的哇。

---

### A Tutorial Introduction

`updated on 2017-01-11`

这章是C语言的快速入门教程。下面列出其中的一些有趣的点：

*   对于文中的第一个例程，即hello world：

    ``` c
    #include <stdio.h>

    main()
    {
        printf("hello, world!\n");
    }
    ```

    首先我们注意到main函数是没有返回值的，这并非是Undefined behavior，因为根据各版本的标准我们可以知道
    不标明返回值程序就会返回一个不确定的值，但这并非是未定义行为。（注意区分两者差别，也就是说若是一个未定义
    行为，那么这个程序可以选择死循环也可以选择偷偷打电话给你点一份外卖，也就是运行程序后程序的执行行为是不确
    定的，但是返回unspecified value只表明返回的值不确定，但其他行为是可以确定的。

    > A return from the initial call to the main function is equivalent to calling the exit function with the value returned by the main function as its argument. If the } that terminates the main function is reached, the termination status returned to the host environment is unspecified.

    我们用不同的编译器进行测试，发现常用的编译器(gcc, clang)都会适当给出warning，而tcc则是没有任何表
    示：
    ![different compiler](/img/c-programming/main-return-unspecified-value.png)

    相应的运行程序返回值也能得出unspecified value：
    ![unspecified value](/img/c-programming/main-return-unspecified-value.png)

    然后文中说通过`cc hello.c`命令进行编译，这里又有一个小知识，就是cc究竟代表什么？
    我们的实验环境是Ubuntu16.04 based on x86-64：
    ![cc](/img/c-programming/cc.png)
    可以看出其实它就是gcc，然而这事实是属于平台相关的，也就是说在SunOS上可能就是另外的编译器了，如MacOS上
    是clang……

*   在上一点中我们引出了一个有趣而经典的话题，就是C的各个版本的标准问题。
    在本书中使用的是ANSI C也就是传说中的C89/C90的最经典版本，根据维基百科的定义[ANSI C][2]我们可以得
    知最重要的三个版本就是[C89/C90][3]，[C99][4]以及[C11][5]。我们主要讨论的是最早的版本，但也会设计部分的最新版本，毕
    竟需要与时俱进。

    main函数的标准在多个版本中都基本是如下的定义：

    > 5.1.2.2.1 Program startup  
    1. The function called at program startup is named main. The implementation declares 
    no prototype for this function. It shall be defined with a return type of int and 
    with no parameters:  
        `int main(void) { /* ... */ }`  
    or with two parameters (referred to here as argc and argv, though any names may be
    used, as they are local to the function in which they are declared):  
        `int main(int argc, char *argv[]) { /* ... */ }`  
    or equivalent;) or in some other implementation-defined manner.  
    -- From **ISO/IEC 9899:1999 TC3 (n1256)**

    也就是说虽然main函数可以不需要显示写出返回值或者可以返回void值，但标准建议理应是返回int值的。（其他平
    台与实现相关，如某些嵌入式平台可能需要返回值为void）

*   **C语言程序都是由函数和变量组成的。**

*   如果字符串字面量中包含\0那么字符串会在对应地方截断，如

    ``` C
    printf("hello\0 world!\n");
    ```

    会显示`hello`并且不带换行。具体请看维基的[转义字符][6]。

*   由上一点我想到了最近在知乎上看到的一道C语言劝退题，差点就被劝退了：

    ``` C
    #include <stdio.h>
    #include <string.h>

    typedef char(*AP)[5];

    AP foo(char* p) {
        for (int i = 0; i < 3; i++) {
            p[strlen(p)] = 'A';
        }
        return (AP)p+1;
    }

    int main() {
        char s[] = "FROG\0SEAL\0LION\0LAMB";
        puts(foo(s)[1] + 2);
        return 0;
    }

    /*
    作者：孙明琦
    链接：https://zhuanlan.zhihu.com/p/24799071
    来源：知乎
    著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
    */
    ```

    答案的话自己编译运行看看吧我就不剧透了，做不出来的话还是不要学C语言毕竟好……

*   在1.2节中华氏温度转摄氏温度的程序中，提到了整数和浮点数作运算时的算术类型提升问题，这也是很重要的一点，    但是我们跟着书本，将这个知识点放在第二章来讲解。

*   1.4的最后提到printf和putchar两个函数可以交替调用，输出的次序与调用的次序一致。那什么情况下输出的次序
    与调用的次序不一致呢？这时候我们联想到了C++中的cout和C的printf的同步问题了：C++中ios_base库中有定义sync_with_stdio函数，当参数为true时cout则同步stdio中的printf，在这里就不展开讲了。

*   1.5中实现了一个类似[echo][7]的程序，即输出所有输入的字符：

    ``` C
    #include <stdio.h>

    /* 将输入复制到输出，版本一 */
    main() {
        int c;

        c = getchar();
        while (c != EOF) {
            putchar(c);
            c = getchar();
        }
    }
    ```

    我们现在可以写成

    ``` C
    #include <stdio.h>
    /* 将输入复制到输出，现代版本 */
    int main() {
        int c;
        while ((c = getchar()) != EOF)
            putchar(c);
        return 0;
    }
    ```

    这里有个小知识点，为什么c的类型要声明成int呢？因为getchar这个函数返回的是int，但是为什么呢？
    因为EOF可能是-1也可能不是，EOF只保证是一个负整数常量的宏定义，一般为-1

    > which expands to a negative integral constant expression that is
    returned by several functions to indicate end-of-file ,that is, no
    more input from a stream;

    如果这里声明了`char c`而不是`int c`，那么有可能char是无符号整数而导致EOF被转换为非负整数（char并不
    是unsigned char或者signed char，他们是三种类型）再提升为int，而使得`c != EOF`永远不成立。

    具体可以看看阮一峰老师写的一篇[关于EOF的博文][8]。

*   字符字面量的类型是int，也就是说sizeof('a') == sizeof(int)。
    > An integer character constant has type int. (From ANSI C Draft 3.1.3.4)

*   注意事实上并没有else if这种语法，只是因为if-else语句嵌套而出现的if...else if...else...的情况。

    > if ( expression ) statement [ else statement ]  （方括号为选）

    ```
    if (cond_1)
        statement_1
    else if (cond_2)
        statement_2
    else
        statement_3
    ```

    实际上是

    ```
    if (cond_1)
        statement_1
    else
        if (cond_2)
            statement_2
        else
            statement_3
    ```

    只是因为花括号可以省略而出现更加可读的else if的语句而已。

    并且我们经常错误地认为`if () {} else {}`中的花括号可以省掉是因为if-else语句支持有无花括号的两种
    形式，实际上是因为`{ many-statements }`也是一个statement，然后参照上面列出的公式代入即可。

    while同理，即`while (cond) { many-statements }` => `while (cond) statement`。

*   最后吐槽一下，最后一个练习1-24竟然要写一个C语言的语法正确性检查器……这是要裸写C的lexer和parser啊……

---

### Types Operators and Expressions

`updated on 2017-01-12`

*   2.1提到`Don't begin variable names with underscore since libaray routines often use 
    such names.`。

*   > 31 significant initial characters in an internal identifier or a macro name.

    > 6 significant initial characters in an external identifier.

    各种Translation limits具体请参考[ANSI C标准][3]的2.2.4.1部分。（如参数最多有31个之类的）

*   char == 1byte(不一定为8bits); 16bits <= short <= int <= long >= 32bits;

*   char不一定为unsigned char或signed char，又对应机器决定。

*   printable char > 0;

*   ![sizeof-types](/img/c-programming/sizeof-types.png)

*   卡在练习2-1了，没什么头绪，不知道怎么写才能简洁准确。

*   同一枚举中不同的名字可以拥有相同的常量。（冷知识）

*   > The expression that defines the value of an enumeration constant
    shall be an integral constant expression that has a value representable as an int.

*   2.4的最后一段说`The result is implementation-defined if an attempt is made to change
    a const.`是错的，不是implementation-defined而是Undefined-behavior。
    （具体请看[ANSI C标准][3] 3.5.3第/57/点）

`updated on 2017-01-13`

*   对于modulo operation，假设a (the dividend) and n (the divisor), a modulo n 
    (abbreviated as a mod n)，那么在[ANSI C][3]标准下运算结果的符号是Implementation-defined的，
    而[C99][4]则是与Dividend一致。

    另外mod这里对于大部分语言来说有个小坑，即不同语言进行mod操作后正负符号取决于dividend还是divisor
    的问题。如lua和python是与divisor一致（较特别），而C++98与[ANSI C][3]一样是
    Implementation-defined，C++11和[C99][4]以及大部分语言如Rust是与Dividend一致。

*   要在char类型中储存非字符数据，请使用signed char或者unsigned char。（这条炸了，这么多年一直都直
    接用char）即当需要将char转换为int时，请先转换为signed char或unsigned char再让其自动类型转换到
    int，否则正负无法确定。所以千万别用char做下标，有越界的可能，而数组越界是UB。

*   忽然想到，一个很通用的UB就是整数溢出，也就是说
    `int n = 0; while(n++) { ...(n not be changed) }`
    也算是Undefined-behavior，是不保证n从正到溢出为负再增到零然后结束循环的。

*   容我吐槽一下C语言实在太多UB了具体请看[ANSI C][3]的`A.6.2 Undefined behavior`部分。（非空源文
    件不以换行(new-line)结尾也是UB，我的天）

*   进行二元算术运算时不同的算术类型会进行类型提升，表达式整体的类型就取决于此，具体请看[ANSI C标准][3]
    中的`3.2.1.5 Usual arithmetic conversions`部分，或者阅读msdn上的[Usual Arithmetic 
    Conversions][9]。

*   2.7节中有一个有趣的部分，即rand和srand俩函数的实现，我查了一下标准发现它们就是标准里的Example：

    ``` C
    static unsigned long int next = 1;

    int rand(void)   /* RAND_MAX assumed to be 32767 */
    {
        next = next * 1103515245 + 12345;
        return (unsigned int)(next/65536) % 32768;
    }

    void srand(unsigned int seed) { next = seed; }
    ```

    用的是线性同余法求的伪随机数，具体看wiki上的[Linear congruential generator][10]。

*   2.8讲的是自增自减，这里坑毕竟多。

    + (i+j)++不合法，因为右值不能后缀自增，对于左值右值的问题（以及C++里的prvalue、xvalue、glvalue等
      不同类型的值）请参考wiki上的[Value (computer science)][11]。

    + `i++ + ++i`是UB，因为无法确定左操作数和右操作数哪一个先进行求值，同理`func(i++, i)`也是，
      `i = i++`也是，还有很多，具体原理请看序列点原理，即wiki上的[Sequence point][12]。其中最重要的
      一点是：

      > Between the previous and next sequence point a scalar object shall have its stored value modified at most once by the evaluation of an expression.

      摘录一段StackOverflow上关于[序列点产生UB][13]的内容：

      ``` C
      i++ * ++i;   // UB, i is modified more than once btw two SPs
      i = ++i;     // UB, same as above
      ++i = 2;     // UB, same as above
      i = ++i + 1; // UB, same as above
      ++++++i;     // UB, parsed as (++(++(++i)))

      i = (i, ++i, ++i); // UB, there's no SP between `++i` (right most) and assignment
                            to `i` (`i` is modified more than once btw two SPs
      ```
 
*   [strpbrk()][14]这个函数用来写lexer好像很不错啊之前都没有发现。

*   练习2-9有点有趣：

    ``` C
    /* bitcount函数：统计x中值为1的二进制位数 */
    int bitcount(unsigned x) {
        int b = 0;
        while (x &= (x - 1)) b++; /* 利用了x &= (x - 1)可以删除x中最右边值为1的一个二进制位 */
        return b;
    }
    ```

*   三元表达式（condition expression，即?:表达式)的类型由上面提到的[算术提升][9]有关。

*   善用三元表达式：

    ``` C
    /* 格式输出：每10个元素及最后一行换行，其余每个元素用空格隔开 */
    for (int i = 0; i < n; i++)
        printf("%6d%c", a[i], (i % 10 == 9 || i == n - 1) ? '\n' : ' ');
    ```

*   注意sizeof是运算符(operator)不是函数(function)，即事实上不是`sizeof(expr)`而是
    `sizeof expr`或`sizeof(type)`：

    ``` C
    puts(sizeof 'c' == sizeof(int) ? "Equal" : "Not Equal");
    
    /* Output: Equal */
    ```

*   `x = f() + g()`并非一定先运行f()再运行g()，两者求值顺序不定，详细请看上面的“序列点”部分。

*   下面代码会输出什么？

    ``` C
    #include <stdio.h>

    int n;

    void func(int _) { printf("%d\n", n++); }

    int main() {
        printf("%d\n", n);
        func(n++);
        printf("%d\n", n);
        return 0;
    }
    ```

    重点在于 _**标准规定所有对参数的副作用都必须在函数调用前生效**_。

### Control Flow

*   练习3-1中测不出binsearch函数在两种情况下的时间差，因为耗时实在太小了，贴上代码：

    ``` C
    int binsearch(int x, int v[], int n) {
        int first = 0, last = n, mid;

        while (first < last - 1) {
            mid = (first + last) / 2;
            x < v[mid] ? (last = mid) : (first = mid);
        }

        return v[first] == x ? first : -1;
    }
    ```

*   3.5中有提到希尔排序的实现，我认为wiki上的实现比书里的好一些：

    ``` C
    void shell_sort(int arr[], int len) {
        int gap, i, j;
        int temp;
        for (gap = len >> 1; gap > 0; gap >>= 1) {
            for (i = gap; i < len; i++) {
                temp = arr[i];              /* 保存下key值 */
                for (j = i - gap; j >= 0 && arr[j] > temp; j -= gap)
                    arr[j + gap] = arr[j];  /* 书里是在这里进行swap */
                arr[j + gap] = temp;        /* 而wiki这里是最后才恢复临时保存的key值 */
            }
        }
    }
    ```

    wiki上的以列排序解释步长排序挺清晰的，可以参考[Shellsort][15]。

*   atoi()和itoa()函数很有必要自己实现一遍，书里的实现很不错。特别是itoa有坑。（练习3-4）

*   3.8提到goto来跳出多层循环或进行统一错误处理，并显式说明不建议使用。

### Functions and Program Structure

*   [ANSI C][3]允许函数省略返回值，这种情况下返回值为int，所以以前可以写成`main() { return 0; }`
    而[C99][4]后必须要显式写为`int main() { return 0; }`，也可以省略为`int main() {}`，因为：

    > If the return type of the main function is a type compatible with int, a return 
    from the initial call to the main function is equivalent to calling the exit function 
    with the value returned by the main function as its argument; reaching the } that 
    terminates the main function returns a value of 0. (From 5.1.2.2.3 Program 
    termination in [C99 standard draft][4])

    所以现在就不能写没有返回值的函数了噢。

*   4.3信息量挺大的，提到了用外部变量共享栈来实现push和pop，以及用外部变量共享缓冲区（字符数组）实现getch
    和ungetch（即对应标准库的getchar和ungetc）。感觉代码写得挺优雅的……这节实现了一个逆波兰表达式计算
    器，很经典。

*   练习4-4的几种操作都和栈式计算机上的指令很像，如peek和dup什么的。

*   练习4-6是为计算器增加变量储存……想起了以前写过一个带变量的计算器，实际做起来也挺复杂的。

*   extern声明不需要指定数组的长度。

*   读4.7节register variables的时候忽然想到一个点，就是函数参数的类型声明可以有register修饰符但是不允
    许使用auto或者static等其它的storage-class specifier，顺便register这个关键词现在基本是被抛弃了。

*   4.9节中关于变量初始化有点和C++不一样的是：

    ![](/img/c-programming/variables-initialization.png)

*   这里可以看书此书写得很严谨，除了表达严谨，内容排布也很严谨，即不会有超前的知识点，也不会有过多的交叉引
    用等让初学者混乱的内容设计排布，如在3.3节中（尚未提到初始化）使用的是先声明后赋值。

*   4.10提到递归，有三个很经典的例子：

    ``` C
    /* Greatest Common Divisor */
    int gcd(int a, int b) {
        return b > 0 ? gcd(b, a % b) : a;
    }
    ```

    ``` C
    /* The Nth Fibonacci Number */
    int fib(int n) {
        return n < 3 ? 1 : fib(n - 1) + fib(n - 2);
    }
    ```

    ``` C
    /* Quicksort */
    void qsort(int v[], int left, int right) {
        if (left >= right) return;

        int key = v[left], i = left, j = right;
        while (i < j) {
            while (i < j && key <= v[j]) j--;
            v[i] = v[j];
            while (i < j && key >= v[i]) i++;
            v[j] = v[i];
        }
        v[i] = key;

        qsort(v, left, i - 1);
        qsort(v, i + 1, right);
    }
    ```

    书里的qsort也写得不错，只是需要额外的swap函数比较麻烦，练习4-13是用递归翻转字符串。

    ``` C
    #include <string.h>

    /* ugly code... */
    void reverse_aux(char s[], int left, int right) {
        if (left >= right) return;                  /* the terminating procedure */
        s[left] ^= s[right] ^= s[left] ^= s[right]; /* swap s[left] and s[right] */
        reverse_aux(s, left + 1, right - 1);        /* recursive */
    }

    void reverse(char s[]) {
        reverse_aux(s, 0, strlen(s) - 1);
    }
    ```

*   原来宏在4.11节预处理这部分啊…这节并没有提到`do { ... } while(0)`大法，只提到了宏的不卫生性，对
    应的有卫生宏([Hygienic macro][16])的概念。Scheme和Rust等语言有对应的卫生宏实现，rust的实现可
    以参考[Macro-By-Example][17]。

*   宏有很多黑魔法，在这里只提最常用的一个，就是连接两个token：

    ``` C
    /* Macros for concatenating tokens */
    #define suffix 1
    #define __CAT_TOKEN(A, B) A##B
    #define CAT_TOKEN(A, B) __CAT_TOKEN(A, B)
    #define FUNC(name) CAT_TOKEN(name, suffix) /* can not use '##' */
    ```

    附上一个简单的静态栈的宏实现：

    ``` Cpp
    // The Template Stack implementation in C Macro.

    #ifndef STACK_S_MACRO_H_
    #define STACK_S_MACRO_H_

    #ifdef __cplusplus
    extern "C" {
    #endif

    #include <stdlib.h> // for malloc

    typedef struct {
        void *top;
        void *bottom;
    } Stack_s;

    #define initStack(P, TYPE, SIZE) \
        do {\
            (P).top = (P).bottom = malloc(((SIZE) + 1) * sizeof(TYPE));\
        } while (0)

    #define destroyStack(P) \
        do {\
            free((P).bottom);\
        } while (0)

    #define sizeStack(P, TYPE) \
        ((size_t)(((TYPE*)(P).top) - ((TYPE*)(P).bottom)))

    #define emptyStack(P) \
        ((P).top == (P).bottom)

    #define pushStack(P, TYPE, VALUE) \
        do {\
            TYPE* tmp = (TYPE*)((P).top);\
            *tmp = (VALUE);\
            (P).top = (void*)(tmp + 1);\
        } while (0)

    #define popStack(P, TYPE) \
        do {\
            TYPE* tmp = (TYPE*)((P).top);\
            (P).top = (void*)(tmp - 1);\
        } while (0)

    #define peekStack(P, TYPE) \
        (*(((TYPE*)((P).top)) - 1))

    #ifdef __cplusplus
    }
    #endif

    #endif // STACK_S_MACRO_H_
    ```

    这里可能有人会问为什么要用`do { ... } while(0)`而不直接用`{ ... }`。考虑以下情况：

    ``` C
    if (<condition>)
        func_a();
    else
        func_b();
    ```

    如果func_a是个宏函数，并且是用`{ ... }`(block)实现，那么就会出现

    ``` C
    if (<condition>)
        { ... }         /* 因为是文本替换 */
        ;               /* 接下来是原来的分号，而非else，所以使得if语句结束 */
    else                /* else没有对应的if，编译错误 */
        func_b();
    ```

*   再次提醒初学者，头文件加哨兵防止重复包含很重要：

    ``` C
    /* a header file named XXX */
    #ifndef __XXX_H_
    #define __XXX_H_

    /* Some declarations */

    #endif /* __XXX_H_ */
    ```

### Pointers and Arrays

`updated on 2017-01-14`

*   `void *`是通用指针的类型。

*   类似*和++这样的一元运算符遵循从右至左的结合顺序，故`*p++`事实上等价于`*(p++)`。

*   C语言只有传值一种参数传递方式，因为指针也是值。

*   数组下标其实是偏移量(offset)，即`p[i]`等价于`*(p + i)`，其中i就是偏移量，故“从零开始”。

*   数组名不是变量，也不等价于指针常量，只是在作为 _函数形式参数_ 的情况下`char s[]`和`char *s`
    是等价的。

*   C语言只保证对任意大小为N的合法数组提供最多N+1的连续空间，即当对数组arr[N]取&arr[N + 1]时即为
    Undefined-behavior，即使不对其地址解引用（访问其元素）也是UB，这点需要注意。

*   C语言保证，0永远不是有效的数据地址。并且指向不同数组的元素的指针之间的算术或比较运算是未定义行为。

*   注意以下两种定义的区别：

    ``` C
    char amessage[] = "now is the time";    /* define an array */
    char *pmessage = "now is the time";     /* define a pointer */
    ```

    通过pmessage来试图修改字符串是未定义行为。

*   书中的strcpy()函数的实现很美观：

    ``` C
    void strcpy(char *s, char *t) {
        while (*s++ = *t++) /* loop */;
    }
    ```

*   练习5-7想说明访问栈空间比访问堆空间的速度快？

*   除数组的第一维可以不指定大小外，其余各维都必须明确指定大小。

*   5.9节对二维数组和指针数组的区别解释得很清楚，前者储存空间是矩形连续的，而后者不一定。

    ``` C
    int a[10][20];
    int *b[10];
    ```

    前者分配了大小为`10 * 20 * sizeof(int)`的空间，而后者只分配了`10 * sizeof(int*)`的空间。

*   5.10节的命令行参数是毕竟多初学者不清楚的，很多书都对其略过，但还是毕竟重要的。起码需要知道为什么
    main函数的signature是`int main(int argc, char* argv[])`，并且知道两个参数代表什么。

*   5.11中用qsort的改版来解说函数指针，这里用的应该是策略模式([Strategy pattern][18])。

*   5.12中实现了一个简单的递归下降分析算法来解析C语言的复杂的声明（如`char (*(*x[3])())[5]`这类），
    看得我一脸懵逼。

### Structures

`updated on 2017-01-16`

*   在所有运算符中，以下四个运算符优先级最高：结构运算符"."和"->"、用于函数调用的"()"以及用于下标
    的"[]"，即*p++->str意思是先读取指针str指向的对象的值，然后再将p加1。

*   记得有人问过，指针的步长值保存在哪里？事实上是在编译时编译器通过记住指针指向类型的大小乘以对应的步
    数来确定步长的。

*   再次提醒，其实sizeof是一个操作符(operator)而不是函数(function)，即它可以`sizeof expression`
    这样来使用的，而平常只是因为类型需要加上括号`sizeof(type)`所以才让人有它是函数的错觉。sizeof是一种
    编译时(compile-time)的一元运算符。（特别的，[C99][4]的[VLA][19]除外）

    > sizeof cannot be used with function types, incomplete types (including void), or 
    bit field lvalues.

    <del>这里的bit field lvalues不是很懂指代的是什么。</del>

    搞懂了，这句话有点错，应该是[C99][4]里的`an expression that designates a bit-field member`
    毕竟准确，即bit-field的struct的成员（如`struct { unsigned b: 3 } a;`这样的情况就不能
    `sizeof a.b`）。

    >  If the type of the operand is a variable length array([VLA][19]) type, the operand 
    is evaluated; otherwise, the operand is not evaluated and the result is an integer 
    constant.

    VLA是sizeof内表达式(expression)求值的唯一特例，贴上一段C99标准里的例子：

    ``` C
    #include <stddef.h>
    size_t fsize3(int n)
    {
        char b[n+3]; // variable length array
        return sizeof b; // execution time sizeof
    }

    int main()
    {
        size_t size;
        size = fsize3(10); // fsize3 returns 13
        return 0;
    }
    ```


*   注意所谓的”指针退化“指的是数组类型自动转换成指针类型的过程。

    ``` C
    #include <assert.h>

    void func(int b[]) {
        assert(sizeof b == sizeof(void*));
    }

    int main() {
        int a[10];
        assert(sizeof a / sizeof a[0] == 10);
        func(a); /* 这里数组a（类型为int[10]）退化成了指针b（类型为int*） */
    }
    ```

    为此现在的编译器基本都会报warning了。

*   因多次用到了ungetc这个东西，简单说下它的实现：基本思想就是getchar和ungetc共享一个缓冲区(buffer)，
    即一个变量。这个变量可以是一个字符或者是一个字符数组，这取决于你最多能ungetc多少内容。然后当调用
    ungetc时就将内容放进缓冲区，调用getchar时就先判断缓冲区是否为空，不为空则返回缓冲区的值，为空则从标
    准输入获取。特别的，为了储存EOF的值，可能缓冲区的类型需要是int而不是char（或者int[]）。

*   结构体的长度不等于各成员长度之和：

    ``` C
    struct {
        char c;
        int i;
    };
    ```

    可能需要`2 * sizeof(int)`这么多的bytes而不是`sizeof(char) + sizeof(int)`。

    C语言似乎并没有规定ABI。

`updated on 2017-01-17`

*   6.5节自引用结构部分提到相互引用，指针的大小是确定的所以非incomplete-type因此可以如下：

    ``` C
    struct t {
        struct s *p;
    }

    struct s {
        struct t *q;
    }
    ```

*   这节用了一个Word-count的例子来讲解使用自引用结构（二叉树）以及递归操作的程序示例。程序实现得很漂亮，核
    心接口是`struct tnode *addtree(struct tnode *p, char *w)`，即在p的位置或p的下方增加一个w节
    点。这部分还展示了二叉树的中序遍历，还提示了二叉树会出现的worst-case（线性查找）。

*   整型通常必须分配在偶数地址上（对其要求），为啥捏？据说第八章会讲解malloc的一种实现！

    这里想到一个知识点，其实对malloc的返回值是不需要强制类型转换的，因为`void *`会无条件转换为相应的其他
    指针类型（反之亦然），即对于一般写法`int *p = (int*)malloc(sizeof(int));`可以写成
    `int *p = malloc(sizeof(int));`，大家可以按照自己的习惯进行取舍。

*   6.6节讲了散列表的实现，其中出现了一个简短有效的hash函数用作映射字符串到非负整数下标：

    ``` C
    unsigned hash(char *s) {
        unsigned hashval;
        while (*s) hashval = *s++ + 31 * hashval;
        return hashval % HASH_SIZE;
    }
    ```

*   之前一直很容易把`typedef int Length;`中int和Length的位置混淆（因为`#define Length int`作用类
    似），但书中提到的typedef在语法上类似于储存类extern、static等，即通过`extern int Length;`容易记
    忆typedef语句的参数位置。

*   联合(union)事实上就是一个结构(struct)，它的所有成员相对于基地址的偏移量都为0。

    联合只能用其第一个成员类型的值进行初始化。

    在lua的实现中联合起了一个很重要的作用（用于统一表示lua的所有对象的类型）。


*   6.9节提到位字段，通常采用的方法是定义相应的屏蔽码([Mask][20])，即：

    ``` C
    #define KEYWORD  01
    #define EXTERNAL 02
    #define STATIC   04

    flags |=  (EXTERNAL | STATIC);              /* set flags to 1 */
    flags &= ~(EXTERNAL | STATIC);              /* set flags to 0 */
    if (flags & (EXTERNAL | STATIC) == 0) ...;  /* check the flags */
    ```

    而C语言提供了位域(bit-field)来供替代。只是某些机器上字段的分配是从字的左端至右端进行的，而某些
    机器上则相反（即大端小端问题）。这意味着，在选择外部定义数据的情况下，需要考虑大小端问题，即不可
    移植。所以在lua的实现中，使用的是上面提到的Mask方法而非位域。因为编译后的lua二进制程序与大端还
    是小端有关，而lua的目的是可移植的。

    >  C gives no guarantee of the ordering of fields within machine words, so if you do 
    use them for the latter reason, you program will not only be non-portable, it will be 
    compiler-dependent too.

    当你仅需要维护**内部定义的数据结构**时则可以使用。

*   至此基本上C语言的语法部分就差不多了，后两章讲的主要是输入输出和各种库函数的使用和实现，还有Unix的接口，
    内容将会比较多，估计是写不完了。

### Input and Output

*   补上一个冷知识[C_trigraph][21]，另在[C99][4]中"<: :> <% %> %: %|%:"都是合法的punctuator。

*   C语言不保证字符集一定是ASCII，所以用`(c >= 48 && c <= 57)`来代替`isdigit(c)`是不正确的。

*   以下两个表达式不等价：

    ``` C
    printf(s);          /* cannot include the '%' */
    printf("%s", s);    /* ok */
    ```

    以下是printf的格式例子：

    ``` C
    /* for "hello, world" (12 chars) */
    +---------------+----------------+
    |    format     |     output     |
    +---------------+----------------+
    |%s             |hello, world:   |
    |%10s           |hello, world:   |
    |%.10s          |hello, wor:     |
    |%-10s          |hello, world:   |
    |%.15s          |hello, world:   |
    |%-15s          |hello, world   :|
    |%15.10s        |     hello, wor:|
    |%-15.10s       |hello, wor     :|
    +---------------+----------------+
    /* ps: ':' for ending of string. */
    ```

    另还有`printf("%*.*s", min_width, precision, string);`。
    
*   对于printf的接口实现，书中引出了一个概念叫变长参数列表(Variable Argument List)，具体参考
    [stdarg.h][22]。下面演示一个简单的例子：

    ``` C
    #include <stdio.h>
    #include <assert.h>
    #include <stdarg.h>
    
    /* 找出并返回N个参数中最大的一个，N需要为正整数 */
    int max(int N, ...) {
        assert(N > 0);

        va_list ap;
        va_start(ap, N);

        int max_val = va_arg(ap, int);
        while (--N) {
            int tmp = va_arg(ap, int);
            if (tmp > max_val) max_val = tmp;
        }

        va_end(ap);

        return max_val;
    }

    int main() {
        printf("%d\n", max(4, -1, 3, 1, 0)); /* output: 3 */
    }
    ```

*   对于scanf，书中描述”成功匹配并赋值的输入项的个数将作为函数值返回“，即`scanf("%*c");`的情况
    下输入回车将会返回0，因为虽然匹配了一个值但是并没有赋值给输入项（没有输入项）而是直接抛弃。

    scanf无法直接匹配空白字符，因为格式参数format中的空格或制表符将会被忽略。

    总体来说scanf还是挺复杂的，文中提供了一个处理一般格式不确定的输入的例子：

    ``` C
    while (getline(line, sizeof(line)) > 0) {
        if (sscanf(line, "%d %s %d, &day, monthname, &year) == 3)
            printf("valid: %s\n", line);        /* 25 Dec 1988形式的日期数据 */
        else if (sscanf(line, "%d/%d/%d", &month, &day, &year) == 3)
            printf("valid: %s\n", line);        /* mm/dd/yy形式的日期数据 */
        else
            printf("invalid: %s\n", line);      /* 日期形式无效 */
    }
    ```

*   大家可以思考一下，stdin, stdout, stderr都是常量，那么freopen是如何做到将它们重定向的呢？
    （可能需要点操作系统里File Description的知识）

*   调用fclose是一个好习惯（而非必须），是因为一般来说操作系统会限制一个程序同时打开的文件数，并且
    fclose会执行fflush的行为，即把缓冲区中的内容写到对应文件上。而程序正常终止（调用exit亦可）的
    情况下程序会为每个打开的文件自动调用fclose。

*   吐槽一下malloc和calloc的接口不太一致，前者是”申请n个字节“而后者是”申请n个size字节大小的空间“。

*   练习7-9不是很能明白它想让我干嘛，用节省空间或时间的实现方式实现isupper，难道不是直接
    `#define isupper(c) ((c) >= 'A' && (c) <= 'Z')`吗。

`updated on 2017-01-28`

*   注意下rand()函数的最大取值是RAND_MAX。

*   生成[0, 1)的浮点数可以用`#define frand() ((double)rand() / (RAND_MAX + 1.0))`。（我认
    为这里的double类型转换应该是不用写的…因为1.0本身就是double，参考[ANSI C][3]中3.1.3.1部分中
    的“An unsuffixed floating constant has type double.”，然后根据[算术提升规则][9]，二者
    中有一为double则表达式为double，故除数也是double，同理frand()表达式整体也是double，即rand()
    的强制类型转换是不必要的）

### The UNIX System Interface

新年心情不好，就顺便更下吧。

开章部分有提到“因为ANSI C标准函数是以UNIX系统为基础建立起来的”很重要。

*   一个UNIX的常识是当用shell启动运行一个程序时，它将打开三个文件，分别是标准输入(0)、标准输出(1)、
    标准错误(2)，所以我们平常才可以直接printf或者cout（即ostream头文件里**定义**的类对象），而
    不用手动打开文件。

*   `echo hello >file 2>&1`为“标准输出重定向到file中，标准错误重定向到标准输出（现为file）中”。

*   原来C标准库中有个函数叫remove用于删除文件（底层是一般是unlink()函数或其他系统函数）。

*   文件指针指向的结构(FILE)的内容包括一个指向缓冲区的指针、一个记录缓冲区剩余字符数的计数器、一个指
    向缓冲区下一个字符的指针、文件描述符、读写标志、错误状态标志等。

*   一般以下划线开头的标识(zhi)符表示约定好仅供内部使用。

*   若在“有缓冲”的状态下输出，则一般会在缓冲区满时进行flush，而未满时则只填充缓冲区。

*   On Linux, and Unix in general, "r" and "rb" are the same. 

*   这章内容有点迷…略微有些看不懂，主要以一个用于递归显示文件（夹）大小的fsize的实例来描述UNIX的系统
    调用等知识，提到了inode（文件系统相关）还有文件夹也是文件等的系统相关的内容，反正我好像没学到啥。

`updated on 2017-02-03`

*   鉴于没搞懂8-6的fsize实例，我又认真看了一遍，把代码打了一遍，发现了一个很重要的问题，就是`read()`
    这个系统调用并不能用于文件夹文件（Unix下文件夹也是文件，若read函数的第一个参数是一个文件夹的文件
    描述符，则会出错返回-1，perror显示的结果是“: Is a directory”。所以若我的操作没有问题，那么书
    上的例程应该是不适用于POSIX.1-2001标准的。利用POSIX的`readdir()`、`opendir()`以及`closedir()`
    函数修改后的代码如下：

    ``` C
    #include <stdio.h>
    #include <string.h>
    #include <fcntl.h>
    #include <sys/types.h>
    #include <sys/stat.h>
    #include <unistd.h>
    #include <sys/dir.h>

    void fsize(char *);

    int main(int argc, char* argv[]) {
        if (argc == 1)
            fsize(".");
        else
            while (--argc > 0) fsize(*++argv);
    }

    void dirwalk(char *, void (*fn)(char *));

    void fsize(char *name) {
        struct stat stbuf;

        if (stat(name, &stbuf) == -1) {
            fprintf(stderr, "fsize: can't access %s\n", name);
            return;
        }
        if ((stbuf.st_mode & S_IFMT) == S_IFDIR)
            dirwalk(name, fsize);
        printf("%8ld %s\n", stbuf.st_size, name);
    }

    #define MAX_PATH 1024

    void dirwalk(char *dir, void (*fn)(char *)) {
        char name[MAX_PATH];
        struct dirent *dp;
        DIR *dfd;

        if ((dfd = opendir(dir)) == NULL) {
            fprintf(stderr, "dirwalk: can't open %s\n", dir);
            return;
        }
        while ((dp = readdir(dfd)) != NULL) {
            if (strcmp(dp->d_name, ".") == 0 ||
                strcmp(dp->d_name, "..") == 0)
                continue;
            if (strlen(dir) + strlen(dp->d_name) + 2 > sizeof(name))
                fprintf(stderr, "dirwalk: name %s/%s too long\n",
                        dir, dp->d_name);
            else {
                sprintf(name, "%s/%s", dir, dp->d_name);
                (*fn)(name);
            }
        }
        closedir(dfd);
    }
    ```

    仅供参考。

*   上面这个实例涉及到了与系统相关和与系统无关的代码，但仍非“系统程序”。

*   8-7中的malloc-free实例很有实践意义（据说CSAPP里也有提到？）。首先是提到一点，malloc管理的
    内存空间并一定不是连续的，因为如在Unix下，malloc所管理的空间是由`sbrk()`系统调用来获取相应
    的空间。其中当有空间申请请求时，会通过first-fit方法来扫描malloc所管理的空闲块链表（有点像散
    列表的线性探测法([Linear probing][23])。

    其中有一个重要的点就是内存空间对齐问题，即确保由malloc函数返回的储存空间满足将要保存的对象的对
    齐要求。

    > Computers commonly address their memory in word-sized chunks. A word is a
    computer’s natural unit for data. Its size is defined by the computers architecture.
    Modern general purpose computers generally have a word-size of either 4 byte (32
    bit) or 8 byte (64 bit). Classically, in early computers, memory could only be
    addressed in words. This results in only being able to address memory at offsets
    which are multiples of the word-size. It should be noted, however, that modern
    computers do in fact have multiple word-sizes and can address memory down to
    individual bytes as well as up to at least their natural word size. Recent computers
    can operate on even larger memory chunks of 16 bytes and even a full cache line at
    once (typically 64 bytes) in a single operation using special instructions.
    (From [Alignment in C][24])

    书中利用Union来实现最受限类型的对齐：

    ``` C
    typedef long Align;

    union header {
        struct {
            union header *ptr;
            unsigned size;
        } s;
        Align x;
    };

    typedef union header Header;
    ```

    整个内存管理的思想是通过空闲块链表来管理可用的内存块，每一个内存块都包含一个头部来储存相关信息
    （如size以及下一个块的指针等），每个内存块大小都是头部大小的整数倍。调用malloc时会从链表中取
    出合适的块并返回其可用地址（头部后面），而调用free时则会将空间放回链表中，若有相邻空闲块则合并。

### Reference Manual

等什么时候搞编译原理再补上这章吧。（虽然这么说但估计是没有的了）

### Standard Library

有空的话这章还是可以看看的，很多有用的没有了解过的东西，如头文件`<locale.h>`或者`<float.h>`等等。

再说吧。

### Summary of Changes

这章没什么好看的。

*   第一版的C竟然将8和9用作八进制数字……

*   原来以前是可以`a + = 1`这样用空格隔开的，即赋值增减是两个tokens。

*   算了吐槽这章也没啥意思。

---

## 后记

从开脑洞到今天为止刚好是一个月。当然了，实际看书和写东西的时间还是不多的，应该不到十天。寒假新年颓了
二十天左右，倒也挺舒服。

这本书是第一本尝试逐字弄懂的书，估计也是最后一本。因为看这本书的前提是我已经学过C语言并且用的时间也
不少，以及有一定程度的了解。并且这本书十分薄，两百多页的书，正文也有两百页不到。所以耗时也相对少很多，
更多的时间是耗在回忆知识点（各种坑）还有翻标准文档上，只有很少时间是花在学习吸收新知识和概念上，所以
对阅读SICP等书并没有参考价值，因为后者是在完全不了解的情况下全新地开展阅读。总的来说后者花费的时间应
该最少也是前者的四倍。

所以这篇东西叫做"Book Review"而不是"Learning Record"。

下一篇"Book Review"应该是《程序员的自我修养——链接、装载与库》，不要期待。

`updated on 2017-01-15`

最后补一个C语言劝退题，C89/C90/C99/C11皆可：以下代码能否编译通过？若不行，为什么？若可以，是否是未定义
行为？若是则指出对应位置，若不是那么是否会出现Runtime Error？若不会，输出是什么？若会，为什么？

``` C
#include <stdio.h>

#define r c
#define s t
#define _(a, b) a##b
#define __(a, b) _(a, b)
#define ___(a, b, c) __(__(a, b), c)
#define rev(f, a, b) f(b, a)
#define love(a) __(__(a, r), s)
#define for(a) love(a)
#define i(hate) {hate}
#define ever

int main() {
    char uct[] = "123\0x456";
    rev(__, uct, str) { char* str; }
    a[(char)2 % (signed char)253] = {
        {for(u)}, i(love(u)+4 ever)
    }, *p = a;
    printf("%""c""\\""n\n", (*p->str+=printf(&*p++->str), *++p->str));
}
```

如果这本书的所有内容以及我上面提到的所有内容以及这道劝退题都搞懂了的话，那么你应该可以跟别人说“C语言
吗，我之前学过”。当然，离精通还是很远的。

嗯我也是学过C语言的人了！


[1]: https://en.wikipedia.org/wiki/Scope_(computer_science)
[2]: https://en.wikipedia.org/wiki/ANSI_C
[3]: http://flash-gordon.me.uk/ansi.c.txt
[4]: http://www.open-std.org/jtc1/sc22/WG14/www/docs/n1256.pdf
[5]: http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1570.pdf
[6]: https://en.wikipedia.org/wiki/Escape_sequences_in_C
[7]: http://www.linfo.org/echo.html
[8]: http://www.ruanyifeng.com/blog/2011/11/eof.html
[9]: https://msdn.microsoft.com/en-us/library/3t4w2bkb.aspx
[10]: https://en.wikipedia.org/wiki/Linear_congruential_generator
[11]: https://en.wikipedia.org/wiki/Value_(computer_science)
[12]: https://en.wikipedia.org/wiki/Sequence_point
[13]: http://stackoverflow.com/questions/4176328/undefined-behavior-and-sequence-points
[14]: http://www.cplusplus.com/reference/cstring/strpbrk/
[15]: https://en.wikipedia.org/wiki/Shellsort
[16]: https://en.wikipedia.org/wiki/Hygienic_macro
[17]: https://www.cs.indiana.edu/ftp/techreports/TR206.pdf
[18]: https://en.wikipedia.org/wiki/Strategy_pattern
[19]: https://en.wikipedia.org/wiki/Variable-length_array
[20]: https://en.wikipedia.org/wiki/Mask_(computing)
[21]: https://en.wikipedia.org/wiki/Digraphs_and_trigraphs#C
[22]: https://en.wikipedia.org/wiki/Stdarg.h
[23]: https://en.wikipedia.org/wiki/Linear_probing
[24]: https://wr.informatik.uni-hamburg.de/_media/teaching/wintersemester_2013_2014/epc-14-haase-svenhendrik-alignmentinc-paper.pdf
