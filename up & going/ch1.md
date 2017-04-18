# You Don't Know JS: Up & Going
# Chapter 1: Into Programming

欢迎阅读*You Dont't Know JS*(*YDKJS大法*)系列丛书。

*Up & Going*针对初涉编程或JS的同学，以JS为例介绍了一些基本编程概念以及如何修炼*YDKJS大法*的其余卷。本书开头以高逼格解释编程的基本原则，旨在让没有编程经验的你通过JS这个管子的引导来窥探一下编程之豹。

第一章是对于学习并着手编程需要所要东西的快速浏览，网上有很多给力的介绍编程的资源能帮助你更深入理解这些主题，建议你也学习一下。

当你熟悉了编程基础后，第二章将带领你熟悉JS编程，该章介绍JS概念，再次强调：第2章并不是全面指南，这是*YDKJS大法*系列通关后的套装属性。

如果你已经熟悉JS，首先阅读第3章，直接翻到*YDKJS大法*中你感兴趣的章节。

## Code（代码）

一个程序，是一个告诉计算机完成什么任务的特殊指令集合，也被称作*源代码*或者*代码*。通常代码被保存在文本文件中，但JS可以直接在浏览器的console中输入（后面会讲到）。有效格式和指令结合的规则称为*计算机语言*，也称为*语法*，和汉语告诉你字词来造句然后写文章什么的差不多。

### Statements（语句）

在计算机语言中，由词、数字和操作符组成的完成一个具体任务的群体称为*语句*，在JS中，一条语句可能如下所示：

```js
a = b * 2;
```

字符`a`和`b`称为*变量*，像可以储物的盒子。在编程中，变量保存被会程序使用的值（比如数字`42`），可以将它们看成是不同值的象征性占位符。`2`只是值本身，叫做*字面值*，它没有被保存在变量中而孤立存在。


The `=` and `*` characters are *operators* (see "Operators") -- they perform actions with the values and variables such as assignment and mathematic multiplication.

`=`和`*`是*操作符*，它们表示值和变量之间的行为，比如赋值、乘法。大多数JS语句以`;`结束。`a = b * 2;`告诉计算机：获取变量`b`当前存储的值，乘以值`2`，然后将计算结果保存在另一个叫`a`的变量中。程序就是这些语句的集合，它们一起描述实现程序目的的所有步骤。

### Expressions（表达式）

语句由一个或多个*表达式*组成，一个表达式可以是对变量、值得引用，或者变量与值通过操作符的集合。比如：

```js
a = b * 2;
```

这条语句包含四个表达式：

* `2` 是一个*字面值表达式*
* `b` 是一个*变量表达式，意思是获取该变量的当前值
* `b * 2` 是一个*算术表达式*，意思是做乘法运算
* `a = b * 2` 是一个*赋值表达式*，意思是将表达式`b * 2`的计算结果保存到变量`a`中（关于赋值后面会详细讲解）

一个孤立的被称为*表达式语句*，比如：

```js
b * 2;
```
这种风格的表达式语句并不常见或有用，因为一般它不会对正在运行的程序有任何影响，它会获取`b`的值并乘以`2`，然后用计算结果什么都没做。

更普遍的表达式语句是*调用表达式*语句（参考“Functions”），整条语句就是函数的调用表达式本身：

```js
alert( a );
```

### Executing a Program（执行程序）

这些编程语句的集合是如何告诉计算机该做什么的？程序需要被*执行*，也叫*运行程序*。像`a = b * 2`这样的语句，对开发人员来说读写很方便，但是并不是计算机可以直接理解的形式，所以在计算机上一个特殊的工具（*编译器*或*解释器*）被用来将你写的代码翻译成计算机可以理解的指令。

对某些计算机语言来说，翻译就是每一次程序运行时，从顶部到底部逐行完成，即*解释*代码。对其它语言而言，翻译在执行之前完成，称为*编译*代码，当程序*运行*时，实际上执行的是已经编译好的计算机指令。

JS是*解释*型语言，因为你的JS源代码每次运行时被处理，但也不完全是，JS引擎实际上在运行时*编译*程序然后立即执行编译后的代码。

**注意：** 了解更多关于JS的编译知识，阅读系列中名为*Scope & Closures*的前两章。

## Try It Yourself（动手）

