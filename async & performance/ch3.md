# You Don't Know JS: Async & Performance
# Chapter 3: Promises

在第二章，我们认识了两个使用回调来处理异步编程和并发的主要缺陷：不够线性以及缺少信任。现在我们已经很好的理解了那些问题，现在是着手解决它们的时候了。

第一个我们想要解决的问题时*控制反转*，信任太脆弱以及很容易丢失。

回顾一下，我们将程序的*继续*（运行）放在回调函数里，然后把回调转交给另一部分（也许是外部代码），然后十指交叉，静静地等着它会如期正确地触发回调。

我们这么做是因为“这是*后来*要发生的，在当前步骤完成后”。

但是如果我们可以颠倒这种*控制反转*呢？如果我们不是移交我们程序的继续运行给其它部分，而是可以其它部分可以提供一种能力让我们知道它们的任务已经完成，然后我们自己的代码决定我们接下来做什么？

这种范例就是**Promises**。

，随着开发者以及规范编写者迫切地寻找解决他们代码／设计中的回调问题的方案，Promises已经开始迅速的席卷JS世界。事实上，大多数新的被添加到JS／DOM平台的异步API都是基于Promises的。所以，很有必要深挖一下他们，不是吗？

**注意：** “立即”这个单词将会频繁的出现在本章，通常适用于一些Promise解决方案的行为。然而，本质上所有的情况里，“立即“是作业队列行为里的概念，而不是严格的同步的*当前*场景。

## What Is a Promise?

当开发者决定学习某种技术或模式时，通常第一步都是"Show me the code!"，我们首先就不假思索开始学习也是很自然的一件事。

但结果是API中的一些抽象东西丢失了。Promises就是其中的一个工具，很明显地，一些人使用它却没有理解它是什么和为了什么，而只是学习然后使用这个API。

所以在我展示Promise代码前，我想全面的解释一下Promise的概念。我希望这能在你将Promise引入到你的异步流里时更好的指导你。

因此，我们先看看两个不同的关于Promise*是*什么的类比。

### Future Value

想象这种场景：我走向一个快餐店的点餐台，点了一份牛肉饼，我给了收银员$1.47，通过下单以及支付，我相当于请求一个*值*（牛肉饼），我开始了一场交易。

但是经常，牛肉饼不会立即给你，收银员会给我一个有订单号的收据，订单号就是一个IOU（“我欠你”）*promise*，它可以确保最终我会拿到我的牛肉饼。

所以我紧紧握住我的收据和订单号，我知道它代表着我*未来的牛肉饼*，所以我无需担心任何东西——除了饥饿！

我等待期间，我可以做其他事情，比如给朋友发发短信”嗨，你要来和我一起吃午饭吗？我要吃牛肉饼。“

我已经在想象我的*未来牛肉饼*了，尽管我现在还没有拿到它。我的大脑之所以这样，因为它把订单号当作牛肉饼的占位。这个占位让值成了*时间独立*，这就是**future value**。

然后，我听到了“订单113号！”我手握收据开心地来到点餐台，我把收据交给收银台，然后拿回了我的牛肉饼。

换句话说，一旦我的*future value*准备就绪，我用我的value-promise来换取真实的value。

但是也会发生别的结果，他们叫了我的订单号，但是但我准备取我的牛肉饼时，收银员遗憾的告诉我“很抱歉，牛肉饼卖完了。”先抛开此场景下顾客的沮丧，我们可以看到*future value*的一个重要特点：它们可能是成功的结果也可能是失败的。

每一次我点了一个牛肉饼，我都知道我可能最终会拿到一个牛肉饼，也可能会得到一个牛肉饼告罄的坏消息，而我必须去找别的东西来当午餐。

**注意：** 在代码中，事情并不这么简单，比如订单号永远也没有被叫到，这样地话，我们就一直处于未解决的状态，我们稍后来看这一点。

#### Values Now and Later

所有这些可能听起来太过抽象而无法付诸代码，我们再具体一些。但是，在我们照这样引入Promises的工作原理之前，我们先从我们可以理解的代码（回调）出发来处理这些*future values*。

当你写下解析值的代码时，比如用`number`做算数，不管你有意还是无意，关于这个值你都已经做了基本的假定，即这个值是*当前*的：

```js
var x, y = 2;

console.log( x + y ); // NaN  <-- because `x` isn't set yet
```

`x + y`操作的前提是`x`和`y`已经有了值，用我们很快会解释的术语来讲，就是我们假定`x`和`y`的值已经是*resolved*（译者注：确定的也还行，但是后面依然保持英文）。

期待`+`会自己检测或者等待`x`和`y`都有值然后去操作它们，这不太可能。如果不同的语句有的在*当前*结束，有的在*后来*结束，那样的话可能会导致程序的混乱，是吧？

如果两个语句中有一个（或者全部）可能还没有完成，你怎么可能推导它们之间的关系呢？如果语句2依赖于语句1的完成，只会有两种结果：要么语句1在*当前*完成，一切都进行的很顺利，要么语句1还没有完成，这样语句2将会失败。

如果这些东西听起来像是在第1章有看到过，那很好！

让我们回到`x + y`数学运算，想象有这么一种方式“`x`加上`y`，但是如果两者都没有准备好，那么一直等待，一有值就立即相加“。

你刚刚可能想到了回调，好吧，所以……

```js
function add(getX,getY,cb) {
	var x, y;
	getX( function(xVal){
		x = xVal;
		// both are ready?
		if (y != undefined) {
			cb( x + y );	// send along sum
		}
	} );
	getY( function(yVal){
		y = yVal;
		// both are ready?
		if (x != undefined) {
			cb( x + y );	// send along sum
		}
	} );
}

// `fetchX()` and `fetchY()` are sync or async
// functions
add( fetchX, fetchY, function(sum){
	console.log( sum ); // that was easy, huh?
} );
```

用一点儿事件消化一些这美妙的代码片段。
Take just a moment to let the beauty (or lack thereof) of that snippet sink in (whistles patiently).

尽管不可否认的丑陋，这种异步模式还是有很重要的需要注意的东西。

在上述代码中，我们把`x`和`y`当作future values，我们表达`add(..)`操作并不关心`x`和`y`是否当前有值，换句话说，它使得*当前*和*后来*没有了差别，这样我们可以依赖在一个可以预测结果的`add(..)`操作上。

通过使用这种暂时一致的`add(..)`——它在*当前*和*后来*都是一样的——异步代码推导起来更加容易。

说得更平白些：为了统一处理*当前*和*后来*，我们让它们都成为*后来*：所有的操作成了异步的。

当然，这种粗糙的基于回调的方法还有很多待改进的地方，它只是实现推导*future values*而无需关注何时有值这个大目标的第一小步。

#### Promise Value

我们在本章后续会继续深入Promises的更多细节——所以如果上述有些地方你有困惑，不要担心——但首先让我们看一下我们如果用`Promise`来实现`x + y`这个例子：

```js
function add(xPromise,yPromise) {
	// `Promise.all([ .. ])` takes an array of promises,
	// and returns a new promise that waits on them
	// all to finish
	return Promise.all( [xPromise, yPromise] )

	// when that promise is resolved, let's take the
	// received `X` and `Y` values and add them together.
	.then( function(values){
		// `values` is an array of the messages from the
		// previously resolved promises
		return values[0] + values[1];
	} );
}

// `fetchX()` and `fetchY()` return promises for
// their respective values, which may be ready
// *now* or *later*.
add( fetchX(), fetchY() )

// we get a promise back for the sum of those
// two numbers.
// now we chain-call `then(..)` to wait for the
// resolution of that returned promise.
.then( function(sum){
	console.log( sum ); // that was easier!
} );
```

这段代码里有两层Promise。

`fetchX()`和`fetchY()`被直接调用，它们返回的值（promises！）被传递给`add(..)`，这些promises的最终的值可能*当前*或者*后来*才可访问，但是不管怎样每一个promise将它们的行为标准化为相同的。我们独立于时间而推导`X`和`Y`的值，它们是*future values*。

第二层是`add(..)`（通过`Promise.all([..])`）创建和返回的promise，我们通过调用`then(..)`来等待。当`add(..)`操作完成时，我们的`sum`*future value*也准备好，然后我们可以打印它。我们在`add(..)`里隐藏了等待`X`和`Y`的*future values*。

**注意：** 在`add(..)`里，调用`Promise.all([ .. ])`创建了一个promise（它等待`promiseX`和`promiseY`解析），调用`.then(..)`产生了另一个promise，`return values[0] + values[1]`这一行立即解析（加法的结果）。这样我们放在调用链最后的`then(..)`的调用——代码的最后面——实际上是在第二个promise的返回结果上操作的，而不是在由`Promise.all([..])`创建的第一个。而且，尽管我们没有在第二个我们选择观察／使用的`then(..)`的后面用链式来写，它其实也创建了另一个promise。这种Promise链的内容会在本章的后面详细解释。

正如牛肉饼订单，很可能Promise的结果会是失败而不是成功。与成功的Promise不同，它的值总是可以编程安全的，一个失败的值——一般被叫做“rejection reason”——既可以被直接用代码逻辑设置，也可以是一个隐式的运行时异常。

Promises的`then(..)`可以接受两个函数，第一个处理成功情况（如前面所述），第二个处理失败情况：

```js
add( fetchX(), fetchY() )
.then(
	// fullfillment handler
	function(sum) {
		console.log( sum );
	},
	// rejection handler
	function(err) {
		console.error( err ); // bummer!
	}
);
```

如果获取`X`或`Y`时出了错，或者在加法时出了错，那么`add(..)`这个promise会返回失败，传给`then(..)`的第二个回调错误处理函数会收到promise的拒绝值。

因为Promises在最外面封装了时间独立状态——等待最终值的完成或者拒绝，所以Promise本身也是时间独立的，这样Promises就可以以无视时间或者最终结果的方式被组合。

而且，一旦Promise被解析，它将永远这样——成为*不可变的值*——然后可以被有需要时任意次访问（*观测*）。

**注意：** 因为Promise被解析后，就不再改变，现在把它传递给任何部分都是安全的，而且我们知道它也不会被无意或刻意修改。当涉及多个部分都尝试访问（观测）同一个Promise的解析时也是如此。某一个部分不可能会影响其它部分观测Promise解析的能力，不可修改可能听起来像学术话题，但它的确是Promise设计的最基础最重要的方面之一，不应该被随意忽视。

上面就是理解Promise的最强大和重要的概念之一。经过一定的努力，你可以仅仅使用丑陋的回调组合达到同样的效果，但是这是很低效的策略，尤其是你不得不一遍又一遍的做。

Promises是一个简单地可重复的封装和组合*future values*的机制。

### Completion Event

如前所见，一个Promise就相当于一个*future value*。但是还有一种思路来思考Promise的解析：作为一个流控的机制——一个暂时的这样-然后-那样——两个或更多步骤的异步任务。

假设调用一个函数`foo(..)`来完成一些任务，我们不知道任何更详细的东西，我们也不关心。可能时当前立刻就完成任务，也可能需要花费一定时间。

我们仅仅需要知道何时`foo(..)`完成即可，这样我们可以继续下一个任务。也就是说，如果有一种方式能告知我们`foo(..)`完成了，这样我们就可以*继续*，那将会很赞。

按照JS的典型方式，如果你需要监听一个通知，那么你希望它是一个事件。所以我们可以再塑通知的需求为一个监听`foo(..)`触发的*完成*（或者*继续*）的需求。

