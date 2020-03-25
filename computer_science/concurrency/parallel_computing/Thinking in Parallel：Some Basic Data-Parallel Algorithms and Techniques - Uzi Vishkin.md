DCJ 和 CRCW PRAM 还是有些差异。DCJ 核之间数据传输没有 PRAM 那么简单。

### Chapter 2 Introduction

#### 2.1 The PRAM Model

PRAM (E: exclusive, C: concurrent):
- EREW
- CREW
- CRCW
  - arbitrary
  - priority
  - common

#### 2.2 Work-Depth presentation of algorithms

**Exercise** 1
- 将任意多处理器时所有时刻按照指令数分为 <=p 和 >p 两类。前者仍然能用 p 个处理器运行，没有新增耗时。后者中某时刻指令数量为 x，则最多需要新增 x/p 个时刻。而 x 之和不超过 W(n)，因此新增 W(n)/p 个时刻，总耗时为 W(n)/p+T(n)。因此一个任意多处理器时 W(n)-T(n) 的算法总能在 W(n)/p+T(n) 时间内实现。
  - p=W(n)/T(n) 是算法复杂度保持在 T(n) 的 p 数量的下界。

### Chapter 3 Technique: Balanced Binary Trees; Problem: Prefix-Sums

**PROBLEM**: Prefix-Sums. WT: O(n)-O(logn). #(Theorem 3.1)

**ALGORITHM** 1

#### 3.1 Application - the Compaction Problem

**PROBLEM**: Compaction. 给定 A() 和 B()。取出 B(i) 为 1 的 A(i) 构成一个新序列。 WT: O(n)-O(logn). #(Theorem 3.2)

**Exercise** 2: $p_{2^i}..p_{2^{i+1}-1}$ 将当前的 $2^i$ 个内存地址的数据复制到另外 $2^i$ 个位置上。O(logn)。

**PROBLEM**: Minimum. WT: O(n)-O(logn).
**PROBLEM**: Prefix-Min. WT: O(n)-O(logn).

**Exercise** 3: 
1. Prefix-Sums 1-3 行即可
2. 略
3. 将 Prefix-Sums 的二元运算定义为 min 即可。在 EREW 上要规避 3 和 5-7 中的同时读。

**PROBLEM**: Nearest-One. WT: O(n)-O(logn).
**PROBLEM**: Segmented Prefix-Sums. 计算 nearest-one 到 i 的权值之和。 WT: O(n)-O(logn).

**Exercise** 4:
1. 设 C(i)=[A(i)==1]\*A(i)，对 C(i) 求 Prefix-Max。
2. 设元素为 (nearest_one, sum)。定义 op(a, b) 为 b if b.nearest != 0 else (a.nearest_one, a.sum + b.sum)，按照该定义求 Prefix-Sums。

Exercise 4 的假做法 O(nlogn)-O(logn)：
1. 设 C(h,i) 为 [i-2^h+1, i] 中满足 A(j)=1 且编号最大的 j，不存在则为 0。C(0,i)=[A(i)==1]\*i，C(h,i)=max(C(h-1,i), C(h-1,i-2^(h-1)))。
2. 在第 1 问基础上，设 D(h,i) 为 [i-2^h+1, i] 中的 segmented prefix-sum。D(0,i)=[A(i-1)==1]\*B(i)，D(h,i)=D(h-1,i)+[C(h-1,i)==0]\*D(h-1,i-2^(h-1))。

#### 3.2 Recursive Presentation of the Prefix-Sums Algorithm

并行算法的递归表示。

**PROBLEM**: Matrix Multiplication. WT: O(n^3)-O(logn).

**Exercise** 5:
1. 计算每个 $a_{i,j}b_{k,j}$，对 k 相同的乘积求和。
2. 略
3. 还有 work O(n^3logn) 的做法？？？
4. 略

注：读到这里时想起之前对复杂度的表示不包含处理器数量 p。发现跳过了 Exercise 1。实际上是没有必要的，渐进意义上 W(n)-T(n) 的表示就足够了。好像突然明白了 Exercise 中限制处理器数量的小问的目的。

### 4 The Simple Merging-Sorting Cluster

#### 4.1 Technique: Partitioning; Problem: Merging

**PROBLEM**: Merging. 两个有序数组的合并。 WT: O(n)-O(logn).

Partitioning Paradigm: partitioning, actual work

