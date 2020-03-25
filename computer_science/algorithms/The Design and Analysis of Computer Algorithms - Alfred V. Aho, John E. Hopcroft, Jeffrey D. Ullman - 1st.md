# The Design and Analysis of Computer Algorithms

## Chapter 1 Models of Computation

### 1.3 Computational Complexity of RAM Programs

- worst-case, expected
- uniform, logarithmic
- time, space

### 1.4 A Stored Program Model

random access stored program (RASP)，可以修改指令

### 1.5 Abstractions of the RAM

#### i. Straight-Line Programs

展开循环，得到一个顺序的指令流

#### ii. Bitwise Computation

按位运算/存储。截止成书时，bitwise model 下乘法运算至少需要 $O_\mathrm{B}(n\log n\log\log n)$。

#### iii. Bit Vector Operations

bit vector 一般和问题规模同大小，在固定字长的机器上可以将一个 vector 拆分成多个 word 来实现。$O_{\mathrm{VB}}$

#### iv. Decision Trees

每个内部节点代表一个分支，时间复杂度就是树的高度。$O_\mathrm{C}$, Comparison

### 1.6 A Primitive Model of Computation: the Turing Machine

**polynomially related**: 存在 $p_1(x)$ 和 $p_2(x)$ 对所有 n 满足 $f_1(n) \le p_1(f_2(n))$ and $f_2(n) \le p_2(f_1(n))$

对同一个问题，多带图灵机的步数和 RAM 模型的对数时间复杂度是多项式相关的

图灵机看得头疼，先跳过了……

### 1.7 Relationship between the Turing Machine and RAM Models

## Chapter 2 Design of Efficient Algorithms

1. 链表、队列、栈
2. 集合的链表表示和位表示
3. 图的概念，顶点、边、路径。邻接矩阵、邻接表
4. 树
5. 递归
6. 分治。P64，Theorem 2.1 主定理
   1. [Master Theorem](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms))
7. 平衡
8. 动态规划

## Chapter 3 Sorting and Order Statistics

## Chapter 4 Data Structures for Set Manipulation Problems

Operations
- MEMBER(a, S)
- INSERT(a, S)
- DELETE (a, S)
- UNION(S1, S2, S3)
- FIND(a)
- SPLIT(a, S), 假设集合中元素良序
- MIN(S)

P109, 离线/在线处理

## Chapter 5 Algorithms on Graphs

### 5.11 Dominators in a Directed Acyclic Graph: Putting the Concepts Together

应用于有向无环图。

三个引理分别能移动前向边、删除树边、将交叉边变为前向边。应用第一个引理时，c 和 d 往往是同一个顶点，实际效果是对到达某个点的所有前向边，只保留出点最接近根的一条。设 $high[u]$ 为到达 u 的所有前向边中最接近根的出点。

对没有交叉边的图，算法流程相当简单。以逆 DFS 序的顺序处理，$F[u]$ 是所有从 u 的真祖先出发到 u 的后代（包括自己）的前向边集合，初始时 $F[u]$ 中只有 $(high[u], u)$。顶点 v 的信息合并到 u 时，$(high[u],u)$ 可能与 $F[v]$ 中的前向边“交错”，即存在边 $(a,b)$，深度满足 $high[u]<=a<u<b$，可以根据引理 1 将 $(a,b)$ 修改为 $(high[u],b)$。a 最小，即 $a=u$ 时，u 就是 b 最近一个支配点。

大致就是对于顶点 u，先用它最高的前向边作为最近支配点的候选，然后在向上移动的过程中不断用祖先的前向边改进候选。

交叉边的处理和前向边在同一趟完成。对于交叉边 $(u,v)$，需要求出引理中的 a，即到达 (LCA, u] 的前向边的最高点，这个可以在处理完 $F[u’]$ 时得到，$u'$ 是 LCA 到 u 的路径上离 LCA 最近的顶点。对于引理中 no entering cross edge 的限制，因为交叉边出点的 DFS 序总是大于入点，到达 LCA-u 上点的交叉边必然已经被处理。

## Chapter 6 Matrix Multiplication and Related Operations

### 6.2 Strassen's Matrix Multiplication Algorithm

二分分治，$T(n)\le mT(n/2)+an^2/4$，m 和 a 分别是分块矩阵上的乘法和加法次数。

从主定理可以推知，加法次数对算法复杂度只有常数影响。乘法次数大于 4 时，所得复杂度为 $\Theta(n^{log_2{m}})$。

用一些很扭曲的方法把乘法次数减少到了 7 次。。

### 6.3 Inversion of Matrices

这个求逆？？？

TODO: 跳过了 LUP

## Chapter 7 the Fast Fourier Transform and its Applications

### 7.1 the Discrete Fourier Transform and its Inverse

元素在任意交换环上，且 DFT 的单位元 $\omega$ 满足：
- 不等于乘法单位元
- $\omega^n=1$
- $\sum_{j=0}^{n-1}{\omega^{jp}}=0, 1\le p<n$

最后一条我一直没怎么注意……

Lemma 7.1: $F(\mathbb{a})=A\mathbb{a}$, $A_{i,j}=\omega^{ij}$, $A^{-1}_{i,j}=\omega^{-ij}/n$

**wrapped convolution**

### 7.2 the Fast Fourier Transform Algorithm

这个多项式取模的思路好像没见过啊。。

