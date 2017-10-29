# You Don't Know JS: Types & Grammar
# Chapter 1: Types

很多开发者认为向JS这样的动态语言不存在*类型*，我们先看下ES5.1规格（http://www.ecma-international.org/ecma-262/5.1/）里怎么说：

> 这个规格里的算法操作的都是关联了类型的值。语句中定义的类型正是值的类型，类型后来被归入ECMAScript语言类型和规格类型的亚类型（好绕— —||）。
>
> 一个ECMAScript语言类型对应由程序员使用该语言操作的值，ECMAScript语言类型有：未定义，空，布尔，字符串，数字和对象。

如果你是一个强语言类型的爱好者，你可能会反对这样使用“类型”，在强类型语言中，“类型”比在JS中要求严格的多。

一些人觉得JS不应该声称有“类型”，而应该被称为“标记”或者“亚类型”。呸！我们就是要用这种粗略定义：*类型*是固有的内在的字符集，它们能够唯一代表值的行为，并使该值对引擎**和开发者**而言区别于其它值。

换句话说，如果引擎和开发者认为数字`42`不同于字符串`"42"`，那么这两个值就具有不通的“类型”——`数字`和`字符串`。当你使用`42`时，你*意图*进行一些数字操作，就像做数学一样，而你使用`"42"`时，你*意图*进行一些字符串操作，就像输出页面等，**这两种值就具有了不同的类型。**

这当然不是个完美的定义，但足够说明前面的讨论，而且于JS自己的定义也一致。

# A Type By Any Other Name...

Beyond academic definition disagreements, why does it matter if JavaScript has *types* or not?
先不管学术上定义的争论，为什么JS是否有*类型*很重要？

对*类型*及其底层行为的恰当理解，对正确转换值到不通类型（参考 第4章 Coercion）很关键。几乎每一个JS程序都会遇到需要处理值的强转，所以你在进行这些操作时的自信和对其负责就很重要。

如果你有一个`数字``42`，但是你像把它当作`字符串`处理，比如取出位置`1`处的字符`"2"`，显然你需要先对它进行`数字`到`字符串`的强转。

看起来貌似很简单。

但是强转可能以不同的方式发生，有些很明显，容易分析，且可信，但是如果你不够仔细，强转的结果可能会很让你意外。强转困惑可能是JS开发者中影响最深刻的挫折之一，它经常被批评为如此*危险*甚至被认为是该语言的设计缺陷，应该被避免使用。

通过理解JS类型来武装自己，我们的目的是澄清强转的*坏名声*其实是被夸大的不实际的，为了颠覆你的观点，为了看到强转的强大益处。但是首先，我们得把握值和类型的关键。
## Built-in Types

JS定义了7中内建类型：

* `null`
* `undefined`
* `boolean`
* `number`
* `string`
* `object`
* `symbol` -- ES6新增！

**注意：** 出了`object`其它类型都被叫做“原语”。

`typeof`操作符能够识别给定的值的类型，并返回7种类型之一的字符串，然而它们并不是和上面的类型一一对应的：

```js
typeof undefined     === "undefined"; // true
typeof true          === "boolean";   // true
typeof 42            === "number";    // true
typeof "42"          === "string";    // true
typeof { life: 42 }  === "object";    // true

// added in ES6!
typeof Symbol()      === "symbol";    // true
```

如你所见，`null`没有被列出来，它比较特殊，尤其是当它和`typeof`一起使用时，近乎一个bug：

```js
typeof null === "object"; // true
```
如果它能返回`null`的话那就完美了，然而这种历史遗留bug已经存在太久，目前网上已经存在太多依赖于这种bug的应用，修复该bug只会引入更多的问题。

如果你想检测`null`值，可以添加这样的组合条件：

```js
var a = null;

(!a && typeof a === "object"); // true
```

`null`是唯一`typeof`返回`"object"`但是在布尔域内是`false`的值。

那么第7种`typeof`返回的值是什么呢？

```js
typeof function a(){ /* .. */ } === "function"; // true
```