**注意：** 无论你习惯把它叫做“完成事件”还是“继续事件”，重点是`foo(..)`发生了什么，还是`foo(..)`完成*后*会发生生么？两个观点都没错有意义。事件通知告诉我们`foo(..)`已经*完成了*，同时也意味着可以*继续*进行下一个任务了。事实上，你传递的为事件通知被调用回调本身也是*继续*。因为*完成事件*更强调`foo(..)`本身，也被我们更多的关注，所以我们接下来会倾向于使用*完成事件*这种叫法。

使用回调，“通知”就是由任务（`foo(..)`）触发的回调函数。但是使用Promises，我们反过来，希望自己来监听来自`foo(..)`的事件，当由通知时，就直接去处理。

首先，看几个伪代码：

```js
foo(x) {
	// start doing something that could take a while
}

foo( 42 )

on (foo "completion") {
	// now we can do the next step!
}

on (foo "error") {
	// oops, something went wrong in `foo(..)`
}
```

我们调用`foo(..)`然后创建2个事件监听器，一个给`"completion"`，一个给`"error"`——两个调用`foo(..)`*最终*可能的结果。本质上，`foo(..)`也不会意识到调用代码被这两个事件监听，这样就是一个很好的*关注分离*。

但是，这种代码需要一些JS环境里没有的“魔法”（而且很可能不太现实），下面是我们在JS里可以更自然地表述方式：

```js
function foo(x) {
	// start doing something that could take a while

	// make a `listener` event notification
	// capability to return

	return listener;
}

var evt = foo( 42 );

evt.on( "completion", function(){
	// now we can do the next step!
} );

evt.on( "failure", function(err){
	// oops, something went wrong in `foo(..)`
} );
```

`foo(..)`明显创建了一个可以返回的事件订阅能力，调用代码对它接收和注册两个事件处理器。
从一般的基于回调的反转代码会很清晰，目的也很明确。不再传递回调给`foo(..)`，而是后者返回一个我们叫做`evt`事件功能，它可以接收回调。

如果你回顾第2章，回调本身代表的是*控制反转*。所以，反转回调模式就是*反转的反转*，或者*控制反反转*——我们最初也是想要把控制权回交给调用的代码。

另一个重要的观点是，代码中不同的独立部分可以被给予事件监听功能，当`foo(..)`完成时它们可以独立的被通知从而完成接下来的步骤：

```js
var evt = foo( 42 );

// let `bar(..)` listen to `foo(..)`'s completion
bar( evt );

// also, let `baz(..)` listen to `foo(..)`'s completion
baz( evt );
```

*控制反反转*让*关注分离*更加优雅，`baz(..)`和`bar(..)`无需关注`foo(..)`是如何被调用的，同样的，`foo(..)`无需关注`bar(..)`和`baz(..)`的存在，或是否在等待`foo(..)`完成后通知它们。

本质上，这个`evt`对象是一个不同关注之间的中立的三方协商。

#### Promise "Events"

可能你已经猜到，`evt`事件监听功能就是Promise的类比。

在基于Promise的方法里，前述代码片段`foo(..)`会创建和返回一个`Promise`实例，这个promise会传给`bar(..)`和`baz(..)`。

**注意：** 我们监听的Promise的解析“事件”并不严格是事件（尽管从目的看来它们的行为是事件），但是它们被代表性的称为`"completion"`或`"error"`。相反，我们使用`then(..)`来注册`"then"`事件，或者更精确的说，`then(..)`注册了`"fulfillment"`或`"rejection"`事件，尽管我们在代码里没有明确的看到这些术语。

Consider:

```js
function foo(x) {
	// start doing something that could take a while

	// construct and return a promise
	return new Promise( function(resolve,reject){
		// eventually, call `resolve(..)` or `reject(..)`,
		// which are the resolution callbacks for
		// the promise.
	} );
}

var p = foo( 42 );

bar( p );

baz( p );
```

**注意：** 那个`new Promise( function(..){ .. } )`的模式一般叫做["revealing constructor"](http://domenic.me/2014/02/13/the-revealing-constructor-pattern/)（“实现构造器”？）。传入的两个函数会立即被执行（不同于`then(..)`的回调里的异步延迟），它需要两个参数，在这种情况下，我们起名为`resolve`和`reject`，它们是promise的解析函数，`resolve(..)`通常代表实行（成功），`reject(..)`代表拒绝（失败）。

你可以猜到`bar(..)`和`baz(..)`的内部可能会是这样子：

```js
function bar(fooPromise) {
	// listen for `foo(..)` to complete
	fooPromise.then(
		function(){
			// `foo(..)` has now finished, so
			// do `bar(..)`'s task
		},
		function(){
			// oops, something went wrong in `foo(..)`
		}
	);
}

// ditto for `baz(..)`
```

Promise解析不必总是关于发送消息，虽然它在我们把Promises作为*future values*检验时的确是这样子，但它可以完全被当作流控的信号，如前面的代码片段里。

另一种使用方式如下：

```js
function bar() {
	// `foo(..)` has definitely finished, so
	// do `bar(..)`'s task
}

function oopsBar() {
	// oops, something went wrong in `foo(..)`,
	// so `bar(..)` didn't run
}

// ditto for `baz()` and `oopsBaz()`

var p = foo( 42 );

p.then( bar, oopsBar );

p.then( baz, oopsBaz );
```

**注意：** 如果你以前看到过基于Promise的编程，你会误以为上面代码的最后两行可以被写成`p.then( .. ).then( .. )`，使用链式，而不是`p.then(..); p.then(..)`。那样会有完全不同的处理，小心哦！区别可能还不清晰，但确实是我们还没有见到的不同的异步模式：splitting/forking。别怕！我们后面会讲到它。

不是把promise`p`传给`bar(..)`和`baz(..)`，我们使用这个promise来控制何时`bar(..)`和`baz(..)`被执行（如果可以的话），最大的区别就是错误处理。

第一段代码里的方法`bar(..)`无论`foo(..)`成功或失败都会被调用，它会自己处理失败逻辑如果被通知`foo(..)`失败了，`baz(..)`也是。

第二段代码片段里`bar(..)`只有当`foo(..)`成功时才会被调用，否则`oopsBar(..)`会被调用。`baz(..)`一样。

本身没有哪个*正确*之分，只有不同情况下谁更适用。

无论那种，从`foo(..)`返回的promise`p`都会被用来控制接下来发生什么。

此外，两段代码里都用`then(..)`调用了promsie`p`两次，进一步我们之前的观点，Promises（一旦以及解析）会一直保持该解析，可以被按需要访问（观测）任意次。

无论何时`p`被解析，接下来的步骤都会是一样的，*当前*和*后来*。

## Thenable Duck Typing

在Promises领域，一个很重要细节就是如何确定某个值就是Promise，或者说，某个值是否具备Promise行为？

考虑到Promises是由`new Promise(..)`语法构造的，你可能认为`p instanceof Promise`可以用来检查，但是很不幸，有诸多原因导致它并不充分。

首先，你可能会从其它浏览器窗口（比如iframe等）接收到一个Promise值，它与当前窗口或帧自己的Promise是不同的，那种检查无法识别出Promise实例。

而且，一些库或框架会有自己的Promises而不是使用ES6的自带`Promise`实现，事实上，你很可能在一些根本没有Promise的老浏览器里使用带有Promises的库。

当我们在后续章节讨论Promise解析处理时，你会更明显感觉到为何一个本质上不是但是类似Promise的值可以被识别和类比很重要。但是现在，仅仅记住我的话就行，因为这是解密的很重要一点。

因此，经决定，如何识别一个Promise（或者行为像Promise的东西）就是定义一些有`then(..)`方法的任何对象或函数被称为"thenable"的东西。任何这种值都是一个遵守Promise的thenable。

“类型检查”的鸭子类型一般是指，根据它的外表（表现出来的属性）推测一个值的“类型”——“如果它看起来像鸭子，并且像鸭子一样叫，那么它一定是只鸭子”（参见*Type & Grammer这本书），因此对thenable的鸭子类型检查大概如下：

```js
if (
	p !== null &&
	(
		typeof p === "object" ||
		typeof p === "function"
	) &&
	typeof p.then === "function"
) {
	// assume it's a thenable!
}
else {
	// not a thenable
}
```

呃！且不论丑陋的逻辑，这里还存在更严重的问题。如果你在解析Promise时，遇到了恰好有`then(..)`函数的对象或函数，你一定不想它被当作一个Promise/thenable，不幸的是，它会被识别为thenable并用特别的规则来处理（见后面的章节），当你完全不知道是否有个`then(..)`函数时，这种情况更为明显：

```js
var o = { then: function(){} };

// make `v` be `[[Prototype]]`-linked to `o`
var v = Object.create( o );

v.someStuff = "cool";
v.otherStuff = "not so cool";

v.hasOwnProperty( "then" );		// false
```

`v`看起来不是个Promise或thenable，它只是一个普通的持有一些属性的对象，你会像使用其它对象一样传递它，但是`v`却是`[[Prototype]]`链向另一个对象`o`的，而后者正好有个`then(..)`函数，所以thenable鸭子模型检查会认为`v`是thenable的。

甚至都不需要那么直接：

```js
Object.prototype.then = function(){};
Array.prototype.then = function(){};

var v1 = { hello: "world" };
var v2 = [ "Hello", "World" ];
```

`v1`和`v2`被认为是thenable，你无法控制或预测是否其它代码偶然或不小心的给`Object.prototype`（`Array.prototype`或其它基础原型）添加了`then(..)`，而且如果一个参数是回调的函数没有被调用，那么任何通过该值revolve的Promise会一只挂着。

听起来很难以置信是吧，也许。

但是记住在ES6之前，很多著名的non-Promise库都有一个`then(..)`方法，这些库里的一些选择重命名自己的方法来避免冲突。其它则是简单的降级到“不兼容Promise编程”的不幸状态，因为它们无力对此作出改变。

最合理的防止前面的情况发生的办法就是，`then`属性意味着无论何时（历史、当前、未来）不允许值（或者它的委托）可以持有`then(..)`属性，不管是有意或者无意，不然这个值会和Promise系统中的thenable弄混，从而产生很难定位的bug。

**警告：** 我个人并不喜欢我们通过鸭子模型来识别Promise的thenable，还有更好的方法，比如“branding“甚至”anti-branding“，我们接受的（鸭子模型）像个最坏情况的妥协，但也没有那么坏啦，thenable鸭子模型也很有用（我们后面会说），只要记得这个模型如果错误的识别了Promise，会产生一些问题。
## Promise Trust

在第二章我们提到了*控制反转*中的信任危机问题，现在我们来深入看一下Promise是如何解决这些问题的。

回调编程会有如下几个问题（比如传递给`foo(..)`回调函数）：

* 过早调用了回调
* 过晚调用
* 过多／少调用
* 传递必要参数失败
* 吞没了错误／异常

### Calling Too Early

这个一般是指有时一个任务时同步完成的有时又是异步完成的（称Zalgo），这样就会出现竞态条件。

Promise无论是立即完成（如`new Promise(function(resolve){ resolve(42); })`）还是延迟完成，它都是同步*观测*的。即，当你调用`then(..)`时，即便Promise已经resolved了，你提供给`then(..)`的回调**仍然**会异步的调用（查看第一章的“Jobs“）。

所以，不需要再插入你自己的`setTimeout(..,0)`，Promise已经自动阻止了Zalgo。

### Calling Too Late

与前一点类似，Promise的`then(..)`注册的回调监控，当`resolve(..)`或`reject(..)`被调用时，会自动调用对应的回调，这些已预定的回调会在下一个异步时刻被激活（参考第一章“Jobs”）。

但是无法做到同步观测，所以不能用来进行同步链任务的执行，也就是说，当一个Promise被resolve，所有给它通过`then(..)`注册的回调都会被在下一个异步时刻立即按（注册）顺序调用，这些回调不会影响／延迟彼此的调用。

比如：

```js
p.then( function(){
	p.then( function(){
		console.log( "C" );
	} );
	console.log( "A" );
} );
p.then( function(){
	console.log( "B" );
} );
// A B C
```

这里，`"C"`不会中断或先于`"B"`，基于Promise的操作定义。

#### Promise Scheduling Quirks

需要强调的是，两个独立Promise的回调顺序是不可预测的，如果两个Promise`p1`和`p2`都resolve了，理论上`p1.then(..); p2.then(..)`这种写法会让`p1`的回调先于`p2`的执行，但有一些场景下并非如此：

```js
var p3 = new Promise( function(resolve,reject){
	resolve( "B" );
} );

var p1 = new Promise( function(resolve,reject){
	resolve( p3 );
} );

var p2 = new Promise( function(resolve,reject){
	resolve( "A" );
} );

p1.then( function(v){
	console.log( v );
} );

p2.then( function(v){
	console.log( v );
} );

// A B  <-- not  B A  as you might expect
```

我们会在后面讲解这一点，但是你可以看到，`p1`是通过一个非即时值（`p3`）resolve的，`p3`又是通过`"B"`来resolve的，特别的行为就是将`p3`*拆包*进`p1`，但是是异步的，所以`p1`的回调在异步作业队列（见第一章）上*落*在了`p2`回调后。

为了避免这种问题，你在编程中不应该依赖Promise之间的回调执行／调度顺序，事实上，最佳实践是如果需要按顺序执行回调，你不应该这么编码。请尽量避免哦。

### Never Calling the Callback

这个问题经常会遇到，Promise从几个方面解决了这个问题。

首先，什么（甚至JS错误）也无法阻止Promise通知你它的完成情况（是否resolve了），如果你注册了成功和失败回调，如果Promise被resolve了，那么两者之一会被调用。

当然如果你的回调函数里如果有错误，那么你可能会看不到你期望的结果，但是回调确实被调用了，我们会在后面讲如何在你的回调里通知错误，因为它们不会被吞没。

但是，如果Promise一直都不resolve呢？Promise也为这种情况提供了一个高级别的抽象概念，叫“race”：

```js
// a utility for timing out a Promise
function timeoutPromise(delay) {
	return new Promise( function(resolve,reject){
		setTimeout( function(){
			reject( "Timeout!" );
		}, delay );
	} );
}

// setup a timeout for `foo()`
Promise.race( [
	foo(),					// attempt `foo()`
	timeoutPromise( 3000 )	// give it 3 seconds
] )
.then(
	function(){
		// `foo(..)` fulfilled in time!
	},
	function(err){
		// either `foo()` rejected, or it just
		// didn't finish in time, so inspect
		// `err` to know which
	}
);
```

这里有很多要详解的细节，我们随后会说。重要的是，我们可以保证`foo()`的一个结果，防止它长久挂起程序。

### Calling Too Few or Too Many Times

根据定义，回调应该只能被调用*一*次，“太少“意味着没有调用，”太多“容易解释。Promise被设计成只能被resolve一次，如果Promise的创建代码试图多次调用`resolve(..)`或`reject(..)`，那么Promise只会接受第一次的结果，静默忽略其余的。

因此，`then(..)`注册的（每一个）回调函数也只会被调用一次。

当然，如果你把同一个回调函数注册多次（比如：`p.then(f); p.then(f);`)，它会被多次调用，保证一个响应函数只被调用一次并不能避免你自己搬石头砸自己脚。

