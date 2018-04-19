# You Don't Know JS: *this* & Object Prototypes
# Chapter 2: `this` All Makes Sense Now!

在第一章，我们澄清了关于`this`的几个错误概念，并了解到`this`是在函数激活时根据它**调用现场**（函数是如何被调用的）生成的一个绑定。

## Call-site

为了理解`this`绑定，我们需要理解调用现场：代码里函数被调用的地方（**不是声明的地方**），我们需要审查调用现场来回答这个问题：这个`this`引用的是什么？

找到调用现场一般来讲就是“找到函数被调用的地方”，但实际中并没有这么简单，因为特定的编程模式会掩饰*真正*调用现场。

**调用堆栈**（记录函数是如何一步步进行到当前执行处的栈）是需要着重考虑的，我们关注的调用现场是指当前函数执行*前*的激活步骤。

调用堆栈和现场如下示例：

```js
function baz() {
    // call-stack is: `baz`
    // so, our call-site is in the global scope

    console.log( "baz" );
    bar(); // <-- call-site for `bar`
}

function bar() {
    // call-stack is: `baz` -> `bar`
    // so, our call-site is in `baz`

    console.log( "bar" );
    foo(); // <-- call-site for `foo`
}

function foo() {
    // call-stack is: `baz` -> `bar` -> `foo`
    // so, our call-site is in `bar`

    console.log( "foo" );
}

baz(); // <-- call-site for `baz`
```

在分析代码来（从调用堆栈里）寻找调用现场时需要小心，它是唯一决定`this`绑定的东西。

**注意：** 你可以通过函数调用顺序来想象调用堆栈，但这种方式容易出错，最好是利用浏览器内置的开发者工具（打断点或者在代码里添加`debugger`），在堆栈顶部第二个位置，即调用现场。

## Nothing But Rules

现在我们来看看调用现场时如何决定`this`指向哪里的。你需要审视调用现场，并决定以下四个规则中的哪个适用，我们会分别讲解每一个规则，然后说明当有多个规则适用时，哪个优先适用。

### Default Binding

这条规则就是最普通的函数独立调用，当其它规则都不适用时，默认使用此规则：

```js
function foo() {
	console.log( this.a );
}

var a = 2;

foo(); // 2
```

The first thing to note, if you were not already aware, is that variables declared in the global scope, as `var a = 2` is, are synonymous with global-object properties of the same name. They're not copies of each other, they *are* each other. Think of it as two sides of the same coin.

Secondly, we see that when `foo()` is called, `this.a` resolves to our global variable `a`. Why? Because in this case, the *default binding* for `this` applies to the function call, and so points `this` at the global object.

How do we know that the *default binding* rule applies here? We examine the call-site to see how `foo()` is called. In our snippet, `foo()` is called with a plain, un-decorated function reference. None of the other rules we will demonstrate will apply here, so the *default binding* applies instead.

If `strict mode` is in effect, the global object is not eligible for the *default binding*, so the `this` is instead set to `undefined`.

```js
function foo() {
	"use strict";

	console.log( this.a );
}

var a = 2;

foo(); // TypeError: `this` is `undefined`
```

一个微小却很重要的细节：尽管`this`绑定规则整体是基于调用现场的，但全局对象只有在`foo()`的内容**不是**运行在`strict mode`下时才适用于*默认绑定*规则，`strict mode`下`foo()`的调用现场是不相关的。

```js
function foo() {
	console.log( this.a );
}

var a = 2;

(function(){
	"use strict";

	foo(); // 2
})();
```

**注意：**不赞成在代码里混用`strict mode`和非`strict mode`，但有时你使用的第三方可能是不同的**严格**模式，所以要小心这些兼容性问题。

### Implicit Binding（隐式绑定）

另一个规则是：调用现场是否有一个上下文对象，它同时也是持有其它内容的对象，*这些*额外的关系会很误导人。

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2,
	foo: foo
};