？？？？？解法还没细看，和 Distributed Code Jam 中 Kite 之类的处理技巧是一样的。都是通过子问题规模的不均等分配使得最大子问题规模为均值的常数倍。这大概就是 partitioning 这一步的精髓？当然很多时候是能均分子问题的。

**Exercise** 6: $T(n)=\alpha \log{n}+\beta 2n/x$，$W(n)=2x \cdot T(n)$

**Exercise** 7: 比较暴力的变换。
1. 压缩 A 和 B. O(n)-O(logn). 按 [A(i)>A(i-1)] 做 Compaction，根据原下标计算每种数的重复次数。
2. 使 A 和 B 互异. O(n)-O(1). 令 A(i)=A(i)\*2，B(i)=B(i)\*2+1。
3. Merging/Ranking. O(n)-O(logn).
4. 解压 A 和 B. O(n)-O(logn). 计算合并后序列中每种数出现次数的 Prefix-Sums，得到每个数要填回的段。对段填数类似 Exercise 2，长为 x 的段在 O(x)-O(logx) 时间内完成。
5. 变换为原值. O(n)-O(1). A(i)=A(i)//2，B(i)=B(i)//2。

**Exercise** 8: 将 n,m 扩展到较大值

**Exercise** 9: ？？？？？

#### 4.2 Technique: Divide and Conquer; Problem: Sorting-by-merging

**PROBLEM**: Merge Sort. WT: O(nlogn)-O(log^2n). #(Theorem 4.2)

Divide and Conquer Paradigm 和串行算法中的概念一致。

### Chapter 5 "Putting Things Together" - a First Example. Techniques: Informal Wrok-Depth (IWD), and Accelerating Cascades; Problem: Selection

**PROBLEM**: Selection. 第 k 小.
- Sort. WT: O(nlogn)-O(n)
- Reducing algorithm with Partitioning. WT: O(n)-O(log^2n)
- Accelerating Cascades. WT: O(n)-O(lognloglogn)

#### 5.1 Accelerating Cascades

使用同一问题的一组算法，满足 W1<=W2<=...<=Wm 和 T1>=T2>=...>=Tm，且前 m-1 个算法都是 reducing algorithm，即通过迭代缩小原问题规模进行求解。可以按顺序应用这组算法，平衡 work 和 time。

给定一个期望的 T(n)，可以令每个算法最大程度地缩小问题规模，从而得到对应的 W(n) 的下界。反之也同样可以得出 T(n) 下界，只是需要倒过来推断。

#### 5.2 The Selection Algorithm (continued)

5.1 中提到的算法 2 是一个 O(nlogn)-O(logn) 排序算法。

算法 1：
1. x=n/logn partitioning，用串行的 k-th element 求每段中位数。O(n)-O(logn)。
2. 对所有中位数构成的序列用排序求中位数 a0。O(n)-O(logn)。注意规模缩小对 WT 的影响。
3. 统计 \<a0 =a0 \>a0 数的数量 s1 s2 s3。根据所要求的 k 选取一部分迭代。O(n)-O(1)。
4. 将所有数视为 $\log{n} \cdot (n/\log{n})$ 的矩形，容易证明 $m/4 \le s_1 \le 3m/4$，对 $s_3$ 也是同理。因此迭代求解的复杂度为 O(n)-O(log^2n)。

#### 5.3 A top-down description methodology for parallel algorithms

？？？？？没理解这个 IWD 的精妙之处……

**Exercise** 10: WT: O(nlogn)-O(log^2nloglogn)

### Chapter 6 Integer Sorting

surplus-log, poly-logarithmic

**PROBLEM**: Integer Sorting. 元素范围 [0, r-1]. WT: O(n)-O(r+logn).

以下过程类似计数排序，对 r 的大小要求比串行算法更为严格。通过与串行的基数排序类似的处理方式，可以得到对任意大小 r 的算法，WT: O(kn)-O(k(r^(1/k)+logn))。k 可以取 $\log_{\log{n}}{r}$

1. x=n/r partitioning，做计数排序并计算 v 在段 s 中出现次数 number(v,s) 以及同段中 i 之前与其相同的数的个数 serial(i)。O(n)-O(r)。
2. 对每个 v 计算 number(v,s) 的前缀和 ps(v,s) 以及 cardinality(v)=ps(v,n/r)。O(n)-O(log(n/r))。
3. 计算 cardinality(v) 的前缀和 global_ps(v)。O(r)-O(logr)。
4. 对每个 i，其排序后位置为 1+serial(i)+ps(v,s-1)+global_ps(v-1)，分别是常数、段中相同数位置、之前段中相同数数量、整个序列中小于其的数个数。O(n)-O(1)。