### Failing to Pass Along Any Parameters/Environment

Promise至多只能有一个解决值（成功或失败）。如果你不显式的resolve任何值，值就是`undefiend`，无论值是什么，他都会被传给所有注册了的回调函数，*现在*或将来。

一些需要注意的地方：如果你给`resolve(..)`或`reject(..)`传递了多个参数，那么除了第一个外其余的参数都会被忽略。如果你需要传递多个值，你需要将它们包裹到一个值里，比如`array`或`object`。

对于执行环境来讲，JS中的函数总是会保持它们自己的被定义时的作用域（参考*Scope & Closures*这本书），所以它们可以访问你提供的外部状态，回调函数也是，所以这一点并不是Promise的特性。

### Swallowing Any Errors/Exceptions

这一节是对前面一点的详细说明，如果你reject了一个Promise，并传递了*原因*（比如错误信息），这个值会被传递给失败回调函数里。我们这里还要说一个更大的点，在创建Promise时或者在观测它的解决时，发生了一个JS异常／错误，比如`TypeError`或者`ReferenceError`，这个异常会被捕获，并强制让Promise被reject。

比如：

```js
var p = new Promise( function(resolve,reject){
	foo.bar();	// `foo` is not defined, so error!
	resolve( 42 );	// never gets here :(
} );

p.then(
	function fulfilled(){
		// never gets here :(
	},
	function rejected(err){
		// `err` will be a `TypeError` exception object
		// from the `foo.bar()` line.
	}
);
```

从`foo.bar()`出现的JS异常，成为了Promise的rejection，而你也可以捕获并进行处理。这一点很重要，因为它有效的解决了另一个潜在的Zalgo时刻：当错误发生时，会产生一个同步的反应，而无错误的处理是异步的。Promise把JS异常也转为了异步行为，因此有效减少了竞态条件的发生。

但是，如果Promise是fulfilled，但是在观测时发生了JS异常（在`then(..)`注册的回调里）会怎么样？即使那些没有消失，但你会发现它们的处理是多么的神奇（除非你很懂）：
But what happens if a Promise is fulfilled, but there's a JS exception error during the observation (in a `then(..)` registered callback)? Even those aren't lost, but you may find how they're handled a bit surprising, until you dig in a little deeper:

```js
var p = new Promise( function(resolve,reject){
	resolve( 42 );
} );

p.then(
	function fulfilled(msg){
		foo.bar();
		console.log( msg );	// never gets here :(
	},
	function rejected(err){
		// never gets here either :(
	}
);
```
稍等，上面代码里`foo.bar()`的异常并没有被吞没，不用担心，它的确不会。但是还有更深入的问题，那就是**我们没有监听到这个失败**，`p.then(..)`的调用会返回另一个Promise，它是被`ReferenceError`异常rejected了的。

为什么它不直接调用我们提供的错误处理呢？尽管看起来应该那样子，但是它有悖于Promise的基本设计原则：一旦resolve就**不可改变**，即`p`已经通过`42`被fulfilled了，所以不能因为后面观察`p`的解决时出了错误，就将它改为rejection。

而且，除了设计原则的问题，这种行为会造成严重问题，比如这里有很多`then(..)`注册给`p`的回调函数，如果像上面那种修改其resolve状态，那么就会导致有些回调调用了（有异常的回调前面的），有些还没有（在有异常的那个回调之后的）。

【译者注：比如可以再`.then`一把，就可以获取到异常了】
```js
var p = new Promise( function(resolve,reject){
	resolve( 42 );
} );

p.then(
	function (msg){
		foo.bar();
		console.log( msg );
	},
	function (err){
		console.log(err);
	}
).then(
	function (msg){
		console.log( msg );
	},
	function (err){
		console.log(err);
	}
);
```

### Trustable Promise?

还有最后一点关于Promise模式的信任建立的检验。

可以看到Promise并没有完全抛弃回调，它们只是改变了回调传递的位置，不再是把回调传递给`foo(..)`，然后从`foo(..)`返回获取*一些东西*，而是把回调传给*一些东西*。
You've no doubt noticed that Promises don't get rid of callbacks at all. They just change where the callback is passed to. Instead of passing a callback to `foo(..)`, we get *something* (ostensibly a genuine Promise) back from `foo(..)`, and we pass the callback to that *something* instead.

为什么这比只有回调更可信呢？我们如何确信*一些东西*就是可信的Promise呢？难道不是当我们信任时我们才会信任么？一个很重要却总是被忽视的一点是，它们对此问题有解决方案，ES6中原生的实现就是`Promise.resolve(..)`。如果你传递给`Promise.resolve(..)`一个即时、非Promise、非thenable值，你会得到一个以该值fulfilled的Promise，也就是说，下面`p1`和`p2`这两个promise的行为完全一样：

```js
var p1 = new Promise( function(resolve,reject){
	resolve( 42 );
} );

var p2 = Promise.resolve( 42 );
```

而且，如果你给`Promise.resolve(..)`传递了一个Promise，那么你会得到完全一样的promise：

```js
var p1 = Promise.resolve( 42 );

var p2 = Promise.resolve( p1 );

p1 === p2; // true
```

更重要的是，如果你给`Promise.resolve(..)`传递了一个非promise值，它会尝试拆包该值，并且拆包会一直进行下去，直到有一个非Promise的值出现。

还记得前面关于thenable的讨论吗？思考：

```js
var p = {
	then: function(cb) {
		cb( 42 );
	}
};

// this works OK, but only by good fortune
p
.then(
	function fulfilled(val){
		console.log( val ); // 42
	},
	function rejected(err){
		// never gets here
	}
);
```

这个`p`是thenable的，但它本质上并不是Promise，这很明显，但是如果下面这种呢：

```js
var p = {
	then: function(cb,errcb) {
		cb( 42 );
		errcb( "evil laugh" );
	}
};

p
.then(
	function fulfilled(val){
		console.log( val ); // 42
	},
	function rejected(err){
		// oops, shouldn't have run
		console.log( err ); // evil laugh
	}
);
```

这个`p`也是thenable的，但是它的行为却和promise不同，这两个场景，都不是可信的。

尽管如此，我们仍然可以把这些版本的`p`传递给`Promise.resolve(..)`，这样就可以标准化它们，得到期望的安全结果：

```js
Promise.resolve( p )
.then(
	function fulfilled(val){
		console.log( val ); // 42
	},
	function rejected(err){
		// never gets here
	}
);
```

`Promise.resolve(..)`可以接受任何thenable的东西，然后拆包到它的非thenable的值，但你最后会从`Promise.resolve(..)`里得到一个真正的**一个你可以信任**的Promise。如果你传入的就是一个Promise，那么它会直接返回该Promise，所以没必要再通过`Promise.resolve(..)`来过滤以保证可信。

所以，如果当我们调用一个工具方法`foo(..)`，但我们不确定它是否会返回一个真正的Promise，但是我们确定它会返回一个thenable的东西，此时，`Promise.resolve(..)`会保证提供给我们一个Promise封装的东西，从而可以链调用。

```js
// don't just do this:
foo( 42 )
.then( function(v){
	console.log( v );
} );

// instead, do this:
Promise.resolve( foo( 42 ) )
.then( function(v){
	console.log( v );
} );
```

**注意：**使用`Promise.resolve(..)`封装函数返回值还有另一个作用：它是将函数调用统一到表现良好的异步任务的一种简单方式。如果`foo(42)`有时返回一个即时值，有时一个Promise，`Promise.resolve( foo(42) )`保证它始终返回Promise，并且避免了Zalgo问题。

### Trust Built

希望前面的讨论已经真正“resolve”了Promise之所以可信的原因，以及为何这种信任对建立强壮、可维护的软件至关重要。

你能用JS写出不受信任的异步代码吗？当然可以，我们JS开发者二十多年来一直仅仅使用回调编写异步代码。但是当你开始怀疑这种机制的信任度、可预测性时，你开始意识到回调的根基是如此薄弱。

