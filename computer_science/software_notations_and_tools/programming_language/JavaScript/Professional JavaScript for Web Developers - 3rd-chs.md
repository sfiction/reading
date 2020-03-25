

## 3. 基本概念

#### 3.4.5 Number 类型

- 浮点。如果是整数，实际类型就是整数。
- 数值转换。Number, parseInt, parseFloat
    - number. null->0, undefined->NaN, 前导零视为十进制, ''->0, obj->valueOf/toString

#### 3.4.6 String 类型

- 16bits unicode
- 不可变对象
- toString

#### 3.4.7 Object 类型

- constructor() // also type??
- hasOwnProperty(propertyName)
- isPrototypeOf(object)
- propertyIsEnumerable(propertyName)
- toLocaleString()
- toString()
- valueOf()

宿主对象不一定继承了 Object

### 3.6 操作符

相等和全等

#### 3.6.5 for-in

遍历对象属性

with 设置作用域。需要非严格模式。代价较高，会导致性能下降。

### 3.7 函数

#### 3.7.1 理解参数

arguments[i] 和具名参数是引用同一个对象？严格模式中相互独立，且修改 arguments 会导致错误。

## 4. 变量、作用域和内存问题

分为基本类型（Undefined, Null, Boolean, Number, String）和引用类型

instanceof

实现 `[[call]]` 方法的对象的 typeof 应该为 function，浏览器间 regex 实现不同，因此 typeof 结果也不同。

### 4.2 执行环境及作用域

执行环境、作用域链

变量对象是啥啊……看起来就是符号表？

函数的活动对象（activatoin object）是啥啊

全局作用域和函数以外，try-catch 的 catch 部分和 with 语句也会新增变量对象。但 tmd var 语句怎么还是归上层所有？？区分作用域&变量对象和执行环境。catch 和 with 不新建执行环境。

带 var 的声明添加到最近的执行环境，不带的如果是声明（首次出现）则直接加到全局环境。严格模式下，不带会被视为语法错误，不会加入全局环境。

搜索符号则完全依赖作用域链。

我以为这种分只读和可读可写的嵌套符号表只有 js，没想到 py 的 except 也有类似行为……

## 5. 引用类型

### 5.1 Object

两种初始化方式。两种访问属性的方法：点和方括号。后者使得可以用变量索引属性，并可接受更广泛格式的属性名，如包含空格。

### 5.2 Array

无参构造（空）、单 int 构造（长度）、其他（直接作为内容）

也支持用方括号表示构造（最后一项省略，即 ',' 与 ']' 相邻时，IE8 行为与其他浏览器不同，分别会赋为 undefined 和略去该项）。

0-base

Array length 居然不是只读的。。。修改会直接影响实际长度，新增 undefined。。

用下标索引赋值的时候，如果超出了原先的 length，居然会自动扩容……这 tm 不是 shell 的行为吗。

- Array.isArray()。。
    - 什么，居然还有多 frame 下的 array 判定问题。。
- Array.join()。无参或 undefined 则用逗号作为分隔符。
- Array.push()/pop()
- shift, unshift
- reverse, sort。sort 默认转为 toString 再比较，不管类型。可接受一个三值（<0, 0, >0）的比较函数。
    - 减法适当个 x 啊。。不是会溢出吗。。
- slice, splice
- indexOf, lastIndexOf
- 迭代方法
    - every, bool
    - some, bool
    - filter, bool
    - forEach, no return
    - map
- 归并方法
    - reduce(function(prev, cur, index, array) [, init])
    - reduceRight

### 5.3 Date

- parse
- UTC
- 构造函数，年月必需
- now
- (get|set)(UTC)?(Y|m|d|H|M|S|s)

### 5.4 RegExp

TODO

### 5.5 Function

- 函数声明
- 函数表达式
- Function 构造函数（不推荐）
- 函数声明提升
- arguments.callee, 非严格模式
- arguments.callee.caller, 非严格模式
- length, 期望参数数量
- .apply, 参数用列表或 arguments 传递。第一个参数为作用域（环境对象）
- .call, 参数以参数方式传递
- 严格模式下，不指定环境对象而调用函数，this 值是 undefined 而不是 window

### 5.6 基本包装类型

- 基本类型 Boolean/String/Number 在访问时会临时创建一个包装类型。
- Object 可以按照传入的基本类型返回相应的包装类型对象
- 类型转换函数和包装类型的构造函数不同，后者必须通过 new 调用。
- Number
    - toFixed
    - toExponential
    - toPrecision
- String
    - charAt, charCodeAt
    - concat
    - slice, substring, substr(len)
        - 负数行为差异…… + len, max(0), (+len, max(0))
    - indexOf, lastIndexOf
    - trim
    - to(Locale)?(Upper|Lower)Case
    - match, search,replace, split
    - localCompare
    - String.fromCharCode

### 5.7 单体内置对象

#### 5.7.1 Global

- encodeURI()
- eval
- window, Global 基本上都被视为 window 的一部分

#### 5.7.2 Math

- E, LN10, LN2, LOG2E, LOG10E, PI, SQRT1_2, SQRT2
- min, max
- ceil, floor, round
- random
- abs, exp, log, pow, sqrt, acos, asin, tan, atan, atan2, cos, sin, tan

## 6. 面向对象的程序设计

