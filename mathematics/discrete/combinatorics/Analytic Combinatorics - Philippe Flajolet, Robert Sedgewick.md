##

### I.1 Symbolic enumeration methods

Definition I.1 combinatorial class

Ordinary Genrating Function (OGF): $A(z)=\sum_{n=0}^{\infty}{A_nz^n}$

建立一套描述组合结构的形式化语言，再确定如何将组合结构之间的组合操作映射到其生成函数的代数运算上，从而直接得出一个组合结构的生成函数。

Definition I.6. cartesian product construction 笛卡尔积，从两个类 $\mathcal{B}$ 和 $\mathcal{C}$ 构造有序对
- 元素大小的和 => 幂级数的乘积

类似地定义了不相交组合结构的并以及相应的生成函数之和

### I.2 Admissible constructions and specifications

#### I.2.1 Basic constructions

- neutral class $\mathcal{E}$: consists of a single object of size 0. The OGF $E(z)=1$
- atomic class $\mathcal{Z}$: a single element of size 1. The OGF $Z(z)=z$


constructions
- sequence: $SEQ(\mathcal{B})=\{\epsilon\}+\mathcal{B}+(\mathcal{B} \times \mathcal{B})+\dots=\{(\beta_1,\dots,\beta_{\mathcal{l}}) | \mathcal{l} \ge 0,\beta_j \in \mathcal{B}\}$
- cycle: $CYC(\mathcal{B})=(SEQ(\mathcal{B}) \backslash \{\epsilon\})/\mathrm{S}$
  - $\mathrm{S}$ 是 $\mathcal{B}$ 上的等价关系，旋转同构
- multiset: $MSET(\mathcal{B})=SEQ(\mathcal{B})/\mathrm{R}$
  - $\mathrm{R}$ 是 $\mathcal{B}$ 上的等价关系，将元素映射到相应的多重集合
- powerset: 幂集

#### I.2.2 The admissibility theorem for ordinary generating functions

p27 列出了上述构造的转换规则。

用 exp-log 变换化简连乘符号：$\log{(1+u)}=\frac{u}{1}-\frac{u^2}{2}+\frac{u^3}{3}-\dots$

powerset 和 mutliset 比较简单，cycle 大概必须要用到一些组合知识。。。

I.7 Vallee's identity: $M(z)=P(z)M(z^2)$，依次展开得到 $M(z)=\prod_{k=0}^{\infty}{P(z^{2^k})}$

#### I.2.3 Constructibility and combinatorial specifications

从 $\mathcal{E}$ 和 $\mathcal{Z}$ 出发，用 $+$, $\times$, SEQ, CYC, MSET, PSET 组合得到的组合结构的描述称为 iterative spercification。

tree: $\mathcal{G}=\mathcal{Z} \times SEQ(\mathcal{G})$

I.8 Lim-sup of classes.

p34 列出了很多可被构造的组合类

#### I.2.4 Exploiting generating functions and counting sequences

有些情形中，虽然得到了解析形式的生成函数，但没有得到系数的公式。其中一部分结果的系数仍然是可以快速计算的。例如整数划分，其生成函数为 $\prod_{m=1}^{\infty}{\frac{1}{1-z^m}}$，Note I.13 和 I.19 介绍了计算方法。以及 non-plane trees

渐进分析

### I.3 Integer Compositions and partitions

#### I.3.1 Compositions and partitions

整数类: $\mathcal{I}=z \cdot SEQ(z)$, $I(z)=\frac{z}{1-z}$

Composition: $\mathcal{C}=SEQ(\mathcal{I})$, $C(z)=\frac{1-z}{1-2z}$

partition: $\mathcal{P}=MSET(\mathcal{I})$$P(z)=\prod_{m=1}^{\infty}{\frac{1}{1-z^m}}$

I.13 的计算是不是有点问题……可以用 $O(n^2)$ 的时间计算 $P_n$。Note I.43 P72 介绍了 $O(n\sqrt{n})$ 的算法。

Example I.4 直接从 $C^{\{1,2\}}(z)=\frac{1}{1-z-z^2}$ 得到系数的解析式的方法：解出 $1-z-z^2=0$ 的根，将生成函数分解为两个分式，分别展开即可。

I.17 的 OGF 可以理解为，$x_i$ 的值增加 1 会影响到后面的所有 $x_j$，共需要增加至少 $2^{r-i+1}-1$。

figure I.9

#### I.3.2 Relatd constructions

I.18 distinct summands 和 odd summands 的划分是等价的

### I.4 Words and regular languages