Promise通过信任语义增强了回调，所以它的行为更加合理和可靠。通过反转回调的*控制反转*，我们为控制投入了一个专为清晰异步的可信任系统（Promise）。

## Chain Flow

我们已经提到过多次，Promise不是一个*this-then-that*的单步操作机制，那只是一个最小单元，我们可以使用链式方法来呈现一系列异步操作。

这种能力基于Promise的两个特点：

* 每当你调用一个Promise的`then(..)`方法，它会返回一个新的Promise，我们可以用来*链*调用。
* 你在调用`then(..)`传入的fulfilled回调中返回的，都会自动的成为下一个*链*的fulfilled回调函数的入参。

我们先解释下这两点是什么，然后我们会说明为何它们能帮助我们建立异步的流控制：

```js
var p = Promise.resolve( 21 );

var p2 = p.then( function(v){
	console.log( v );	// 21

	// fulfill `p2` with value `42`
	return v * 2;
} );

// chain off `p2`
p2.then( function(v){
	console.log( v );	// 42
} );
```

通过返回`v * 2`（即`42`），我们fulfill了`p2`这个promise（第一个`then(..)`创建以及返回），当`p2`的`then(..)`调用执行时，它得到了`return v * 2`语句的fulfillment，当然，`p2.then(..)`又创建了另一个promise，我们把它赋给了`p3`。这个比较麻烦，因为需要额外的创建变量`p2`（或`p3`等），庆幸的是，我们可以这么链式调用：

```js
var p = Promise.resolve( 21 );

p
.then( function(v){
	console.log( v );	// 21

	// fulfill the chained promise with value `42`
	return v * 2;
} )
// here's the chained promise
.then( function(v){
	console.log( v );	// 42
} );
```

所以，第一个`then(..)`是异步序列的第一步，第二个第二步。这个只要你想，就可以一直持续下去，只需要简单的在前一个`then(..)`创建的promise上继续链式调用就行。

但是别急，好像还差点儿什么。如果我们想要步骤2在步骤1进行异步处理时一直等待呢？我们使用立即`return`语句，它会立即fulfill这个promise链。

使得Promise序列真正在每一步都有异步能力的是前面说过的`Promise.resolve(..)`操作，回忆下当你把Promise或者thenable而不是即时值传给它时它的处理。`Promise.resolve(..)`直接返回了一个接收到的Promise，或者拆包接收到的thenable——一只递归到出现即时值。

当你从fulfillment（或者rejection）里返回一个Promise或thenble时，同样的拆包也会发生，比如：

```js
var p = Promise.resolve( 21 );

p.then( function(v){
	console.log( v );	// 21

	// create a promise and return it
	return new Promise( function(resolve,reject){
		// fulfill with value `42`
		resolve( v * 2 );
	} );
} )
.then( function(v){
	console.log( v );	// 42
} );
```

尽管我们把`42`封装到promise里来返回，在promise链调用里仍然被拆包了，这样，第二个`then(..)`仍然接收到`42`，如果我们引入异步操作，仍然没问题：

```js
var p = Promise.resolve( 21 );

p.then( function(v){
	console.log( v );	// 21

	// create a promise to return
	return new Promise( function(resolve,reject){
		// introduce asynchrony!
		setTimeout( function(){
			// fulfill with value `42`
			resolve( v * 2 );
		}, 100 );
	} );
} )
.then( function(v){
	// runs after the 100ms delay in the previous step
	console.log( v );	// 42
} );
```

很好很强大，现在我们可以构建一个异步序列了，每一步都可以按需延迟下一步（也可以不延迟）。

当然，每一步到另一步的值传递是非必选的，如果你没有显式返回一个值，那么隐式的`undefined`会返回，而且各个promise之间的链关系仍然一样，每一个Promise的resolution是下一个步骤开始执行的标志。

为了更进一步说明，我们概括一个延迟Promise创建（没有resolution信息）为工具方法，我们可以重复使用它：

```js
function delay(time) {
	return new Promise( function(resolve,reject){
		setTimeout( resolve, time );
	} );
}

delay( 100 ) // step 1
.then( function STEP2(){
	console.log( "step 2 (after 100ms)" );
	return delay( 200 );
} )
.then( function STEP3(){
	console.log( "step 3 (after another 200ms)" );
} )
.then( function STEP4(){
	console.log( "step 4 (next Job)" );
	return delay( 50 );
} )
.then( function STEP5(){
	console.log( "step 5 (after another 50ms)" );
} )
...
```

调用`delay(200)`创建一个200ms后fulfill的promise，然后我们从第一个`then(..)`的fulfillment回调里返回它，这样第二个`then(..)`的promise会等待这个200ms的promise。
Calling `delay(200)` creates a promise that will fulfill in 200ms, and then we return that from the first `then(..)` fulfillment callback, which causes the second `then(..)`'s promise to wait on that 200ms promise.

**注意：** 如前面所描述的，严格说来，是有2个promise：200ms延迟promise以及第二个`then(..)`链接的promise。但你会发现把它们合在一起看待更自然，因为Promise机制会自动帮你合并它们的状态，这样，你可以把`return delay(200)`看作是创建一个替换前面返回的链promise的promise。
 As described, technically there are two promises in that interchange: the 200ms-delay promise and the chained promise that the second `then(..)` chains from. But you may find it easier to mentally combine these two promises together, because the Promise mechanism automatically merges their states for you. In that respect, you could think of `return delay(200)` as creating a promise that replaces the earlier-returned chained promise.

讲真，一系列没有信息传递的延迟并不是Promise流控制的常用场景，下面这个（Ajax请求）才是：

```js
// assume an `ajax( {url}, {callback} )` utility

// Promise-aware ajax
function request(url) {
	return new Promise( function(resolve,reject){
		// the `ajax(..)` callback should be our
		// promise's `resolve(..)` function
		ajax( url, resolve );
	} );
}
```

首先我们定义一个`request(..)`工具方法，它可以构造代表啊`ajax(..)`调用实现的promise：

```js
request( "http://some.url.1/" )
.then( function(response1){
	return request( "http://some.url.2/?v=" + response1 );
} )
.then( function(response2){
	console.log( response2 );
} );
```

**注意：** 开发者经常会遇到这样的场景：它们想要使用本身并不具备Promise能力的工具方法（比如这里的`ajax(..)`，它需要的是回调）来处理如Promise这样的异步流控制。尽管原生ES6里的`Promise`机制没有自动解决这个问题，但很多Promise库却做了。它们通常把这种处理叫做“lifting”或“promisifying”或其它变体，我们很快会讲到这个技术。

使用`request(..)`这样的返回Promise的方法，我们通过使用第一个URL调用它来链的第一步，然后通过第一个`then(..)`返回的promise继续链接。

一旦`response1`返回，我们用这个值来构造第二个URL，然后生成第二个`request(..)`调用，第二个`request(..)`promise返回后，异步流控制的第三步等待Ajax调用完成，最终，当`response2`有值时，我们打印`response2`。

我们构造的Promise链不仅仅是表示几个异步序列的流控制，它还称为信息通道，在每一步到另一步之间进行信息传递。

那么如果在某一步中出现了问题呢？一个错误／异常也是Promise的，意味着我们可以在链的任何位置（【译者注：错误发生那一步的后面任意位置】捕获它，这种捕获类似于在该位置把链“重置”到正常的运行上：

```js
// step 1:
request( "http://some.url.1/" )

// step 2:
.then( function(response1){
	foo.bar(); // undefined, error!

	// never gets here
	return request( "http://some.url.2/?v=" + response1 );
} )

// step 3:
.then(
	function fulfilled(response2){
		// never gets here
	},
	// rejection handler to catch the error
	function rejected(err){
		console.log( err );	// `TypeError` from `foo.bar()` error
		return 42;
	}
)

// step 4:
.then( function(msg){
	console.log( msg );		// 42
} );
```
当第二步发生错误时，第三步的rejection处理程序会捕获它，其中返回的值（如果有的话，如上例的`42`），会fulfill下一步（第四步）的promise，这样promise链就又回到了了fulfillment状态。

**注意：** 正如我们之前讨论的，当从fulfillment的处理程序例返回了一个promise时，它会被拆包，从而延迟下一步，这对于从rejection处理程序里返回promise也适用，这样的话，如果在第三步返回了promise而不是`return 42`，那么这个promise会延迟第四步，不论在rejection还是在fulfillment的`then(..)`调用里抛出的异常，都会导致下一个在链上的promise立即使用该异常被reject。

如果你调用一个promise的`then(..)`，而你只传入了一个fulfillment处理程序，一个假定的（即默认的）rejection处理程序会被替换：

```js
var p = new Promise( function(resolve,reject){
	reject( "Oops" );
} );

var p2 = p.then(
	function fulfilled(){
		// never gets here
	}
	// assumed rejection handler, if omitted or
	// any other non-function value passed
	// function(err) {
	//     throw err;
	// }
);
```

如你所见，这个假定的rejection处理程序只是简单的抛出错误，从而强制`p2`被同样的错误原因reject。本质上，这允许错误沿着Promise链一直传递下去直到遇见显式的定义了rejection处理程序。

**注意：** 我们后面会更详细的讲解Promise的错误处理，因为还有一些需要专注的知识点。

如果没有有效的函数作为fulfillment处理程序而传递给`then(..)`，也会有默认替代处理程序：

```js
var p = Promise.resolve( 42 );

p.then(
	// assumed fulfillment handler, if omitted or
	// any other non-function value passed
	// function(v) {
	//     return v;
	// }
	null,
	function rejected(err){
		// never gets here
	}
);
```

如你所见，默认的fulfillment处理程序只是简单的把接收的值继续传递给下一步（Promise）。

**注意：** `then(null,function(err){ .. })`这种只处理错误（rejection）的模式，其实就是`catch(function(err){ .. })`的缩写，我们下一节会讲到。

Let's review briefly the intrinsic behaviors of Promises that enable chaining flow control:
我们稍微复习一下Promise实现流控制链的本质：

* 调用Promise的`then(..)`自动的（从调用程序里）创建新的Promise。
* 在fulfillment／rejection处理程序里，无论返回了一个值还是抛出了一个异常，一个新创建的Promise会被相应（fulfillment／rejection）的resolve。
* 如果fulfillment或rejection处理程序返回了一个Promise，它会被拆包，从而无论resolution是什么，都会成为`then(..)`返回的Promise的resolution。

尽管流控制链很有用，把它当作Promise组合在一起时的额外好处而不是主要特点更为精确。我们已经详细讨论好几次，Promise会标准化异步，然后封装时间相关的值状态，这样保证我们可以链式使用它们。

### Terminology: Resolve, Fulfill, and Reject

在深入Promise之前，几个关于“resolve”、“fulfill”、“reject”的困惑需要澄清，我们先看看`Promise(..)`构造器：

```js
var p = new Promise( function(X,Y){
	// X() for fulfillment
	// Y() for rejection
} );
```

可以看到它需要两个回调（这里的`X`和`Y`），第一个*一般*用来标示Promise被fulfill了，第二个标示被reject了。这里的*一般*指的是什么？精确的命名这些参数暗示着什么？

其实，这些不过是你的代码，那些特别的名字对引擎来说没有任何意义，所以*严格来讲*（怎么命名）是无所谓的。`foo(..)`和`bar(..)`是相同的，但是单词不仅影响你思考代码，也会影响团队里的其他人，错误的思考一段异步代码明显比面条式的回调方式还要糟糕。

所以你怎么命名，还是很重要的。

第二个参数很容易确定，几乎所有的语言都用`reject(..)`来命名，它也的确是那样子的，这个命名很给力，我强烈推荐你就使用`reject(..)`。