**Exercise** 11: 略

**Exercise** 12: 计数排序。

#### 6.1 An orthogonal-trees algorithm

本节假设数字范围是 [1, n]。

计数排序的本质是计算序列中小于当前位置的数。特别地，为了使得排序稳定，对于元素相等的情形，用下标作为第二关键字。很容易想到用 n^2 个处理器对所有数量两两比较，然后对每个下标做前缀和求出位置。WT: O(n^2)-O(logn)。

本节介绍的算法与上面的暴力方法有些类似。所有处理器 i，都从 A(i) 对应的满二叉树的第 i 个叶子开始向根移动。若两个处理器在某个结点相遇，则其中一个停止运行，另一个继续向根移动。这相当于删去了暴力算法求解前缀和时值为 0 的叶子。

### Chapter 7 2-3 trees; Technique: Pipelining

### Chapter 8 Maximum Finding

**PROBLEM**: Maximum Finding. 全序集. 
- Summation. WT: O(n)-O(logn)
- CRCW. WT: O(n^2)-O(1)
  - 两两比较，对较小值（相同时比较下标）对应标记赋值 1，0 值的即为最大值
- Divide and Conquer. WT: O(nloglogn)-O(loglogn)
  - 考虑分治做法，且令每层为 O(n)-O(1)。则根据 T(n)=s\*T(n/s)+s^2，应该有 s^2=O(n)，s=n^0.5。
- deterministic. WT: O(n)-O(loglogn)
  - 最后一层取块大小 loglogn 且串行处理
- randomized. WT: O(n)-O(1)

#### 8.1 A doubly-logarithmic Paradigm

**PROBLEM**: Nearest-One. WT: O(n)-O(loglogn).
**PROBLEM**: Segmented Prefix-Min. 计算 nearest-one 到 i 的权值最小值。 WT: O(n)-O(loglogn).

**Exercise** 17:
1. 类似 Exercise 4，同样转化为 Prefix-Max。在 doubly logarithmic tree 上做和 Balanced Binary Tree 类似的过程。push_up 时每个顶点对应区间，push_down 时每个顶点对应前缀。在计算结点 u 对应前缀的最大值时，需要 u.father.prevSibling 以及同父亲兄弟结点中，u 以及 u 左侧的结点。如果 u 父亲有 s 个儿子，则计算 u 所在层结点对应前缀和共需 s^3 个处理器，因此结点对应规模按照 n'=n/(n^(1/3))=n^(2/3) 分治。所得 WT 为 O(nloglogn)-O(loglogn)。将倒数第二层顶点的儿子数改为 loglogn，push_up 和 push_down 的过程改用串行方式，即可得到 WT 为 O(n)-O(loglogn) 的解法。
2. 扩展 Exercise 4 第二问中定义的运算，即先计算 nearest_one，然后根据 nearest_one 所在的位置选取结点计算 min。分治过程与上一小问类似。

#### 8.2 Random Sampling

**Exercise** 18: $-\log{(1-\epsilon)}, \epsilon < 1$

**Exercise** 19: 串行的快速选择，期望划分层数是 O(logn)，每次划分都需要常数次 prefix-sums，因此期望的 WT 为 O(nlogn)-O(log^2n)。

**Exercise** 20: 和串行的快速排序一样，期望的划分层数是 O(logn) 的，每次划分都需要两次 Compaction，因此期望的 WT 为 O(nlogn)-O(log^2n)。

### Chapter 9 The List Ranking Cluster; Techniques: Euler Tours; pointer jumping; randomized and deterministic symmetry breaking

#### 9.1 The Euler Tour Technique for Trees

**PROBLEM**: Tree Rooting. 给定一个无向边表，每条边出现两次，给定根 r，确定每条边的方向. WT: as List Ranking.
- 为了使 reducing 部分为 O(n)-O(1) 需要一些特殊条件：边表中边按照出点排序，相反方向的两条边之间有跳转指针

通过边表构造一个为欧拉回路的链表，然后对该链表做 List Ranking。考虑树上欧拉回路的性质，可以通过比较 (u,v) 和 (v,u) 的 rank 确定边 (u,v) 的方向。

##### 9.1.1 More tree problems

**PROBLEM**: Preorder numbering. WT: as List Ranking.
**PROBLEM**: Size of subtress.
**PROBLEM**: Level of vertices.

