# You Don't Know JS: *this* & Object Prototypes
# Chapter 1: `this` Or That?

`this`是JS中一个最容易让人困惑的关键词，它是一个在每一个函数作用域内自动定义的关键标识符，但它真正的引用有时即使老司机也会翻车。


> 任何足够*高级*的技术都与魔法无异。—— C. Clarke

JS的`this`事实上并没有*那么*高级，但开发者经常引用这句话，其实不过是他们自己过分强调了“复杂”和“困惑”，当然如果没有清楚理解，`this`就会在你的困惑中魔幻起来。

## Why `this`?

既然`this`这么坑爹，连老司机都翻车，那么为什么我们还是要用它？所以我们在讲解*how*前，先来讨论下*why*。

Let's try to illustrate the motivation and utility of `this`:
我们试着表示一下`this`的动机和作用：

```js
function identify() {
	return this.name.toUpperCase();
}

function speak() {
	var greeting = "Hello, I'm " + identify.call( this );
	console.log( greeting );
}

var me = {
	name: "Kyle"
};

var you = {
	name: "Reader"
};

identify.call( me ); // KYLE
identify.call( you ); // READER

speak.call( me ); // Hello, I'm KYLE
speak.call( you ); // Hello, I'm READER
```

如果你理解不了上面代码里的*how*，无妨，我们现在说的是*why*，请继续往下看：

这段代码允许在不同的 *上下文*（`me`和`you`对象）重复使用函数`identify()`和`speak()`，而不是对每个对象都需要独立的函数。当然，你可以不使用`this`，而是直接把特定的上下文对象传给两个函数：

```js
function identify(context) {
	return context.name.toUpperCase();
}

function speak(context) {
	var greeting = "Hello, I'm " + identify( context );
	console.log( greeting );
}

identify( you ); // READER
speak( me ); // Hello, I'm KYLE
```
然后，`this`机制却提供了一种隐式“传递”对象引用的优雅方式，从而让API设计更清晰和更容易重复利用。（不好意思，我没看出来怎么个优雅法……）

使用方式越复杂，你越会清楚的认识到，把上下文当作特定参数传递经常会一团糟，远不如传递`this`上下文。当我们后面探索对象和原型时，你会看到一系列可以自动引用合适上下文对象的函数的好处。

## Confusions

在我们开始学习`this`的工作原理前，我们先消除一些错误观念。

当开发者从字面上来理解“this”时，就会产生困惑，一般有两种错误假设：

### Itself

一种就是认为`this`引用的时函数本身，从文字语法上来讲，确实是的。
The first common temptation is to assume `this` refers to the function itself. That's a reasonable grammatical inference, at least.

但是你在函数中为何还要多此一举的引用它自己呢？当然出了递归或者在首次调用时来解绑的事件句柄除外。

新接触JS的开发者通常认为把函数作为对象来引用（JS中的所有函数都是对象！），可以保存函数之间调用时的 *状态* （属性值），尽管这是可行的并且有一些有限的用处，本书的剩余部分将会阐述许多其它*更好的*的存储状态的方式。

稍等，我们会讲到那个模式，我们先看一下`this`无法如我们所期望的获取到函数自身引用的一个例子：

下面的代码意图跟踪函数`foo()`被调用次数：

```js
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		foo( i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 0 -- WTF?
```

尽管我们可以看到`console.log`打印了4次，表明`foo(..)`被调用了4次，但是`foo.count`*一直都* 是`0`，这个困惑源自*太文字*理解`this`（在`this.count`）的含义。

当代码执行`foo.count = 0`时，它确实给`foo`增加了一个属性`count`，但是函数中的`this.count`，`this`并不*总是*指向这个函数对象，尽管属性名是一样的，本质的对象不同，所以困惑紧跟着出现。

**注意：** 一个靠谱的开发者此时*应该*有这样的问题：“如果我增加的不是我期望的`count`，那它是谁（的）？” 事实上，如果深挖下去，他会发现其实他创建了一个全局变量`count`（参考第二章了解它*如何*发生！），它当前的值为`NaN`，当他发现这一点时，他又回问“它时如何成为全局变量的，以及为何它的值是`NaN`而不是其它值？”（仍然参考第二章）

很多开发者到这里，一般不是去深入了解一下为何`this`并没有如期行为，然后回答这些难却重要的问题，而是会避开这些问题，采用一些hack解决方式，比如创建一个持有`count`属性的对象：

```js
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	data.count++;
}

var data = {
	count: 0
};

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		foo( i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( data.count ); // 4
```

尽管这种方式“解决”了那个问题，可惜的是它只是忽律了真实的问题 —— 理解`this`的意思以及它工作原理，又回到了自己更熟悉的舒适区：词法作用域。

