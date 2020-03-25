[finish]

##  Abstract

自然生成的网络的度数大多满足幂律（power-law）分布。Pregel 和 GraphLab 都没有针对这一性质进行优化。

## 1 Introduction

Pregel 和 GraphLab 将运算表述为 vertex-programs，其并行效果依赖于所有顶点的度数都很小这一假设（借此高效地划分顶点，减少通讯代价（没看过这两种方法，估计是划分为数个独立集，只在集合间进行通信吧））。

## 2 Graph-Parallel Abstractions

图并行抽象，由一个稀疏图和并行执行的顶点程序（vertex-program）组成，顶点可以和邻居们交互。

### 2.1 Pregel

bulk synchronous 批量同步？

Pregel 的运行过程就是一系列 super-step。每个 super-step 中，每个顶点接收上个 super-step 中邻居发给自己的信息，再将信息发送给下个 super-step 的邻居。这样每个 super-step 内，顶点程序间可以完全并行。super-step 之间则是完全串行。

顶点间通过显式的消息（SendMsg）通信。

每个顶点接收的信息会先通过用户编写的 combiner 组合。

### 2.2 GraphLab

异步、分布式、共享内存。顶点程序可以访问每个顶点、每条边上存储的数据。用户不必像 Pregel 一样显式通信。

GraphLab 通过保证邻居顶点上的顶点程序不同时运行来避免冲突。

通过区分顶点和边上的数据，用户得以更精确地控制哪些信息分享给所有邻居，哪些信息分享给特定邻居。

### 2.3 Characterization

对顶点程序的抽象

**GAS: Gather, Apply, Scatter**

- gather, 对每个入邻居，根据两个点和入边上的数据计算对应结果，然后将所有邻居对应的结果求“和”
- apply, 根据当前点的原有值和 gather 阶段的“和”，计算当前顶点的新值
- scatter, 根据当前点的新值和出邻居的值计算出边上的新值

高性能的扇入和扇出对图上的计算非常重要。

## 3 Challenge of Natural Graphs

power-law 的定量描述：$\mathrm{P}(d) \propto d^{-\alpha}$，常见的自然世界中的图的 $\alpha$ 都在 2 左右。

点度数的幂律分布对图计算的负载均衡、子图切分、机器通信、存储、计算都带来了挑战，因为其中很多部分消耗都与度数成正比。

## 4 PowerGraph Abstraction

借鉴了 GraphLab 中图上数据的表示方法，使用户不必显式控制数据移动。借鉴了 Pregel 中 gather 阶段需要组合操作满足交换律和结合律的要求。

### 4.1 GAS Vertex-Programs

gather 阶段最终的聚合结果大小以及 apply 阶段的复杂度对 PowerGraph 的网络和存储需求非常重要，关于度数的复杂度应当是次线性的，最好是常数级别。

scatter 阶段可以返回一个可选的值，用于动态更新出邻居的新值，和 gather 阶段稍有重合。

在 scatter 阶段根据终止条件判断是否激活出邻居的新一轮计算。

### 4.2 Delta Caching

有些情况下，每个点的邻居只有少量被更新，用 gather 进行全量更新比较浪费。因此加入了维护一个缓存的聚合值，在 scatter 阶段增量更新的方法。（增量是指只用到邻居中更新过的顶点）

Fig.3 中 PageRank 和 SSSP 两种方法对增量更新的使用方法略有不同。后者的 sum 实现是最小值，因此天然不需要未更新值。形式化的表述应当是 $a \oplus a' = a' \Rightarrow  a \oplus b \oplus a' = b \oplus a'$。（不够准确，仅能描述 sum 和 apply 一致的情形）

而 PageRank 将普通的对 rank 求和改写为对 rank 的差值求和来实现增量更新。需要满足 $a \oplus b' = a \oplus b \oplus (b' \oplus -b)$，即交换群。

### 4.3 Initiating Future Computation

顶点可以激活自己或邻居，仅限各个 phase 的参数，例如 scatter 阶段的出邻居。（QNS: 有需要激活入邻居的场景吗？）

#### 4.3.1 Bulk Synchronous Execution

所有激活顶点的 gather, apply, scatter 3 个阶段分别按顺序集中运行，称为 3 个 minor-step。这 3 个 minor-step 又构成 1 个 super-step。

#### 4.3.2 Asynchronous Execution

异步运行往往能更快收敛。甚至部分算法在同步下无法收敛，只能在异步下收敛。

GraphLab 通过禁止相邻顶点同时运行来保证可串行化。具体实现是待运行顶点依次取所有邻居的锁，但这对于高度数顶点来说更困难。

QNS: （推论，文中未给出）PowerGraph 的顶点划分方式使得高度数顶点只需要取得自己所处机器上的邻居的锁，降低了冲突概率。

### 4.4 Comparison with GraphLab/Pregel

通过令 gather 阶段生成关于邻居信息的列表，PowerGraph 可以模拟 GraphLab/Pregel 的顶点程序。

## 5 Distributed Graph Placement

需要将图中的顶点均匀分配到 p 台机器上（即 p 路划分），且两个顶点位于不同机器的边尽可能少。常用的图划分工具对幂律图很难均匀划分顶点，因此经常选择对顶点哈希的做法。哈希方法虽然能均匀分配顶点，但会导致大多数边都成为割边。

### 5.1 Balanced p-way Vertex-Cut

PowerGraph 支持对图进行顶点划分。每条边只分配到 1 台机器，顶点则允许存在多个拷贝。因为 gather 和 scatter 阶段都可以多机并行，只有 apply 阶段需要同步顶点数据。

CMNT: Pregel 和 GraphLab 只是没有对顶点程序分阶段才比较难做顶点划分吧，毕竟无法预期用户会对邻居集合做什么，用户也无法获知顶点的邻居在不同机器上的分配情况。

顶点划分中通信消耗就依赖顶点拷贝的数量，因此最优化目标为 $\min\limits_{A}\sum\limits_{v \in V}{|A(v)|}$，其中 $A(v) \ne \empty$ 即顶点 $v$ 所属的机器数量。还需要一个关于边分配均衡的约束条件 $\max\limits_{m}|\{e\in E|A(e)=m\}|<\lambda\frac{|E|}{p}$，其中 $A(e)$ 是顶点所属的机器，$p$ 为机器数量，$\lambda \ge 1$ 是个可调的常数。

在一个顶点的所有拷贝中随机选取一个作为主顶点，其他作为镜像。apply 阶段必须在主顶点上进行，再将新值同步给镜像。

根据渗流理论（percolation theory），幂律图上顶点划分能取得较好的结果。

证明略。

定理 5.3 充分证明了点划分的优越性……

### 5.2 Greedy Vertex-Cuts

贪心方法，对新加入的边 $(u,v)$，根据当前 $A(u)$ 和 $A(v)$ 是否为空、交集是否为空分情况决定边的归属。

两种贪心方法的分布式实现：
- 维护分布式表来查询 $A(u)$
- 不进行通信，各机器独立处理，维护对各机器负载和边分配情况的估计（？？？）