obj.foo(); // 2
```

首先注意`foo()`是如何被声明以及作为一个属性引用添加到`obj`上的，无论`foo()`是从一开始就声明在`obj`上，还是后来作为引用增加上去的，**函数**都不是真实由`obj`持有或包含的。

然而，调用现场*使用*`obj`对象上下文来**引用**函数，所以你*可以*说`obj`对象在函数被调用时“持有“或”包含“**函数引用**。

无论如何称呼，当`foo()`被调用时，它在一个引用`obj`的对象之前。每当有上下文对象提供给函数引用时，*隐式绑定*规则宣称正是*这个*对象应该被用来作为函数调用时的`this`绑定。

由于`obj`是`foo()`调用时的`this`，所以`this.a`即`obj.a`。

调用现场只关心对象属性引用链的顶部和底部，比如：

```js
function foo() {
	console.log( this.a );
}

var obj2 = {
	a: 42,
	foo: foo
};

var obj1 = {
	a: 2,
	obj2: obj2
};

obj1.obj2.foo(); // 42
```

#### Implicitly Lost（隐式丢失）

一个很常见的由`this`绑定造成的误区就是当一个*implicitly bound*函数失去了绑定时，意味着会回退到*default binding*，即全局对象或`undefined`，取决于`strict mode`。

思考：

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2,
	foo: foo
};

var bar = obj.foo; // function reference/alias!

var a = "oops, global"; // `a` also property on global object

bar(); // "oops, global"
```

尽管`bar`好像引用`obj.foo`，事实上，它只是另一个指向`foo`的引用，而且，调用现场才是关键，在这里是`bar()`，也就是普通的无修饰的调用，因此属于*default binding*范畴。

更微妙、常见、又比较绕的方式就是当传递一个回调函数时：

```js
function foo() {
	console.log( this.a );
}

function doFoo(fn) {
	// `fn` is just another reference to `foo`

	fn(); // <-- call-site!
}

var obj = {
	a: 2,
	foo: foo
};

var a = "oops, global"; // `a` also property on global object

doFoo( obj.foo ); // "oops, global"
```

参数传递仅仅是隐式的赋值，因为我们传递的时一个函数，所以它是一个隐式的引用赋值，所以最终结果和前一个例子一样。

那么，如果传递的是内建的函数而不是你自定义的呢？一样的。

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2,
	foo: foo
};

var a = "oops, global"; // `a` also property on global object

setTimeout( obj.foo, 100 ); // "oops, global"
```

思考这段粗略的理论上的JS内建函数`setTimeout()`的伪实现：

```js
function setTimeout(fn,delay) {
	// wait (somehow) for `delay` milliseconds
	fn(); // <-- call-site!
}
```

It's quite common that our function callbacks *lose* their `this` binding, as we've just seen. But another way that `this` can surprise us is when the function we've passed our callback to intentionally changes the `this` for the call. Event handlers in popular JavaScript libraries are quite fond of forcing your callback to have a `this` which points to, for instance, the DOM element that triggered the event. While that may sometimes be useful, other times it can be downright infuriating. Unfortunately, these tools rarely let you choose.

Either way the `this` is changed unexpectedly, you are not really in control of how your callback function reference will be executed, so you have no way (yet) of controlling the call-site to give your intended binding. We'll see shortly a way of "fixing" that problem by *fixing* the `this`.

### Explicit Binding（显式绑定）

在*implicit binding*中我们看到，为了能够让函数可以自引用，我们需要修改对象，利用属性函数来间接（隐式）地把`this`绑定到对象上。

那么如果你不想在对象上新增一个属性方法，却要做到能让函数调用时能使用特定的对象绑定`this`呢？

“所有”函数都有工具（通过`[[Prototype]]`，后面会讲）来完成这个任务，比如函数有`call(..)`和`apply(..)`方法，技术上来讲，JS主机环境有时也会提供一些特别的函数，它们没有这种功能。但是很少见，大多数提供的函数，以及你自己创建的，都有`call(..)`和`apply(..)`方法。

这些工具如何作用？它们都将第一个入参用作`this`，然后使用这个`this`激活函数。因为此时你是直接指定了`this`，所以我们把这种绑定叫做*explicit binding*。

思考：

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```

通过`foo.call(..)`*显式绑定*来激活`foo`，这样就强制让`this`是那个`obj`。

如果你传入的是基础类型的值（比如`string`、`number`、`boolean`）作为`this`绑定，那么基础值会被封装位对应的对象形式（`new String(..)`、`new Number(..)`、`new Boolean(..)`）的值，即“装箱”。

**注意：**在`this`绑定上，`call(..)` and `apply(..)`是完全一样的，但是它们在其它的入参上却不一样，这些不是我们这里关注的。