但是对第一个参数却有些争议，Promise一般叫做`resolve(..)`，这个词明显代表着“resolution”，也是本书一直在使用的用来描述给Promise设置最终值／状态的叫法。我们已经多次使用“resolve the Promise”来表示fulfilling或者rejecting一个Promise了。

但是如果这个参数是用来表示fulfill了一个Promise，那么我们为什么不直接叫做`fulfill(..)`，却叫做`resolve(..)`呢？为了回答这个问题，我们先看2个`Promise`的API：

```js
var fulfilledPr = Promise.resolve( 42 );

var rejectedPr = Promise.reject( "Oops" );
```

`Promise.resolve(..)`创建一个resolve到给定值的Promise，在这个例子里，`42`是普通的非Promise以及非thenable的值，所以fulfilled的promise`fulfilledPr`为`42`创建，`Promise.reject("Oops")`创建一个原因为`"Oops"`的rejected promise`rejectedPr`。

现在我来解释一下为何单词”resolve“（比如`Promise.resolve(..)`）是含糊却又更精确的，如果用于一个可以有fulfillment也可以有rejection结果的场景下：

```js
var rejectedTh = {
	then: function(resolved,rejected) {
		rejected( "Oops" );
	}
};

var rejectedPr = Promise.resolve( rejectedTh );
```

`Promise.resolve(..)`会直接返回传入的Promise，或者拆包thenable，如果这个thenable拆包时发现它处于rejected状态，那么从`Promise.resolve(..)`返回的Promise也就会是rejected状态。所以，`Promise.resolve(..)`是一个良好的、精确命名的API方法，它实际上可以出现两种结果（fulfillment或rejection）。

`Promise(..)`的第一个回调参数构造器会拆包thenable（同于`Promise.resolve(..)`），或者一个Promise：

```js
var rejectedPr = new Promise( function(resolve,reject){
	// resolve this promise with a rejected promise
	resolve( Promise.reject( "Oops" ) );
} );

rejectedPr.then(
	function fulfilled(){
		// never gets here
	},
	function rejected(err){
		console.log( err );	// "Oops"
	}
);
```

所以，`resolve(..)`这种命名作为`Promise(..)`的第一个回调参数是合适的。

**警告：** 前面提到的`reject(..)`**不会**像`resolve(..)`那样进行拆包，如果你给`reject(..)`传入了Promise或者thenable值，这个值会被作为rejection原因，后续的rejection处理程序会接收到这个值（而不是它封装的即时值）。

现在我们来看看`then(..)`，它们应该如何命名（字面上以及代码里）？我建议`fulfilled(..)`和`rejected(..)`：

```js
function fulfilled(msg) {
	console.log( msg );
}

function rejected(err) {
	console.error( err );
}

p.then(
	fulfilled,
	rejected
);
```

传递给`then(..)`的第一个参数，它是单意义上的fulfillment，所以这里无需关注“resolve”这个术语，另外，ES6特性使用`onFulfilled(..)`和`onRejected(..)`来标记这两个回调，所以它们的命名是准确的。

## Error Handling

我们已经见过好几种Promise的rejection示例了——比如直接调用`reject(..)`或者意外的JS异常，我们再回过头来看下更多的知识。开发者最熟悉的错误处理形式就是`try..catch`这种了，但是，它是同步的，所以在异步场景下没什么用：

```js
function foo() {
	setTimeout( function(){
		baz.bar();
	}, 100 );
}

try {
	foo();
	// later throws global error from `baz.bar()`
}
catch (err) {
	// never gets here
}
```

`try..catch`很好用，但是不适用于异步操作。也就是说除非有外部环境支持，我们在第四章的generators里会讲。在回调中，一些错误处理模式的标准已经存在，最常见的形式就是“error-first callback”：

```js
function foo(cb) {
	setTimeout( function(){
		try {
			var x = baz.bar();
			cb( null, x ); // success!
		}
		catch (err) {
			cb( err );
		}
	}, 100 );
}

foo( function(err,val){
	if (err) {
		console.error( err ); // bummer :(
	}
	else {
		console.log( val );
	}
} );
```

**注意：** `try..catch`只有在`baz.bar()`即时的同步的成功或失败才有用，如果它本身是异步的，或者任何异步的，那么任何异步错误都无法被捕获。

我们传递给`foo(..)`的回调期待接收一个由第一个参数`err`持有的错误信号，如果出现，那么就认为错误发生了，如果没有，就认为成功了。

这种错误处理严格来讲是*异步的*，但是它设计的并不好，多个级别的error-first回调混在一起，使用这种`if`判断检查会让你陷入回调地狱（见第二章）。

所以我们回到Promise的错误处理，借助传递给`then(..)`的rejection处理程序，Promise没有使用流行的“error-first”回调设计形式，而是使用“分离回调”形式，一个用于fulfillment的回调，一个用于rejection的回调：

```js
var p = Promise.reject( "Oops" );

p.then(
	function fulfilled(){
		// never gets here
	},
	function rejected(err){
		console.log( err ); // "Oops"
	}
);
```

尽管这种模式的错误处理看起来没问题，但是Promise的错误处理实际上还是稍微有点儿要再次说明的地方：

```js
var p = Promise.resolve( 42 );

p.then(
	function fulfilled(msg){
		// numbers don't have string functions,
		// so will throw an error
		console.log( msg.toLowerCase() );
	},
	function rejected(err){
		// never gets here
	}
);
```

如果`msg.toLowerCase()`抛出了异常，为何我们的错误处理程序没有被通知到？我们前面已经解释过，因为*那个*错误处理程序是提供给`p`的，它已经被值`42`resolve了，`p`已经不可再修改，所以唯一可以被通知到错误的就是`p.then(..)`返回的promise，这个例子里我们没有捕获。

这也就是为何Promise的异常处理容易被错误使用，太容易吞没错误了，这肯定不是你想要的。

**警告：** 如果你非法使用Promise API，从而当Promise在被构造时发生了错误，那么会立即抛出异常，**而不是rejected Promise**，一些错误使用Promise构造的例子：`new Promise(null)`、`Promise.all()`、`Promise.race(42)`等。如果你没有正确的构造出Promise，那么你肯定无法有效使用它提供的API。

### Pit of Despair

为了防止错误被吞没，开发者们提出了一个Promise链的“最佳实践”，总是使用`catch(..)`来结束链：

```js
var p = Promise.resolve( 42 );

p.then(
	function fulfilled(msg){
		// numbers don't have string functions,
		// so will throw an error
		console.log( msg.toLowerCase() );
	}
)
.catch( handleErrors );
```

因为我们没有给`then(..)`传递一个rejection处理程序，默认的处理程序被替换，它只是简单的传递错误给下一个promise，从而，`p`中的错误，以及`p`的resolution中的错误（比如`msg.toLowerCase()`）都会进入到最终的`handleErrors(..)`。看起来好像问题解决了是吧？别急！

如果`handleErrors(..)`本身发生了错误呢？谁来捕获这个错误？所以`catch(..)`返回的错误，我们没有捕获，也没有为它注册rejection的处理程序。肯定不能再加一个`catch(..)`来解决，它也可能会出现错误，无论怎样，总会有个无法处理的错误。

看起来好像是个不能解的问题了。

### Uncaught Handling

的确不是一个容易解决的问题，但有其它的方法来逼近它，相对来讲*更好一些*。一些Promise库增加了“全局未处理rejection”处理程序，它会被调用而不是抛出全局的错误。但是如何识别一个错误是“未捕获”，它们一般通过一个任意长度的计时器，比如3秒，当rejection发生时开始计时，如果Promise被rejected了，但是在计时器启动前没有错误处理程序注册，那么它就假定你将不会注册处理程序，所以它是“未捕获”的。

事实上，这种方式在很多库上运行良好，因为大多数的使用者不会大量延迟Promise的rejection和观测rejection。但是这种方式容易出现问题，因为3秒是一个任意的值（即使是经验值），而且在一些场景下，你想要一个Promise在rejectedness上保持特定的事件周期，你一定不想让你的“未捕获”程序处理来处理这些假阳性（还未处理的“未捕获错误”）的调用。

另一个更普通的建议是，Promise应该在最后面加一个`done(..)`，这样可以保证promise链“完成”了，`done(..)`不会产生或者返回一个Promise，所以传递给`done(..)`的回调明显不会报告一个并不存在的Promise链的问题。

那么会发生什么？它会像你在未捕获的错误条件下预期的一样：任何在`done(..)`rejection处理程序中的异常都会抛出一个全局未捕获错误（在开发者模式下的console里）。

```js
var p = Promise.resolve( 42 );

p.then(
	function fulfilled(msg){
		// numbers don't have string functions,
		// so will throw an error
		console.log( msg.toLowerCase() );
	}
)
.done( null, handleErrors );

// if `handleErrors(..)` caused its own exception, it would
// be thrown globally here
```

这比从不结束的链或着任意超时看起来好一些，但是最大的问题是ES6标准并不支持，所以无论听起来多美好，它称为一个可靠的普遍的解决方案这条路还很长。

我们就困到这里了嘛？并不。

浏览器拥有我们代码不具备的能力：它们跟踪并且清楚对象在何时被抛弃，然后进行垃圾回收，所以，浏览器也可以跟踪Promise对象，每当有rejection出现，浏览器清楚的知道这是一个合法的“未捕获错误”，它们就开始垃圾回收，从而可以很确信它应该被报告给开发者console。

**注意：** 当写作此书时，Chrome和火狐都已经开始尝试这个”未捕获rejection“的能力，虽然还没有完美支持。

然而，如果一个Promise没有被垃圾回收——在许多不同的编码模式里很容易出现，浏览器的垃圾回收嗅探无法帮你检测到静默的rejected Promise。

是否还有别的选择？是的。

### Pit of Success

以下仅仅是一个理论，Promise未来*可能*会作出的改变。我相信它会是目前我们拥有的超集，而且我认为这个改变可能会是ES6之后的，因为我不认为它会破坏ES6 Promise的兼容性。而且，它可以被polyfilled，我们来看看：

* 当某个特定时刻，没有错误处理程序被注册给Promise，那么Promise默认可以在下一个作业或者事件轮询时刻，报告任何rejection（给开发者console）。
* 为了让你可以在一定时间内保持某个被rejected的Promise的rejected状态，你可以调用`defer()`，它会抑制Promise的自动报告错误。

如果某个Promise被rejected，它默认会报告给开发者console（而不是默认静默）。你可以隐式的处理（在rejection之前注册一个错误处理程序），或者显式的处理（使用`defer()`），无论哪种情况，你都可以控制假阳性问题。

```js
var p = Promise.reject( "Oops" ).defer();

// `foo(..)` is Promise-aware
foo( 42 )
.then(
	function fulfilled(){
		return p;
	},
	function rejected(err){
		// handle `foo(..)` error
	}
);
...
```

当我们创建`p`时，我们知道我们将会在使用／观测它的rejection之前等待一会儿，所以我们叫做`defer()`（不会有全局通知）。`defer()`只是返回相同的promise，为了链式使用。

从`foo(..)`返回的promise立即得到一个错误处理程序，所以它隐式的退出并且不会有全局通知。但是从`then(..)`调用返回的promise没有`defer()`或者附加的错误处理程序，所以如果它（从任何一个resolution处理程序里）reject的话，它会被当作未捕获的错误报告给开发者console。

**这个设计是pit of success。**默认情况下，所有的错误不是被处理就是被报告，几乎所有开发者在任何场景都可以预料的。你要么不得不注册一个处理程序要么直接退出，并表明你会在*后来*处理错误，你选择特殊情况下额外负责。

