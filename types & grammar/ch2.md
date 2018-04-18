# You Don't Know JS: Types & Grammar

# Chapter 2: Values

`array`、`string`以及 `number`是任何编程的最基本结构单元，但是JS又有一些自己的比较奇葩的特性，它们有时会让你很爽有时会令你挫败。我们一起来看一下JS的一些内建值类型，通过探索我们能更深入理解，并合理利用它们的特性。

## Arrays

相对于其它强类型的语言，JS的`array`只是一个可以容纳任何类型值的容器，什么`string`啦、`number`啦、`object`啦，甚至`array`（多维数组）。

```js
var a = [ 1, "2", [3] ];

a.length;		// 3
a[0] === 1;		// true
a[2][0] === 3;	// true
```

你无需另设置`array`的大小，你只需要声明数组，然后在有需要时增加值即可：

```js
var a = [ ];

a.length;	// 0

a[0] = 1;
a[1] = "2";
a[2] = [ 3 ];

a.length;	// 3
```

**警告:** 对`array`的元素使用`delete`会删除该`array`对应位置的槽，即使最终该位置对应的元素被删除了，数组的`length`属性也**不**会更新，所以使用时要小心。我们会在第5章讨论`delete`操作符。

创建“松散”的`array`时要小心：

```js
var a = [ ];

a[0] = 1;
// no `a[1]` slot set here
a[2] = [ 3 ];

a[1];		// undefined

a.length;	// 3
```

尽管上面的代码不会出错，但是“空槽”可能会引发令人困惑的行为，尽管它看起来有一个`undefined`的值，但是与直接赋值（如上面代码中a[1]=`undefined`）却不相同，第3章的“数组”会详细介绍。

`array`是数字索引（如你所见），但是有趣的是它们也是对象，可以有`string`键/属性（但是不会计入数组的`length`）：

```js
var a = [ ];

a[0] = 1;
a["foobar"] = 2;

a.length;		// 1
a["foobar"];	// 2
a.foobar;		// 2
```

但是，这里有一点需要注意，如果键的是一个可以被强转为十进制`number`的`string`的话，它会认为你想要把那个健当作一个`number`索引：

```js
var a = [ ];

a["13"] = 42;

a.length; // 14
```

一般，不推荐给`array`增加`string`类型的键／属性，键／属性的值用`object`，严格的数字索引的值用`array`。

### Array-Likes

有时，你需要将一个类`array`的值（按数字索引的值的集合）转为真正的`array`，然后你就可以对这些集合值使用数组的方法（比如`indexOf(..)`、`concat(..)`、`forEach(..)`）。

比如，不同的DOM查询操作返回一组DOM元素，这个组不是`array`而是类`array`很需要进行转换，另一个比较常见的例子是函数内使用`arguments`对象（ES6已经弃用）访问入参列表。

一般通过`slice(..)`方法来进行转换：

```js
function foo() {
	var arr = Array.prototype.slice.call( arguments );
	arr.push( "bam" );
	console.log( arr );
}

foo( "bar", "baz" ); // ["bar","baz","bam"]
```

如果`slice()`调用时没有传入其它参数，如上面的代码示例，那么会返回一个全部元素组成的数组。

ES6中使用`Array.from(..)`来完成该转换：

```js
...
var arr = Array.from( arguments );
...
```

**注意：** `Array.from(..)`有好些个强大的能力，我们会在系列的*ES5 & Beyond*里介绍。

## Strings

一般都认为`string`就是许多字符组成的`array`，无论内部实现是否使用`array`，我们都需要意识到JS的`string`和字符`array`并不一样，相似只不是是看起来像。

比如，我们考虑这样两个值：

```js
var a = "foo";
var b = ["f","o","o"];
```
字符串确实和`array`（或类`array`）表面类型，它们都有`length`属性，有`indexOf(..)`方法（ES5中的`array`），以及`concat(..)`方法：

```js
a.length;							// 3
b.length;							// 3

a.indexOf( "o" );					// 1
b.indexOf( "o" );					// 1

var c = a.concat( "bar" );			// "foobar"
var d = b.concat( ["b","a","r"] );	// ["f","o","o","b","a","r"]

a === c;							// false
b === d;							// false

a;									// "foo"
b;									// ["f","o","o"]
```

所以，它们都是“字符数组”，对吧？**不一定哦**：

```js
a[1] = "O";
b[1] = "O";

a; // "foo"
b; // ["f","O","o"]
```

JS的`string`是不可变的，而`array`可以改变，而且，`a[1]`访问字符串在JS中并不完全被支持，比如在老的IE浏览器中，这种写法是不允许的，需要通过`a.charAt(1)`去访问。