然而，仅仅使用*显式绑定*无法解决前面提到的问题：如果一个函数“失去了”它原本的`this`绑定，或者被框架覆盖了等。

#### 硬绑定

但是，基于*显式绑定*的一个变体模式却可以解决这个问题：

```js
function foo() {
	console.log( this.a );
}

var obj = {
	a: 2
};

var bar = function() {
	foo.call( obj );
};

bar(); // 2
setTimeout( bar, 100 ); // 2

// `bar` hard binds `foo`'s `this` to `obj`
// so that it cannot be overriden
bar.call( window ); // 2
```

我们来看下这种变体是如何生效的：我们创建一个内部调用`foo.call(obj)`的函数`bar()`，它强制让`foo`调用绑定`obj`给`this`，无论你以何种方式激活函数`bar`，它始终会用`obj`来激活`foo`，这种绑定既明显有强迫，所以叫做*硬绑定*。

一个使用*硬绑定*封装的函数，会创建一个直接通道，它无视入参、返回：

```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

var obj = {
	a: 2
};

var bar = function() {
	return foo.apply( obj, arguments );
};

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

另一个这种模式的使用场景就是创建可复用的帮助函数：

```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

// simple `bind` helper
function bind(fn, obj) {
	return function() {
		return fn.apply( obj, arguments );
	};
}

var obj = {
	a: 2
};