本章将会以小段代码来介绍每一个编程概念。强调一下：当你翻阅本章时（你可能会多次翻阅），你应该动手练习每一个概念，最简单的方式就是打开浏览器的开发者模式，使用console窗口。

**提示：** 通常，你可以通过按下快捷键或点击一个菜单来启动开发者console，了解更多如何在浏览器中使用console，参考"Mastering The Developer Tools Console" (http://blog.teamtreehouse.com/mastering-developer-tools-console) 。为了多行输入，可以按下 `<shift> + <enter>`，一旦按下了`<enter>`，console会执行你输入的所有东西。

让我们熟悉一下在console中执行代码，首先，建议在浏览器中打开一个新的标签（快捷键一般是`<ctrl> + <T>`，或者在地址栏输入`about:blank`），然后打开console（快捷键一般是`F12`），输入如下代码：

```js
a = 21;
b = a * 2;
console.log( b );
```

Chrome浏览器中的运行结果如下:

<img src="fig1.png" width="500">

嗯，不错继续练习吧，学习编程的最好方式就是开始写代码！

### Output（输出）

在前一个代码片段中我们使用了 `console.log(..)`，我们来看下它是什么意思。你可能已经猜到了，它正是在console下打印文本（也称为*输出*给用户），该语句有两点要解释一下：

第一，`log( b )`是函数调用，将变量 `b`传递给该函数，函数内部功能是：获取`b`的值，然后将它打印到console中。

第二，`console.`是函数`log(..)`定位的对象引用，我们会在第二章详细讲解对象以及他们的特性。

另一种生成可见输出的方式是运行`alert(..)`语句，比如：

```js
alert( b );
```

执行后你会发现，它并不是将结果打印在console里，而是弹出内容是`b`的值的确认对话框，然而，使用`console.log(..)`更方便一些，因为你可以一次性输出很多值，而且不会像`alert(..)`那样中断浏览器的交互。

### Input（输入）

当我们讨论输出时，你可能好奇*输入*（即从用户那里获取信息）。最常见的方式就是通过HTML页面上的用户可以输入的表格元素（比如文本框），使用JS将这些值读取到你的程序中的变量里。

但是还有最简单的用来学习或演示的获取输入的方式，使用函数`prompt(..)`：

```js
age = prompt( "Please tell me your age:" );

console.log( age );
```

传递给`prompt(..)`的值会打印在弹出框里，本例中的`"Please tell me your age:"`：

<img src="fig2.png" width="500">

当你通过点击“OK”提交输入文本后，你会观察到你输入的值被存储在`age`中，然后通过*输出* `console.log(..)`打印出来：

<img src="fig3.png" width="500">

简单起见，在我们学习基础编程概念时，本书的示例不会需要输入。

## Operators（操作符）

操作符代表在变量和值之间执行某个操作，我们已经见过JS的两个操作符：`=`和`*`。 `*` 操作符代表数学乘法，操作符`=`用于*赋值*，首先计算`=`右边的值（源值），然后将它存储到左边的变量（目标变量值）中。

**注意：** 这看起来似乎和一般的赋值顺序是相反的，有些人可能更习惯使用如`42 -> a`（这不是有效的JS！）这样的源值在左目标变量在右方式，而不是`a = 42`。然而，`a = 42`类似的表示，在现代编程语言中很流行，所以，如果你感觉不自然，花些时间来练习直到习惯它吧。

思考:

```js
a = 2;
b = a + 1;
```

这里，将`2`赋值给`a`，然后获取`a`的值，加`1`，得到结果`3`，然后将它存储到变量`b`中。
`var`虽然不是操作符，但是在每个程序中都需要关键字`var`，因为它是你*声明*变量（详见Variables）的主要方式。你应该在使用变量之前声明它的名字，但是在同一个*作用域*（详见Scope）只需要声明一次，之后可以按需使用多次，比如：

```js
var a = 20;

a = a + 1;
a = a * 2;

console.log( a );	// 42
```

以下是JS中常见的操作符:

* 赋值：`=` 比如：`a = 2` 。
* 计算：`+` (加)、 `-` (减)、`*` (乘)、`/` (除)，如`a * 2`。
* 复合赋值：`+=`、`-=`、`*=`、`/=` 都是结合计算和赋值的复合操作符，比如`a += 2`等价于`a = a + 2`。
* 自增/减：`++` (自增), `--` (自减)，比如`a++` 等价于`a = a + 1`。
* 对象属性获取：`.` 如 `console.log()`。

   对象是指在特殊命名位置（属性）的保存其它值的值，`obj.a`表示一个叫`obj`的对象值的一个叫`a`的属性，也可以通过`obj["a"]`访问属性，详见第二章。
* 相等: `==` 宽松等于，`===` 严格等于，`!=` 宽松不等于，`!==` 严格不等于，比如`a == b`。

   详见"Values & Types"和第二章。
* 大小: `<` 小于, `>` 大于, `<=` 小于等于, `>=` 大于等于，比如`a <= b`。

   详见"Values & Types"和第二章。
* 逻辑: `&&` 与, `||` 或, 比如`a || b` 选择 `a` *或* `b`。

   这些操作符用于表示复合条件（见"Conditionals"），比如是否`a` *或* `b` 为真。

**注意:** 了解更详细知识或以上未提及操作符，参考Mozilla Developer Network (MDN)'s "Expressions and Operators" (https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators) 。

## Values & Types（值和类型）

当你在一个手机店铺询问店员某个手机多少钱时，他们回答“1999”（即￥1999），他们给你一个你需要支付的真实的人民币数值。如果你想要买2个，你可以很容易新算出需要花费“￥3998”。如果那个店员拿起另一部相同的手机却说它是“免费”的（可能是加了双引号的），他们没有明确的给你一个数字，而是另一种你知道花费是"￥0"的表达——“免费”。当你又问手机是否包含充电器，答案可能只是“是”或“否”。

同样的，当你在程序中表达一个值时，你可以根据它们的用途而选择不同的表述，这些值得不同表述在编程术语中称为*类型*，JS有针对这些*原*值的内置类型：

* 当你需要做计算时，你需要`number`。
* 当你需要打印值到屏幕上时，你需要`string` （一个或多个字符，单词，句子）。
* 当你需要在程序中做判断时，你需要`boolean` (`true` 或 `false`)。

直接包含在源代码中的值被称为*字面值*，`string`字面值由单引号(`'...'`)或双引号(`"..."`)包裹（两者没有区别），`number`和`boolean`字面值以其本身表现（即如：`42` `true`等）。

示例:

```js
"I am a string";
'I am also a string';

42;

true;
false;
```

除了 `string`/`number`/`boolean` 类型，编程语言还有比较常用的类型，如*arrays*, *objects*, *functions*等，通过本章和下一章的学习你会了解更多。

### Converting Between Types（类型转换）

如果想输出`number`到屏幕，你需要将其转换为`string`，在JS中，这种转换叫做"强制转换"（简称“强转”），同样的，从网页输入的一串数字字符是`string`，但是如果想要进行数学运算，你需要将它*强转*为`number`。

JS为*类型*之间的强制转换提供很多便利方式，比如：

```js
var a = "42";
var b = Number( a );

console.log( a );	// "42"
console.log( b );	// 42
```

使用 `Number(..)` (一个内置函数)如上示例是将任意类型*显式*强转为`number`类型，这很简单粗暴，但是当需要比较两个不同类型的值时，会存在一个争议问题（统一转到到哪个类型再比较？），这时需要*隐式*强转，比如：当比较字符串`"99.99"`和数字`99.99`时，大家普遍认为它们应该相等，但是它们严格来说并不一样是吧。这是同一个值得两种不同的表述（两种不同*类型*），你可以说它们是“宽松相等的”，为了帮助你解决这种情况，JS提供了*隐式*强转将值转为匹配的类型。如果你使用了宽松等于操作符`==`来比较`"99.99" == 99.99`，JS会将左边的`"99.99"`转为`number`，比较结果是`true`。

尽管这样设计帮助你解决了上述问题，但是当你没有花时间去学习这些控制其行为的规则时，隐式强转会让你产生困惑，很多JS开发人员就是这样，所以公认隐式强转是令人迷惑的会因为预料外的bugs破坏程序，所以应该被避免使用，有时甚至认为它是该语言的设计缺陷。隐式强转是一种对任何想要严肃对待JS编程的人来说*可以学*且*应该被学*的机制，不仅仅是当你学习了它的规则后不再疑惑，而是它可以让你编程的更好，你的值得拥有！

**注意:** 关于强转详见第二章和卷*Types & Grammar*的第四章。

## Code Comments（代码注释）

手机卖店的店员可能会略记下新发布的手机的特性或他们公司提供的新套餐，这些笔记只给店员用的（不是给顾客阅读的），通过编排出她应该告诉顾客hows和whys，这些笔记帮助店员更好的工作。学习写代码中最重要课程之一就是，写代码不仅仅给计算机，也同样是给其它开发人员的。

计算机只关心一串串*编译*而来的二进制机器代码，你可以写出几乎无限种生成同样的01序列的程序，你如何写代码不仅仅对你，对你的团队其它人员以及未来的你同样重要。你不应该只是把代码写到功能正确，更要写到当被审阅时有意义，在选择好的变量（参考“Variales”)和函数（参见“Functions”）命名的努力中，你可以走的更远。