这种方式真正危险的是如果你`defer()`了一个promise，但是无法观测／处理它的rejection。你可能会故意调用`defer()`来选择pit of despair，默认的是pit of success，这样的话我们也无法把你从自己的错误里拯救出来。

我认为Promise的错误处理还是值的期待的（post-ES6），我希望相关人能够考虑这种场景以及这个选择。同时，你也可以自己实现，或者使用更*smarter*的Promise库来替你做到这些。

**注意：** 这个确切的错误处理／报告模型在我的*asyncqueue*Promise抽象库里被实现了，你可以在附录A里看到更多关于它的信息。

## Promise Patterns

### Promise.all([ .. ])

在异步序列（Promise链）中，某一时刻只会有一个异步任务在执行，第二步始终会在第一步后，第三步严格在第二步后，但是如果我们想要多个步骤并发（即“并行”）呢？

在传统编程术语中，“门”指的是一个等待两个或多个并行／并发任务的完成，它不关注它们的完成顺序，只关注是否所有的都已经完成，从而可以开门放行，在Promise的API中，这种模式叫做`all([..])`。

假设你想要同时发送两个Ajax请求，然后等它们都完成，不在乎它们的顺序，然后再发送第三个Ajax请求：

```js
// `request(..)` is a Promise-aware Ajax utility,
// like we defined earlier in the chapter

var p1 = request( "http://some.url.1/" );
var p2 = request( "http://some.url.2/" );

Promise.all( [p1,p2] )
.then( function(msgs){
	// both `p1` and `p2` fulfill and pass in
	// their messages here
	return request(
		"http://some.url.3/?v=" + msgs.join(",")
	);
} )
.then( function(msg){
	console.log( msg );
} );
```

`Promise.all([ .. ])`的入参是Promise实例组成的数组，返回是对应顺序的fulfillment信息数据。

**注意：** 严格来讲，入参数据中可以是Promise、thenable、即时值，每一个值都会通过`Promise.resolve(..)`，从而确保它最终是一个Promise，如果传入的是一个空数组，那么主Promise会立即fulfill。

`Promise.all([ .. ])`返回的主promise只有当所有的入参promise都被fulfill了才会fulfill，如果任何一个被rejected，那么主promise会立即rejected，并丢弃其它promise返回的结果。

记得给每一个promise添加rejection／error处理程序，尤其是从`Promise.all([ .. ])`返回的。

### Promise.race([ .. ])

`Promise.all([ .. ])`要求所有的promise都fulfill然后它才会fulfill，而`Promise.race([ .. ])`只要有一个fulfill它就会fulfil。

**警告：** 不要把“静态条件”和`Promise.race([ .. ])`搞混了。

`Promise.race([ .. ])` also expects a single `array` argument, containing one or more Promises, thenables, or immediate values. It doesn't make much practical sense to have a race with immediate values, because the first one listed will obviously win -- like a foot race where one runner starts at the finish line!

Similar to `Promise.all([ .. ])`, `Promise.race([ .. ])` will fulfill if and when any Promise resolution is a fulfillment, and it will reject if and when any Promise resolution is a rejection.

**Warning:** A "race" requires at least one "runner," so if you pass an empty `array`, instead of immediately resolving, the main `race([..])` Promise will never resolve. This is a footgun! ES6 should have specified that it either fulfills, rejects, or just throws some sort of synchronous error. Unfortunately, because of precedence in Promise libraries predating ES6 `Promise`, they had to leave this gotcha in there, so be careful never to send in an empty `array`.

Let's revisit our previous concurrent Ajax example, but in the context of a race between `p1` and `p2`:

```js
// `request(..)` is a Promise-aware Ajax utility,
// like we defined earlier in the chapter

var p1 = request( "http://some.url.1/" );
var p2 = request( "http://some.url.2/" );

Promise.race( [p1,p2] )
.then( function(msg){
	// either `p1` or `p2` will win the race
	return request(
		"http://some.url.3/?v=" + msg
	);
} )
.then( function(msg){
	console.log( msg );
} );
```

Because only one promise wins, the fulfillment value is a single message, not an `array` as it was for `Promise.all([ .. ])`.

#### Timeout Race

We saw this example earlier, illustrating how `Promise.race([ .. ])` can be used to express the "promise timeout" pattern:

```js
// `foo()` is a Promise-aware function

// `timeoutPromise(..)`, defined ealier, returns
// a Promise that rejects after a specified delay

// setup a timeout for `foo()`
Promise.race( [
	foo(),					// attempt `foo()`
	timeoutPromise( 3000 )	// give it 3 seconds
] )
.then(
	function(){
		// `foo(..)` fulfilled in time!
	},
	function(err){
		// either `foo()` rejected, or it just
		// didn't finish in time, so inspect
		// `err` to know which
	}
);
```

This timeout pattern works well in most cases. But there are some nuances to consider, and frankly they apply to both `Promise.race([ .. ])` and `Promise.all([ .. ])` equally.

#### "Finally"

The key question to ask is, "What happens to the promises that get discarded/ignored?" We're not asking that question from the performance perspective -- they would typically end up garbage collection eligible -- but from the behavioral perspective (side effects, etc.). Promises cannot be canceled -- and shouldn't be as that would destroy the external immutability trust discussed in the "Promise Uncancelable" section later in this chapter -- so they can only be silently ignored.

But what if `foo()` in the previous example is reserving some sort of resource for usage, but the timeout fires first and causes that promise to be ignored? Is there anything in this pattern that proactively frees the reserved resource after the timeout, or otherwise cancels any side effects it may have had? What if all you wanted was to log the fact that `foo()` timed out?

Some developers have proposed that Promises need a `finally(..)` callback registration, which is always called when a Promise resolves, and allows you to specify any cleanup that may be necessary. This doesn't exist in the specification at the moment, but it may come in ES7+. We'll have to wait and see.

It might look like:

```js
var p = Promise.resolve( 42 );

p.then( something )
.finally( cleanup )
.then( another )
.finally( cleanup );
```

**Note:** In various Promise libraries, `finally(..)` still creates and returns a new Promise (to keep the chain going). If the `cleanup(..)` function were to return a Promise, it would be linked into the chain, which means you could still have the unhandled rejection issues we discussed earlier.

In the meantime, we could make a static helper utility that lets us observe (without interfering) the resolution of a Promise:

```js
// polyfill-safe guard check
if (!Promise.observe) {
	Promise.observe = function(pr,cb) {
		// side-observe `pr`'s resolution
		pr.then(
			function fulfilled(msg){
				// schedule callback async (as Job)
				Promise.resolve( msg ).then( cb );
			},
			function rejected(err){
				// schedule callback async (as Job)
				Promise.resolve( err ).then( cb );
			}
		);

		// return original promise
		return pr;
	};
}
```

Here's how we'd use it in the timeout example from before:

```js
Promise.race( [
	Promise.observe(
		foo(),					// attempt `foo()`
		function cleanup(msg){
			// clean up after `foo()`, even if it
			// didn't finish before the timeout
		}
	),
	timeoutPromise( 3000 )	// give it 3 seconds
] )
```

This `Promise.observe(..)` helper is just an illustration of how you could observe the completions of Promises without interfering with them. Other Promise libraries have their own solutions. Regardless of how you do it, you'll likely have places where you want to make sure your Promises aren't *just* silently ignored by accident.

### Variations on all([ .. ]) and race([ .. ])

While native ES6 Promises come with built-in `Promise.all([ .. ])` and `Promise.race([ .. ])`, there are several other commonly used patterns with variations on those semantics:

* `none([ .. ])` is like `all([ .. ])`, but fulfillments and rejections are transposed. All Promises need to be rejected -- rejections become the fulfillment values and vice versa.
* `any([ .. ])` is like `all([ .. ])`, but it ignores any rejections, so only one needs to fulfill instead of *all* of them.
* `first([ .. ])` is like a race with `any([ .. ])`, which is that it ignores any rejections and fulfills as soon as the first Promise fulfills.
* `last([ .. ])` is like `first([ .. ])`, but only the latest fulfillment wins.

Some Promise abstraction libraries provide these, but you could also define them yourself using the mechanics of Promises, `race([ .. ])` and `all([ .. ])`.

For example, here's how we could define `first([ .. ])`:

```js
// polyfill-safe guard check
if (!Promise.first) {
	Promise.first = function(prs) {
		return new Promise( function(resolve,reject){
			// loop through all promises
			prs.forEach( function(pr){
				// normalize the value
				Promise.resolve( pr )
				// whichever one fulfills first wins, and
				// gets to resolve the main promise
				.then( resolve );
			} );
		} );
	};
}
```

**Note:** This implementation of `first(..)` does not reject if all its promises reject; it simply hangs, much like a `Promise.race([])` does. If desired, you could add additional logic to track each promise rejection and if all reject, call `reject()` on the main promise. We'll leave that as an exercise for the reader.

### Concurrent Iterations

Sometimes you want to iterate over a list of Promises and perform some task against all of them, much like you can do with synchronous `array`s (e.g., `forEach(..)`, `map(..)`, `some(..)`, and `every(..)`). If the task to perform against each Promise is fundamentally synchronous, these work fine, just as we used `forEach(..)` in the previous snippet.

But if the tasks are fundamentally asynchronous, or can/should otherwise be performed concurrently, you can use async versions of these utilities as provided by many libraries.

For example, let's consider an asynchronous `map(..)` utility that takes an `array` of values (could be Promises or anything else), plus a function (task) to perform against each. `map(..)` itself returns a promise whose fulfillment value is an `array` that holds (in the same mapping order) the async fulfillment value from each task:

```js
if (!Promise.map) {
	Promise.map = function(vals,cb) {
		// new promise that waits for all mapped promises
		return Promise.all(
			// note: regular array `map(..)`, turns
			// the array of values into an array of
			// promises
			vals.map( function(val){
				// replace `val` with a new promise that
				// resolves after `val` is async mapped
				return new Promise( function(resolve){
					cb( val, resolve );
				} );
			} )
		);
	};
}
```

**Note:** In this implementation of `map(..)`, you can't signal async rejection, but if a synchronous exception/error occurs inside of the mapping callback (`cb(..)`), the main `Promise.map(..)` returned promise would reject.

Let's illustrate using `map(..)` with a list of Promises (instead of simple values):

```js
var p1 = Promise.resolve( 21 );
var p2 = Promise.resolve( 42 );
var p3 = Promise.reject( "Oops" );

// double values in list even if they're in
// Promises
Promise.map( [p1,p2,p3], function(pr,done){
	// make sure the item itself is a Promise
	Promise.resolve( pr )
	.then(
		// extract value as `v`
		function(v){
			// map fulfillment `v` to new value
			done( v * 2 );
		},
		// or, map to promise rejection message
		done
	);
} )
.then( function(vals){
	console.log( vals );	// [42,84,"Oops"]
} );
```

## Promise API Recap

Let's review the ES6 `Promise` API that we've already seen unfold in bits and pieces throughout this chapter.