var bar = bind( foo, obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

由于*硬绑定*太过常用，所以ES5将它内置在了函数里：`Function.prototype.bind`，使用方法如下：

```js
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

var obj = {
	a: 2
};

var bar = foo.bind( obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

`bind(..)`返回一个硬编码的函数，它强制使用你指定的`this`上下文来调用源函数。

**注意：**在ES6里，硬绑定函数`bind(..)`生成的函数还有一个`.name`属性，它继承自源*目标函数*，比如`bar = foo.bind(..)`会让`bar.name`等于`"bound foo"`，它是函数的调用名，会显示在调用堆栈里。

#### API调用“上下文”

很多库函数、JS的内建函数以及主机环境，都提供一个可选参数，通常叫做“上下文”，它保证你的回调函数使用特定的`this`，让你免去使用`bind(..)`来指定的麻烦。

比如：

```js
function foo(el) {
	console.log( el, this.id );
}

var obj = {
	id: "awesome"
};

// use `obj` as `this` for `foo(..)` calls
[1, 2, 3].forEach( foo, obj ); // 1 awesome  2 awesome  3 awesome
```

这些众多函数内部都使用了`call(..)`或`apply(..)`*显式绑定*，节省你的时间。

### `new`绑定

第四种（也是最后一种）`this`绑定规则需要我们重新思考一个关于JS中函数和对象的常见误解。

在传统面向类的语言里，“构造器”是类的特殊方法，当通过`new`操作符来实例化一个类时，构造器会被调用，比如：

```js
something = new MyClass(..);
```

JS也有`new`操作符，而且使用方法和上面的面向类的语言是一样的，大多数开发者以为JS也是在做同样的事情，然而，两者其实并没有*任何关系*。

首先，我们来重定义一下JS中的“构造器”，在JS里，构造器就**只是函数**，碰巧通过`new`操作符来调用罢了。他们不是附加在类上的，也不是用来实例化类的。他们甚至都不是特殊的函数，他们只是很普通的函数，本质上，在被激活时被`new`劫持了。

比如，`Number(..)`函数作为一个构造器，引用ES5.1 spec：

> 15.7.2 The Number Constructor
>
> When Number is called as part of a new expression it is a constructor: it initialises the newly created object.

所以，几乎所有的函数，包括如`Number(..)`（参考第3章）这样的内建函数都可以通过`new`来调用，这样使得函数调用成为*构造器调用*。这是很微妙又关键的区别：JS里并没有“构造器函数”这样的东西，只有函数的*构造调用*。

当一个函数被`new`激活时，（即构造器调用），如下的步骤自动完成：

1. 一个全新的对象被凭空创建（也称被构造）
2. *新构造的对象*是`[[Prototype]]`链接的*
3. 新构造的对象被当作函数调用的`this`绑定
4. 如果函数没有返回它自己的替代**对象**，那么`new`激活的函数调用会*自动*返回新构造的对象

1. a brand new object is created (aka, constructed) out of thin air
2. *the newly constructed object is `[[Prototype]]`-linked*
3. the newly constructed object is set as the `this` binding for that function call
4. unless the function returns its own alternate **object**, the `new`-invoked function call will *automatically* return the newly constructed object.

步骤1、3、4符合我们目前的描述，我们先跳过步骤2（第5章会详述）：

```js
function foo(a) {
	this.a = a;
}

var bar = new foo( 2 );
console.log( bar.a ); // 2
```

通过`new`调用`foo(..)`，我们已经构造了一个新对象，并把这个新对象作为调用`foo(..)`时的`this`，**以上，`new`是一个函数调用的`this`绑定的最后一种方式**，我们称其为*new绑定*。

## 规则优先级

至此，我们讲到了函数调用时的4种`this`绑定规则，*所有*你需要做的就是找到调用现场，然后审视它，看哪种规则适用。但是，如果调用现场有多个规则都适用呢？肯定需要有优先级，我们接下来说明按照什么顺序来使用规则：

首先*默认绑定*是最低优先级的规则，所以我们先把它放一边。

*隐式绑定*和*显示绑定*哪个优先级更高？我们看代码先：

```js
function foo() {
	console.log( this.a );
}

var obj1 = {
	a: 2,
	foo: foo
};

var obj2 = {
	a: 3,
	foo: foo
};

obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call( obj2 ); // 3
obj2.foo.call( obj1 ); // 2
```

所以，*显式绑定*优先级高于*隐式绑定*，当你在检查是否符合*隐式绑定*规则前，应该先检查是否*显式绑定*。

现在，我们来看看*new绑定*的优先级：

```js
function foo(something) {
	this.a = something;
}

var obj1 = {
	foo: foo
};

var obj2 = {};

obj1.foo( 2 );
console.log( obj1.a ); // 2

obj1.foo.call( obj2, 3 );
console.log( obj2.a ); // 3

var bar = new obj1.foo( 4 );
console.log( obj1.a ); // 2
console.log( bar.a ); // 4
```

好的，*new绑定*优先级高于*隐式绑定*，那么和*显式绑定比呢？

**注意：**`new`和`call/apply`不能同时使用，所以`new foo.call(obj1)`是禁止的，为了测*new绑定*和*显式绑定*，我们可以使用*硬绑定*来代替*显式绑定*。

在我们使用代码探索这个问题前，回想一下*硬绑定*的工作原理，`Function.prototype.bind(..)`创建一个封装函数，它被硬编码为忽略自己的`this`绑定（无论是什么），而使用一个我们手动提供的。

通过上面分析，明显可以假定*硬绑定*优先级高于*new绑定*，从而不会被`new`重写。

我们来测试一下：

```js
function foo(something) {
	this.a = something;
}

var obj1 = {};

var bar = foo.bind( obj1 );
bar( 2 );
console.log( obj1.a ); // 2

var baz = new bar( 3 );
console.log( obj1.a ); // 2
console.log( baz.a ); // 3
```

哦！`bar`硬绑定了`obj1`，但是`new bar(3)`却**没有**如我们所预期的将`obj1.a`的值修改为`3`，相反，对`bar(..)`调用*硬绑定*（给`obj1`）***可以***被`new`重写，因为`new`生效了，所以得到了新创建的对象，名为`baz`，而且`baz.a`的值是`3`。

如果你回到我们的“伪”绑定帮助函数，你会很吃惊：

```js
function bind(fn, obj) {
	return function() {
		fn.apply( obj, arguments );
	};
}
```

分析了帮助函数的工作原理后，你发现它没有一种让`new`操作符可以重写硬绑定到`obj1`的方法。

但是，ES5内建的`Function.prototype.bind(..)`更复杂，下面是MDN上的`bind(..)`的polyfill：

```js
if (!Function.prototype.bind) {
	Function.prototype.bind = function(oThis) {
		if (typeof this !== "function") {
			// closest thing possible to the ECMAScript 5
			// internal IsCallable function
			throw new TypeError( "Function.prototype.bind - what " +
				"is trying to be bound is not callable"
			);
		}

		var aArgs = Array.prototype.slice.call( arguments, 1 ),
			fToBind = this,
			fNOP = function(){},
			fBound = function(){
				return fToBind.apply(
					(
						this instanceof fNOP &&
						oThis ? this : oThis
					),
					aArgs.concat( Array.prototype.slice.call( arguments ) )
				);
			}
		;

		fNOP.prototype = this.prototype;
		fBound.prototype = new fNOP();

		return fBound;
	};
}
```

**注意：** 上面展示的`bind(..)`polyfill和ES5内建的`bind(..)`在硬绑定的函数被用于`new`构造时有所不同，因为polyfill无法创建一个没有`.prototype`的函数，而内建工具可以，在行为表现上两者几乎相同，如果你打算使用`new`在一个依赖polyfill的硬绑定函数上，你需要小心一些。

允许`new`复写的代码如下：

```js
this instanceof fNOP &&
oThis ? this : oThis

// ... and:

fNOP.prototype = this.prototype;
fBound.prototype = new fNOP();
```

这里我们不深入展开这种技巧的工作原理（很复杂，已经超出了我们这里的范围），但本质上使用方式决定了硬绑定函数是否可以被`new`调用（生成一个新构造的对象作为它的`this`），如果是，它会使用*那个*新生成的`this`而不是前面*硬绑定*的`this`。

为什么`new`复写*硬绑定*很有用？

首要原因是这样可以创建一个本质上忽略*硬绑定*的`this`却预先给一些（或全部）函数入参赋了值的函数，
The primary reason for this behavior is to create a function (that can be used with `new` for constructing objects) that essentially ignores the `this` *hard binding* but which presets some or all of the function's arguments. One of the capabilities of `bind(..)` is that any arguments passed after the first `this` binding argument are defaulted as standard arguments to the underlying function (technically called "partial application", which is a subset of "currying").

For example:

```js
function foo(p1,p2) {
	this.val = p1 + p2;
}

// using `null` here because we don't care about
// the `this` hard-binding in this scenario, and
// it will be overridden by the `new` call anyway!
var bar = foo.bind( null, "p1" );

var baz = new bar( "p2" );

baz.val; // p1p2
```

### Determining `this`

现在，我们来总结一下从函数调用现场决定`this`的规则，顺着优先级由高到低，当有规则适用时即停止。

1. 函数是否由`new`调用？如果是，那么`this`就是新构造的对象。

`var bar = new foo()`

2. 函数是否由`call`或`apply`调用，或者由`bind`调用？如果是，`this`则是显式指定的对象。

`var bar = foo.call( obj2 )`

3. 函数是否通过上下文调用，或者函数本身就是对象持有的属性？如果是，那么`this`就是这个上下文对象。

`var bar = obj1.foo()`

4. 否则，`this`是默认绑定，在严格模式下取`undefined`，其它取`global`对象。

就酱紫，这就是关于普通函数的`this`绑定的规则理解的全部了，哦……几乎全部。

## Binding Exceptions

As usual, there are some *exceptions* to the "rules".

The `this`-binding behavior can in some scenarios be surprising, where you intended a different binding but you end up with binding behavior from the *default binding* rule (see previous).

### Ignored `this`

If you pass `null` or `undefined` as a `this` binding parameter to `call`, `apply`, or `bind`, those values are effectively ignored, and instead the *default binding* rule applies to the invocation.

```js
function foo() {
	console.log( this.a );
}

var a = 2;

foo.call( null ); // 2
```

Why would you intentionally pass something like `null` for a `this` binding?

It's quite common to use `apply(..)` for spreading out arrays of values as parameters to a function call. Similarly, `bind(..)` can curry parameters (pre-set values), which can be very helpful.

```js
function foo(a,b) {
	console.log( "a:" + a + ", b:" + b );
}

// spreading out array as parameters
foo.apply( null, [2, 3] ); // a:2, b:3

// currying with `bind(..)`
var bar = foo.bind( null, 2 );
bar( 3 ); // a:2, b:3
```

Both these utilities require a `this` binding for the first parameter. If the functions in question don't care about `this`, you need a placeholder value, and `null` might seem like a reasonable choice as shown in this snippet.

**Note:** We don't cover it in this book, but ES6 has the `...` spread operator which will let you syntactically "spread out" an array as parameters without needing `apply(..)`, such as `foo(...[1,2])`, which amounts to `foo(1,2)` -- syntactically avoiding a `this` binding if it's unnecessary. Unfortunately, there's no ES6 syntactic substitute for currying, so the `this` parameter of the `bind(..)` call still needs attention.

However, there's a slight hidden "danger" in always using `null` when you don't care about the `this` binding. If you ever use that against a function call (for instance, a third-party library function that you don't control), and that function *does* make a `this` reference, the *default binding* rule means it might inadvertently reference (or worse, mutate!) the `global` object (`window` in the browser).

Obviously, such a pitfall can lead to a variety of *very difficult* to diagnose/track-down bugs.

#### Safer `this`

Perhaps a somewhat "safer" practice is to pass a specifically set up object for `this` which is guaranteed not to be an object that can create problematic side effects in your program. Borrowing terminology from networking (and the military), we can create a "DMZ" (de-militarized zone) object -- nothing more special than a completely empty, non-delegated (see Chapters 5 and 6) object.

If we always pass a DMZ object for ignored `this` bindings we don't think we need to care about, we're sure any hidden/unexpected usage of `this` will be restricted to the empty object, which insulates our program's `global` object from side-effects.

Since this object is totally empty, I personally like to give it the variable name `ø` (the lowercase mathematical symbol for the empty set). On many keyboards (like US-layout on Mac), this symbol is easily typed with `⌥`+`o` (option+`o`). Some systems also let you set up hotkeys for specific symbols. If you don't like the `ø` symbol, or your keyboard doesn't make that as easy to type, you can of course call it whatever you want.

Whatever you call it, the easiest way to set it up as **totally empty** is `Object.create(null)` (see Chapter 5). `Object.create(null)` is similar to `{ }`, but without the delegation to `Object.prototype`, so it's "more empty" than just `{ }`.

```js
function foo(a,b) {
	console.log( "a:" + a + ", b:" + b );
}

// our DMZ empty object
var ø = Object.create( null );

// spreading out array as parameters
foo.apply( ø, [2, 3] ); // a:2, b:3

// currying with `bind(..)`
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2, b:3
```

Not only functionally "safer", there's a sort of stylistic benefit to `ø`, in that it semantically conveys "I want the `this` to be empty" a little more clearly than `null` might. But again, name your DMZ object whatever you prefer.

### Indirection

Another thing to be aware of is you can (intentionally or not!) create "indirect references" to functions, and in those cases,  when that function reference is invoked, the *default binding* rule also applies.

One of the most common ways that *indirect references* occur is from an assignment:

```js
function foo() {
	console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); // 2
```

The *result value* of the assignment expression `p.foo = o.foo` is a reference to just the underlying function object. As such, the effective call-site is just `foo()`, not `p.foo()` or `o.foo()` as you might expect. Per the rules above, the *default binding* rule applies.

Reminder: regardless of how you get to a function invocation using the *default binding* rule, the `strict mode` status of the **contents** of the invoked function making the `this` reference -- not the function call-site -- determines the *default binding* value: either the `global` object if in non-`strict mode` or `undefined` if in `strict mode`.

### Softening Binding

We saw earlier that *hard binding* was one strategy for preventing a function call falling back to the *default binding* rule inadvertently, by forcing it to be bound to a specific `this` (unless you use `new` to override it!). The problem is, *hard-binding* greatly reduces the flexibility of a function, preventing manual `this` override with either the *implicit binding* or even subsequent *explicit binding* attempts.

It would be nice if there was a way to provide a different default for *default binding* (not `global` or `undefined`), while still leaving the function able to be manually `this` bound via *implicit binding* or *explicit binding* techniques.

We can construct a so-called *soft binding* utility which emulates our desired behavior.

```js
if (!Function.prototype.softBind) {
	Function.prototype.softBind = function(obj) {
		var fn = this,
			curried = [].slice.call( arguments, 1 ),
			bound = function bound() {
				return fn.apply(
					(!this ||
						(typeof window !== "undefined" &&
							this === window) ||
						(typeof global !== "undefined" &&
							this === global)
					) ? obj : this,
					curried.concat.apply( curried, arguments )
				);
			};
		bound.prototype = Object.create( fn.prototype );
		return bound;
	};
}
```

The `softBind(..)` utility provided here works similarly to the built-in ES5 `bind(..)` utility, except with our *soft binding* behavior. It wraps the specified function in logic that checks the `this` at call-time and if it's `global` or `undefined`, uses a pre-specified alternate *default* (`obj`). Otherwise the `this` is left untouched. It also provides optional currying (see the `bind(..)` discussion earlier).

Let's demonstrate its usage:

```js
function foo() {
   console.log("name: " + this.name);
}

var obj = { name: "obj" },
    obj2 = { name: "obj2" },
    obj3 = { name: "obj3" };

var fooOBJ = foo.softBind( obj );

fooOBJ(); // name: obj

obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2   <---- look!!!

fooOBJ.call( obj3 ); // name: obj3   <---- look!

setTimeout( obj2.foo, 10 ); // name: obj   <---- falls back to soft-binding
```

The soft-bound version of the `foo()` function can be manually `this`-bound to `obj2` or `obj3` as shown, but it falls back to `obj` if the *default binding* would otherwise apply.

## Lexical `this`

Normal functions abide by the 4 rules we just covered. But ES6 introduces a special kind of function that does not use these rules: arrow-function.

Arrow-functions are signified not by the `function` keyword, but by the `=>` so called "fat arrow" operator. Instead of using the four standard `this` rules, arrow-functions adopt the `this` binding from the enclosing (function or global) scope.

Let's illustrate arrow-function lexical scope:

```js
function foo() {
	// return an arrow function
	return (a) => {
		// `this` here is lexically adopted from `foo()`
		console.log( this.a );
	};
}

var obj1 = {
	a: 2
};

var obj2 = {
	a: 3
};

var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, not 3!
```

The arrow-function created in `foo()` lexically captures whatever `foo()`s `this` is at its call-time. Since `foo()` was `this`-bound to `obj1`, `bar` (a reference to the returned arrow-function) will also be `this`-bound to `obj1`. The lexical binding of an arrow-function cannot be overridden (even with `new`!).

The most common use-case will likely be in the use of callbacks, such as event handlers or timers:

```js
function foo() {
	setTimeout(() => {
		// `this` here is lexically adopted from `foo()`
		console.log( this.a );
	},100);
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```

While arrow-functions provide an alternative to using `bind(..)` on a function to ensure its `this`, which can seem attractive, it's important to note that they essentially are disabling the traditional `this` mechanism in favor of more widely-understood lexical scoping. Pre-ES6, we already have a fairly common pattern for doing so, which is basically almost indistinguishable from the spirit of ES6 arrow-functions:

```js
function foo() {
	var self = this; // lexical capture of `this`
	setTimeout( function(){
		console.log( self.a );
	}, 100 );
}

var obj = {
	a: 2
};

foo.call( obj ); // 2
```

While `self = this` and arrow-functions both seem like good "solutions" to not wanting to use `bind(..)`, they are essentially fleeing from `this` instead of understanding and embracing it.

If you find yourself writing `this`-style code, but most or all the time, you defeat the `this` mechanism with lexical `self = this` or arrow-function "tricks", perhaps you should either:

1. Use only lexical scope and forget the false pretense of `this`-style code.

2. Embrace `this`-style mechanisms completely, including using `bind(..)` where necessary, and try to avoid `self = this` and arrow-function "lexical this" tricks.

A program can effectively use both styles of code (lexical and `this`), but inside of the same function, and indeed for the same sorts of look-ups, mixing the two mechanisms is usually asking for harder-to-maintain code, and probably working too hard to be clever.

## Review (TL;DR)

从函数调用现场决定`this`的规则，顺着优先级由高到低，当有规则适用时即停止。

1. 由`new`调用，`this`就是新构造的对象。
2. 由`call`或`apply`调用，或者由`bind`调用，`this`则是显式指定的对象。
3. 通过上下文调用，或者函数本身就是对象持有的属性，那么`this`就是这个上下文对象。
4. 否则，`this`是默认绑定，在严格模式下取`undefined`，其它取`global`对象。

Be careful of accidental/unintentional invoking of the *default binding* rule. In cases where you want to "safely" ignore a `this` binding, a "DMZ" object like `ø = Object.create(null)` is a good placeholder value that protects the `global` object from unintended side-effects.

Instead of the four standard binding rules, ES6 arrow-functions use lexical scoping for `this` binding, which means they adopt the `this` binding (whatever it is) from its enclosing function call. They are essentially a syntactic replacement of `self = this` in pre-ES6 coding.