`string`的不可修改性导致它所有的方法都不是修改自身，二十重新生成一个`string`，而`array`却是直接修改自己：

```js
c = a.toUpperCase();
a === c;	// false
a;			// "foo"
c;			// "FOO"

b.push( "!" );
b;			// ["f","O","o","!"]
```

此外，许多`array`的方法可以被用来处理`string`（因为它们自己本身没有该方法），我们可以借用不可改变性+`array`方法来处理`string`：

```js
a.join;			// undefined
a.map;			// undefined

var c = Array.prototype.join.call( a, "-" );
var d = Array.prototype.map.call( a, function(v){
	return v.toUpperCase() + ".";
} ).join( "" );

c;				// "f-o-o"
d;				// "F.O.O."
```
再举一个栗子：反转一个`string`（一个很常见的JS面试题），`array`自身就有`reverse()`方法，而`string`没有：

```js
a.reverse;		// undefined

b.reverse();	// ["!","o","O","f"]
b;				// ["!","o","O","f"]
```

然而，“借用”`array`的方法在这里不会有用，因为`string`是不可变的，无法修改自身：

```js
Array.prototype.reverse.call( a );
// still returns a String object wrapper (see Chapter 3)
// for "foo" :(
```
另一种变通尝试（即 hack）是将`string`转为`array`，然后调用需要的方法，再转换为`string`。

```js
var c = a
	// split `a` into an array of characters
	.split( "" )
	// reverse the array of characters
	.reverse()
	// join the array of characters back to a string
	.join( "" );

c; // "oof"
```

不够优雅，哈哈。但是，*实现了功能*，所以如果你需要快不计较优雅不优雅，这种方法就可以搞定。

