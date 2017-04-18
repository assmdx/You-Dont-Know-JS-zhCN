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

**注意:** 想更深入了解*条件*语句的验证表达式（if中的()）中的隐式强转，参见卷*Types & Grammar*的第四章。

## Loops（循环）

高峰时期，顾客需要排队才能与手机店员讲到话，如果一直有人排队，她就需要继续服务。重复某些个行为直到条件不符合，或者说当条件满足时一直重复，这就是编程中循环的工作，循环可以有不同的形式，但它们都满足这个基本行为。

一个循环包括条件验证和一个块语句（还记得`{ .. }`吧），每一次循环块语句的执行称为一次*迭代*。比如，`while`循环和`do..while`循环表示重复一个块语句知道条件不再等于`true`：

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

这两个循环的唯一区别就是在验证条件之前是（`do..while`）否（`while`）先执行一次块语句。当条件验证为`false`时，下一次迭代就不会执行，也就是说，如果条件一开始就是`false`，`while`循环根本不会执行，而`do..while`循环会运行（第）一次。

有时候，你会有意通过一个计数器来进行循环，比如从`0`到`9`（10个数字），你可以通过设置循环变量比如`i`值为`0`并且在每次迭代增加`1`。

**注意:** 历史问题，几乎所有的编程语言的计数都是从`0`开始，而不是`1`。如果你不熟悉这种思维模式，一开始可能会有困惑，花些时间练习从`0`开始的计数并熟悉它吧！

条件在每一次迭代都会验证，就好像在循环里有一个隐藏的`if`语句，可以使用JS的`break`语句来停止某个循环，如果没有`break`机制，那么你会发现创建一个永不会停的循环是那么的容易，像这样：

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

**注意:** 这不是在实际中你想要用到的循环，这里只是为了说明问题。

除了`while` (或`do..while`) 可以自动完成任务，还有`for`循环也可以：

```js
for (var i = 0; i <= 9; i = i + 1) {
	console.log( i );
}
// 0 1 2 3 4 5 6 7 8 9
```

如你所见，两个代码示例中的条件`i <= 9`在前10次迭代中始终为`true`，但是当`i`等于`10`的时候，条件为`false`。

`for`循环有三个子句：初始化子句（`var i=0`）、条件验证子句（`i <= 9`）、更新子句（`i = i + 1`）。所以如果你想在循环中计数，`for`循环可能是更好的选择。

还有一些为迭代特定值的特殊循环形式，如对象的属性（参见第二章），隐式条件验证是是否所有的属性已经被处理，但无论形式如何变化，“循环直到条件不符”的原理不会变。

## Functions（函数）

手机店店员可能不会随身携带计算器来计算出要缴纳的税以及最终需要支付的金额，这是一个她需要定义一次，然后重复利用的任务，公司有一个结算台（计算机、平板等）有这些“功能”。

类似的，你的程序当然希望把代码的任务划分为可以重复利用的片段，而不是不断的重复再重复，我们称这为`function`，一个函数一般是一段有命名的可以被再次调用的代码，每次调用，函数内的代码就会被执行：

```js
function printAmount() {
	console.log( amount.toFixed( 2 ) );
}

var amount = 99.99;

printAmount(); // "99.99"

amount = amount * 2;

printAmount(); // "199.98"
```

函数还可以接受参数（你传递给函数的值），函数还可以返回一个值。

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

函数`printAmount(..)`接受一个叫`amt`的参数，函数`formatAmount()`返回一个值，当然，你可以将两者同时使用。

函数经常被用于你想要多次使用的代码，即使是只使用一次，把相关代码放到一个命名的集合里也是很有用的：

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

尽管`calculateFinalPurchaseAmount(..)`只被调用了一次，将它的行为划分到一个命名的函数里，代码看起来逻辑更清晰（`amount = calculateFinal...`语句）。如果函数内有更多语句，那这种优势就更加明显。

### Scope（作用域）

如果你要求手机店店员给你它们专柜不销售的机型，她将无法卖给你，她只能获取到她的店铺中的手机，你可能需要到另一家手机店去寻找你所需要的机型。