一个很重要的部分就是代码注释，它是插入到程序中的一段只给人类看的解释文本，编译器会忽略这些注释。有很多关于关于良好注释代码的观点，我们无法定义一个绝对的通用规则，但是一些调查报告和指导方针很有用：

* 没有注释的代码是次优的。
* 多度注释（比如一行代码一行注释）可能意味着低质量代码。
* 注释应该解释代码*why*，而不是*what*，当代码特别让人迷惑时注释可以选择性的解释*how*。

JS中，有两种注释方式：单行注释、块注释。如:

```js
// This is a single-line comment

/* But this is
       a multiline( i.e. block)
             comment.
                      */
```

当你在一个语句上或行尾注释时，单行注释`//`就很方便，`//`所在行`//`后面的所有内容都会被当做注释（编译器忽略）:

```js
var a = 42;		// 42 is the meaning of life
```

当你有多行注释要写时，请使用块注释`/* .. */`，如:

```js
/* The following value is used because
   it has been shown that it answers
   every question in the universe. */
var a = 42;
```

块注释也可以出现在一行中的任意位置，甚至行内，因为`*/`会结束注释，如：

```js
var a = /* arbitrary value */ 42;

console.log( a );	// 42
```

块注释中不能出现`*/`,因为它会被解释为注释的结束。