你可能会觉得`function`应该是JS中的基本内建类型，尤其是考虑到`typeof`返回的值，然而，如果你读过规格书，你就知道它只是`object`的亚类型。更具体的讲，函数是一个可以被调用的对象——一个有可以被激活的内部属性`[Call]`的对象。

函数是对象这个事实很重要，很多时候，它们可以拥有属性，比如：

```js
function a(b,c) {
	/* .. */
}
```

上面的函数有一个`length`属性，赋值为它声明的参数个数。

```js
a.length; // 2
```

那么数组呢？它们是JS原生的，它们是否有特定的类型？

```js
typeof [1,2,3] === "object"; // true
```

答案是否，它们也是对象。最好把它们也当作是对象的“亚类型”，使用数字索引（与普通的通过字符串为key的对象不同）的并且自动更新`length`属性的对象。

## Values as Types

在JS中，变量没有类型——**值有类型**，变量可以在任何时候持有值。

另一种理解JS类型的方式是JS没有“强制类型”，引擎不会要求*变量*一直持有*与最初类型相同*的值。一个变量可以在某个语句里持有`string`值，也能在下个语句中持有`nunber`值，等等。

*值*`42`有一个本质`number`类型，该类型不会改变，另一个`string`类型的值`"42"`，可以*由*`number`值`42`**强转**（见第4章）创建。

如果对变量使用`typeof`，它不是在在询问“这个变量的类型是什么？”，而是询问“这个变量*中*的值是什么类型？“。

```js
var a = 42;
typeof a; // "number"

a = true;
typeof a; // "boolean"
```

操作符`typeof`总是返回一个字符串，所以：

```js
typeof typeof 42; // "string"
```

第一个`typeof 42`返回`"number"`，然后`typeof "number"`返回`"string"`。

### `undefined` vs "undeclared"

那些还没有赋值的变量，实际上默认有`undefined`值，所以使用`typeof`时会返回`"undefined"`：

```js
var a;

typeof a; // "undefined"

var b = 42;
var c;

// later
b = c;

typeof b; // "undefined"
typeof c; // "undefined"
```

开发者很容易弄混“未定义”和它的同义词“未声明”，在JS中，这两个概念完全不同，一个“未定义”的变量是指在可见域内已经声明的变量，但是*当前*还没有赋值，而“未声明”的变量是指在可见域内还没有被正式声明的变量。

```js
var a;

a; // undefined
b; // ReferenceError: b is not defined
```

令人讨厌的就是浏览器在这种场景下的提示，你很容易以为“not define”意思就是“undefined”，然而并不是，这里其实把提示换为“not found“或者”not declared“会更清楚一些。

还有一个`typeof`与未声明的特殊行为需要说明一下，对一个未声明的变量使用`typeof`会返回`undefiend`，**不会报错**，像安全卫士。

```js
var a;

typeof a; // "undefined"

typeof b; // "undefined"
```

### `typeof` Undeclared

虽然如此，当在浏览器中处理JS时，多个脚本文件会加载变量到共享的全局命名空间里，这时安全卫士就是一个很有用的特性了。

**注意：** 许多开发者认为不应该在全局命名空间有任何变量存在，所有的东西都应该被包含在模块和私有/分离的命名空间中。理论上很对，但是在实践中很难，但它仍然是一个前进的目标，幸运的是，ES6已经很好的支持模块化，这将会使得理论更接近实现。

举个简单的栗子，假设你的程序中有一个全局变量`DEBUG`控制”调试模式“，你需要在执行一个调试任务（比如打印一个信息到控制台）前检查该变量是否被声明，一个最高层全局声明`var DEBUG = tue`包含在"debug.js"文件中，只有在开发／测试环境中才会被加载。此时，你需要在其它代码中小心的检查全局变量`DEBUG`，因为你需要保证不能抛出`ReferenceError`这样的错误，此时，安全卫士`typeof`就很重要了：

```js
// oops, this would throw an error!
if (DEBUG) {
	console.log( "Debugging is starting" );
}

// this is a safe existence check
if (typeof DEBUG !== "undefined") {
	console.log( "Debugging is starting" );
}
```