评：味同嚼蜡
- 6.1 对后续的帮助只有实例或 prototype 的 constructor 属性如何得以隐藏。getter 和 setter 与后面几节无关，虽然很有意思。
- 6.2 只有前三章必要，第四章过于显然，可以并入第三章。再后面的内容也都很显然，只是写法上的区别，只有 6.2.7 有点安全性的考量。
- 6.3 讲完原型链就可以直接讲 6.3.6 了，让原型对象的 prototype 为另一个类型的原型对象不是非常显然吗，原型链里就在这么干了？？直接说调整一下代码分离属性和方法不就好了？？6.3.4 和 6.3.5 简直就是垃圾……

### 6.1 理解对象

- Object.defineProperty, Object.defineProperties
- Object.getOwnPropertyDescription（只能用于自身属性，不能访问原型的属性）
- 数据属性的特性
    - `[[Configureable]]`, 能够通过 delete 删除从而重新定义
    - `[[Enumerable]]`, 能否通过 for-in 遍历
    - `[[Writable]]`, 能否修改
    - `[[Value]]`, 数据值
- 访问器属性的特性
    - `[[Configureable]]`
    - `[[Enumerable]]`
    - `[[Get]]`, getter 函数
    - `[[Set]]`, setter 函数

### 6.2 创建对象

工厂模式

#### 6.2.2  构造函数模式

自称为构造函数，增加了 new 操作符的支持。不过 new 的功能也可以通过普通函数来实现。因此去掉语法糖的部分还不需要动语言的基础部分。

- new 操作符原理
    - 创建新对象
    - 通过 apply 转移作用域
    - 用构造函数给新对象增加属性
    - 给新对象增加 constructor 属性
- .constructor 属性
    - 缺陷：方法的重复创建

#### 6.2.3 原型模式

共享内容的显然解法，与工厂模式到构造函数模式的演进不同，需要支持在 prototype 中的二次查询，需要更深入的修改。

- 为每个函数增加一个 prototype 属性，指向其原型对象
- 原型对象的 constructor 属性指向相应的构造函数
- 每个通过该函数生成的实例也有一个属性（`[[Prototype]]`）指向函数的原型对象
    - 标准没有规定实例中哪个属性指向原型
    - 实例好像没有 constructor 属性
- isPrototypeOf
- Object.getPrototypeOf
- 获取值和方法时会先查找实例，再查找原型对象
- hasOwnProperty, 判断属性属于实例还是原型
- in 和 for-in 不区分实例和原型属性
- 用字面值方法设置原型时，实际上是创建了新的 Object，因此要手工设置 constructor 属性。注意设置 enumerable 为 false
- 修改原生对象（String/Number etc.）的原型

其他，只是些简单的混合或变种，不涉及语言功能的变动
- 组合使用构造函数模式和原型模式。非常显然的增强……
- 动态原型模式。增加了无谓的判断
- 寄生构造函数模式。和原型无关。。。。
- 稳妥对象模式

### 6.3 继承

#### 6.3.1 原型链

直接将某个构造函数的 prototype 指向另一个类型的实例。导致仅在父类型实例中出现的属性被子类型的所有实例共享。

#### 6.3.2 借用构造函数

```javascript
function SuperType(name){this.name = name;}
function SubType(){SuperType.call(this, "Nicholas"); this.age = 29;}
```

#### 6.3.3 组合继承

用构造函数添加属性，原型对象添加方法。

其他
- `Object.create($SuperTypeInstance, $new_properties)`。这 tm 和原型链有什么区别。
- 这 tm 不就是把外部或者参数里的增强过程移到里面。

#### 6.3.6 寄生组合式继承

作者写得这么麻烦。。到头来不就是令 SubType 的原型对象的 prototype 指向 SuperType 的原型对象，然后给 SubType 的原型对象添加方法，在构造函数里添加属性。

## 7. 函数表达式

命名函数表达式。函数名只对函数体内部可见。

通过作用域链实现闭包。大概如果分析为需要用到外部变量就保留作用域链。图 7.2 中作用域链大概不是真正的实现方式，只是活动对象之间的引用链吧，把闭包加入考虑应该是个树结构。

注：闭包应该分成至少三种：符号、引用、值。c++ 因为语言限制，只能做到第一种和第三种。但第二种可以通过第三种的包装来实现。js 和 py 实现第三种比较奇怪，因此实际上是在前两种之间选择了符号方式。
- 符号方式直接保留相应作用域，因此同一作用域下生成的多个闭包之间能够共享该符号，即赋值操作能够反映到原符号上。
- 引用方式下，虽然符号是同一个，但变量是不同的，只是恰好指向了同一个内存对象。特征是赋值操作不会影响同作用域下的其他闭包。
- 值方式就是连内存地址也不同。

7.2.2 提了一些关于 this 的神坑。。

7.2.3 这个内存泄漏的坑应该已经修好了吧……

用函数表达式模拟快作用域。

利用闭包给类型增加私有成员和操作他们的函数。但这些闭包在每次构造时都会创建一次。

模块模式不就是单例对象。。7.4.3 毫无必要。。

## 8 BOM

### 8.1 window 对象

用 var 语句添加的 window 属性的 `[[Configurable]]` 特性被设置为 false，因此无法被删除。

TODO

## 9 

## 13 事件

### 13.1 事件流

冒泡与捕获。

## 17. 错误处理与调试

### 17.2.1 try-catch 语句





















## js 补遗

- ES6 之后引入 let，和 var 类似，但是支持块作用域。