你肯定在学习编程之初就想要养成注释的好习惯，在后续章节，你会看到我用注释解释一些东西，所以在你的练习中也同样进行，相信我，每一个读你代码的人都会感谢你。

## Variables（变量）

大多数有用的程序都需要跟踪某个值，因为在程序中会发生改变，因为程序分配的某个任务而进行不同操作。应对这种情况最简单的方式就是将值分配给一个字符容器，即*变量*，之所以叫变量是这个容器内的值会随着时间按需改变。

有些编程语言中，可以声明一个变量（容器）来存储特定类型的值，比如`number`、`string`，这种*静态类型*，也叫*类型强制*的引入，是为了通过禁止非预期的值转换而保证程序正确性。

其它语言强调值的类型，而不是变量类型，这种*弱类型*，也叫*动态类型*，允许一个变量在任何时间任何时间保存不同类型的值，它的引入是为了保证程序的灵活性，它允许某一个变量代表一个可以在程序逻辑流程中的特定时刻取任意形式值的值。

JS使用的是*动态类型*，意味着变量可以取任何*类型*的值，而不受*类型*强制，如前所述，我们通过`var`来声明变量，注意在声明中没有*类型*信息，比如：

```js
var amount = 99.99;

amount = amount * 2;

console.log( amount );		// 199.98

// convert `amount` to a string, and
// add "$" on the beginning
amount = "$" + String( amount );

console.log( amount );		// "$199.98"
```

变量`amount`一开始取值是数字`99.99`，然后是`amount * 2`的`number`类型结果，即`199.8`，第一个`console.log(..)`将`number`类型的值隐式强转为`string`类型进而（将转换后的值）打印出来。然后语句`amount = "$" + String(amount)`将`199.98`值*显式*强转为`string`类型并且在前面添加`"$"`字符。至此，`amount`存储着`string`类型的值`"$199.98"`，所以第二个`console.log(..)`不进行任何强转直接打印。

