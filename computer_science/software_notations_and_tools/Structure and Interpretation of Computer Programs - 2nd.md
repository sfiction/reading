scheme load

## 1 构造过程抽象

### 1.1 程序设计的基本元素

- 基本表达形式
- 组合的方法
- 抽象的方法：为复合对象命名，并当作单元去操作

#### 1.1.3 组合式的求值

- 求值子表达式
- 将运算符应用于实际参数

特殊形式的求值规则，如 define（似乎都是语法糖，仍然可以被统一描述

```scheme
(define (<function-name> <formal-parameters>) <body>)
```

#### 1.1.5 过程应用的代换模型

**应用序的代换模型**

- 解释器对组合式的各个元素求值，而后将得到的过程（运算符）应用于实际参数
- 将复合过程应用于实际参数，就是在将过程体中的每个形参用**相应**的实参取代之后，对这一过程体求值
  - **对应**究竟是什么呢

应用序和正则序

#### 1.1.6 条件表达式和谓词

cond, if, and, or, not

exercise 1.5: 应用序无限递归，正则序求值 0

exercise 1.6: 无限递归，因为应用序下 new-if 的每个参数都要求值再传入

#### 1.1.8 过程作为黑箱抽象

形式参数，约束变量

词法作用域

### 1.2 过程和它们所产生的运算

介绍了很多经典算法

exercise 1.22: display 的使用

### 1.3 用高阶函数做抽象

```scheme
(lambda (<formal-parameters>) <body>)
(let ([(<var> <exp>)]) <body>)
```

用 lambda 实现 let 的功能

## 2 构造数据抽象

### 2.1 数据抽象导引

分离过程的使用与过程的实现

#### 2.1.1

有序对：`cons`, `car`, `cdr`

#### 2.1.3

多种有序对的过程定义

exercise 2.6: Church 计数
- 似乎是用“接受一个参数 f，返回一个过程 fn，fn 接受一个参数 x，返回将 f 在 x 上应用 n 次所得的结果”来表示数字 n

### 2.2 层次性数据和闭包性质

闭包性质：通过某种操作组合数据对象得到的结果，可以继续用该操作组合，则称该操作具有闭包性质。

#### 2.2.1 序列的表示

`list` 以及等价的 `cons` 表示

`null?` 检查空表，`pair?` 检查序对

脚注 78：scheme 提供了一种变参 map，接受一个多参数的 op，将多个列表的同位置的元素作为参数传入，不知道如何实现……
- 原来 scheme 还有个 `apply`，那么问题来了，`apply` 是怎么实现的

exercise 2.20：变长参数的实现
- `(define (f x y . z) <body>)`，z 是剩余参数组成的表

`map <proc> <items>`, `for-each <proc> <items>`

#### 2.2.3 序列作为一种约定的界面

书中 accumulate 运算方向的选择，使得 `(accumulate cons nil (list <terms>))` 能够返回和原序列相同的序列，但不能方便用迭代方式实现。

mit-scheme 中 accumulate 是 reduce。但是很奇怪，这个 reduce 的 initial 好像是没用的……

flatmap

### 2.3 符号数据

#### 2.3.1 引号

以 `'` 开头的符号表明将后面的符号视为数据对象而不是表达式

- `eq?` 进行符号的比较
- `symbol?`
- `memq`
- `equal?`

作用于复合对象的引号本质上是宏 `quote` 的的语法糖

2.3.2 实例：求导
2.3.3 实例：集合表示

### 2.4 抽象数据的多重表示

统一数据的不同等价表示

方法的分派

消息传递

- put
- get
- apply-generic

### 2.5 带有通用型操作的系统

put-coercion

这节介绍了大量实例……

## 3 模块化、对象和状态

设计系统结构的两种不同观点：基于对象（对象之间的行为）和基于信息流

### 3.1 赋值和局部状态

？？？？？？`set!` 和 `define` 到底有什么差别……见 3.2.1

`begin <exp_1> ... <exp_k>`, 类似 C 语言中的逗号运算符，按顺序对表达式求值，并返回最后一个表达式的值。

以蒙特卡罗算法为例，说明赋值能隐藏一些局部状态，更容易以模块化的方式构造系统。

### 3.2 求值的环境模型

QNS: 此处的 Frame 翻译成框架有点令人迷惑，建议改为帧。

- 绑定（binding）：变量（variable，或理解为名字 name）和值（value）的对应关系
- 帧（frame）：绑定的集合
- 环境（environment）：一个帧的序列，每个帧指向自己的上个帧