**Exercise** 21: 构造 Euler Tour 时改为 i-1 (mod deg(v))，求 Preorder 再反向。

**Exercise** 22:
1. 必要性：由 preorder(u)>preorder(father(u)) 和 postorder(u)<postorder(father(u)) 推出 preorder(u)>preorder(ancestor(u)) 和 postorder(u)<postorder(ancestor(u))。充分性：反证法。若 u 不是 v 的祖先，两个点的最近公共祖先为 w，且 u 和 v 分别属于子树 T_u 和 T_v。如果 T_u 在 T_v 左侧，则 preorder 和 postorder 中 T_u 所有点都先于 T_v。T_u 在 T_v 右侧同理。
2. 利用前一小问的结论。

**Exercise** 23: 首先随意选取点为根定向。初始化 ava(u)=0。对每个点 u，如果 size(u)>n/2，则置其父亲的标记 ava(father(u))=1，如果 n-size(u)>n/2，则置自己的标记 ava(u)=1。ava(u)=0 的就是合法结点。除定向外部分 O(n)-O(1)。

#### 9.2 A first list ranking algorithm: Technique: Pointer Jumping

**PROBLEM**: List Ranking. 给定一个链表，知道链表中所有结点的内存地址，计算每个结点到链表尾部的距离.

- Pointer Jumping. WT: O(nlogn)-O(logn).
- Randomized Symmetry Breaking. WT: O(n)-O(lognloglogn) with high probability.
  - average: O(n)-O(log^2n).
- Deterministic Symmetry Breaking. WT: O(nlog*n)-(logn).

Pointer Jumping，即算法竞赛中常说的倍增方法。

**Exercise** 24: 拷贝一份 distance 和 next。

更快的 List Ranking solution 是 reducing algorithm。介绍了 uniform sparsity 和 uniform density 两种性质。两种性质均满足的选取方法是 odd/even。

#### 9.3 A Framework for Work-optimal List Ranking Algorithms

Accelerating Cascades，组合了一个 O(n)-O(log^2n) 的 reducing algorithm 和 O(nlogn)-O(logn) 的 Pointer Jumping。得到算法复杂度为 O(n)-O(lognloglogn)。

之后介绍该 reducing algorithm 的两个版本。两者均通过选取一个足够大的 sparse set S，将其从链表中去除来减小问题规模。因为连续删除会影响复杂度，因此要求 S 满足 uniform sparsity。为了减少问题规模需要做 compaction，这是单次迭代 O(n)-O(logn) 的瓶颈。

#### 9.4 Randomized Symmetry Breaking

i in S iff rand(i) & !rand(next(i))

本小节 lemma 的证明中，(1) 将每个元素的选取与否视为独立事件，这是不正确的。(2) 中为了避免详细讨论，只选取了一半的元素。

用 chernoff bound 对二项分布的定理证明了选中至少 1/16 元素的概率。

推论的证明里界真的被放得很宽啊……

怎么还证明了 avarage time/work 啊，感觉很强。

**Exercise** 25: 略。

#### 9.5 Deterministic Symmetry Breaking

r-ruling set，推广了 uniform density。

intermediate objective: 原先的 linked ring 的内存地址可以四号位标号 [0,n-1]。对 linked ring 重标号，使得整数标号范围为 [0, x-1]，且 x << n。需要满足 local asymmetry property，即相邻元素权值互异。

将 intermediate objective 的局部最大值标记，再将相邻元素均未标记的局部最小值标记，即得到一个 (x-1)-ruling set。

重标号的思路：根据相邻元素的原权值进行某种运算，然后用其中固定侧的元素的某个性质保持 local asymmetry property。例如原权值异或的最低非零位，以及 i 在该位置上的值，deterministic coin tossing。

**Exercise** 26: 略

**PROBLEM**: Prefix-Sums. WT: O(n)-O(logn/loglogn).

**Exercise** 27:
1. 2logn=lgn, 2log(2logn)=lglgn
2. 设 $b_i$ 为 i 轮迭代后 tag 落入 [0,5] 的最大的 n，有 $b_0=6$，$b_i=2^{b_{i-1}/2}$。设 $a_i$ 为 $log^*n=i$ 的最大的 n，有 $a_0=2$，$a_i=2^{a_{i-1}}$。有 $b_1=8$，用数学归纳法可证 $b_i \ge 2a_{i-1}+4$。因此只需 $log^*n+1$ 次迭代即可使范围收敛到 [0,5]。
3. 注意到对于任意常数 c，从 c-ruling set 求 2-ruling set 都可以视为 O(1) 的过程。最简单的实现中，我们令 5-ruling set 中每个 marked node 向后寻找下一个 marked node 或者 end，如果距离大于 2，则再标记中点。
  - 其他的一些观察：将 [0,5] 标号根据高二位划分连续段。长度大于 1 的连续段中，0 和 1 必定交替出现。可以利用这一性质，先处理 >1 连续段，然后对剩余结点构成的连续段再进行处理。