JS开发人员会看到使用`amount`变量存储`99.99`、`199.98`、`"$199.98"`三种不同类型的值，静态类型热爱者就需要使用不同的变量，比如`amountStr`存储最终结果`"$199.98"`，因为它们是不同类型。

无论哪种方式，你会注意到`amount`保存一个随着程序而变化的运行时的值，这正好说明了变量的主要目的：管理程序*状态*。换言之，在程序运行时，*状态*跟踪值的变化。

变量的另一个用途是集中设置值，当你声明一个变量，并赋值给它，期望它在整个程序中都*不会改变*，这种叫做*常量*。一般在程序开头声明这些*常量*，这样方便你修改它，按惯例，JS中常量一般大写，单词以`_`连接，如示例:

```js
var TAX_RATE = 0.08;	// 8% sales tax

var amount = 99.99;

amount = amount * 2;

amount = amount + (amount * TAX_RATE);

console.log( amount );				// 215.9784
console.log( amount.toFixed( 2 ) );	// "215.98"
```

**注意:** 类似于`console.log(..)`是一个通过对象属性访问的函数`log(..)`，`toFixed(..)`是一个可以从`number`类型的值访问的函数。JS`number`不会自动给人民币格式化，引擎不知道你的意图，并且没有供货币使用的类型。`toFixed(..)`允许我们指定`number`类型的值被四舍五入到小数点后多少位，然后转为`string`类型并返回。

`TAX_RATE`变量就是*常量*啦，在程序中没有什么能改变它，当然如果城市将营业税提高到9%，我们可以只在一个地方给`TAX_RATE`赋值为`0.09`，轻松更新程序，而不是找很多`0.08`然后更新它们。

新版本的JS（“ES6”）包含一种新的声明*常量*的方式，使用`const`代替`var`：

```js
// as of ES6:
const TAX_RATE = 0.08;

var amount = 99.99;

// ..
```

常量就如值不会改变的变量一样有用，它还避免了首次赋值后在其它地方的意外修改，如果你在`TAX_RATE`首次声明（并赋值）后尝试去再次给它赋值，你的程序会拒绝改变它（在严格模式下，以错误提示赋值失败——参见第二章中的"Strict Mode"）。顺带一提，这种"保护"防止了一定的错误，就像静态类型中的类型强制，这也是为何静态类型在其它语言中会受欢迎。

**注意:** 了解更多变量取不同值的知识，参见本系列的卷*Types & Grammar*。

## Blocks（块语句）

当你购买手机时，手机店店员必须通过一系列步骤来完成结算，同样的，在代码中我们经常需要将一系列语句放在一起，通常称为*块*。在JS中，使用一对花括号`{ .. }`包裹一条或更多的语句来定义一个代码块，比下：

```js
var amount = 99.99;

// a general block
{
	amount = amount * 2;
	console.log( amount );	// 199.98
}
```

这种一般的代码块有效但是并不常见，代码块一般伴随着控制语句，比如`if`语句（参见“Conditionals”）或循环语句（参见"Loops"），比如：

```js
var amount = 99.99;

// is amount big enough?
if (amount > 10) {			// <-- block attached to `if`
	amount = amount * 2;
	console.log( amount );	// 199.98
}
```

我们会在下一节解释`if`语句，如你所见，将两条语句的代码块`{ .. }`附加给`if (amount > 10)`，代码块只有在条件通过时才会执行。

**注意:** 与其它如`console.log(amount);`语句不同的是：一个块语句不需要`;`来结束。

## Conditionals（条件语句）

"Do you want to add on the extra screen protectors to your purchase, for $9.99?" The helpful phone store employee has asked you to make a decision. And you may need to first consult the current *state* of your wallet or bank account to answer that question. But obviously, this is just a simple "yes or no" question.

“你是否需要购买一个屏保，只要￥40元哦？”热心的手机店店员想让你做一个选择，这时你可能需要先询问下你的钱包或者银行账户的当前*状态*再回答这个问题，但是很明显，这只是一个“是”或“否是”的问题。在程序中有很多方式可以表述*条件*（即决策）。

最常见的一个就是`if`语句，本质上就是“*if*这个条件是真，执行下面的...”，比如：

```js
var bank_balance = 302.13;
var amount = 99.99;

if (amount < bank_balance) {
	console.log( "I want to buy this phone!" );
}
```

