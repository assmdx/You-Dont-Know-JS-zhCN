# You Don't Know JS: Up & Going
# Chapter 2: Into JavaScript

前一章中我介绍了编程的基本概念，比如变量、循环、条件和函数，所有代码都以JS编写。本章聚焦于成为一个真正JS开发人员所需要知道的东西。

这一章中会介绍很多概念，我们会在*YDKJS*大法系列丛书中进行全面探究。尤其是当你初识JS，你应该能预料到自己将会花费一定的时间来复习概念和代码示例。所有的好基础都是一砖一瓦堆砌而来，所以不要想着自己一开始就能全面掌握所有的东西。

深入学习JS之路从这里开始。

**注意:** 正如我在第一章中所说，在你阅读这一章的过程中你应该自己尝试遇到的每一段代码，一些代码使用的是本书编写时的最新版JS语法（一般称为“ES6”代表ECMAScript第6版），如果你恰好使用的是ES6以前版本的浏览器，代码可能无法正常工作，最好使用最新版的浏览器（如Chrome、火狐、或者IE）。

## Values & Types（值和类型）

在第一章已经说过，JS得值有类型之分，变量没有类型的区分，以下是JS内置的类型：
* `string`
* `number`
* `boolean`
* `null` 和 `undefined`
* `object`
* `symbol` (ES6新引入)

JS提供一个`typeof`操作符，可以用来检验某个值的类型：

```js
var a;
typeof a;				// "undefined"

a = "hello world";
typeof a;				// "string"

a = 42;
typeof a;				// "number"

a = true;
typeof a;				// "boolean"

a = null;
typeof a;				// "object" -- weird, bug

a = undefined;
typeof a;				// "undefined"

a = { b: "c" };
typeof a;				// "object"
```

`typeof`操作符总是返回6种（ES6是7种）类型中的1种，并且是字符串，比如`typeof "abs"`返回`"string"`而不是`string`。

注意观察这段代码中变量`a`是如何持有不同类型的值得，不是如语法表面那样，`typeof a`不是询问`a`的类型，而是询问`a`当前值得类型。再强调一次：JS中值才有类型，变量只是存储这些值的容器。

`typeof null`比较有意思，因为它错误的返回了`"object"`，实际上我们期望的是返回`"null"`。

**警告:** 这是JS长期存在的很可能永远不会被修复的bug，因为目前网络上已经有很多代码依赖于这个bug，如果修复它，只会引入更多的bug。（好坑— —||）

还需注意的是，`a = undefined`，我们显式的将`undefined`赋值给`a`，但是这个和声明了某个变量没有赋初始值是一样的效果。一般一个变量可以由多种方式被赋`undefined`，包括无返回值的函数以及使用`void`操作符。

### Objects（对象）

`object`类型表示一个复合值，你可以给它设置属性，每一个属性的值可以是任何类型，这可能是JS中最有用的类型了。

```js
var obj = {
	a: "hello world",
	b: 42,
	c: true
};

obj.a;		// "hello world"
obj.b;		// 42
obj.c;		// true

obj["a"];	// "hello world"
obj["b"];	// 42
obj["c"];	// true
```

视觉感受一下这个`obj`可能会对你有帮助。

<img src="fig4.png">

可以使用*点记号*（即`obj.a`）或*括号记法*（即`obj["a"]`）来访问属性，点记号便捷，所以尽可能优先使用。当属性名有特殊字符时（如`obj["hello world!"]`），括号记法就体现它的价值了，当通过括号来访问属性时，属性经常被称为*键*，`[ ]`记法需要一个变量或者一个需要用`".."或`'..'包裹起来的`string`*文字*。

当一个属性（键）的名字存储在某个变量中时，访问就要通过括号来实现：

```js
var obj = {
	a: "hello world",
	b: 42
};

var b = "a";

obj[b];			// "hello world"
obj["b"];		// 42
```

**注意:** 更多关于JS`object`的知识，参见卷 *this & Object Prototypes*，尤其是第三章。



在JS程序中，你经常会见到如 *数组* 和 *函数* 这些类型，但是它们不是内置类型，而是子类型（`object`类型的特定版本）。

#### Arrays（数组）

数组就是对象，只是它的属性/键是有序的数字，每个序号对应的位置可以存储任意类型的值。

```js
var arr = [
	"hello world",
	42,
	true
];

arr[0];			// "hello world"
arr[1];			// 42
arr[2];			// true
arr.length;		// 3

typeof arr;		// "object"
```

**注意:** 以0为起始的语言，比如JS，0代表第一个元素。

可视化的`arr`:

<img src="fig5.png">

由于数组是特殊的对象（如`typeof`表明），所以它也可以有属性，包括自动更新的`lenght`属性，理论上你可以将数组作为一个拥有自定义名字属性的普通对象，但是，这个可能有些浪费`array`的功能。