**注意：** 词法作用域是一个完全OK和有用的机制，我并不是轻视使用它（参考本系列书的 *Scope & Closure* 那本）。但是不理解`this`的使用方法，经常错误的使用，采用回退到词法作用域来解决，并且从来不主动去学习*为何*与`this`缘分这么浅并不是个好理由。

为了从内部引用函数对象本身，`this`足够胜任，通常情况下你只需要通过词法标识符（变量）就可以做到：

```js
function foo() {
	foo.count = 4; // `foo` refers to itself
}

setTimeout( function(){
	// anonymous function (no name), cannot
	// refer to itself
}, 10 );
```

第一个函数，叫做“命名函数”，`foo`是一个可以从函数内部引用函数自己的引用。但第二个传递给`setTimeout(..)`的函数没有标识符（所谓的“匿名函数”），所以没有合适的方法来引用函数对象本身。

**注意：** 旧时代已经弃用且不建议使用的`arguments.callee`引用在函数里*也*指向函数对象本身，这是唯一的从内部获取匿名函数对象的方法，最好的方式，其实是避免使用匿名函数，至少对于那些需要自引用的，使用命名函数。

所以文章开始那个例子的另一个解决方式就是使用`foo`标识符来作为函数引用，而不是使用`this`：

```js
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	foo.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		foo( i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 4
```

但是，这种方式也是回避了理解`this`，完全依赖`foo`的词法作用域。

当然，另一个解决方式就是强制让`this`指向`foo`函数对象：

```js
function foo(num) {
	console.log( "foo: " + num );

	// keep track of how many times `foo` is called
	// Note: `this` IS actually `foo` now, based on
	// how `foo` is called (see below)
	this.count++;
}

foo.count = 0;

var i;

for (i=0; i<10; i++) {
	if (i > 5) {
		// using `call(..)`, we ensure the `this`
		// points at the function object (`foo`) itself
		foo.call( foo, i );
	}
}
// foo: 6
// foo: 7
// foo: 8
// foo: 9

// how many times was `foo` called?
console.log( foo.count ); // 4
```

**不是回避`this`，我们拥抱它。** 我们将会解释这种方式如何工作，所以不要担心现在还看不明白！

### Its Scope

另一个对`this`的错误理解是，认为它是引用的函数作用域。这个问题比较刁，因为从某种程度上它是正确的，但是却很误导人。

严格说来，`this`不是引用函数的**词法作用域**。从内部来说，作用域相当于一个每一个可访问变量都是属性的对象，但是作用域“对象”对JS代码来说是不可接受的，这是由*引擎*的内部实现决定的。

考虑这段尝试（会失败！）使用`this`跨越边界来隐式引用函数词法作用域的代码：

```js
function foo() {
	var a = 2;
	this.bar();
}

function bar() {
	console.log( this.a );
}

foo(); //undefined
```

这段代码有不止一处的错误，尽管它看起来是人为的，你看到的代码是在现实世界上在公共帮助论坛上交流过的。它正好说明了`this`的假设多么有误导性。

首先，尝试通过`this.bar()`引用`bar()`，它能生效是一个 *意外* ，但我们会简单解释它。最直观的触发`bar()`的方式是省略`this.`，直接在词法作用域内调用它。

然而，写出这段代码的开发者尝试使用`this`来创建`foo()`和`bar()`之间的纽带，所以`bar()`可以访问`foo()`内部作用域的`a`变量。**这种纽带是不可能的。**你不能通过`this`来在超出词法作用域寻找东西，这不科学。

每一次你感觉自己在使用`this`尝试混合词法作用域做查找，提醒自己：*没有纽带*。

## What's `this`?

通过消除错误的假设，我们现在回到`this`究竟如何工作。

我们前面说过`this`不是一个创建期绑定而是运行时绑定，它是基于函数被触发的条件，`this`绑定与函数在何处声明没有关系，而是和它是以何种方式被调用的有关。

当一个函数被激活时，一个激活记录，或者说一个执行上下文被创建，这个记录包含函数在何处被调用（call-stack），函数*如何*被激活，传入的参数是什么等等。这个记录的属性之一就是`this`引用，它会被用于函数执行期间。

下一章，我们会学习寻找一个函数的**call-site**（调用现场？），进而弄懂它的执行是如何绑定`this`的。

## Review (TL;DR)

`this`对JS开发者来说是一个很常见的困惑源，这些开发者一般不会花时间去学习它的工作原理。猜、试错以及盲目的从Stack Overflow复制粘贴并不是一个有效且合适的方法来利用*这个*重要的`this`机制。

为了学习`this`，首先你需要知道`this`不是什么，清除所有你学习之路上的错误假设和概念，`this`不是引用函数自身，也不是引用函数的*词法*作用域。

`this`实际上是一个函数被激活后的绑定关系，它引用的是*什么*需要根据函数被调用处的调用现场来决定。