`if`语句要求一个可以判断是`true`还是`false`的表达式包裹在`( )`内。在这段代码里，我们提供表达式`amount < bank_balance`，它实际上随着`bank_balance`的值不同而可以是`true`或`false`。

你还可以提供如果if不满足时的选择，称为`else`语句，如：

```js
const ACCESSORY_PRICE = 9.99;

var bank_balance = 302.13;
var amount = 99.99;

amount = amount * 2;

// can we afford the extra purchase?
if ( amount < bank_balance ) {
	console.log( "I'll take the accessory!" );
	amount = amount + ACCESSORY_PRICE;
}
// otherwise:
else {
	console.log( "No, thanks." );
}
```

如果`amount < bank_balance` 是 `true`，会打印出`"I'll take the accessory!"`并且给`amount`增加`9.99`，否则，`else`语句告诉我们应该礼貌回答`"No, thanks."`并且保持`amount`的值不变。

正如我们之前在"Values & Types"里讨论的，当值不是所期望的类型时，会被强转为期望类型，`if`语句期望一个`boolean`类型，但是如果你传递给它一个非`boolean`类型，强转就会发生。

JS定义了一组当被强转时取值为`false`的特殊值，如`0`、`""`。其它不在该组里的值强转为`boolean`类型时，取值`true`，比如`99.99`、`free`，了解更多参见第二章的"Truthy & Falsy"。

除了`if`语句外，还有其它*条件*，比如`swtich`语句，它可以用来简化一系列的`if..else`语句（参考第二章）。循环（参见"Loops"）使用*条件*来决定循环继续还是终止。

**注意:** 想更深入了解*条件*语句的测试表达式（if中的()）中的隐式强转，参见卷*Types & Grammar*的第四章。

## Loops（循环）

During busy times, there's a waiting list for customers who need to speak to the phone store employee. While there's still people on that list, she just needs to keep serving the next customer.

Repeating a set of actions until a certain condition fails -- in other words, repeating only while the condition holds -- is the job of programming loops; loops can take different forms, but they all satisfy this basic behavior.

A loop includes the test condition as well as a block (typically as `{ .. }`). Each time the loop block executes, that's called an *iteration*.

For example, the `while` loop and the `do..while` loop forms illustrate the concept of repeating a block of statements until a condition no longer evaluates to `true`:

```js
while (numOfCustomers > 0) {
	console.log( "How may I help you?" );

	// help the customer...

	numOfCustomers = numOfCustomers - 1;
}

// versus:

do {
	console.log( "How may I help you?" );

	// help the customer...

	numOfCustomers = numOfCustomers - 1;
} while (numOfCustomers > 0);
```

The only practical difference between these loops is whether the conditional is tested before the first iteration (`while`) or after the first iteration (`do..while`).

In either form, if the conditional tests as `false`, the next iteration will not run. That means if the condition is initially `false`, a `while` loop will never run, but a `do..while` loop will run just the first time.

Sometimes you are looping for the intended purpose of counting a certain set of numbers, like from `0` to `9` (ten numbers). You can do that by setting a loop iteration variable like `i` at value `0` and incrementing it by `1` each iteration.

**Warning:** For a variety of historical reasons, programming languages almost always count things in a zero-based fashion, meaning starting with `0` instead of `1`. If you're not familiar with that mode of thinking, it can be quite confusing at first. Take some time to practice counting starting with `0` to become more comfortable with it!

The conditional is tested on each iteration, much as if there is an implied `if` statement inside the loop.

We can use JavaScript's `break` statement to stop a loop. Also, we can observe that it's awfully easy to create a loop that would otherwise run forever without a `break`ing mechanism.

Let's illustrate:

```js
var i = 0;

// a `while..true` loop would run forever, right?
while (true) {
	// stop the loop?
	if ((i <= 9) === false) {
		break;
	}

	console.log( i );
	i = i + 1;
}
// 0 1 2 3 4 5 6 7 8 9
```

**Warning:** This is not necessarily a practical form you'd want to use for your loops. It's presented here for illustration purposes only.

While a `while` (or `do..while`) can accomplish the task manually, there's another syntactic form called a `for` loop for just that purpose:

```js
for (var i = 0; i <= 9; i = i + 1) {
	console.log( i );
}
// 0 1 2 3 4 5 6 7 8 9
```

