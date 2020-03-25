Python 源码剖析

Python2

## 编译 Python

- 整体架构：解释器、Runtime、lib
- 源代码组织：
    - include: C/C++ 二次开发所需头文件
    - lib: python lib
    - modules: 对速度有要求的模块，用 C 编写
    - arser: 解释器的 Scanner 和 Parser 部分
    - objects: 所有内建对象的实现
    - python: 解释器的 compiler 和执行引擎

## 对象机制

- 基本对象，定长与变长对象
    - `PyObject` (`struct _object`): a.k.a PyObject_HEAD
        - `struct _object *_ob_next`, 有 Py_RRACE_REFS 开关时，用于将所有堆上对象组成双向链表
        - `struct _object *_ob_prev`, 同上
        - `int ob_refcnt`, 引用计数
        - `struct _typeobject *ob_type`, 对象类型
    - `PyVarObject`: a.k.a PyObject_VAR_HEAD
        - PyObject_HEAD
        - `int ob_size`, 元素数量
        - VarObject 也有 mutable 和 immutable 两种。前者如 list，后者如 str 和 tuple
- 类型对象
    - `PyTypeObject` (`struct _typeobject`)
        - PyObject_VAR_HEAD
        - `char *tp_name`, 类型名
        - `int tp_basicsize`, 该类型的对象大小
        - `int tp_itemsize`, 应该是给变长对象准备的
        - 各种标准运算/操作的函数指针
    - 用 `PyType_Type` 实例来作为类型对象的类型，包括其自身
- 对象间的继承和多态
    - python 用 `(PyObject*)->(PyTypeObject*)->XXX` 的方式泛型处理标准运算/操作
- 引用计数
    - `_Py_NewRefernece(op)`
    - `_Py_Dealloc(op)`
    - `Py_INCREF(op)`, `Py_XINCREF(op)`
    - `Py_DECREF(op)`, `Py_XDECREF(op)`
- Python 对象分类
    - 数值对象：Integer, Long, Float, Complex
    - 容器对象：List, Dict, String, Tuple
    - 程序结构对象：Class, Method, Function, Module, File
    - 内部对象：Buffer, Type, Frame, Cell, Code

## 整数对象

小整数（默认 -5~100）提前创建，大整数不 free，引擎回收内存重复使用。

### PyIntObject

## 字符串对象

空串和单字符串类似小整数，但是动态创建，永不析构。

- `PyStringObject`,  PyObject_VAR_HEAD
    - `long ob_shash`, hash value
    - `int ob_sstate`, intern 标志
    - `char ob_sval[1]`, QNS: 为什么指向的内存必须用 '\0' 结尾？明明都用了 ob_size？

天了噜，申请的内存居然是 sizeof(PyStringObject) + ob_size，怪不得类型是字符数组。


```C
[stringobject.c]
static long string_hash(PyStringObject *a) {
    register int len;
    register unsigned char *p;
    register long x;
    if (a->ob_shash != -1)
        return a->ob_shash;
    len = a->ob_size;
    p = (unsigned char *) a->ob_sval;
    x = *p << 7;
    while (--len >= 0)
        x = (1000003*x) ^ *p++;
    x ^= a->ob_size;
    if (x == -1)
        x = -2;
    a->ob_shash = x;
    return x;
}
```

### intern 机制

用一个 PyDict 维护所有字符串，创建一个新 PyString 之后，在该 dict 中查询有相同内容的串的对象，改为指向该对象。新生成的 PyString 直接析构。

intern 分为 mortal 和 immortal 两种，后者与 runtime 同样生命周期，前者则仍然在引用计数管理下。

呃，对 nullstring 和 character 同时应用 immortial intern 和解除引用计数，感觉有点多此一举，应该只需要后者吧？？

并不是所有创建 pystring 的场景都会 intern。

.join 只用一次 malloc

## 列表对象 List

- `PyListObject`: PyObject_VAR_HEAD
    - `PyObject **ob_item`
    - `int allocated`, 分配在 ob_item 中的元素数，即 cpp vector 的 capacity

和 vector 很像。

对 list 建立了一个大小为 MAXFREELISTS (80) 的缓冲池。缓冲池未满时，本该被 dealloc 的 PyList 会被加入其中。

## 字典对象 Dict

由于 Dict 大量应用于 python 自身的实现，选择了理论性能更好的 hash table 而不是平衡树。

天了噜，Python Dict 解决冲突居然用了探测函数+伪删除。

- `PyDictEntry`
    - `long me_hash`, 缓存 hash
    - `PyObject *me_key`
    - `PyObject *me_value`
- `PyDictObject`: PyObject_HEAD
    - `int ma_fill`, active + dummy
    - `int ma_used`, active
    - `int ma_mask`, 应该是使用 smalltable 时，每个位置状态的 bitmap，16bits 吧
    - `PyDictEntry *ma_table`,最初指向  ma_smalltable，增大时会指向一块另外申请的内存，因此始终只用到 ma_table
    - `PyDictEntry *(*ma_lookup)(PyDictObject *mp, PyObject *key, long hash)`, 自定义的 lookup
    - `PyDictEntry ma_smalltable[PyDict_MINSIZE]`

用一个特殊的 PyObject dummy 来标记伪删除状态。

天了噜，居然真的有个 '<dummy key>' 的 PyString，这就是为什么 intern 没有内置在 PyString_FromString 里的原因？？

和 list 一样用了大小为 80 的缓冲池。