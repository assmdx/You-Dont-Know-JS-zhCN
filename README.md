# You-Dont-Know-JS-zhCN
[zh-CN] translation of "You Dont Know JS", original: https://github.com/getify/You-Dont-Know-JS

-----
**Personal Study Use Only, 仅供个人学习使用。**

-----

# 目录
## up & going
### [Chapter 1: Into Programming](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/up%20%26%20going/ch1.md)
基本编程概念：变量、操作符、表达式、函数等。

### [Chapter 2: Into JavaScript](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/up%20%26%20going/ch2.md)
相对全面的概述了JS中的基本概念，让新手可以快速的了解到这门语言的方方面面。

### [Chapter 3: Into YDKJS](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/up%20%26%20going/ch3.md)
介绍了该系列丛书的其它几卷涉及的主题以及正确的打开方式。

## types & grammar
### [Chapter 1: Types](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/types%20%26%20grammar/ch1.md)
JS的变量没有类型，变量持有的值才有类型。

### [Chapter 2: Values](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/types%20%26%20grammar/ch2.md)

> 在JS里，`array` 是数字索引的任何值类型的集合，`string` 虽然有些像 `array`，但它们还是有区别的，所以当你把它用作数组时一定要小心。JS中的数字包括整数和浮点数。
>
> 几个特殊的值是基础类型。
>
> `null` 类型有自己的值 `null`， `undefined` 也是，`undefined` 是当变量或属性没有其它值时的默认值，`void` 操作符让你可以用任何值生成 `undefined`值。
>
> `number` 中有几个特殊值，比如 `NaN`（说的不是「非数」，而是「非法数字」），`+Infinity`、`-Infinity`、`-0`。
>
> 简单的基础值（`string`、`number` 等）是值拷贝来传递、赋值的，而符合值（`object` 等）则是引用拷贝传递／赋值的。引用不像其它语言里的引用／指针，它们不会指向其它变量／引用，只会指向真实的值。

### [Chapter 3: Natives](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/types%20%26%20grammar/ch3.md)

### [Chapter 4: Coercion](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/types%20%26%20grammar/ch4.md)

### [Chapter 5: Grammar](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/types%20%26%20grammar/ch5.md)

### [Appendix A: Mixed Environment JavaScript](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/types%20%26%20grammar/apA.md)

### [Appendix B: Thank You's!](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/types%20%26%20grammar/apB.md)

## *this* & Object Prototypes

### [Chapter 1: `this` Or That?](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/this%20%26%20object%20prototypes/ch1.md)

> `this`对JS开发者来说是一个很常见的困惑源，这些开发者一般不会花时间去学习它的工作原理。猜、试错以及盲目的从Stack Overflow复制粘贴并不是一个有效且合适的方法来利用*这个*重要的`this`机制。
>
> 为了学习`this`，首先你需要知道`this`不是什么，清除所有你学习之路上的错误假设和概念，`this`不是引用函数自身，也不是引用函数的*词法*作用域。
>
> `this`实际上是一个函数被激活后的绑定关系，它引用的是*什么*需要根据函数被调用处的调用现场来决定。

### [Chapter 2: `this` All Makes Sense Now!](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/this%20%26%20object%20prototypes/ch2.md)

> 从函数调用现场决定`this`的规则，顺着优先级由高到低，当有规则适用时即停止。
> 
> 1. 由`new`调用，`this`就是新构造的对象。
> 2. 由`call`或`apply`调用，或者由`bind`调用，`this`则是显式指定的对象。
> 3. 通过上下文调用，或者函数本身就是对象持有的属性，么`this`就是这个上下文对象。
> 4. 否则，`this`是默认绑定，在严格模式下取`undefined`，其它取`global`对象。

### [Chapter 4: Mixing (Up) "Class" Objects](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/this%20%26%20object%20prototypes/ch4.md)

>类是一种设计模式，很多语言提供支持面向类的软件设计语法，JS也有，但是它和你在其它语言中使用的**大为不同**。
> **类意味着复制。**
> 当传统的类被实例化时，从类复制到实例的行为就发生，当类被继承时，一个从父到子的复制行为发生。
> 多态性（在继承链上存在不同等级的同名函数）看起来好像是有一个隐式的由子反指向父的链接，但它仍然是复制的结果。
> JS**不会自动**在对象之间创建复制（虽然类暗示着复制）。
> 混合模式（显式和隐式）经常被用来模拟类的复制行为，但是这会导致出现如显式伪-多态（`OtherObj.methodName.call(this, ...)`）这样的丑陋、脆弱的语法，最终代码变得难以理解和维护。
> 显式混合也并不完全与类*复制*相同，因为对象（和函数！）只会复制引用，而不是复制它们自身，不注意到这一点会引发许多问题。
> 通常，JS中的伪类更多的带来了未来编码的坑，而不是解决当前的*真正*的问题。

## async & performance

### [Chapter 1: Asynchrony: Now & Later](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/async%20%26%20performance/ch1.md)

这一章主要讲解异步概念以及常见的并发处理手段。

### [Chapter 2: Callbacks](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/async%20%26%20performance/ch2.md)

> 回调是JS异步的基础，但是他们还不足以作为JS异步编程的成熟演进模型。
>
> 首先，我们的大脑是线性、阻塞、单线程的方式来计划事情的，但是回调表达异步流却是一种非线性、非顺序的方式，这让分析这种代码变得很艰难，难以分析的代码就是那些导致糟糕bug的坏代码。
>
> 我们需要一种能以更同步、阻塞、线性的方式来表达异步，就像我们的大脑那样。
>
> 然后，也是更重要的一点，回调因为把控制权交给了另一方（通常是不受你控制的第三方工具）来触发你的程序*继续*（运行），所以会受*控制反转*的影响。控制权的转移导致一些信任问题，比如是否回调被调用次数超过了我们的预期。
>
> 创建专门解决这种信任问题的逻辑时可能的，但是却超过了成本，并且会产生很多冗余的、难以理解的维护代码，同时还会有那些直到你明显被坑才会发现是因为没有充分规避危害的bug。
>
> 我们需要一个通用于**所有信任问题**的解决方案，一个可以被许多回调重用而不需要再在开头创建引用的方案。
>
> 我们需要一些比回调更优雅的东西，虽然回调已广泛服务于大家，但是JS的*未来*呼吁更成熟和强大的异步模式。

### [Chapter 3: Promises](https://github.com/NoName4Me/You-Dont-Know-JS-zhCN/blob/master/async%20%26%20performance/ch3.md)

>