当你不是在处理用户定义的变量（比如`DEBUG`）时，这种检查很有用，如果你是要进行内建API的特性检查，你也会发现这种保证不抛出错误的方式很有用：

```js
if (typeof atob === "undefined") {
	atob = function() { /*..*/ };
}
```

**注意：** 如果你是在给某个不存在的特性做"polyfill"，你可能需要避免使用`var`来声明`atob`，如果你在`if`语句中声明`var atob`，这种声明会被提升到作用域的顶部（参考系列*Scope & Closures*），即使`if`语句条件不成立不被执行，（`atob`已经存在）。在一些浏览器和一些全局内建变量的特定类型（被称为“host objects“），这种重复声明会抛出错误，所以省略`var`可以避免这种声明提升。

另一种进行这种全局变量检查但是不使用安全卫士`typeof`的场景是，全局变量同时也是全局对象的属性，在浏览器中就是`window`，所以，上面的检查也可以这样写：

```js
if (window.DEBUG) {
	// ..
}

if (!window.atob) {
	// ..
}
```

与引用未声明变量不同，如果你尝试访问某个对象的属性不存在（即使是全局对象`window`），它不会抛出`ReferenceError`。

另一方面，通过`window`手动引用全局变量有时需要开发者避免使用，尤其当你的代码需要在不同的JS环境（不仅仅是浏览器，可能在服务器端比如node.js），此时，全局变量就不一定叫做`window`了。

技术上来讲，安全卫士`typeof`在非全局变量中也很实用，尽管这种场景并不常见，而且有些开发者会发现这种设计模式并不受欢迎。假设你有一个想让大家复制-粘贴的工具函数，此时你需要检查是否该程序中已经定义了该变量：

```js
function doSomethingCool() {
	var helper =
		(typeof FeatureXYZ !== "undefined") ?
		FeatureXYZ :
		function() { /*.. default feature ..*/ };

	var val = helper();
	// ..
}
```

`doSomethingCool()`检查`FeatureXYZ`，如果存在，则使用它，如果不存在则使用自定义的。如果有人在他的模块或者程序中使用这个工具，它会安全的检查是否已经定义了一个`FeatureXYZ`：

```js
// an IIFE (see "Immediately Invoked Function Expressions"
// discussion in the *Scope & Closures* title of this series)
(function(){
	function FeatureXYZ() { /*.. my XYZ feature ..*/ }

	// include `doSomethingCool(..)`
	function doSomethingCool() {
		var helper =
			(typeof FeatureXYZ !== "undefined") ?
			FeatureXYZ :
			function() { /*.. default feature ..*/ };

		var val = helper();
		// ..
	}

	doSomethingCool();
})();
```

这里，`FeatureXYZ`不是一个全局变量，但是我们仍然使用了安全卫士`typeof`来检查，重要的是，这里没有类似于`window`这样的对象供我们使用，所以`typeof`很有用。

有些开发者更喜欢一种“依赖注入”的设计模式，不是通过在`doSomethingCool()`里隐式检查`FeatureXYZ`是否已经被定义，而是通过参数传入的方式：

```js
function doSomethingCool(FeatureXYZ) {
	var helper = FeatureXYZ ||
		function() { /*.. default feature ..*/ };

	var val = helper();
	// ..
}
```

设计这种功能有很多种方式，无所谓对错——每一种方法都有自己的权衡，但是欣慰的是检查未声明的安全卫士`typeof`给了我们更多选择。

## Review

JS有7个内建*类型*：`null`, `undefined`,  `boolean`, `number`, `string`, `object`, `symbol`，可以通过`typeof`来识别。

变量没有类型，但是变量中的值有，这些类型决定值的行为。

许多开发者会认为“undefined”和“undeclared”是同样的东西，但是在JS中，它们完全不同，`undefined`是一个可以赋给变量的值，而“undeclared”是指一个变量还未被声明（不存在）。

不幸的是JS有点儿合并了这两种东西，不仅仅是浏览器里的错误提示（引用未声明变量抛出错误："ReferenceError: a is not defined"），而且`typeof`返回的都是`undefined`。

然而，在特定场景下安全卫士（防止抛出错误）`typeof`被用于检查未声明变量时很有用。