**警告：** 小心哦！这种方法在复杂（unicode）编码字符集中（astral字符、多字节字符等）**不可工作**。你需要借助更专业的可以识别unicode的工具库来完成，参考Mathias Bynens关于该主题的工作：*Esrever* (https://github.com/mathiasbynens/esrever)。

另一个角度看待这个问题：如果你经常需要对你的字符串进行*字符数组*操作，也许你最好将它存储为`array`而不是`string`，这样就会为你节省很多`string`转`array`的麻烦，你可以随时调用`join(..)`来将*字符*`array`转为`string`。

## Numbers

JS只有一个数值类型：`number`，该类型包括“整数”、浮点数。之所以给”整数“加了引号，是很长时间以来，JS因为没有类似其它语言里的真正整数而被诟病。未来可能会有所改变，但目前，我们只有`number`类型。

所以，在JS中，一个“整数”只是没有小数点的值，即`42.0`与“整数”`42`差不多。

类似现代语言，包括几乎所有脚本语言，JS的`number`是基于“IEEE 754”实现的，也称为“浮点型”。JS使用该标准的“双精度”（即“64-bit二进制”）形式。

网上已经有很多关于二进制浮点数如何在内存中存储的详解，以及不同实现的意义。因为理解存储中的点滴模式，对理解如何正确使用`number`没有必要，感兴趣的读者可以自行通过IEEE 754标准了解更多。

### Numeric Syntax

字面数字一般通过十进制数字表示：

```js
var a = 42;
var b = 42.3;
```

小数点前的`0`可以省略：

```js
var a = 0.42;
var b = .42;
```

同样的，小数点后的`0`也可以省略：

```js
var a = 42.0;
var b = 42.;
```

**警告：** `42.`一般很少用，为了避免造成困惑，建议不要这么使用，即使它是有效的。

一般`number`都是以10进制输出，并且会将小数点的末尾`0`移除。

```js
var a = 42.300;
var b = 42.0;

a; // 42.3
b; // 42
```
非常大或小的`number`默认会以科学计数法输出，类似方法`toExponential()`的输出。

```js
var a = 5E10;
a;					// 50000000000
a.toExponential();	// "5e+10"

var b = a * a;
b;					// 2.5e+21

var c = 1 / a;
c;					// 2e-11
```

由于`number`值可以使用`Number`对象封装（见第3章），`number`值可以访问`Number.prototype`里内建的方法。比如`toFixed(..)`方法让你可以限定浮点数的小数位数：

```js
var a = 42.59;

a.toFixed( 0 ); // "43"
a.toFixed( 1 ); // "42.6"
a.toFixed( 2 ); // "42.59"
a.toFixed( 3 ); // "42.590"
a.toFixed( 4 ); // "42.5900"
```

注意输出是`number`值的`string`类型，并且在位数不足时使用`0`补齐。

`toPrecision(..)`类似，但是它表示的是总共使用多少位数字来表达该值：

```js
var a = 42.59;

a.toPrecision( 1 ); // "4e+1"
a.toPrecision( 2 ); // "43"
a.toPrecision( 3 ); // "42.6"
a.toPrecision( 4 ); // "42.59"
a.toPrecision( 5 ); // "42.590"
a.toPrecision( 6 ); // "42.5900"
```

你无需通过持有这些值的变量去调用那些方法，你可以直接通过`number`字面值调用，但是要小心`.`操作符，因为它也是一个数字字符，如果可能，它首先会被解释为`number`的字面值，而不是属性的访问器。

```js
// invalid syntax:
42.toFixed( 3 );	// SyntaxError

// these are all valid:
(42).toFixed( 3 );	// "42.000"
0.42.toFixed( 3 );	// "0.420"
42..toFixed( 3 );	// "42.000"
```

`42.toFixed(3)`语法错误，因为`.`被当作`42.`的一部分被吞噬，从而没有`.`来表明调用`toFixed`调用的对象。

`42..toFixed(3)`能够正常运行，因为第一个`.`被当作`number`的一部分，第2个`.`是属性操作符，但是看起来很怪异，实际上也很少这么用，而且也很少有直接通过基本类型来调用方法。不常用并不是说*坏*或者*错*。

**注意：** 但是有一些扩展了`Number.prototype`（见第3章）来提供`number`的额外操作，从而在这些场景下，通过使用类似`10..makeItRain()`触发10秒的钱雨动画，或者类似的逗比事情。

严格来讲，下面的也是有效的（注意空格）：

```js
42 .toFixed(3); // "42.000"
```

然而，使用这种特殊的`number`字面值，**这明显是很让人迷惑的编码风格**，除了让其他开发者骂娘没有任何别的作用。请避免使用。

`number`也可以使用指数形式，尤其在表示大`number`时：

```js
var onethousand = 1E3;						// means 1 * 10^3
var onemilliononehundredthousand = 1.1E6;	// means 1.1 * 10^6
```

`number`字面值也可以通过其它进制表达，比如二进制、八进制、十六进制：

```js
0xf3; // hexadecimal for: 243
0Xf3; // ditto

0363; // octal for: 243
```

**注意：** 从ES6和`strict`模式开始，`0363`代表的八进制字面值不再被支持，但在非`strict`模式下仍可以正常使用，单最好还是不要继续使用，为了和谐的未来（也因为你从现在开始应该使用`strict`模式！）。

对于ES6，下面的形式都是合法的：

```js
0o363;		// octal for: 243
0O363;		// ditto

0b11110011;	// binary for: 243
0B11110011; // ditto
```

为了你的猿友：不要使用`0O363`形式，大写`O`和`0`放在一起只能造成迷惑，养成用小写的好习惯：`0x`、`0b`、`0o`。

### Small Decimal Values

使用二进制浮点数（不单单是JS，其他使用IEEE 754的语言都有）的最明显的缺陷是：

```js
0.1 + 0.2 === 0.3; // false
```
数学上我们知道，该语句应该是`true`，可为什么实际上是`false`嘞？

简单说来，在二进制浮点数里`0.1`和`0.2`并不精确，所以当它们进行加法运算时，结果并不严格等于`0.3`，`0.30000000000000004`很接近，但是接近终是不相等的。


**注意：** JS是否应该切换到使用精确的表达值的`number`实现？一些人认为应该如此。这些年来，已经有好多可选的方法被提出来，但没有一个被接受，或许永远也不会。改变并不像握手言和说一句“已经修复了这个bug“那么简单。不然，肯定很早以前就改变了。

现在的问题是，既然`number`无法*确保*精确，是否意味着我们不能再使用它了。**当然不是。**

在有些处理小数的应用里，你应该当心。很多（也许绝大多数）应用处理的都是整数，或者更大的百万、万亿级别，这些应用实际上用JS处理数字是**很安全**的。

如果我们确实需要比较两个`number`呢，比如`0.1 + 0.2`和`0.3`，我们知道直接那么比较会出问题。

最被认可的方法就是使用很小的“舍入误差”作为*容差*来作比较，这个很小的值称为“机器精度“，在JS的`number`中，一般取值为`2^-52`（`2.220446049250313e-16`）。

ES6中，`Number.EPSILON`是一个预定义的容差值，可以直接使用，你也可以为pre-ES6进行兼容：

```js
if (!Number.EPSILON) {
	Number.EPSILON = Math.pow(2,-52);
}
```

我们可以使用`Number.EPSILON`来比较两个`number`的“相等性”（在容差范围内）：

```js
function numbersCloseEnoughToEqual(n1,n2) {
	return Math.abs( n1 - n2 ) < Number.EPSILON;
}

var a = 0.1 + 0.2;
var b = 0.3;

numbersCloseEnoughToEqual( a, b );					// true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 );	// false
```

最大浮点数可以粗略表示为`1.798e+308`（已经很大很大了！），预定义为`Number.MAX_VALUE`，另一面，`Number.MIN_VALUE`约等于`5e-324，表示最接近0的最小正数。

### Safe Integer Ranges

鉴于`number`的表示方式，JS的“整数”有一个安全范围，远远小于`Number.MAX_VALUE`。

The maximum integer that can "safely" be represented (that is, there's a guarantee that the requested value is actually representable unambiguously) is `2^53 - 1`, which is `9007199254740991`. If you insert your commas, you'll see that this is just over 9 quadrillion. So that's pretty darn big for `number`s to range up to.

This value is actually automatically predefined in ES6, as `Number.MAX_SAFE_INTEGER`. Unsurprisingly, there's a minimum value, `-9007199254740991`, and it's defined in ES6 as `Number.MIN_SAFE_INTEGER`.

The main way that JS programs are confronted with dealing with such large numbers is when dealing with 64-bit IDs from databases, etc. 64-bit numbers cannot be represented accurately with the `number` type, so must be stored in (and transmitted to/from) JavaScript using `string` representation.

Numeric operations on such large ID `number` values (besides comparison, which will be fine with `string`s) aren't all that common, thankfully. But if you *do* need to perform math on these very large values, for now you'll need to use a *big number* utility. Big numbers may get official support in a future version of JavaScript.

### Testing for Integers

可以通过ES6定义的方法`Number.isInteger(..)`来验证是否是整数：

```js
Number.isInteger( 42 );		// true
Number.isInteger( 42.000 );	// true
Number.isInteger( 42.3 );	// false
```

兼容pre-ES6的`Number.isInteger(..)`：

```js
if (!Number.isInteger) {
	Number.isInteger = function(num) {
		return typeof num == "number" && num % 1 == 0;
	};
}
```

通过ES6定义的方法`Number.isSafeInteger(..)`来验证是否是*安全整数*：

```js
Number.isSafeInteger( Number.MAX_SAFE_INTEGER );	// true
Number.isSafeInteger( Math.pow( 2, 53 ) );			// false
Number.isSafeInteger( Math.pow( 2, 53 ) - 1 );		// true
```

同样的，pre-ES6兼容`Number.isSafeInteger(..)`：

```js
if (!Number.isSafeInteger) {
	Number.isSafeInteger = function(num) {
		return Number.isInteger( num ) &&
			Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
	};
}
```

### 32-bit（有符号）整数

尽管整数最大到9万亿也是安全的（53比特），但是有些数字操作符（比如位运算）只适用于32位的数，所以数的安全范围应该是小于这个的。

所以范围应该是 `Math.pow(-2,31)`（`-2147483648`，大概 -21 亿）到 `Math.pow(2,31)-1`（`2147483647`，大概 +21 亿）。

可以通过 `a | 0` 来强制让数字 `a` 成为32位有符号整数，之所以可以这样是位操作 `|` 只对 32位整数起作用（意味着它只关注32位，其它位都会被丢弃），其次，和数字 0「或」运算是位范畴里的空操作。

**注意：** 如 `NaN` 和 `Infinity` 这些特定的值不是「32位」安全的（下一节会讲），当它们被用于位运算中时，会经过 `ToInt32` 操作（见第4章），然后转化为值 `+0` 以便进行位运算。

## 特殊的值

有不少特殊值需要JS开发者*警惕*，并合理使用。

### The Non-value Values

对于 `undefined` 类型，它有且只有一个值：`undefined`。`null` 类型也只有值 `null`。即这两个类型的标记也是它们各自的值。

`undefined` 和 `null` 经常被用做可以相互替换的「空值」或「无值」，有些开发者认为两者有细微差别：

* `null` 是空值
* `undefined` 缺值

或者：

* `undefined` 还没有被赋值
* `null` 已有值

无论你选择哪种「定义」，`null` 是一个特殊的关键字，不是标识符，所以你无法把它当作一个变量并给它赋值，而 `undefined` **是**标识符，我的天呐！

### Undefined

在非严格模式下，你是可以给全局域里的 `undefined` 标识符赋值的：

```js
function foo() {
	undefined = 2; // really bad idea!
}

foo();
```

```js
function foo() {
	"use strict";
	undefined = 2; // TypeError!
}

foo();
```

无论在严格模式还是非严格模式下，你都可以创建一个名为 `undefined` 的局部变量，再次强调，这种做法很糟糕！

```js
function foo() {
	"use strict";
	var undefined = 2;
	console.log( undefined ); // 2
}

foo();
```

**Friends don't let friends override `undefined`.** Ever.

#### `void` 操作符

尽管 `undefined` 是内建的标识符，它持有内建的值 `undefined`（除非被修改了，比如上面的例子），另一个获取这个值的方法是使用 `void` 操作符。

表达式 `void ___` 无效化了任何的值，所以这种表达式的结果通常都是 `undefined`。它没有修改已存在的值，它只是确保这个表达式里操作符不会返回任何值：

```js
var a = 42;

console.log( void a, a ); // undefined 42
```

按照惯例（大多数来自 C 语言编程），为了不使用 `undefined` 来表示 `undefined` 值，你会选择 `void 0`（尽管 `void true` 或其它 `void` 表达式的结果都是一样的）。实际上，`void 0`、`void 0` 和 `undefined`没有区别。

但是 `void` 操作符在某些场景下比较实用，比如：你想确保某个表达式没有任何结果（即使它有副作用）。

```js
function doSomething() {
	// note: `APP.ready` is provided by our application
	if (!APP.ready) {
		// try again later
		return void setTimeout( doSomething, 100 );
	}

	var result;

	// do some other stuff
	return result;
}

// were we able to do it right away?
if (doSomething()) {
	// handle next tasks right away
}
```

上面，函数 `setTimeout(..)` 会返回一个数字（用于取消该定时器时的唯一标识），但是我们想要 `void` 这个函数的返回值，这样我们的 `if` 语句就不会（因为这个标识数字强转为布尔值）出现假阳性。

许多开发者开发者使用这种没有 `void` 的方式来实现：

```js
if (!APP.ready) {
	// try again later
	setTimeout( doSomething, 100 );
	return;
}
```

通常，如果某个值是存在的（来自一些表达式），但是如果把它设置为 `undefined` 会更有用，这种场景并不常见，但是当你真正遇到时，会觉得 `void` 很有用。

### 特殊的数字

`number` 类型还包括一些特殊的值，我们下面来一一探讨。

#### 不是数字的数字

任何数学运算如果两边的操作数不同时为`number`（或者可以被转义为10进制或16进制的一般`number`），那么运算会失败，此时你会得到一个`NaN`值。

`NaN`字面意思是“不是一个`number`“，这种描述很具有误导性，如果将它看作一个”无效数字“，”失败的数字“甚至”糟糕的数字“，都比”非数“更精确。

如示例：

```js
var a = 2 / "foo";		// NaN

typeof a === "number";	// true
```

也就是说：“非数的类型居然是‘数字’！“，为这迷惑人的名字和语义点赞。

`NaN` 是 `number` 集合里的错误条件下的「标记值」，错误条件是指“我试着做数学运算，但是失败了，所以结果就是一个失败的 `number`“。

所以，如果你想测试一些变量是否有 `NaN` 值，你可能首先想到的是把这些变量与 `NaN` 来做比较，但是这个值并不能像 `null` 或 `undefined` 那样使用。

```js
var a = 2 / "foo";

a == NaN;	// false
a === NaN;	// false
```

`NaN` 比较特殊，它并不等于任何其它 `NaN`（即它与自身也不相等），它是唯一一个不指代自己的值，所以 `NaN !== NaN`，很神奇吧。

所以如果无法与 `NaN`比较，那我们如何检测它呢？

```js
var a = 2 / "foo";

isNaN( a ); // true
```

很简单吧，我们使用内建的全局工具方法 `isNaN(..)`，它可以检测值是否为 `NaN`。问题解决。

别急，`isNaN(..)` 有个严重的问题，它取了 `NaN` 的字面意思 —— “不是一个数字”，所以：

```js
var a = 2 / "foo";
var b = "foo";

a; // NaN
b; // "foo"

window.isNaN( a ); // true
window.isNaN( b ); // true -- ouch!
```

很明显，`"foo"` 字面上理解**不是 `numebr`**，但是它也绝对不是 `NaN`，这个bug在JS刚出现时就已经存在（大概 19 年了）。

到了ES6，终于提出了一个工具方法 `Number.isNaN(..)`，一个简单的polyfill让你在不兼容ES6的浏览器上也能检测 `NaN`：

```js
if (!Number.isNaN) {
	Number.isNaN = function(n) {
		return (
			typeof n === "number" &&
			window.isNaN( n )
		);
	};
}

var a = 2 / "foo";
var b = "foo";

Number.isNaN( a ); // true
Number.isNaN( b ); // false -- phew!
```

事实上，我们可以利用 `NaN` 的**不等于自身**这个特性来实现polyfill：

```js
if (!Number.isNaN) {
	Number.isNaN = function(n) {
		return n !== n;
	};
}
```

很奇怪是吧，但它可行！

`NaN` 在真实JS编程里很可能会出现，有意或无意的，所以最好是使用可靠的检测比如 `Number.isNaN(..)`（或者polyfill），来识别它们。

If you're currently using just `isNaN(..)` in a program, the sad reality is your program *has a bug*, even if you haven't been bitten by it yet!

#### Infinities

传统编译语言的开发者比如C，可能习惯于看到编译错误或者运行时异常，比如「除以0」的操作：

```js
var a = 1 / 0;
```

然而，在JS中，这种运算被定义为值为`Infinity`（即 `Number.POSITIVE_INFINITY`）：

```js
var a = 1 / 0;	// Infinity
var b = -1 / 0;	// -Infinity
```

如你所见：`-Infinity`（即 `Number.NEGATIVE_INFINITY`）是由负数除以 0 产生的。

JS使用有限的数字表示（IEEE 754 浮点数，我们前面已经说过），所以对比单纯的数学运算，在加减运算中它都可能会溢出，这样你会得到 `Infinity` 或 `-Infinity`。

比如：

```js
var a = Number.MAX_VALUE;	// 1.7976931348623157e+308
a + a;						// Infinity
a + Math.pow( 2, 970 );		// Infinity
a + Math.pow( 2, 969 );		// 1.7976931348623157e+308
```

根据规范，如果一个运算比如加法的结果太大无法表示，IEEE 754 「舍入到最近」模式规定了它的值应该是什么，所以粗略看来，`Number.MAX_VALUE + Math.pow( 2, 969 )` 更接近 `Number.MAX_VALUE` 而不是 `Infinity`，所以它被「向下舍去」，而 `Number.MAX_VALUE + Math.pow( 2, 970 )`更接近 `Infinity`，所以它被「向上近入」。

如果你过多思考这个问题，你会觉得头疼，所以就到这里吧。

一旦溢出到**无限**，就无法返回到有限。

一个哲学意义上的提问：「无限除以无限是什么」，你可能会觉得是「1」，或者「无限」，然而都不对，在数学范畴和JavaScript范畴，`Infinity / Infinity` 不是一个有定义的运算，在JS中，它的结果是 `NaN`。

那么如果是其它正 `number` 除以 `Infinity` 呢？很简单！ `0`。那负 `number` 除以 `Infinity` 呢？请继续阅读。

#### Zeros

这可能会让数学思维的读者感到困惑，JavaScript中有一般的 `0`（即正零 `+0`）**和**负零 `-0`。在我们解释为何会有 `-0` 之前，我们先看看JS是如何处理它的，因为它相当有迷惑性：

除了直接赋值为 `-0`，负零还可能是来自特定的数学运算，比如：

```js
var a = 0 / -3; // -0
var b = 0 * -3; // -0
```

加法和减法不会生成负零。

开发者控制台输出负零得到 `-0`，其它老的浏览器会得到 `0`。

然而，如果你尝试将负零字符串化，你会得到 `"0"`：

```js
var a = 0 / -3;

// (some browser) consoles at least get it right
a;							// -0

// but the spec insists on lying to you!
a.toString();				// "0"
a + "";						// "0"
String( a );				// "0"

// strangely, even JSON gets in on the deception
JSON.stringify( a );		// "0"
```

但从 `string` 负零到 `number` 会保持负号：

```js
+"-0";				// -0
Number( "-0" );		// -0
JSON.parse( "-0" );	// -0
```

**警告：**  当你对比 `JSON.stringify( -0 )` 得到 `"0"` 和 `JSON.parse( "-0" )` 得到 `-0` 这两个不一致的结果时，你会觉得比较奇怪。

除了字符串化负零会隐藏真实值外，比较操作符也会。

```js
var a = 0;
var b = 0 / -3;

a == b;		// true
-0 == 0;	// true

a === b;	// true
-0 === 0;	// true

0 > -0;		// false
a > b;		// false
```

很明显，如果你想要在代码里区分 `-0` 和 `0`，你不能止依赖开发者控制台的输出，你需要更聪明些：

```js
function isNegZero(n) {
	n = Number( n );
	return (n === 0) && (1 / n === -Infinity);
}

isNegZero( -0 );		// true
isNegZero( 0 / -3 );	// true
isNegZero( 0 );			// false
```

现在我们看看为什么需要负零。

在某些应用里，开发者需要使用梯度值来表示一段信息（比如每一帧动画的移动速度），`number` 的符号代表了它运动方向的信息。

在这些应用里，举个例子，如果一个变量成了零值并且丢失了符号，那么你就无法知道它是从哪个方向成为零的，为零值保留符号就是为了减少不必要的信息丢失。

### 特殊的相等

像前面提到的，`NaN` 值和 `-0` 值在做比较时会有特殊性，`NaN` 不等于自身，所以我们使用 `Number.isNaN(..)` 来检测，同样的，`-0` 与 `0` 是严格相等的，可以通过 `isNegZero(..)` 来检测。

ES6里有更便利的方法 `Object.is(..)`，它比较两个值是否相等：

```js
var a = 2 / "foo";
var b = -3 * 0;

Object.is( a, NaN );	// true
Object.is( b, -0 );		// true

Object.is( b, 0 );		// false
```

下面是一个简单的 `Object.is(..)` 的polyfill：

```js
if (!Object.is) {
	Object.is = function(v1, v2) {
		// test for `-0`
		if (v1 === 0 && v2 === 0) {
			return 1 / v1 === 1 / v2;
		}
		// test for `NaN`
		if (v1 !== v1) {
			return v2 !== v2;
		}
		// everything else
		return v1 === v2;
	};
}
```

`Object.is(..)` 可能不应该被用于已知的**安全**的 `==` 或 `===`（参考第4章的“强转”），因为操作符可能更高效一些，当然也更常见。`Object.is(..)` 常用于一些特殊场景的相等比较。

## Value vs. Reference

在很多其它语言中，值可以根据语法通过值的复制或引用的复制被赋／传递。

比如，在C++中，如果你想要把 `number` 变量传给一个函数，然后修改它，你可以把函数的参数声明为 `int& myNum`，当你传入一个值比如 `x`时，`myNum` 就是**指向 `x`** 的引用，引用就好像特殊形式的指针，你获得了一个指向另一个变量的指针（或者叫**别名**）。如果你声明的不是一个引用参数，那么传入的值总是会被复制，即使它是复杂的对象。

而JavaScript里没有指针，引用也有所不同，你无法用一个变量引用另一个。
In JavaScript, there are no pointers, and references work a bit differently. You cannot have a reference from one JS variable to another variable. That's just not possible.

JS里的引用指向（共享的）**值**，所以你可以有10个不同的引用，它们指向一个共享的值，**它们互相之间没有引用或指针**。

而且，在JS里，值的赋值／传递和引用的没有语法上的区别，相反，值的**类型**决定了是值复制还是引用复制。

我们看代码：

```js
var a = 2;
var b = a; // `b` is always a copy of the value in `a`
b++;
a; // 2
b; // 3

var c = [1,2,3];
var d = c; // `d` is a reference to the shared `[1,2,3]` value
d.push( 4 );
c; // [1,2,3,4]
d; // [1,2,3,4]
```

简单值**总是**通过值复制来传递／赋值：`null`、`undefined`、`string`、`number`、`boolean` 以及ES6的 `symbol`。

复合值 `object`（包括 `array`，以及所有基本类型的封装对象，参考第3章）和 `function` 在传递或赋值时**总是**新建引用复制。

在上述代码片段里，因为 `2` 是一个基本值，`a` 持有该值的原始副本，然后 `b` 被赋值一个该值的副本，当修改 `b` 时，你不可能修改到 `a`。

但是 **`c` 和 `d`** 是对相同的共享值 `[1,2,3]` 的不同引用，即一个复合值。需要指出的是，无论 `c` 还是 `d` 「不拥有」`[1,2,3]` 这个值，它俩只是都引用了这个值。所以，使用任何一个引用修改（`.push(4)`）共享的 `array` 时，修改的就是共享的值，而且两者都是引用这个修改后的值 `[1,2,3,4]`的。

因为引用指向的是值本身而不是变量，你无法使用一个引用来修该另一个引用指向谁：

```js
var a = [1,2,3];
var b = a;
a; // [1,2,3]
b; // [1,2,3]

// later
b = [4,5,6];
a; // [1,2,3]
b; // [4,5,6]
```

当我们进行 `b = [4,5,6]` 赋值时，我们丝毫没有影响到 `a` 所指向的东西（`[1,2,3]`），而要做到这一点，`b` 应该是指向 `a` 的指针，而不是引用 `array`，但JS里没有这种能力！

这种困惑经常发生在函数参数中：

```js
function foo(x) {
	x.push( 4 );
	x; // [1,2,3,4]

	// later
	x = [4,5,6];
	x.push( 7 );
	x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // [1,2,3,4]  not  [4,5,6,7]
```

当传入 `a` 时，它将 `a` 的引用赋值给了 `x`，`x` 和 `a` 是指向共享值 `[1,2,3]` 的不同引用，然后在函数里，我们可以使用这个引用修改值（`push(4)`），但是当我们做了 `x = [4,5,6]` 这样的赋值后，它对 `a` 引用没有任何影响，它仍然指向 `[1,2,3,4]`。

不可能通过 `x` 引用来修改 `a` 的引用，我们只能修改 `a` 和 `x` 指向的共享值。

为了完成修改 `a` 到 `[4,5,6,7]` 这个任务，你不能新建 `array` 然后赋值，你只能修改已有的 `array`：

```js
function foo(x) {
	x.push( 4 );
	x; // [1,2,3,4]

	// later
	x.length = 0; // empty existing array in-place
	x.push( 4, 5, 6, 7 );
	x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // [4,5,6,7]  not  [1,2,3,4]
```

你也看到了，`x.length = 0` 和 `x.push(4,5,6,7)` 没有新建 `array`，而是修改已有的共享数组，所以 `a` 当然引用的是新内容 `[4,5,6,7]`。

记住：你不能直接控制／重写值的复制和引用的，这些都只是由其内部的值来控制的。

如果你想传递一个值复制的复合值，你需要新建一个副本，从而传递的引用不再指向最初的值：

```js
foo( a.slice() );
```

没有传入参数的函数 `slice(..)` 默认（浅）拷贝一份 `array`。所以我们传入的是引用的 `array` 的副本，这样 `foo(..)` 就无法修改 `a` 的内容了。

反过来，如果想传入基本值，并让它能够被修改，像引用那样，你需要将它封装成复合值（`object`、`array` 等），这样就成了引用复制了：

```js
function foo(wrapper) {
	wrapper.a = 42;
}

var obj = {
	a: 2
};

foo( obj );

obj.a; // 42
```

这里，`obj` 扮演了一个基础值属性 `a`的包装，我们可以使用这个包装的引用来访问共享的对象，以及修改它的属性，函数运行结束后，`obj.a` 就可以拿到修改后的值 `42`了。

你可能觉得如果传入的是 `2` 这样的基础值，那么你可以使用它的对象包装  `Number`（见第3章）。**的确**这个 `Number` 的引用会被传入函数，但是，引用共享的对象并不能像你预期的那样提供给你修改共享的基本值的能力：

```js
function foo(x) {
	x = x + 1;
	x; // 3
}

var a = 2;
var b = new Number( a ); // or equivalently `Object(a)`

foo( b );
console.log( b ); // 2, not 3
```

问题出在底层的基本值是**不可修改**的，（`String` 和 `Boolean`也是）。如果 `Number` 对象持有基本值 `2`，这个 `Number` 对象将无法被修改为持有其它值，你只能使用不同的值来新建 `Number` 对象。

当 `x` 被用于 `x + 1` 表达式时，底层的基本值 `2` 被从 `Number` 对象里自动拆箱，所以 `x = x + 1` 这行无法将 `x` 从一个共享引用变成 `Number` 对象的引用，进而持有基础值 `3` 作为加法运算 `2 + 1` 的结果。因此，在外部的 `b` 仍然引用的时原始的没有修改／不可修改的 持有值为 `2` 的 `Number` 对象。

你**可以**给 `Number` 对象增加属性（不修改它内部基本值），从而你就可以间接地通过这些属性交换信息。

这并不常用，而且也不被认为是好的方法。

不使用像上面那样使用 `Number` 装箱对象，而使用更好的可修改对象包装方式。这并不是说，装箱对象没有聪明的用处，而是说你在大多数场景下，你更愿意使用基本值。

引用很强大，但有时它们会阻碍你，有时你在没有这些东西时需要它们，你能控制的只有值类型，所以你应该通过决定值类型来间接的影响赋值／传值行为。

## Review

在JS里，`array` 是数字索引的任何值类型的集合，`string` 虽然有些像 `array`，但它们还是有区别的，所以当你把它用作数组时一定要小心。JS中的数字包括整数和浮点数。

几个特殊的值是基础类型。

`null` 类型有自己的值 `null`， `undefined` 也是，`undefined` 是当变量或属性没有其它值时的默认值，`void` 操作符让你可以用任何值生成 `undefined`值。

`number` 中有几个特殊值，比如 `NaN`（说的不是「非数」，而是「非法数字」），`+Infinity`、`-Infinity`、`-0`。

简单的基础值（`string`、`number` 等）是值拷贝来传递、赋值的，而符合值（`object` 等）则是引用拷贝传递／赋值的。引用不像其它语言里的引用／指针，它们不会指向其它变量／引用，只会指向真实的值。
