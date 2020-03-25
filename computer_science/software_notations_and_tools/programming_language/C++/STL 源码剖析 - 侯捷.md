STL 源码剖析

## 

`set_new_handler`, 用于指定内存申请失败时调用的函数，会返回旧的函数指针。该函数默认为空指针，此时会直接抛出 bad_alloc 异常。

### 2.2

- 空间配置函数 `allocate`/`deallocate`，大于阈值就调用第一级配置器，否则维护本地的小区块链表来分配/回收内存，链表为空时调用 `refill` 补充相应链表的区块。
- `refill`调用 `chunk_alloc` 从内存池中获得区块（一段连续内存）
- `chunk_alloc(size, n_objs)`
  - 能提供一个以上的区块则使用内存池剩余空间
  - 将内存池剩余空间分配给较小的 `free_list`，然后尝试用 malloc 申请内存
    - 如果申请失败，则尝试将较大的 `free_list` 的区块分解利用
      - 没有任何可用内存，尝试用一级配置器处理
  - 用申请到的内存提供区块

### 2.3 内存基本处理工具

这节感觉像是把一段内容复制了三遍。。。

`uninitialized_copy/fill/fill_n` 3 个函数，都通过 type traits 特判了 POD 类型，直接调用相应的 STL 函数来优化。

`uninitialized_copy` 还特判了字符类型，用 `memmove` 加速。

## 3 迭代器

### 3.4 Traits

3.3 和 3.4 提到的问题，即解引用的类型和函数返回值的类型，在 C++11 中以 decltype 解决了。

自定义的迭代器需要声明 traits，为了对原生指针兼容，利用模板偏特化再包装一层。即定义模板为从类型域中取出所需类型定义，对指针和常量指针再偏特化一个模板，直接定义所需类型。

迭代器所用的 iterator_traits 有五种

- value type
- difference type, 迭代器之间的距离，原生指针为 ptrdiff_t
- reference type, 根据迭代器自身常量与否返回 `T&` 或 `const T&`
- pointer type, 与 reference 类似
- iterator ategory, 被分为五类
  - input
  - output
  - forward
  - bidirectional
  - random access

### 3.5 `std::iterator`

继承 `std::iterator` 减轻设计迭代器类的工作量。

### 3.6 实现

注意因为 value 和 difference 不像 iterator category 一样是空类型，实现中的 `value_type` 和 `distance_type` 函数返回类型是相应 traits 的指针。

### 3.7 `__type_traits`

需要判断一个类型的 default ctor/copy ctor/assignment/dtor 是否 trival，以及是否是一个 POD type。STL 可以利用这些信息来优化一些基本操作，如直接使用内存级别的复制而不是调用复制构造函数。

用类型 `__true_type` 和 `__false_type` 表示上述 5 个判定的结果。

部分编译器可以对用户自定义类型自动生成 type traits