**Note:** The following API is native only as of ES6, but there are specification-compliant polyfills (not just extended Promise libraries) which can define `Promise` and all its associated behavior so that you can use native Promises even in pre-ES6 browsers. One such polyfill is "Native Promise Only" (http://github.com/getify/native-promise-only), which I wrote!

### new Promise(..) Constructor

The *revealing constructor* `Promise(..)` must be used with `new`, and must be provided a function callback that is synchronously/immediately called. This function is passed two function callbacks that act as resolution capabilities for the promise. We commonly label these `resolve(..)` and `reject(..)`:

```js
var p = new Promise( function(resolve,reject){
	// `resolve(..)` to resolve/fulfill the promise
	// `reject(..)` to reject the promise
} );
```

`reject(..)` simply rejects the promise, but `resolve(..)` can either fulfill the promise or reject it, depending on what it's passed. If `resolve(..)` is passed an immediate, non-Promise, non-thenable value, then the promise is fulfilled with that value.

But if `resolve(..)` is passed a genuine Promise or thenable value, that value is unwrapped recursively, and whatever its final resolution/state is will be adopted by the promise.

### Promise.resolve(..) and Promise.reject(..)

A shortcut for creating an already-rejected Promise is `Promise.reject(..)`, so these two promises are equivalent:

```js
var p1 = new Promise( function(resolve,reject){
	reject( "Oops" );
} );

var p2 = Promise.reject( "Oops" );
```

`Promise.resolve(..)` is usually used to create an already-fulfilled Promise in a similar way to `Promise.reject(..)`. However, `Promise.resolve(..)` also unwraps thenable values (as discussed several times already). In that case, the Promise returned adopts the final resolution of the thenable you passed in, which could either be fulfillment or rejection:

```js
var fulfilledTh = {
	then: function(cb) { cb( 42 ); }
};
var rejectedTh = {
	then: function(cb,errCb) {
		errCb( "Oops" );
	}
};

var p1 = Promise.resolve( fulfilledTh );
var p2 = Promise.resolve( rejectedTh );

// `p1` will be a fulfilled promise
// `p2` will be a rejected promise
```

And remember, `Promise.resolve(..)` doesn't do anything if what you pass is already a genuine Promise; it just returns the value directly. So there's no overhead to calling `Promise.resolve(..)` on values that you don't know the nature of, if one happens to already be a genuine Promise.

### then(..) and catch(..)

Each Promise instance (**not** the `Promise` API namespace) has `then(..)` and `catch(..)` methods, which allow registering of fulfillment and rejection handlers for the Promise. Once the Promise is resolved, one or the other of these handlers will be called, but not both, and it will always be called asynchronously (see "Jobs" in Chapter 1).

`then(..)` takes one or two parameters, the first for the fulfillment callback, and the second for the rejection callback. If either is omitted or is otherwise passed as a non-function value, a default callback is substituted respectively. The default fulfillment callback simply passes the message along, while the default rejection callback simply rethrows (propagates) the error reason it receives.

`catch(..)` takes only the rejection callback as a parameter, and automatically substitutes the default fulfillment callback, as just discussed. In other words, it's equivalent to `then(null,..)`:

```js
p.then( fulfilled );

p.then( fulfilled, rejected );

p.catch( rejected ); // or `p.then( null, rejected )`
```

`then(..)` and `catch(..)` also create and return a new promise, which can be used to express Promise chain flow control. If the fulfillment or rejection callbacks have an exception thrown, the returned promise is rejected. If either callback returns an immediate, non-Promise, non-thenable value, that value is set as the fulfillment for the returned promise. If the fulfillment handler specifically returns a promise or thenable value, that value is unwrapped and becomes the resolution of the returned promise.

### Promise.all([ .. ]) and Promise.race([ .. ])

The static helpers `Promise.all([ .. ])` and `Promise.race([ .. ])` on the ES6 `Promise` API both create a Promise as their return value. The resolution of that promise is controlled entirely by the array of promises that you pass in.

For `Promise.all([ .. ])`, all the promises you pass in must fulfill for the returned promise to fulfill. If any promise is rejected, the main returned promise is immediately rejected, too (discarding the results of any of the other promises). For fulfillment, you receive an `array` of all the passed in promises' fulfillment values. For rejection, you receive just the first promise rejection reason value. This pattern is classically called a "gate": all must arrive before the gate opens.

For `Promise.race([ .. ])`, only the first promise to resolve (fulfillment or rejection) "wins," and whatever that resolution is becomes the resolution of the returned promise. This pattern is classically called a "latch": first one to open the latch gets through. Consider:

```js
var p1 = Promise.resolve( 42 );
var p2 = Promise.resolve( "Hello World" );
var p3 = Promise.reject( "Oops" );

Promise.race( [p1,p2,p3] )
.then( function(msg){
	console.log( msg );		// 42
} );

Promise.all( [p1,p2,p3] )
.catch( function(err){
	console.error( err );	// "Oops"
} );

Promise.all( [p1,p2] )
.then( function(msgs){
	console.log( msgs );	// [42,"Hello World"]
} );
```

**Warning:** Be careful! If an empty `array` is passed to `Promise.all([ .. ])`, it will fulfill immediately, but `Promise.race([ .. ])` will hang forever and never resolve.

The ES6 `Promise` API is pretty simple and straightforward. It's at least good enough to serve the most basic of async cases, and is a good place to start when rearranging your code from callback hell to something better.

But there's a whole lot of async sophistication that apps often demand which Promises themselves will be limited in addressing. In the next section, we'll dive into those limitations as motivations for the benefit of Promise libraries.

## Promise Limitations

Many of the details we'll discuss in this section have already been alluded to in this chapter, but we'll just make sure to review these limitations specifically.

### Sequence Error Handling

We covered Promise-flavored error handling in detail earlier in this chapter. The limitations of how Promises are designed -- how they chain, specifically -- creates a very easy pitfall where an error in a Promise chain can be silently ignored accidentally.

But there's something else to consider with Promise errors. Because a Promise chain is nothing more than its constituent Promises wired together, there's no entity to refer to the entire chain as a single *thing*, which means there's no external way to observe any errors that may occur.

If you construct a Promise chain that has no error handling in it, any error anywhere in the chain will propagate indefinitely down the chain, until observed (by registering a rejection handler at some step). So, in that specific case, having a reference to the *last* promise in the chain is enough (`p` in the following snippet), because you can register a rejection handler there, and it will be notified of any propagated errors:

```js
// `foo(..)`, `STEP2(..)` and `STEP3(..)` are
// all promise-aware utilities

var p = foo( 42 )
.then( STEP2 )
.then( STEP3 );
```

Although it may seem sneakily confusing, `p` here doesn't point to the first promise in the chain (the one from the `foo(42)` call), but instead from the last promise, the one that comes from the `then(STEP3)` call.

Also, no step in the promise chain is observably doing its own error handling. That means that you could then register a rejection error handler on `p`, and it would be notified if any errors occur anywhere in the chain:

```
p.catch( handleErrors );
```

But if any step of the chain in fact does its own error handling (perhaps hidden/abstracted away from what you can see), your `handleErrors(..)` won't be notified. This may be what you want -- it was, after all, a "handled rejection" -- but it also may *not* be what you want. The complete lack of ability to be notified (of "already handled" rejection errors) is a limitation that restricts capabilities in some use cases.

It's basically the same limitation that exists with a `try..catch` that can catch an exception and simply swallow it. So this isn't a limitation **unique to Promises**, but it *is* something we might wish to have a workaround for.

Unfortunately, many times there is no reference kept for the intermediate steps in a Promise-chain sequence, so without such references, you cannot attach error handlers to reliably observe the errors.

### Single Value

Promises by definition only have a single fulfillment value or a single rejection reason. In simple examples, this isn't that big of a deal, but in more sophisticated scenarios, you may find this limiting.

The typical advice is to construct a values wrapper (such as an `object` or `array`) to contain these multiple messages. This solution works, but it can be quite awkward and tedious to wrap and unwrap your messages with every single step of your Promise chain.

#### Splitting Values

Sometimes you can take this as a signal that you could/should decompose the problem into two or more Promises.

Imagine you have a utility `foo(..)` that produces two values (`x` and `y`) asynchronously:

```js
function getY(x) {
	return new Promise( function(resolve,reject){
		setTimeout( function(){
			resolve( (3 * x) - 1 );
		}, 100 );
	} );
}

function foo(bar,baz) {
	var x = bar * baz;

	return getY( x )
	.then( function(y){
		// wrap both values into container
		return [x,y];
	} );
}

foo( 10, 20 )
.then( function(msgs){
	var x = msgs[0];
	var y = msgs[1];

	console.log( x, y );	// 200 599
} );
```

First, let's rearrange what `foo(..)` returns so that we don't have to wrap `x` and `y` into a single `array` value to transport through one Promise. Instead, we can wrap each value into its own promise:

```js
function foo(bar,baz) {
	var x = bar * baz;

	// return both promises
	return [
		Promise.resolve( x ),
		getY( x )
	];
}

Promise.all(
	foo( 10, 20 )
)
.then( function(msgs){
	var x = msgs[0];
	var y = msgs[1];

	console.log( x, y );
} );
```

Is an `array` of promises really better than an `array` of values passed through a single promise? Syntactically, it's not much of an improvement.

But this approach more closely embraces the Promise design theory. It's now easier in the future to refactor to split the calculation of `x` and `y` into separate functions. It's cleaner and more flexible to let the calling code decide how to orchestrate the two promises -- using `Promise.all([ .. ])` here, but certainly not the only option -- rather than to abstract such details away inside of `foo(..)`.

#### Unwrap/Spread Arguments

The `var x = ..` and `var y = ..` assignments are still awkward overhead. We can employ some functional trickery (hat tip to Reginald Braithwaite, @raganwald on Twitter) in a helper utility:

```js
function spread(fn) {
	return Function.apply.bind( fn, null );
}

Promise.all(
	foo( 10, 20 )
)
.then(
	spread( function(x,y){
		console.log( x, y );	// 200 599
	} )
)
```

That's a bit nicer! Of course, you could inline the functional magic to avoid the extra helper:

```js
Promise.all(
	foo( 10, 20 )
)
.then( Function.apply.bind(
	function(x,y){
		console.log( x, y );	// 200 599
	},
	null
) );
```

These tricks may be neat, but ES6 has an even better answer for us: destructuring. The array destructuring assignment form looks like this:

```js
Promise.all(
	foo( 10, 20 )
)
.then( function(msgs){
	var [x,y] = msgs;

	console.log( x, y );	// 200 599
} );
```

But best of all, ES6 offers the array parameter destructuring form:

```js
Promise.all(
	foo( 10, 20 )
)
.then( function([x,y]){
	console.log( x, y );	// 200 599
} );
```

We've now embraced the one-value-per-Promise mantra, but kept our supporting boilerplate to a minimum!

**Note:** For more information on ES6 destructuring forms, see the *ES6 & Beyond* title of this series.

### Single Resolution

One of the most intrinsic behaviors of Promises is that a Promise can only be resolved once (fulfillment or rejection). For many async use cases, you're only retrieving a value once, so this works fine.

But there's also a lot of async cases that fit into a different model -- one that's more akin to events and/or streams of data. It's not clear on the surface how well Promises can fit into such use cases, if at all. Without a significant abstraction on top of Promises, they will completely fall short for handling multiple value resolution.

Imagine a scenario where you might want to fire off a sequence of async steps in response to a stimulus (like an event) that can in fact happen multiple times, like a button click.

This probably won't work the way you want:

```js
// `click(..)` binds the `"click"` event to a DOM element
// `request(..)` is the previously defined Promise-aware Ajax

var p = new Promise( function(resolve,reject){
	click( "#mybtn", resolve );
} );

p.then( function(evt){
	var btnID = evt.currentTarget.id;
	return request( "http://some.url.1/?id=" + btnID );
} )
.then( function(text){
	console.log( text );
} );
```

The behavior here only works if your application calls for the button to be clicked just once. If the button is clicked a second time, the `p` promise has already been resolved, so the second `resolve(..)` call would be ignored.

Instead, you'd probably need to invert the paradigm, creating a whole new Promise chain for each event firing:

```js
click( "#mybtn", function(evt){
	var btnID = evt.currentTarget.id;

	request( "http://some.url.1/?id=" + btnID )
	.then( function(text){
		console.log( text );
	} );
} );
```

This approach will *work* in that a whole new Promise sequence will be fired off for each `"click"` event on the button.

But beyond just the ugliness of having to define the entire Promise chain inside the event handler, this design in some respects violates the idea of separation of concerns/capabilities (SoC). You might very well want to define your event handler in a different place in your code from where you define the *response* to the event (the Promise chain). That's pretty awkward to do in this pattern, without helper mechanisms.

**Note:** Another way of articulating this limitation is that it'd be nice if we could construct some sort of "observable" that we can subscribe a Promise chain to. There are libraries that have created these abstractions (such as RxJS -- http://rxjs.codeplex.com/), but the abstractions can seem so heavy that you can't even see the nature of Promises anymore. Such heavy abstraction brings important questions to mind such as whether (sans Promises) these mechanisms are as *trustable* as Promises themselves have been designed to be. We'll revisit the "Observable" pattern in Appendix B.

### Inertia

One concrete barrier to starting to use Promises in your own code is all the code that currently exists which is not already Promise-aware. If you have lots of callback-based code, it's far easier to just keep coding in that same style.

"A code base in motion (with callbacks) will remain in motion (with callbacks) unless acted upon by a smart, Promises-aware developer."

Promises offer a different paradigm, and as such, the approach to the code can be anywhere from just a little different to, in some cases, radically different. You have to be intentional about it, because Promises will not just naturally shake out from the same ol' ways of doing code that have served you well thus far.

Consider a callback-based scenario like the following:

```js
function foo(x,y,cb) {
	ajax(
		"http://some.url.1/?x=" + x + "&y=" + y,
		cb
	);
}

foo( 11, 31, function(err,text) {
	if (err) {
		console.error( err );
	}
	else {
		console.log( text );
	}
} );
```

Is it immediately obvious what the first steps are to convert this callback-based code to Promise-aware code? Depends on your experience. The more practice you have with it, the more natural it will feel. But certainly, Promises don't just advertise on the label exactly how to do it -- there's no one-size-fits-all answer -- so the responsibility is up to you.

As we've covered before, we definitely need an Ajax utility that is Promise-aware instead of callback-based, which we could call `request(..)`. You can make your own, as we have already. But the overhead of having to manually define Promise-aware wrappers for every callback-based utility makes it less likely you'll choose to refactor to Promise-aware coding at all.

Promises offer no direct answer to that limitation. Most Promise libraries do offer a helper, however. But even without a library, imagine a helper like this:

```js
// polyfill-safe guard check
if (!Promise.wrap) {
	Promise.wrap = function(fn) {
		return function() {
			var args = [].slice.call( arguments );

			return new Promise( function(resolve,reject){
				fn.apply(
					null,
					args.concat( function(err,v){
						if (err) {
							reject( err );
						}
						else {
							resolve( v );
						}
					} )
				);
			} );
		};
	};
}
```

OK, that's more than just a tiny trivial utility. However, although it may look a bit intimidating, it's not as bad as you'd think. It takes a function that expects an error-first style callback as its last parameter, and returns a new one that automatically creates a Promise to return, and substitutes the callback for you, wired up to the Promise fulfillment/rejection.

Rather than waste too much time talking about *how* this `Promise.wrap(..)` helper works, let's just look at how we use it:

```js
var request = Promise.wrap( ajax );

request( "http://some.url.1/" )
.then( .. )
..
```

Wow, that was pretty easy!

`Promise.wrap(..)` does **not** produce a Promise. It produces a function that will produce Promises. In a sense, a Promise-producing function could be seen as a "Promise factory." I propose "promisory" as the name for such a thing ("Promise" + "factory").

The act of wrapping a callback-expecting function to be a Promise-aware function is sometimes referred to as "lifting" or "promisifying". But there doesn't seem to be a standard term for what to call the resultant function other than a "lifted function", so I like "promisory" better as I think it's more descriptive.

**Note:** Promisory isn't a made-up term. It's a real word, and its definition means to contain or convey a promise. That's exactly what these functions are doing, so it turns out to be a pretty perfect terminology match!

So, `Promise.wrap(ajax)` produces an `ajax(..)` promisory we call `request(..)`, and that promisory produces Promises for Ajax responses.

If all functions were already promisories, we wouldn't need to make them ourselves, so the extra step is a tad bit of a shame. But at least the wrapping pattern is (usually) repeatable so we can put it into a `Promise.wrap(..)` helper as shown to aid our promise coding.

So back to our earlier example, we need a promisory for both `ajax(..)` and `foo(..)`:

```js
// make a promisory for `ajax(..)`
var request = Promise.wrap( ajax );

// refactor `foo(..)`, but keep it externally
// callback-based for compatibility with other
// parts of the code for now -- only use
// `request(..)`'s promise internally.
function foo(x,y,cb) {
	request(
		"http://some.url.1/?x=" + x + "&y=" + y
	)
	.then(
		function fulfilled(text){
			cb( null, text );
		},
		cb
	);
}

// now, for this code's purposes, make a
// promisory for `foo(..)`
var betterFoo = Promise.wrap( foo );

// and use the promisory
betterFoo( 11, 31 )
.then(
	function fulfilled(text){
		console.log( text );
	},
	function rejected(err){
		console.error( err );
	}
);
```

Of course, while we're refactoring `foo(..)` to use our new `request(..)` promisory, we could just make `foo(..)` a promisory itself, instead of remaining callback-based and needing to make and use the subsequent `betterFoo(..)` promisory. This decision just depends on whether `foo(..)` needs to stay callback-based compatible with other parts of the code base or not.

Consider:

```js
// `foo(..)` is now also a promisory because it
// delegates to the `request(..)` promisory
function foo(x,y) {
	return request(
		"http://some.url.1/?x=" + x + "&y=" + y
	);
}

foo( 11, 31 )
.then( .. )
..
```

While ES6 Promises don't natively ship with helpers for such promisory wrapping, most libraries provide them, or you can make your own. Either way, this particular limitation of Promises is addressable without too much pain (certainly compared to the pain of callback hell!).

### Promise Uncancelable

Once you create a Promise and register a fulfillment and/or rejection handler for it, there's nothing external you can do to stop that progression if something else happens to make that task moot.

**Note:** Many Promise abstraction libraries provide facilities to cancel Promises, but this is a terrible idea! Many developers wish Promises had natively been designed with external cancelation capability, but the problem is that it would let one consumer/observer of a Promise affect some other consumer's ability to observe that same Promise. This violates the future-value's trustability (external immutability), but moreover is the embodiment of the "action at a distance" anti-pattern (http://en.wikipedia.org/wiki/Action_at_a_distance_%28computer_programming%29). Regardless of how useful it seems, it will actually lead you straight back into the same nightmares as callbacks.

Consider our Promise timeout scenario from earlier:

```js
var p = foo( 42 );

Promise.race( [
	p,
	timeoutPromise( 3000 )
] )
.then(
	doSomething,
	handleError
);

p.then( function(){
	// still happens even in the timeout case :(
} );
```

The "timeout" was external to the promise `p`, so `p` itself keeps going, which we probably don't want.

One option is to invasively define your resolution callbacks:

```js
var OK = true;

var p = foo( 42 );

Promise.race( [
	p,
	timeoutPromise( 3000 )
	.catch( function(err){
		OK = false;
		throw err;
	} )
] )
.then(
	doSomething,
	handleError
);

p.then( function(){
	if (OK) {
		// only happens if no timeout! :)
	}
} );
```

This is ugly. It works, but it's far from ideal. Generally, you should try to avoid such scenarios.

But if you can't, the ugliness of this solution should be a clue that *cancelation* is a functionality that belongs at a higher level of abstraction on top of Promises. I'd recommend you look to Promise abstraction libraries for assistance rather than hacking it yourself.

**Note:** My *asynquence* Promise abstraction library provides just such an abstraction and an `abort()` capability for the sequence, all of which will be discussed in Appendix A.

A single Promise is not really a flow-control mechanism (at least not in a very meaningful sense), which is exactly what *cancelation* refers to; that's why Promise cancelation would feel awkward.

By contrast, a chain of Promises taken collectively together -- what I like to call a "sequence" -- *is* a flow control expression, and thus it's appropriate for cancelation to be defined at that level of abstraction.

No individual Promise should be cancelable, but it's sensible for a *sequence* to be cancelable, because you don't pass around a sequence as a single immutable value like you do with a Promise.

### Promise Performance

This particular limitation is both simple and complex.

Comparing how many pieces are moving with a basic callback-based async task chain versus a Promise chain, it's clear Promises have a fair bit more going on, which means they are naturally at least a tiny bit slower. Think back to just the simple list of trust guarantees that Promises offer, as compared to the ad hoc solution code you'd have to layer on top of callbacks to achieve the same protections.

More work to do, more guards to protect, means that Promises *are* slower as compared to naked, untrustable callbacks. That much is obvious, and probably simple to wrap your brain around.

But how much slower? Well... that's actually proving to be an incredibly difficult question to answer absolutely, across the board.

Frankly, it's kind of an apples-to-oranges comparison, so it's probably the wrong question to ask. You should actually compare whether an ad-hoc callback system with all the same protections manually layered in is faster than a Promise implementation.

If Promises have a legitimate performance limitation, it's more that they don't really offer a line-item choice as to which trustability protections you want/need or not -- you get them all, always.

Nevertheless, if we grant that a Promise is generally a *little bit slower* than its non-Promise, non-trustable callback equivalent -- assuming there are places where you feel you can justify the lack of trustability -- does that mean that Promises should be avoided across the board, as if your entire application is driven by nothing but must-be-utterly-the-fastest code possible?

Sanity check: if your code is legitimately like that, **is JavaScript even the right language for such tasks?** JavaScript can be optimized to run applications very performantly (see Chapter 5 and Chapter 6). But is obsessing over tiny performance tradeoffs with Promises, in light of all the benefits they offer, *really* appropriate?

Another subtle issue is that Promises make *everything* async, which means that some immediately (synchronously) complete steps still defer advancement of the next step to a Job (see Chapter 1). That means that it's possible that a sequence of Promise tasks could complete ever-so-slightly slower than the same sequence wired up with callbacks.

Of course, the question here is this: are these potential slips in tiny fractions of performance *worth* all the other articulated benefits of Promises we've laid out across this chapter?

My take is that in virtually all cases where you might think Promise performance is slow enough to be concerned, it's actually an anti-pattern to optimize away the benefits of Promise trustability and composability by avoiding them altogether.

Instead, you should default to using them across the code base, and then profile and analyze your application's hot (critical) paths. Are Promises *really* a bottleneck, or are they just a theoretical slowdown? Only *then*, armed with actual valid benchmarks (see Chapter 6) is it responsible and prudent to factor out the Promises in just those identified critical areas.

Promises are a little slower, but in exchange you're getting a lot of trustability, non-Zalgo predictability, and composability built in. Maybe the limitation is not actually their performance, but your lack of perception of their benefits?

## Review

Promises are awesome. Use them. They solve the *inversion of control* issues that plague us with callbacks-only code.

They don't get rid of callbacks, they just redirect the orchestration of those callbacks to a trustable intermediary mechanism that sits between us and another utility.

Promise chains also begin to address (though certainly not perfectly) a better way of expressing async flow in sequential fashion, which helps our brains plan and maintain async JS code better. We'll see an even better solution to *that* problem in the next chapter!