正常情况下，数组被用来存储有序的值，而对象则存储被命名的属性。

#### Functions（函数）

另一个你在JS编程中总会用到`object`子类型就是函数：
```js
function foo() {
	return 42;
}

foo.bar = "hello world";

typeof foo;			// "function"
typeof foo();		// "number"
typeof foo.bar;		// "string"
```

与数组中提到的一样，函数因为是特殊的对象，所以也可以有属性，但应该更多关注函数的功能。

**注意:** 了解更多关于值和类型的知识，参见卷 *Types & Grammar* 的前两章。

### Built-In Type Methods（内置类型方法）

我们提到的内置的类型和子类型的以属性和方法表现出来的行为非常给力，比如：

```js
var a = "hello world";
var b = 3.14159;

a.length;				// 11
a.toUpperCase();		// "HELLO WORLD"
b.toFixed(4);			// "3.1416"
```

调用`a.toUpperCase()`后面的原理比你看到的表面的意思要复杂的多，简言之，有一种`String`（大写`S`）对象包装器形式，也叫“native”，它可以匹配原始的`string`类型，这个对象装箱在它的原型中定义`toUpperCase()`方法。

当你引用原始值比如`"hello world"`的属性或方法，你是把它当做了一个`object`，JS自动将值“打包”到它对应的对象包装器（不可见）。

`string`类型的值可以被`String`对象包装，`number`可以被`Number`对象包装，`boolean`被`Boolean`，多数情况下，你无需在意或直接使用这些值的包装形式（在实际中尽量使用原始值，JS会帮你完成剩下的工作）。

**注意:** 更多关于JS native和“包装”，参见 *Types & Grammar* 的第三章，为更好的理解原型和对象，参见卷 *this & Object Prototypes* 的第五章。

### Comparing Values（值的比较）

在JS编程中主要有两类值的比较：*相等* 和 *不等*，任何比较结果都有只能是 `boolean` 类型（ `true` 或 `false` ）。

#### Coercion（强制类型转换）

在第一章已经提到强制类型转换，我们再来说一下。

JS中的强转有两种形式：*显式* 和 *隐式*，显式是你从代码就可以明显看到转换的发生，而隐式则是一些操作的不那么明显的结果。

你可能听说过“强制类型转换很坑爹”这样的说法，因为在一些情况下，强转的确会产生很意外的结果，或许最能让开发人员感觉到沮丧的时候就是这个编程语言总是出现“惊喜”。

强转并不坑爹，也没什么好惊讶的，事实上，大多数你使用强转的场景都是很容易理解的，你甚至可以用它来提高代码的可读性，但这里我们不深入讨论，会放在卷 *Types & Grammar* 的第四章。

显式强转的一个小例子：

```js
var a = "42";

var b = Number( a );

a;				// "42"
b;				// 42 -- the number!
```

这个是 *隐式* 强转的例子：:

```js
var a = "42";

var b = a * 1;	// "42" implicitly coerced to 42 here

a;				// "42"
b;				// 42 -- the number!
```

#### Truthy & Falsy（真假）

在第一章我们简单提到了值的真和假：当一个非`boolean`类型的值被强转为`boolean` 时，它是成为`true` 呢还是`false` 呢？

JS中特殊的假值如下:

* `""` (空字符串)
* `0`, `-0`, `NaN` (无效 `number`)
* `null`, `undefined`
* `false`

非假，即真值：

* `"hello"`
* `42`
* `true`
* `[ ]`, `[ 1, "2", 3 ]` (数组)
* `{ }`, `{ a: 42 }` (对象)
* `function foo() { .. }` (函数)

记住上面这个非 `boolean` 类型的值的强转规则很重要，这样当你遇到一个看似在将值强转为`boolean`类型实际并不是的情况时，你就不会再那般困惑。

#### Equality（相等）

T有四种判断相等的操作符：`==`, `===`, `!=`, 以及 `!==`。 加`!`的形式是指不相等。

通常认为`==`和`===`的区别是，前者只比较值的相等性，而后者还要再比较类型是否相同，但这种说法并不精确。`==`是在允许强制类型转换时比较值的相等性，`===`在不允许的情况下做比较，因此`===`叫做严格相等。

允许隐式强转的宽松相等`==`和不允许强转的严格相等`===`如下示例：

```js
var a = "42";
var b = 42;

a == b;			// true
a === b;		// false
```

`a==b`比较中，JS发现两者类型不匹配，所以通过一系列步骤将一个或两个值强转为其它类型直到两者的类型一致，然后检验值的相等性。思考一下，通过强转可以有两种方法让`a==b`为`true`，是`42 == 42`呢还是`"42" == "42"`呢？

