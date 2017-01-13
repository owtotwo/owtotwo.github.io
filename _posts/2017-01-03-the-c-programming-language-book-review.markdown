---
layout:     post
title:      "The C Programming Language - Second Edition" Book Review
subtitle:   "《C程序设计语言第二版》阅读记录"
date:       2017-01-03
author:     "owtotwo"
header-img: "img/hill.jpg"
tags:
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

希望能在有生之年把这个坑填完…

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

### Functions and Program Structure

### Pointers and Arrays

### Structures

### Input and Output

### The UNIX System Interface

### Reference Manual

### Standard Library

### Summary of Changes


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

---
## 后记
应该是不会有的了