As you can see, in both cases the conditional `i <= 9` is `true` for the first 10 iterations (`i` of values `0` through `9`) of either loop form, but becomes `false` once `i` is value `10`.

The `for` loop has three clauses: the initialization clause (`var i=0`), the conditional test clause (`i <= 9`), and the update clause (`i = i + 1`). So if you're going to do counting with your loop iterations, `for` is a more compact and often easier form to understand and write.

There are other specialized loop forms that are intended to iterate over specific values, such as the properties of an object (see Chapter 2) where the implied conditional test is just whether all the properties have been processed. The "loop until a condition fails" concept holds no matter what the form of the loop.

## Functions

The phone store employee probably doesn't carry around a calculator to figure out the taxes and final purchase amount. That's a task she needs to define once and reuse over and over again. Odds are, the company has a checkout register (computer, tablet, etc.) with those "functions" built in.

Similarly, your program will almost certainly want to break up the code's tasks into reusable pieces, instead of repeatedly repeating yourself repetitiously (pun intended!). The way to do this is to define a `function`.

A function is generally a named section of code that can be "called" by name, and the code inside it will be run each time. Consider:

```js
function printAmount() {
	console.log( amount.toFixed( 2 ) );
}

var amount = 99.99;

printAmount(); // "99.99"

amount = amount * 2;

printAmount(); // "199.98"
```

Functions can optionally take arguments (aka parameters) -- values you pass in. And they can also optionally return a value back.

```js
function printAmount(amt) {
	console.log( amt.toFixed( 2 ) );
}

function formatAmount() {
	return "$" + amount.toFixed( 2 );
}

var amount = 99.99;

printAmount( amount * 2 );		// "199.98"

amount = formatAmount();
console.log( amount );			// "$99.99"
```

The function `printAmount(..)` takes a parameter that we call `amt`. The function `formatAmount()` returns a value. Of course, you can also combine those two techniques in the same function.

Functions are often used for code that you plan to call multiple times, but they can also be useful just to organize related bits of code into named collections, even if you only plan to call them once.

Consider:

```js
const TAX_RATE = 0.08;

function calculateFinalPurchaseAmount(amt) {
	// calculate the new amount with the tax
	amt = amt + (amt * TAX_RATE);

	// return the new amount
	return amt;
}

var amount = 99.99;

amount = calculateFinalPurchaseAmount( amount );

console.log( amount.toFixed( 2 ) );		// "107.99"
```

Although `calculateFinalPurchaseAmount(..)` is only called once, organizing its behavior into a separate named function makes the code that uses its logic (the `amount = calculateFinal...` statement) cleaner. If the function had more statements in it, the benefits would be even more pronounced.

### Scope

If you ask the phone store employee for a phone model that her store doesn't carry, she will not be able to sell you the phone you want. She only has access to the phones in her store's inventory. You'll have to try another store to see if you can find the phone you're looking for.

Programming has a term for this concept: *scope* (technically called *lexical scope*). In JavaScript, each function gets its own scope. Scope is basically a collection of variables as well as the rules for how those variables are accessed by name. Only code inside that function can access that function's *scoped* variables.

A variable name has to be unique within the same scope -- there can't be two different `a` variables sitting right next to each other. But the same variable name `a` could appear in different scopes.

```js
function one() {
	// this `a` only belongs to the `one()` function
	var a = 1;
	console.log( a );
}

function two() {
	// this `a` only belongs to the `two()` function
	var a = 2;
	console.log( a );
}

one();		// 1
two();		// 2
```

Also, a scope can be nested inside another scope, just like if a clown at a birthday party blows up one balloon inside another balloon. If one scope is nested inside another, code inside the innermost scope can access variables from either scope.

Consider:

```js
function outer() {
	var a = 1;

	function inner() {
		var b = 2;

		// we can access both `a` and `b` here
		console.log( a + b );	// 3
	}

	inner();

	// we can only access `a` here
	console.log( a );			// 1
}

outer();
```

Lexical scope rules say that code in one scope can access variables of either that scope or any scope outside of it.

So, code inside the `inner()` function has access to both variables `a` and `b`, but code in `outer()` has access only to `a` -- it cannot access `b` because that variable is only inside `inner()`.

Recall this code snippet from earlier:

```js
const TAX_RATE = 0.08;

function calculateFinalPurchaseAmount(amt) {
	// calculate the new amount with the tax
	amt = amt + (amt * TAX_RATE);

	// return the new amount
	return amt;
}
```

The `TAX_RATE` constant (variable) is accessible from inside the `calculateFinalPurchaseAmount(..)` function, even though we didn't pass it in, because of lexical scope.

**Note:** For more information about lexical scope, see the first three chapters of the *Scope & Closures* title of this series.

## Practice

There is absolutely no substitute for practice in learning programming. No amount of articulate writing on my part is alone going to make you a programmer.

With that in mind, let's try practicing some of the concepts we learned here in this chapter. I'll give the "requirements," and you try it first. Then consult the code listing below to see how I approached it.

* Write a program to calculate the total price of your phone purchase. You will keep purchasing phones (hint: loop!) until you run out of money in your bank account. You'll also buy accessories for each phone as long as your purchase amount is below your mental spending threshold.
* After you've calculated your purchase amount, add in the tax, then print out the calculated purchase amount, properly formatted.
* Finally, check the amount against your bank account balance to see if you can afford it or not.
* You should set up some constants for the "tax rate," "phone price," "accessory price," and "spending threshold," as well as a variable for your "bank account balance.""
* You should define functions for calculating the tax and for formatting the price with a "$" and rounding to two decimal places.
* **Bonus Challenge:** Try to incorporate input into this program, perhaps with the `prompt(..)` covered in "Input" earlier. You may prompt the user for their bank account balance, for example. Have fun and be creative!

OK, go ahead. Try it. Don't peek at my code listing until you've given it a shot yourself!

**Note:** Because this is a JavaScript book, I'm obviously going to solve the practice exercise in JavaScript. But you can do it in another language for now if you feel more comfortable.

Here's my JavaScript solution for this exercise:

```js
const SPENDING_THRESHOLD = 200;
const TAX_RATE = 0.08;
const PHONE_PRICE = 99.99;
const ACCESSORY_PRICE = 9.99;

var bank_balance = 303.91;
var amount = 0;

function calculateTax(amount) {
	return amount * TAX_RATE;
}

function formatAmount(amount) {
	return "$" + amount.toFixed( 2 );
}

// keep buying phones while you still have money
while (amount < bank_balance) {
	// buy a new phone!
	amount = amount + PHONE_PRICE;

	// can we afford the accessory?
	if (amount < SPENDING_THRESHOLD) {
		amount = amount + ACCESSORY_PRICE;
	}
}

// don't forget to pay the government, too
amount = amount + calculateTax( amount );

console.log(
	"Your purchase: " + formatAmount( amount )
);
// Your purchase: $334.76

// can you actually afford this purchase?
if (amount > bank_balance) {
	console.log(
		"You can't afford this purchase. :("
	);
}
// You can't afford this purchase. :(
```

**Note:** The simplest way to run this JavaScript program is to type it into the developer console of your nearest browser.

How did you do? It wouldn't hurt to try it again now that you've seen my code. And play around with changing some of the constants to see how the program runs with different values.

## Review

Learning programming doesn't have to be a complex and overwhelming process. There are just a few basic concepts you need to wrap your head around.

These act like building blocks. To build a tall tower, you start first by putting block on top of block on top of block. The same goes with programming. Here are some of the essential programming building blocks:

* You need *operators* to perform actions on values.
* You need values and *types* to perform different kinds of actions like math on `number`s or output with `string`s.
* You need *variables* to store data (aka *state*) during your program's execution.
* You need *conditionals* like `if` statements to make decisions.
* You need *loops* to repeat tasks until a condition stops being true.
* You need *functions* to organize your code into logical and reusable chunks.

Code comments are one effective way to write more readable code, which makes your program easier to understand, maintain, and fix later if there are problems.

Finally, don't neglect the power of practice. The best way to learn how to write code is to write code.

I'm excited you're well on your way to learning how to code, now! Keep it up. Don't forget to check out other beginner programming resources (books, blogs, online training, etc.). This chapter and this book are a great start, but they're just a brief introduction.

The next chapter will review many of the concepts from this chapter, but from a more JavaScript-specific perspective, which will highlight most of the major topics that are addressed in deeper detail throughout the rest of the series.