编程中对这样的概念有个术语叫*作用域*（技术上称*词法范围*），在JS中，每个函数有自己的作用域，作用域是指变量和通过名字如何访问这些变量的规则的集合。只有函数内部代码才可以访问到函数*作用域*内的变量。

同一个作用域内变量名必须唯一，不可能存在两个相邻的不同变量`a`，但是同一个名为`a`的变量可以出现在不同的作用域。

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

而且，作用域可以嵌套于另一个作用域，正如在生日趴的小丑在一个气球里吹出另一个气球，如果一个作用域被嵌套于另一个，最内部的作用域可以访问到外部作用域的变量，如：

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

词法范围规则说明作用域内的代码可以访问自身作用域以及任意外部作用域里的变量，所以函数`inner()`内可以访问到变量`a`、`b`，但是`outer()`内只能访问到`a`，无法访问`b`因为该变量位于`inner()`里，回顾前面的一段代码：

```js
const TAX_RATE = 0.08;

function calculateFinalPurchaseAmount(amt) {
	// calculate the new amount with the tax
	amt = amt + (amt * TAX_RATE);

	// return the new amount
	return amt;
}
```

即使没有将常量`TAX_RATE`传递给函数`calculateFinalPurchaseAmount(..)`，但从其内部仍然可以访问到，这就是此法范围。

**注意:** 了解更多词法范围，查阅卷*Scope & Closures*的前三章。

## Practice（练习）

学习编程除了练习别无它法，在我看来，没有一定的代码量，你不可能成为一个程序员。让我们关于本章新学的一些概念进行练习吧，我会给出“要求”，然后你自己尝试完成，最后参考后面我的实现代码。

* 编写一个计算你购买手机总花费的程序，你将会一直购买（提示：循环！）直到你的钱不够了，只要你的消费额低于你的预算门限，你也会为每一个手机购买配件。
* 当你计算出你的消费金额后，加上缴税额，然后以合适的格式打印出来。
* 最后，检查你的银行账户存款看自己是否能支付得起。
* 你应该为“税率”、“手机价格”、“配件价格”、“预算”以及“银行账户存款”建立常量。
* 你应该为计算缴税额、以“￥”格式化价格并保留小数点后两位而定义函数。
* **额外挑战:** 试试将input引入到这个程序中，也许使用前面”Input"中讲到的`prompt(..)`，比如提示用户输入银行账户存款，随便你创造！

好了，开始你的表演吧，在你完成之前不要偷看我的代码哦~

**注意:** 因为这是一本关于JS的书，所以我使用JS来完成练习，但是你可以使用其它你更喜爱的语言。

以下是我的作业：

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

**注意:** 执行这段代码的最简单方式就是将它输入到浏览器的console里。

练习的如何？看了我的代码，你可以运行一下它，并且改变常量看看程序的运行情况。

## Review（回顾）

学习编程不应该是复杂的且让人挫败的过程，这里有几个基本概念你应该记住。

这些就好像堆积木，为了建一座高塔，你最开始就是不断累积一块到另一块，编程亦如是，下面是一些基本的编程积木块：

* 你需要*操作符*来实现值之间的行为。
* 你需要值和*类型*来实现不同的行为比如`number`的计算，`string`的输出。
* 你需要*变量*来存储程序运行时的数据（也就是*状态*）。
* 你需要*条件*比如`if`语句来做决策。
* 你需要*循环*来重复某些任务直到条件不再是`true`。
* 你需要*函数*来组织你的代码，让它更加清晰和可复用。

代码注释是编写高可读代码的一个有效方式，注释会让你的程序易于理解、维护以及解决问题（如果有的话）。

最后，不要忽视练习的力量，学写代码的最佳方式就是写代码。

很高兴你已经在学习如何编程的道路上步履稳定起来，继续保持，不要忘了查看其它关于编程初学的资源（书、博客、在线培训等）。本章及这本书是一个好的开始，但它们只是一个简短的介绍。

下一章会复习本章的很多概念，但是是从JS语言的角度出发，这些概念也是在其余系列中的将会进行深入详细探讨的主题。