答案是`"42"`转换为`42`，进而比较`42 == 42`。在这个例子中，无论选择哪种方法都无所谓，因为最终结果都是`true`，但是在很多复杂情况下却很重要，不光因为比较结果，还因为 *如何* 得到比较结果。

`a === b`的结果是`fales`，因为不允许强转，所以值的比较明显就会失败。许多开发人员感觉`===` 更容易预期到结果，所以他们鼓励尽量使用它，并且远离`==`，但我认为这种观点很浅薄。我相信 *如果你花时间弄明白它的原理* 你会发现它是一个很有助于你编程的工具。

这里我们不细致的讲解`==`比较的原理，因为它很大一部分都很清晰，但也有一些比较重要的小知识点需要关注，你可以从[ES5 specification](http://www.ecma-international.org/ecma-262/5.1/) 的第11.9.3章节了解所有规则，你会发现它并不像传说中那么不堪。

方便起见，我将众多细节浓缩为一些简单原则，帮助你来针对不同情况选择`===`或`==`：

* 如果等号两侧有任意一个可能是`true`或`false`，使用`===`。
* 如果等号两侧有任意一个可能是特殊值（`0`, `""`, 或空数组`[]`），使用`===`。
* 在其它 *所有* 情况下，你可以放心使用`==`，它能让你的代码简单且更具有可读性。（译者代码写的少，并没发现可读性的益处）

这些浓缩的规则需要你重新审视的的代码，并且去考虑变量传过来的值的可能性，如果你对值很确定，那就放心使用`==`，如果不确定，使用`===`，就这么简单。

`!=`与`==`成对，`!==`与`===`成对，使用原则一样，但是带`!`表示不等。

当你比较非基本值，比如对象（包括函数、数组）时，要特别注意，因为这些值实际上是通过引用获取的，`==`和`===`只是比较引用是否一致，而不是被引用指向的值。

比如，`array`强转为`string`时，默认会使用逗号（`,`）来拼接所有元素，你可能认为具有相同内容的两个数组是`==`相等的，但实际并不：

```js
var a = [1,2,3];
var b = [1,2,3];
var c = "1,2,3";

a == c;		// true
b == c;		// true
a == b;		// false
```

**注意:** 了解更多`==`的比较规则，参见ES5 specification（第11.9.3章节），或者卷 *Types & Grammar* 的第四章，以及该卷的第二章了解值和引用的知识。

#### Inequality（大小）

 `<`, `>`, `<=`, and `>=`操作符被用来描述大小关系，一般它们被用于可排序的值，比如`number`，JS的`string`类型的值也可以比较大小（字母表顺序），比如`"bar" < "foo"`。

与`==`一样的强转规则（严格来讲有一点儿区别），没有`===`这样不允许强转的比较大小的操作符。

如下:

```js
var a = 41;
var b = "42";
var c = "43";

a < b;		// true
b < c;		// true
```

发生了什么呢？在ES5 specification的11.8.5章节有说，如果`<`两侧的值都是`string`类型，比如上面代码中的`b < c`，会进行字面值比较（即字母表顺序），但是如果有一侧或者两侧不是`string`，比如上面的`a < b`，那么两侧的值都会被强制转换为`number`类型，然后进行数值比较。

你经常会遇到值无法被强转为`number`的情况：

```js
var a = 42;
var b = "foo";

a < b;		// false
a > b;		// false
a == b;		// false
```

什么鬼？`a`和`b`的三种比较都是`false`？因为`b`在`<`和`>`比较中被强转为“无效数值类型”——`NaN`，specification指出：`NaN`不大于也不小于任何其他值。

**注意:** 了解更多比较大小的规则，参考ES5 specification的11.8.5章节，或者卷 *Types & Grammar* 的第四章。

## Variables（变量）

在JS中，变量名必须是有效的 *标识符* ，如果你考虑到非传统的字符比如Unicode，那么严格且完备的标识符规则就有些复杂，而如果只考虑典型的ASCII字母字符，那么就会简单多。

一个标识符必须以`a`-`z`，`A`-`Z`，`$`或者`_`开始，然后可以包含这些字符或`0`-`9`数字。

一般的，变量标识符与属性名的命名规则一样，然而，有一些特殊的单词不能用于变量名，却可以用于属性名，这些单词被称为“预留关键词”，包括JS的关键词（`for`, `in`, `if`等），以及`null`、`true`、`false`。

**注意:** 了解更多预留关键词，参考卷 *Types & Grammar* 的Appendix A。

### Function Scopes（函数作用域）

你使用`var`关键字来声明变量，它包含在当前函数的作用域内，或者变量在任何函数外，属于全局作用域。

#### Hoisting（提升）

无论一个`var`出现在作用域的何处，这个声明会被提升到整个作用域，域内的任何地方都能访问到。这种行为叫做 *提升* ，可以认为是一个`var`声明被移动到作用域的顶部，技术上来讲，用代码编译过程来解释这个过程可能更精确，但是我们暂时可以跳过这些细节。

有如下代码:

```js
var a = 2;

foo();					// works because `foo()`
						// declaration is "hoisted"

function foo() {
	a = 3;

	console.log( a );	// 3

	var a;				// declaration is "hoisted"
						// to the top of `foo()`
}

console.log( a );	// 2
```

**警告:** 借助变量提升来在它声明之前使用它并不是一个明智的做法，这样做会出现让人困惑的结果。但使用提升后的函数却很常见，比如上面代码中在`foo()`正式声明之前调用它。

#### Nested Scopes（作用域嵌套）

当你声明一个变量后，它在作用域的任何地方都能被访问到，即使是更内层作用域，比如：

```js
function foo() {
	var a = 1;

	function bar() {
		var b = 2;

		function baz() {
			var c = 3;

			console.log( a, b, c );	// 1 2 3
		}

		baz();
		console.log( a, b );		// 1 2
	}

	bar();
	console.log( a );				// 1
}

foo();
```

注意在`bar()`中访问不到`c`，因为它是在更内层的`baz()`中声明的，同理，在`foo()`中也访问不到`b`。

如果你试图访问一个不在自己作用域内的变量，你会得到一个`ReferenceError`异常。如果你试图给一个没有声明的变量赋值，那么就会在全局域内生成一个变量（这种做法很不好），或者得到一个错误，两种情况取决于是否开启了严格模式。看代码：

```js
function foo() {
	a = 1;	// `a` not formally declared
}

foo();
a;			// 1 -- oops, auto global variable :(
```

这种做法很糟糕，尽量使用有正式声明的变量。

除了在函数层面创建变量外，ES6还允许变量（通过`let`声明）只属于一个块作用域内（`{ .. }`内）。除了一些细微差别，它的作用域规则基本与我们刚才看到的函数作用域一样：

```js
function foo() {
	var a = 1;

	if (a >= 1) {
		let b = 2;

		while (b < 5) {
			let c = b * 2;
			b++;

			console.log( a + c );
		}
	}
}

foo();
// 5 7 9
```

因为使用了`let` 而不是`var`, `b`只属于`if`语句的作用域而不是`foo()`的整个函数作用域，同样的，`c`只属于`while`循环内，块作用域让你可以在更细的粒度管理你的代码，这让你的代码以后维护起来更容易。

**Note:** 了解更多关于作用域的知识，参见卷 *Scope & Closures* ，参见 *ES6 & Beyond* 了解更多关于`let`的块作用域。

## Conditionals（条件语句）

除了在第一章中介绍的`if`语句外，我们来看一下JS提供的其它条件语句，有时你会发现自己写了一堆`if..else..if`的代码：

```js
if (a == 2) {
	// do something
}
else if (a == 10) {
	// do another thing
}
else if (a == 42) {
	// do yet another thing
}
else {
	// fallback to here
}
```

这种结构可以工作，但是有点儿冗余，因为你要针对每一种情况来验证`a`，我们还有更好的选择`switch`：

```js
switch (a) {
	case 2:
		// do something
		break;
	case 10:
		// do another thing
		break;
	case 42:
		// do yet another thing
		break;
	default:
		// fallback to here
}
```

`break`保证只有一个`case`里的语句被执行，如果你从一个`case`里删除`break`，那个`case`匹配并运行后，会忽略跟在它后面的`case`的条件，而直接执行其中语句，这被叫做"fall through"，很有用哦：

```js
switch (a) {
	case 2:
	case 10:
		// some cool stuff
		break;
	case 42:
		// other stuff
		break;
	default:
		// fallback
}
```

如果`a`取值`2`或`10`，将会执行"some cool stuff"区域的代码。

JS中的另一种条件语句是"条件操作符"也被称为"三元操作符"， 它就像是`if..else` 语句的简写：

```js
var a = 42;

var b = (a > 41) ? "hello" : "world";

// similar to:

// if (a > 41) {
//    b = "hello";
// }
// else {
//    b = "world";
// }
```

如果条件验证（比如这里的`a > 41`）是`true`，那么第一个子句（`"hello"`）结果被赋给`b`，否则第二子句（`"world"`）的结果被赋给`b`。

条件操作符不是只能用于而是常用于赋值。

**注意:** 了解更多条件验证以及`switch`、`? :`的其它模式，参见卷 *Types & Grammar* 。

## Strict Mode（严格模式）

ES5引入了“严格模式”，它对一些行为做了更严格的约束。通常，这些约束是为了让代码更加安全和标准，当然，使用严格模式能让引擎将你的代码更加优化，你应该在你的程序中一直使用它。

你可以在某个函数内或者整个文件使用严格模式，取决你在何处放置严格模式的指示：

```js
function foo() {
	"use strict";

	// this code is strict mode

	function bar() {
		// this code is strict mode
	}
}

// this code is not strict mode
```

比较:

```js
"use strict";

function foo() {
	// this code is strict mode

	function bar() {
		// this code is strict mode
	}
}

// this code is strict mode
```

严格模式的一个重要改进就是，不再允许将无`var`声明的变量隐式自动提升为全局变量：:

```js
function foo() {
	"use strict";	// turn on strict mode
	a = 1;			// `var` missing, ReferenceError
}

foo();
```

如果你的代码开启了严格模式，你遇到了错误，或者代码开始出现bug，你可能想直接关掉严格模式，但是那可能并不是一个好的选择，如果严格模式在你的代码中引起了问题，那说明你的程序确实有需要修复的东西。

严格模式并不只是保证你的代码更加安全，也并不只是让你的代码更加优化，它也代表了这个语言的发展方向。现在就习惯使用严格模式会比你推迟使用更简单——越晚转变越困难！

**注意:** 详细了解严格模式，参见卷 *Types & Grammar* 的第五章。

## Functions As Values（函数作为值）

目前为止，我们只讨论了JS中函数作为 *域* 这种基本机制的知识，你可以想起来典型的声明函数的语法：

```js
function foo() {
	// ..
}
```

虽然从语法看来并不明显，但是`foo`在它的闭合作用域外的确只是一个引用到声明`function`的变量，即`function`本身（译者注：函数的定义）只是一个值，类似于`42`或者`[1,2,3]`。

这个概念初看起来很奇怪，所以花些时间仔细想想，你不仅可以将值（参数）传递 *到* 一个函数，而且 *函数本身可以是一个值* ，这个值可能是被赋给了变量，或者传递给其他函数，或来时其他函数的返回值。

如此，一个函数值应该被当做一个表达式，就像其它的值或表达式那样，如下：

```js
var foo = function() {
	// ..
};

var x = function bar(){
	// ..
};
```

第一个函数表达式被赋值给变量`foo`，因为没有`name`所以叫做*匿名*。第二个函数表达式是 *命名* （`bar`），甚至为了引用它，还被赋值给了变量`x`，*命名函数表达式* 个人更喜欢一些，尽管 *匿名函数表达式* 依然很常见。

了解更多，参见卷 *Scope & Closures* 。

### Immediately Invoked Function Expressions (IIFEs)（立即执行函数）


在前面的代码片段中，没有任何函数表达式被执行过，如果我们想要执行可以使用`foo()`或`x()`，但还有一种方式来执行，也就是所谓的 *立即执行函数*：

```js
(function IIFE(){
	console.log( "Hello!" );
})();
// "Hello!"
```

函数表达式`(function IIFE(){ .. })` 中外层包裹的 `( .. )` 正是JS语法中用来防止它被当成一个普通的函数声明的精妙之处。表达式最后面的 `()`（在`})();`那行）意思是执行在它前面的函数表达式。

这个可能有点儿怪，但是它并不像第一眼那么陌生，观察`foo`和`IIFE`的相同点：

```js
function foo() { .. }

// `foo` function reference expression,
// then `()` executes it
foo();

// `IIFE` function expression,
// then `()` executes it
(function IIFE(){ .. })();
```

如你所见，在`()`执行前的`(function IIFE(){ .. })`与`foo`本质是一样的，两者都是函数在`()`后立即执行函数引用。

由于IIFE只是一个函数，有函数就有变量 *作用域*，所以一般使用IIFE这种方式来声明那些IIFE外围代码的变量，比如：

```js
var a = 42;

(function IIFE(){
	var a = 10;
	console.log( a );	// 10
})();

console.log( a );		// 42
```

IIFE也可以有返回值：

```js
var x = (function IIFE(){
	return 42;
})();

x;	// 42
```

可以返回`42`的IIFE被执行后，返回`42`，赋值给`x`。

### Closure（闭包）

*必报* 是JS里一个很重要但是少理解的概念，这里我们不深入展开（想深入了解参考卷 *Scope & Closures*）。但我还是要说一些好让你有个大致概念，闭包会是你JS技能里的最重要之一。

你可以将闭包当做是一种“存储”进而访问函数作用域（它的变量）的一种方式，即使函数已经运行完毕，比如：

```js
function makeAdder(x) {
	// parameter `x` is an inner variable

	// inner function `add()` uses `x`, so
	// it has a "closure" over it
	function add(y) {
		return y + x;
	};

	return add;
}
```

每一次调用外函数`makeAdder(..)`都会返回一个对内函数`add(..)`的引用，这个引用可以存储传入`makeAdder(..)`的任何`x`的值，现在，让我们使用`makeAdder(..)`：

```js
// `plusOne` gets a reference to the inner `add(..)`
// function with closure over the `x` parameter of
// the outer `makeAdder(..)`
var plusOne = makeAdder( 1 );

// `plusTen` gets a reference to the inner `add(..)`
// function with closure over the `x` parameter of
// the outer `makeAdder(..)`
var plusTen = makeAdder( 10 );

plusOne( 3 );		// 4  <-- 1 + 3
plusOne( 41 );		// 42 <-- 1 + 41

plusTen( 13 );		// 23 <-- 10 + 13
```

代码工作流程说明：

1. 当调用`makeAdder(1)`时，我们得到一个指向它内函数`add(..)`的引用，这个引用存储了`x`的值为1，当我们将这个函数引用命名为`plusOne`。
2. 当调用`makeAdder(10)`时，我们得到一个指向它内函数`add(..)`的引用，这个引用存储了`x`的值为10，当我们将这个函数引用命名为`plusTen`。
3. 当调用`plusOne(3)`时，它会给`1`（存储的`x`）加上`3`（传递的参数`y`），我们得到`4`，返回。
4. 当调用`plusTen(13)`时，它会给`10`（存储的`x`）加上`13`（传递的参数`y`），我们得到`23`，返回。

如果刚开始有些困惑和觉得奇怪，别慌，的确会那样！多练习你就会理解。

但是相信我，一旦你掌握了它，在你以后的编程中，它会是一个很强大很有用的技术，所以慢慢去理解闭包，在下一节，我们会有一些闭包的练习。

#### Modules（模块）

JS中最常使用闭包的是模块模式，有了模块，你就可以定义一个只暴露给外部访问的API，并且隐藏具体的实现细节（变量啊函数啊）。比如:

```js
function User(){
	var username, password;

	function doLogin(user,pw) {
		username = user;
		password = pw;

		// do the rest of the login work
	}

	var publicAPI = {
		login: doLogin
	};

	return publicAPI;
}

// create a `User` module instance
var fred = User();

fred.login( "fred", "12Battery34!" );
```

函数`User()`是外部作用域，持有变量`username`和`password`，内函数`doLogin()`也持有这两个变量，它们都是模块`User`的私有细节，无法从外部直接访问。

**警告:** 我们有意不使用`new User()`，即使这是大多数读者的最常见的用法。`User()`只是一个函数，不是待实例化的类，所以它只是正常的函数调用方式，实际上使用`new`可能不合适且浪费资源。

执行`User()`会创建模块`User`的一个 *实例*，一个新的作用域会被创建，同时也复制了一份所有内部变量、函数。我们将这个实例赋值给`fred`，如果我们再次执行`User()`，我们会得到一个全新的与`fred`没有半毛钱关系的实例。

内函数`doLogin()`有一个持有`username`和`password`的闭包，意味着即使`User()`已经运行完毕，它依然可以访问这两个变量。

`publicAPI`是有一个属性（或方法）`login`的对象，该属性引用内函数`doLogin()`，当从`User()`返回`publicAPI`后，它变成一个叫做`fred`的实例。到这里，外函数`User()`已经运行完，一般我们认为内部变量如`username`和`password`也应该消失了，但是并没有，因为`login()`函数中有一个持有它们的闭包使得它们不会消失。

这就是为何当我们调用`fred.login(..)`（与调用内函数`doLogin(..)`一样）时，它可以访问到内部变量`username`和`password`。

只是简单的了解一下闭包和模块模式，你很可能会困惑，没关系，你需要一些努力来理解它，所以你可以阅读卷 *Scope & Closures* 来深入探索。

## `this` Identifier（`this`标识符）

JS中另一个容易被理解错的感念就是`this`标识符，在卷 *this & Object Prototypes* 中有几章详细讲解它，所以这里我们只是简单介绍一下这个概念。

`this`在“面向对象编程模式”中很常见，但是与JS中`this`的机制并不一样。如果函数中有一个`this`引用，这个`this`通常指向一个`object`，但这个对象指向取决于函数如何被调用。

很重要的一点，`this` *并不是* 指向函数本身，这是最常见的会被理解错的地方，如下示例：

```js
function foo() {
	console.log( this.bar );
}

var bar = "global";

var obj1 = {
	bar: "obj1",
	foo: foo
};

var obj2 = {
	bar: "obj2"
};

// --------

foo();				// "global"
obj1.foo();			// "obj1"
foo.call( obj2 );	// "obj2"
new foo();			// undefined
```

关于`this`如何取值有四点规则，如上面代码的最后四行所示。

1. 在非严格模式下，`foo()`将`this`指向全局对象，所以`this.bar`返回`"global"`，而严格模式下，`this`会是`undefined`，访问属性`bar`会得到一个错误。
2. `obj1.foo()`使`this`指向对象`obj1`。
3. `foo.call(obj2)`使`this`指向对象`obj2`。
4. `new foo()`将`this`指向一个全新的空对象。

总结：为了理解`this`的指向，你必须检查函数是如何被调用的，它会是上面四种的一种，然后你就可以回答`this`是什么这个问题了。

**注意:** 了解更多关于`this`，参见卷 *this & Object Prototypes* 的第一、二章。

## Prototypes（原型）

JS的原型机制比较复杂，我们这里只先了解一下，为了全面学习，你需要花时间阅读 *this & Object Prototypes* 的第四至六章。
When you reference a property on an object, if that property doesn't exist, JavaScript will automatically use that object's internal prototype reference to find another object to look for the property on. You could think of this almost as a fallback if the property is missing.
当你引用某个对象的属性时，假设该属性不存在，JS会自动在该对象的内部原型引用的另一个对象中查找该属性，你可以把它看做是当属性缺失时的一个fallback（这个不知道该怎么译— —||）。

The internal prototype reference linkage from one object to its fallback happens at the time the object is created. The simplest way to illustrate it is with a built-in utility called `Object.create(..)`.
当对象被创建时，从一个对象到它的fallback的内部原型链就会出现，我们用内置工具方法`Object.create(..)`来举例说明一下：

```js
var foo = {
	a: 42
};

// create `bar` and link it to `foo`
var bar = Object.create( foo );

bar.b = "hello world";

bar.b;		// "hello world"
bar.a;		// 42 <-- delegated to `foo`
```

下图形象的说明了`foo`和`bar`对象以及它们的关系：

<img src="fig6.png">

属性`a`实际上并不在对象`b`中，但是因为`bar`的原型链向`foo`，所以JS自动的回退到对象`foo`中查找并找到了属性`a`。这种原型链看起来好像是一个陌生的特性，这种特性最常使用的地方是使用“继承”来模拟“类”（这点我会进行讨论和说明）。

实际上原型的最佳使用方式是一种被叫做 *行为委托* 的模式，你设计链接的对象，从而可以将一个对象中需要的行为 *委托* 给另一个对象。

**注意:** 了解更多原型和行为委托参见卷 *this & Object Prototypes* 的第四至六章。

## Old & New（旧&新）

我们已经涉及到一些的JS特性，并且在本系列中会涉及到的，是新引入的特性，可能在旧浏览器中并不支持，甚至一些最新的特性在稳定版本的浏览器中都还未被支持。

所以，你该怎么办呢？难道要等到所有的旧浏览器淡出市场？这是很多人会思考的当前形势，但是这不是一个开启JS新世界的正确方式。

有两个主要方式来讲JS的新特性引入到旧浏览器中：polyfilling和transpiling。

### Polyfilling

"polyfill"是由[Remy Sharp](https://remysharp.com/2010/10/08/what-is-a-polyfill)发明的一个术语，指将编写一段与新特性完全一致的可以在旧JS环境中运行的代码。 used to refer to taking the definition of a newer feature and producing a piece of code that's equivalent to the behavior, but is able to run in older JS environments.

比如，ES6定义了一个工具方法`Number.isNaN(..)`，它精确的无bug的判断是否是`NaN`值，并弃用了原始的`isNaN(..)`工具方法。你可以很容易polyfill这个工具方法，使你的代码可以无论在ES6或其它环境中都能正确运行，如下：

```js
if (!Number.isNaN) {
	Number.isNaN = function isNaN(x) {
		return x !== x;
	};
}
```

`if`语句防止在已经有这个工具方法的ES6浏览器执行polyfill的定义，而如果没有这个工具方法，那么我们定义`Numebr.isNaN(..)`。

**注意:** 我们定义的工具方法中利用`NaN`值的特殊属性（它是JS语言中唯一一个不等于自身的值）来验证是否为NaN，所以只有`NaN`才能让`x !== x`为`true`。

并不是所有的新特性都可以完全被polyfill，大多数行为都可以被polyfill，但是有时仍有一些偏差，在你自己实现polyfill时一定要格外小心，要确保你严格遵从了新特性。

更好的选择是使用已经被审查过的polyfill，比如由ES5-Shim(https://github.com/es-shims/es5-shim)、和ES6-Shim(https://github.com/es-shims/es6-shim)提供的polyfill集合。

### Transpiling（转译）

新加入的语法没有办法去polyfill，并且在旧JS引擎中会被当做无法识别或无效的东西而抛出错误。

所以更好的选择是使用一个可以将新代码转换到旧代码的运行环境中，这个过程就是“转译”，一个代表转换（transforming）和编译（compiling）的术语。

本质上，你的源代码是用新语法形式编写的，但是你部署到浏览器上的是已经被转译为旧语法形式的代码，你只需要将转译器插入到你的构建过程中，类似于代码语法检查器或压缩器。

你可能好奇为什么你要自找麻烦去写新语法格式然后又将它转译成旧语法格式的代码，为什么不直接写旧语法格式的代码？

下面有几个关于转译的重要原因你应该关注：

* 新引入的语法是为了让你的代码更可读和可维护，但是类似功能的旧语法可能更加复杂，你肯定更倾向于编写更新、更清晰的代码，不仅仅为了你自己，也为了开发团队中的其他人员。
*  如果转译只是为了旧浏览器，但是新浏览器仍使用新语法，这样你就能用到新语法给浏览器性能优化带来的好处，这也让制造浏览器的人有更多真实代码去测试他们的实现和优化。
* 尽早使用新语法可以让代码更早的进行真实的健壮性测试，也尽早给JS委员会（TF39）提供反馈，及时发现问题可以让设计缺陷在成为历史遗留问题之前被修复。

以下是一个转译的例子，ES6新增了一个叫“参数默认值”的特性，如下：

```js
function foo(a = 2) {
	console.log( a );
}

foo();		// 2
foo( 42 );	// 42
```

很简单是吧？也很有帮助！但是在ES6之前的引擎里这个新语法是无效的，所以为了保证在旧环境下也能运行转译是如何处理的呢？

```js
function foo() {
	var a = arguments[0] !== (void 0) ? arguments[0] : 2;
	console.log( a );
}
```

如你所见，它检查`arguments[0]`的值是否为`void 0`（即`undefined`），如果是，则赋值`2`，否则，赋值任何传递过来的值。

至此，你除了能在旧浏览器中使用新语法外，通过查看转译后的代码，你会发现它更清楚的解释了行为的意图。仅仅是看ES6版本，你不会知道`undefined`是唯一一个不能显式传递给有默认值参数的值，但是在转译后的代码里，你却可以清楚的看到这点。

最后一个需要强调的关于转译器的重要细节是，它们应该被当做JS发展生态和进程中标准的一部分。JS会比以往更快的继续演进，所以每过几个月就可能有新的特性被引入。

如果你默认使用转译器，你就能够随时按需要切换到新语法，而不是等好几年直到当前的浏览器消失。

有很多给力的转译器供你选择，下面是写本书时几个比较好的选择：

* Babel (https://babeljs.io) (formerly 6to5): 将ES6+转译到ES5。
* Traceur (https://github.com/google/traceur-compiler): 将 ES6, ES7及ES7+转译到ES5。

## Non-JavaScript（非JS）

目前为止，我们说到的都只是JS语言本身，实际上，很多JS是运行在像浏览器这样的环境中的，你编写的代码的很大一部分严格说来并不是由JS控制的，这可能听起来很怪异。

最常见的非JS的JS就是DOM API，比如：

```js
var el = document.getElementById( "foo" );
```

当你的代码运行在浏览器里，就会有一个全局的`document`变量，它不是由JS引擎提供，也不是由JS语法特性控制，它从某种程度上讲更像是一个JS“对象”，但却不是JS中的对象。它是一个特殊的`object`，通常也叫“宿主对象”。

而且`document`的`getElementById(..)`方法看起来很像JS的函数，但是它只是一个你的浏览器的DOM提供的一个内置方法对外的接口，在某些（新）浏览器中，这一层也可能在JS中，但是传统的DOM和它的行为都是在如C/C++中实现的。

另一个例子就是输入/输出（I/O）。

大家喜爱的`alter(..)`在用户的浏览器中弹出一个信息窗口，`alert(..)`是由浏览器提供给你的JS程序的，不是JS引擎自身，你调用它时会将消息发送给浏览器内部，它处理绘制和显示这个消息窗口。

同样的，你的浏览器还提供`console.log(..)`机制，并且将它们与开发者工具结合起来。

这本书，以及所有系列，专注于JS语言本身。这也是为何你没有看到大量关于非JS的内容，然而，需要了解它们，因为在你编写的每一个JS程序中都可能会出现。

## Review（回顾）

学习JS编程的第一步即使理解它的核心机制，比如值、类型、函数、闭包、`this`以及原型。

当然，每一个主题都值得更全面细致的了解，而不仅仅是这里看到的这些，所以有了其它涉及到它们的系列丛书。当你熟悉本章的概念和代码后，其它系列都已准备好让你更深入的学习这门语言。

这本书的最后一章是针对后续几卷名字的简短概述，以及其它我们还没有探索的概念。