不懂为什么要用环境的概念，明明只是一个顶点为帧的树形图？

一个变量在特定环境中的值，是包含该变量的第一个（最内层）帧中所绑定的值

#### 3.2.1 求值规则

求值的环境模型中，过程可以视为代码和指向某个环境（求值 lambda 表达式，创建该过程时的环境）的指针的二元组。

将过程应用于一组实参时，会创建一个包含所有过程形参与实参对应关系的帧，这一新的帧指向过程的环境。

`define` 可以在当前帧中添加一个绑定。`set!` 会向上查找到变量对应的绑定（所处的帧），然后修改这个绑定中的值。

### 3.3 用变动数据做模拟

#### 3.3.1 变动的表结构

`set-car!` 和 `set-cdr!` 用于修改一个序对的 car 或 cdr

可以用 `set-car!`、`set-cdr!` 和 `get-new-pair` 重新实现 `cons`。`get-new-pair` 是对 Lisp 系统来说是必须的，见 5.3.1。

`eq?` 本质上是检查两个名字是否对应同一个指针。

#### 3.3.2 队列的表示

用两个对象分别指向列表的首尾来维护一个队列。

IMPT: 不使用赋值无法构造出队列。

#### 3.3.3 表格的表示

### 3.4 并发

### 3.5 流

#### 3.5.1 流作为延时的表

`(cons-tream <a> <b>)` 是个特殊形式，以避免对传参时对 `<b>` 的求值。

**流实现的行为方式**这一小标题最后列出的两个流的状态，给人一种 stream 的 cdr 也会在求值出 car 的错觉，实际上第一个 delay 后的 cdr 应该是求值后的结果。

`memo-proc` 返回的闭包中的局部符号 `result` 值一般是 `cons-stream` 的求值结果，即 `(<value> <closure>)`

根据实际测试，scheme 好像已经内置了 `memo-proc`。

exercise 2.52: 0, 0, 1, 6, 10, 136, 210

#### 3.5.2 无穷流

应用 `memo-proc` 下，这一小节中的 `integers`，生成前 $n$ 个数的时间复杂度应该是 $O(n)$ 的。值 $n$ 对应的第 $n$ 个闭包局部符号 `result_n` 可以展开为

```scheme
(n (lambda ()
      (if (not run?)
        (begin (set! result_n+1
                ((lambda ()
                    (stream_map +
                                (stream-cdr result_ones_n-1)
                                (stream-cdr result_n-1)))))
              (set! run? true)
              result_n+1)
        result_n+1)))
```

容易看出，从 `result_n` 到 `result_n+1` 只需要常数计算。

exercise 2.58: 短除法，会生成 radix 进制下 num 除以 den 的小数形式。

原来那些调用 define 自身的写法正确的前提是在 `cons-stream` 里……他们并没有被立即求值……

谨慎使用 `let`，以免过早对流求值。

#### 3.5.3 流计算模式的使用

通过构建流的自环，可以将迭代操作表示为流。

介绍了用于加速逼近收敛的欧拉方法 $S_{n+1}-\frac{(S_{n+1}-S_n)^2}{S_{n-1}-2S_n+S_{n+1}}$，将其迭代应用于原序列，得到一个逼近序列的序列。

从两个流创建序对的流，类似有理数生成。但是和常见的蛇形排列不同，书中用 `interleave` 来实现流的交错。

exercise 3.66: $f(0,0)=0$, $f(0,1)=1$, $f(0,j)=f(0,j-1)+2=2j-1$, $f(i,j)=2f(i-1,j-1)+2=2^{i}f(0,j-i)+2^{i+1}-2=2^{i+1}(j-i)+2^{i}-2, j>i$

exercise 3.68: 对 `pairs` 的无穷递归调用

exercise 3.71: 可以设计一个 group 函数，合并相邻的 key 相同的元素。

最后一小节没有介绍新内容，只是将流应用于信号处理系统，或更基础的电路。对应之前使用赋值方法模拟。

#### 3.5.4 流和延时求值

显式地使用 `force` 和 `delay` 来实现流的循环。借助 `cons-stream` 可以方便地产生自环，但却难以创建多个流的循环，需要显式地延迟其中一个输入。本质上是修改了求值顺序，使得一部分代码更接近规范序。

#### 3.5.6 函数式程序的模块化和对象的模块化

用随机数的流代替随机数生成器。

## 4 元语言抽象

### 4.1 元循环求值器

#### 4.1.1 求值器的内核