4. ？？？？？应该是首轮收敛到 2-ruling，O(nlog\*n)-O(log\*n)。之后每轮都利用上一轮某个常数大小（如[0,5]）的标号产生新标号，而不是内存地址之类保证互异的标号，这样就只需要常数次 deterministic coin tossing，O(n)-O(1)。不过 O(logn/loglogn)-O(n) 的 prefix-sum 进行 logn 轮迭代复杂度也不是 O(logn) 啊？

**Exercise** 28: ？？？？？？链转树的问题，告辞

#### 9.6 An Optimal-Work 2-Ruling set Algorithm

？？？？？？？？？？？？

### Chapter 10 Tree Contraction

本节涉及的二叉树中结点均为叶子或有两个儿子，是英文维基中的 full/proper/plane binary tree。但中文的满二叉树并非这个释义。我见到的部分文章里将其称为完满二叉树，暂定。

定义了二叉树的 rake operation，描述了一棵二叉树“收缩”的过程。rake operation: 将叶 x 及其父亲 y 删除，将 y 的另一个儿子 w（不一定存在）连接到 y 的父亲（称为 stem）上，即取代 y 原先的位置。注意到该操作最终将一棵完满二叉树变为仅剩根及其两个儿子的结构。

parallel rake 是一组 rake，满足不同 rake operation 涉及的删除结点不相邻。parallel tree contraction scheme 通过多轮 parallel rake 将树减小到 3 个结点。

一种 parallel tree contraction scheme 的简单构造。将所有叶子按 DFS 序中出现的顺序标号，取其中奇数标号的叶子进行 rake operation。可以通过枚举情形证明每个 stem 最多只和另外 1 个 stem 相邻，因此可以简单分组，用两次 parallel rake 完成这些 rake operation。WT: O(n)-O(logn)。

应用：二元表达式树的并行计算。每个结点需要的参数应该是个运算符数量的多项式，感觉推广起来有点难度。仅限加法和乘法的话简单代入就能导出 rake operation 后的参数变动。

**Exercise** 32: 对仅限加法和乘法的表达式，只需验证 leaf 和 stem 运算符的四种组合即可。

**Exercise** 33: 每轮 parallel rake 令叶子数量减半，每轮 O(n)-O(1)，因此总计 O(n)-O(logn)。
- 对于包含一元运算符的结构，可以利用第 9 章的算法对一元运算符的链进行收缩。考虑

**Exercise** 34: ？？？？？1)将所有孤立结点选中并删除。2)将所有叶结点的邻居标记为选中，然后将叶结点及其邻居都删除。尝试优化到和树结构无关……不过这题为什么会被放在这里呢，这一章的内容只对 full binary tree 有效啊……

### Chapter 11 Graph connectivity

- incidence list: 邻接表
- adjacency matrix: 邻接矩阵

**Exercise** 35: 对 (out(u), u) 排序。

**Exercise** 36: ？？？？？容易想到 O(mh)-O(h) 的算法。

**Exercise** 37: ？？？？？

pointer graph 相关概念可参考并查集。

#### 11.1 A first connectivity algorithm

算法分两轮：每个 root 连向 supervertex graph 中相连的 root 中标号最小者，互联时较小标号放弃 hooking。做 parallel pointer jumping，消除边。

1. 按照 n/k 分段，计算数 v 在对应段 s 中出现次数 cnt(v,s) 以及同段中下标 i 之前与其相同的数的个数 pre(i)。time cost: n/k。
2. 按照 v/k 分段，对每个 v 计算 cnt(v,s) 的前缀和 prefix(v,s) 以及 tot(v)=prefix(v,n/k)。time cost: k\*v/v=v。
3. 计算 tot(v) 的前缀和 pretot(v)。time cost: v/k。
4. 对每个 i，其排序后位置为 1+pre(i)+prefix(v,s-1)+pretot(v-1)，分别是常数、段中相同数位置、之前段中相同数数量、整个序列中小于其的数个数。time cost: n/k。