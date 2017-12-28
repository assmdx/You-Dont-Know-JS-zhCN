# You Don't Know JS: Async & Performance
# Chapter 2: Callbacks

在第1章，我们探索了JS编程里的异步概念。我们着重于理解单线程下的事件轮询队列驱动事件（异步函数调用）。我们也探索了不同并发模式，它们解释了*同步*事件运行链和“进程”（任务、函数调用等）的关系。

第1章的所有例子都使用函数作为个人的最小的的操作单元，即函数中的语句是按照预期的顺序执行（编译器级别的上一层），但是在函数顺序级别，事件（即异步函数调用）发生的顺序是可变的。

在所有的场景下，函数被作为一个“回调”，因为它的角色是事件轮训“召回”到程序里的目标，只要它在队列里就会被处理。

你也确实看到了，回调是目前为止JS编程里最普通的异步表达和管理方式，的确，回调是这门语言里大多数异步模式的基础。

无数JS程序，即使是精细又复杂的那些，也都是把回调当作异步基础的（当然包括我们在第1章里探索的并发交互模式），回调函数是JS异步主力，并且工作的很好。

除了……回调并不是没有缺点，许多开发者激动于更好的异步模式*promises*（双关语！），但是如果你不理解它的概念和原理你不能有效的使用任何概念。

这一章，我们会深入探索其中几个，从而解释为何更成熟的异步模式是必要的。

## Continuations

我们回到第1章开始的异步回调例子，为了突出一点，我稍微修改了一下：

```js
// A
ajax( "..", function(..){
	// C
} );
// B
```

`// A`和`// B`代表程序的第一部分（即*当前*），`//C`代表程序的第二部分（即*后来*）。第一部分立刻执行，然后中间有一段“暂停”，未来某时刻，如果Ajax调用有了响应，那么程序会从刚才暂停的地方*继续*完成第二部分的执行。

换句话说，回调函数包裹或封装了*继续*程序，我们让代码更简单一些：

```js
// A
setTimeout( function(){
	// C
}, 1000 );
// B
```

停下来想一想，你会如何（给那些对JS运行原理知之甚少的人）描述程序的运转，说出来，这会对你理解接下来的知识点很有帮助。

大多数读者刚才可能会想或说类似这这样的东西“执行A，然后创建一个计时器等待1000毫秒，计时结束时，执行C。”，你的和这个有多相似？

你也可能会这样讲：“执行A，创建一个1000毫秒的定时器，然后执行B，当计时结束时，执行C。”这个比上面那个更加精确，你能看出区别吗？

尽管第二种说法更精确，但是两个版本在我们理解代码，以及引擎理解代码方面都不够准确。这种代沟很微妙却也很长久，也是理解回调来实现异步的缺点的关键。

我们引入一个回调函数的扩展，意味着我们允许大脑和代码运作之间存在分歧。而一旦这两者有分歧（如你所见，到目前为止，它不仅仅发生在上面这种场景），我们就会觉得代码难以理解，分析，调试以及维护。

## Sequential Brain
我相信大多数读者都听过一些人（甚至读者本人）都说过：”我可以三心二意“。“三心二意”的结果不是搞笑的（比如儿童游戏patting-head-rubbing-stomach）就是平凡的（边走路边嚼口香糖），甚至完全是危险的（边开车边发短信）。

但我们真的可以“三心二意”吗？我们真的可以同时做两件需要专注动作和思考的事情吗？我们大脑的最高水平是可以并行多线程运转吗？

答案可能让你吃惊：**或许并不能。**

我们大脑的还真不是这么构造的，我们只不过是大家都不愿承认的单任务者。我们在一个时刻真的只能思考一件事情。

我说的不是我们的无意识、潜意识和大脑自动功能，比如心跳、呼吸和眨眼。这些都是我们生存的关键，但是我们不需要故意去消耗脑力给它们。庆幸地是，当我们沉浸在一分钟15次查看社交消息里时，我们的大脑在后台（进程！）继续那些重要的任务。

说说你当前大脑里排在最前列的那个任务，于我，是写本书的这些文字，我有同时在做其它高级功能的脑部活动吗？当然没有。我很快很容易被干扰——在上几段落里有几十次！

当我们*假装*三心二意，比如试图在与友人或家人讲电话时写下点儿东西，我们实际上更像是在快速切换。也就是说，我们在两个及更多任务里快速连续来回切换，*同时*以小、快的小部分处理每一个任务。我们进行的如此之快，从外部来看，我们好像是在*并行*。

是不是听起来和异步并发事件很像（正如JS里一般发生的一样）？！如果没有，请回头看第1章！

事实上，为了将庞杂的神经学简化为我们可以在此讨论的东西：我们的大脑像事件轮询队列。

如果把我写下的每一个字母（或单词）当作一个异步事件，在这句话里有好几次机会让其他事件中断我的大脑，比如我的感觉，甚至我随机的想法。

我不会在每一次机会前都被中断并被拉到另一个“进程”里，但是经常会发生这样的事情，我的大脑几乎经常切换到不同的场景里（即“进程”），这种糟糕的感觉JS引擎一定也有吧。

### Doing Versus Planning

所以，我们的大脑可以被认为是单线程的事件轮询队列，JS引擎也是，这听起来好像很配。

但我们需要注意那些比我们所分析的更细微的地方，我们计划任务与我们大脑实际处理这些任务有可观测的大不同。

又一次，我写下这样的比喻，我此刻大略的计划是一直写，依次完成我想到的几点。我没有打算让任何中断或者非线性的活动插入进来，但是我的大脑总是一直在切换。

尽管在某种程度上来讲，我们的大脑是异步事件驱动，但我们似乎是顺序同步的计划任务。“我需要去商场，买些牛奶，然后去干洗店洗衣“。

你可能发现这种高等级的想法（计划）并不是很异步，实际上，我们一般很少刻意以事件的形式来思考。相反，我们仔细顺序地计划事情（A，然后B，然后C），假设从某种程度上有一种暂时的阻力，让B等待A。

当一个开发者写代码时，他们在计划一系列要发生的行为。如果他们够专业，他们会**仔细的计划**。“我需要把`x`的值赋给`z`，然后赋给`y`“，等等。

当我们写同步代码时，一条语句接着一条，就好像我们的待办事项：

```js
// swap `x` and `y` (via temp variable `z`)
z = x;
x = y;
y = z;
```

这三条语句是同步的，所以`x = y`等待`z = x`执行完毕，`y = z`等待`x = y`完毕，即这三条语句当前必须按照一定顺序执行，一条接着一条。幸好这里我们无需在意异步事件，否则代码很快就变得更加复杂！

所以，如果同步的大脑计划映射出同步的代码语句，那我们的大脑映射异步代码水平如何？

事实是，我们在代码里（借助回调）表达异步并不如同步大脑计划映射的那么好。

你能像下面这样来计划你的待办事项吗？

> “我需要去超市，在路上我肯定会接到一个电话，所以‘嗨，妈妈’，在她开始讲话时，我将在GPS上查找超市的地点，但是要话费几秒来加载，所以我会调小音量，所以我可以更好的听妈妈讲话，然后我意识到现在好冷，我忘记穿夹克了，无妨，继续开车和与妈妈讲电话，然后安全带响了提醒我系上安全带，所一‘啊呀，妈妈，我在系安全带，我一直都这样！’，啊，终于GPS有了导航，现在……”

上面这种我们如此计划我们的日常以及思考该做什么该按什么顺序，听起来可能很搞笑，但是这的确时我们大脑的在功能水平上的处理，记住，这不是多任务，只不过是快速的场景切换。

之所以开发者写异步事件代码很困难，尤其是只有回调这种方式，意识流的思考／计划对我们来说很反常。

我们是按步骤思考的，但是当我们从同步进入到异步时，代码中的工具（回调）却不是按步骤来的。

**这**就是为何使用回调的异步JS代码这么难创建和分析：因为这不是我们大脑做计划的套路。

**注意：** 只知道为何有些代码不生效并不意味着就知道了它真正的原理！这就是经典的“纸牌屋”比喻：“有效，但不知道原因，所以任何人都不要动它！”你可能也听说过“他人即地狱”（萨特），程序员修改了这句名言，“他人的代码就是地狱”。我确信这个：“不懂我自己的代码才是地狱”。回调就是主犯之一。

### Nested/Chained Callbacks

有这样的代码：

```js
listen( "click", function handler(evt){
	setTimeout( function request(){
		ajax( "http://some.url.1", function response(text){
			if (text == "hello") {
				handler();
			}
			else if (text == "world") {
				request();
			}
		} );
	}, 500) ;
} );
```

这种代码你应该很熟悉，我们在这里嵌套了3个函数，每一个都代表着异步中的一步（任务，“进程”）。这种代码常被称为“回调地狱“，有时也叫做“噩梦金字塔”（因为嵌套缩紧出现的三角形）。

但是“回调地狱”和嵌套／缩进没什么关系，它有更深层次的原因，我们稍后章节会详细说明。

首先，我们等待“点击”事件，然后等待定时器到期，然后等待Ajax响应，然后再来一次。

乍看之下，这段代码似乎把异步自然地映射到了顺序的大脑计划。

首先（*现在），我们：

```js
listen( "..", function handler(..){
	// ..
} );
```

然后（*后来*），我们：

```js
setTimeout( function request(..){
	// ..
}, 500) ;
```

然后（*后来*），我们：

```js
ajax( "..", function response(..){
	// ..
} );
```

最终（最后一个*后来*），我们：

```js
if ( .. ) {
	// ..
}
else ..
```

但是按照这种线性方式分析是有问题的：

首先，我们的步骤按照顺序（1，2，3然后4……）是个巧合，在真实的异步JS程序里，一定有许多其它的噪声干扰存在，这些噪声我们必须熟练的在脑中演练，当我们从一个函数跳到另一个时。在充满回调的代码里理解异步工作流不是不可能，但是很不容易，即使花费了大量的练习。

而且，还有更深层的错误，在上述示例中并不明显，我们看另一个场景（伪代码）示例：

```js
doA( function(){
	doB();

	doC( function(){
		doD();
	} )

	doE();
} );

doF();
```
尽管你们中经验丰富的人能正确的指出执行顺序，但我打赌看到它的第一眼你一定很困惑，并且花费了些脑力来识别出顺序，执行顺序如下：

* `doA()`
* `doF()`
* `doB()`
* `doC()`
* `doE()`
* `doD()`

你看到的第一眼是否得出了正确的结论？

好的，有些读者可能会觉得我函数命名不够公平，会误导人，我发誓我只是按照代码里出现的先后顺序来命名的，但是让我们再尝试：

```js
doA( function(){
	doC();

	doD( function(){
		doF();
	} )

	doE();
} );

doB();
```

现在，我按照它们的真实执行顺序进行了命名，我仍然打赌，即使这种场景你已经有了经验，也不见得有些读者会顺着`A -> B -> C -> D -> E -> F`这样的顺序，你的眼睛会在这段代码上频繁地上下跳动。

But even if that all comes natural to you, there's still one more hazard that could wreak havoc. Can you spot what it is?

What if `doA(..)` or `doD(..)` aren't actually async, the way we obviously assumed them to be? Uh oh, now the order is different. If they're both sync (and maybe only sometimes, depending on the conditions of the program at the time), the order is now `A -> C -> D -> F -> E -> B`.

That sound you just heard faintly in the background is the sighs of thousands of JS developers who just had a face-in-hands moment.

Is nesting the problem? Is that what makes it so hard to trace the async flow? That's part of it, certainly.

But let me rewrite the previous nested event/timeout/Ajax example without using nesting:

```js
listen( "click", handler );

function handler() {
	setTimeout( request, 500 );
}

function request(){
	ajax( "http://some.url.1", response );
}

function response(text){
	if (text == "hello") {
		handler();
	}
	else if (text == "world") {
		request();
	}
}
```

This formulation of the code is not hardly as recognizable as having the nesting/indentation woes of its previous form, and yet it's every bit as susceptible to "callback hell." Why?

As we go to linearly (sequentially) reason about this code, we have to skip from one function, to the next, to the next, and bounce all around the code base to "see" the sequence flow. And remember, this is simplified code in sort of best-case fashion. We all know that real async JS program code bases are often fantastically more jumbled, which makes such reasoning orders of magnitude more difficult.

Another thing to notice: to get steps 2, 3, and 4 linked together so they happen in succession, the only affordance callbacks alone gives us is to hardcode step 2 into step 1, step 3 into step 2, step 4 into step 3, and so on. The hardcoding isn't necessarily a bad thing, if it really is a fixed condition that step 2 should always lead to step 3.

But the hardcoding definitely makes the code a bit more brittle, as it doesn't account for anything going wrong that might cause a deviation in the progression of steps. For example, if step 2 fails, step 3 never gets reached, nor does step 2 retry, or move to an alternate error handling flow, and so on.

All of these issues are things you *can* manually hardcode into each step, but that code is often very repetitive and not reusable in other steps or in other async flows in your program.

Even though our brains might plan out a series of tasks in a sequential type of way (this, then this, then this), the evented nature of our brain operation makes recovery/retry/forking of flow control almost effortless. If you're out running errands, and you realize you left a shopping list at home, it doesn't end the day because you didn't plan that ahead of time. Your brain routes around this hiccup easily: you go home, get the list, then head right back out to the store.

But the brittle nature of manually hardcoded callbacks (even with hardcoded error handling) is often far less graceful. Once you end up specifying (aka pre-planning) all the various eventualities/paths, the code becomes so convoluted that it's hard to ever maintain or update it.

**That** is what "callback hell" is all about! The nesting/indentation are basically a side show, a red herring.

And as if all that's not enough, we haven't even touched what happens when two or more chains of these callback continuations are happening *simultaneously*, or when the third step branches out into "parallel" callbacks with gates or latches, or... OMG, my brain hurts, how about yours!?

Are you catching the notion here that our sequential, blocking brain planning behaviors just don't map well onto callback-oriented async code? That's the first major deficiency to articulate about callbacks: they express asynchrony in code in ways our brains have to fight just to keep in sync with (pun intended!).

## Trust Issues

The mismatch between sequential brain planning and callback-driven async JS code is only part of the problem with callbacks. There's something much deeper to be concerned about.

Let's once again revisit the notion of a callback function as the continuation (aka the second half) of our program:

```js
// A
ajax( "..", function(..){
	// C
} );
// B
```

`// A` and `// B` happen *now*, under the direct control of the main JS program. But `// C` gets deferred to happen *later*, and under the control of another party -- in this case, the `ajax(..)` function. In a basic sense, that sort of hand-off of control doesn't regularly cause lots of problems for programs.

But don't be fooled by its infrequency that this control switch isn't a big deal. In fact, it's one of the worst (and yet most subtle) problems about callback-driven design. It revolves around the idea that sometimes `ajax(..)` (i.e., the "party" you hand your callback continuation to) is not a function that you wrote, or that you directly control. Many times, it's a utility provided by some third party.

We call this "inversion of control," when you take part of your program and give over control of its execution to another third party. There's an unspoken "contract" that exists between your code and the third-party utility -- a set of things you expect to be maintained.

### Tale of Five Callbacks

It might not be terribly obvious why this is such a big deal. Let me construct an exaggerated scenario to illustrate the hazards of trust at play.

Imagine you're a developer tasked with building out an ecommerce checkout system for a site that sells expensive TVs. You already have all the various pages of the checkout system built out just fine. On the last page, when the user clicks "confirm" to buy the TV, you need to call a third-party function (provided say by some analytics tracking company) so that the sale can be tracked.

You notice that they've provided what looks like an async tracking utility, probably for the sake of performance best practices, which means you need to pass in a callback function. In this continuation that you pass in, you will have the final code that charges the customer's credit card and displays the thank you page.

This code might look like:

```js
analytics.trackPurchase( purchaseData, function(){
	chargeCreditCard();
	displayThankyouPage();
} );
```

Easy enough, right? You write the code, test it, everything works, and you deploy to production. Everyone's happy!

Six months go by and no issues. You've almost forgotten you even wrote that code. One morning, you're at a coffee shop before work, casually enjoying your latte, when you get a panicked call from your boss insisting you drop the coffee and rush into work right away.

When you arrive, you find out that a high-profile customer has had his credit card charged five times for the same TV, and he's understandably upset. Customer service has already issued an apology and processed a refund. But your boss demands to know how this could possibly have happened. "Don't we have tests for stuff like this!?"

You don't even remember the code you wrote. But you dig back in and start trying to find out what could have gone awry.

After digging through some logs, you come to the conclusion that the only explanation is that the analytics utility somehow, for some reason, called your callback five times instead of once. Nothing in their documentation mentions anything about this.

Frustrated, you contact customer support, who of course is as astonished as you are. They agree to escalate it to their developers, and promise to get back to you. The next day, you receive a lengthy email explaining what they found, which you promptly forward to your boss.

Apparently, the developers at the analytics company had been working on some experimental code that, under certain conditions, would retry the provided callback once per second, for five seconds, before failing with a timeout. They had never intended to push that into production, but somehow they did, and they're totally embarrassed and apologetic. They go into plenty of detail about how they've identified the breakdown and what they'll do to ensure it never happens again. Yadda, yadda.

What's next?

You talk it over with your boss, but he's not feeling particularly comfortable with the state of things. He insists, and you reluctantly agree, that you can't trust *them* anymore (that's what bit you), and that you'll need to figure out how to protect the checkout code from such a vulnerability again.

After some tinkering, you implement some simple ad hoc code like the following, which the team seems happy with:

```js
var tracked = false;

analytics.trackPurchase( purchaseData, function(){
	if (!tracked) {
		tracked = true;
		chargeCreditCard();
		displayThankyouPage();
	}
} );
```

**Note:** This should look familiar to you from Chapter 1, because we're essentially creating a latch to handle if there happen to be multiple concurrent invocations of our callback.

But then one of your QA engineers asks, "what happens if they never call the callback?" Oops. Neither of you had thought about that.

You begin to chase down the rabbit hole, and think of all the possible things that could go wrong with them calling your callback. Here's roughly the list you come up with of ways the analytics utility could misbehave:

* Call the callback too early (before it's been tracked)
* Call the callback too late (or never)
* Call the callback too few or too many times (like the problem you encountered!)
* Fail to pass along any necessary environment/parameters to your callback
* Swallow any errors/exceptions that may happen
* ...

That should feel like a troubling list, because it is. You're probably slowly starting to realize that you're going to have to invent an awful lot of ad hoc logic **in each and every single callback** that's passed to a utility you're not positive you can trust.

Now you realize a bit more completely just how hellish "callback hell" is.

### Not Just Others' Code

Some of you may be skeptical at this point whether this is as big a deal as I'm making it out to be. Perhaps you don't interact with truly third-party utilities much if at all. Perhaps you use versioned APIs or self-host such libraries, so that its behavior can't be changed out from underneath you.

So, contemplate this: can you even *really* trust utilities that you do theoretically control (in your own code base)?

Think of it this way: most of us agree that at least to some extent we should build our own internal functions with some defensive checks on the input parameters, to reduce/prevent unexpected issues.

Overly trusting of input:
```js
function addNumbers(x,y) {
	// + is overloaded with coercion to also be
	// string concatenation, so this operation
	// isn't strictly safe depending on what's
	// passed in.
	return x + y;
}

addNumbers( 21, 21 );	// 42
addNumbers( 21, "21" );	// "2121"
```

Defensive against untrusted input:
```js
function addNumbers(x,y) {
	// ensure numerical input
	if (typeof x != "number" || typeof y != "number") {
		throw Error( "Bad parameters" );
	}

	// if we get here, + will safely do numeric addition
	return x + y;
}

addNumbers( 21, 21 );	// 42
addNumbers( 21, "21" );	// Error: "Bad parameters"
```

Or perhaps still safe but friendlier:
```js
function addNumbers(x,y) {
	// ensure numerical input
	x = Number( x );
	y = Number( y );

	// + will safely do numeric addition
	return x + y;
}

addNumbers( 21, 21 );	// 42
addNumbers( 21, "21" );	// 42
```

However you go about it, these sorts of checks/normalizations are fairly common on function inputs, even with code we theoretically entirely trust. In a crude sort of way, it's like the programming equivalent of the geopolitical principle of "Trust But Verify."

So, doesn't it stand to reason that we should do the same thing about composition of async function callbacks, not just with truly external code but even with code we know is generally "under our own control"? **Of course we should.**

But callbacks don't really offer anything to assist us. We have to construct all that machinery ourselves, and it often ends up being a lot of boilerplate/overhead that we repeat for every single async callback.

The most troublesome problem with callbacks is *inversion of control* leading to a complete breakdown along all those trust lines.

If you have code that uses callbacks, especially but not exclusively with third-party utilities, and you're not already applying some sort of mitigation logic for all these *inversion of control* trust issues, your code *has* bugs in it right now even though they may not have bitten you yet. Latent bugs are still bugs.

Hell indeed.

## Trying to Save Callbacks

There are several variations of callback design that have attempted to address some (not all!) of the trust issues we've just looked at. It's a valiant, but doomed, effort to save the callback pattern from imploding on itself.

For example, regarding more graceful error handling, some API designs provide for split callbacks (one for the success notification, one for the error notification):

```js
function success(data) {
	console.log( data );
}

function failure(err) {
	console.error( err );
}

ajax( "http://some.url.1", success, failure );
```

In APIs of this design, often the `failure()` error handler is optional, and if not provided it will be assumed you want the errors swallowed. Ugh.

**Note:** This split-callback design is what the ES6 Promise API uses. We'll cover ES6 Promises in much more detail in the next chapter.

Another common callback pattern is called "error-first style" (sometimes called "Node style," as it's also the convention used across nearly all Node.js APIs), where the first argument of a single callback is reserved for an error object (if any). If success, this argument will be empty/falsy (and any subsequent arguments will be the success data), but if an error result is being signaled, the first argument is set/truthy (and usually nothing else is passed):

```js
function response(err,data) {
	// error?
	if (err) {
		console.error( err );
	}
	// otherwise, assume success
	else {
		console.log( data );
	}
}

ajax( "http://some.url.1", response );
```

In both of these cases, several things should be observed.

First, it has not really resolved the majority of trust issues like it may appear. There's nothing about either callback that prevents or filters unwanted repeated invocations. Moreover, things are worse now, because you may get both success and error signals, or neither, and you still have to code around either of those conditions.

Also, don't miss the fact that while it's a standard pattern you can employ, it's definitely more verbose and boilerplate-ish without much reuse, so you're going to get weary of typing all that out for every single callback in your application.

What about the trust issue of never being called? If this is a concern (and it probably should be!), you likely will need to set up a timeout that cancels the event. You could make a utility (proof-of-concept only shown) to help you with that:

```js
function timeoutify(fn,delay) {
	var intv = setTimeout( function(){
			intv = null;
			fn( new Error( "Timeout!" ) );
		}, delay )
	;

	return function() {
		// timeout hasn't happened yet?
		if (intv) {
			clearTimeout( intv );
			fn.apply( this, [ null ].concat( [].slice.call( arguments ) ) );
		}
	};
}
```

Here's how you use it:

```js
// using "error-first style" callback design
function foo(err,data) {
	if (err) {
		console.error( err );
	}
	else {
		console.log( data );
	}
}

ajax( "http://some.url.1", timeoutify( foo, 500 ) );
```

Another trust issue is being called "too early." In application-specific terms, this may actually involve being called before some critical task is complete. But more generally, the problem is evident in utilities that can either invoke the callback you provide *now* (synchronously), or *later* (asynchronously).

This nondeterminism around the sync-or-async behavior is almost always going to lead to very difficult to track down bugs. In some circles, the fictional insanity-inducing monster named Zalgo is used to describe the sync/async nightmares. "Don't release Zalgo!" is a common cry, and it leads to very sound advice: always invoke callbacks asynchronously, even if that's "right away" on the next turn of the event loop, so that all callbacks are predictably async.

**Note:** For more information on Zalgo, see Oren Golan's "Don't Release Zalgo!" (https://github.com/oren/oren.github.io/blob/master/posts/zalgo.md) and Isaac Z. Schlueter's "Designing APIs for Asynchrony" (http://blog.izs.me/post/59142742143/designing-apis-for-asynchrony).

Consider:

```js
function result(data) {
	console.log( a );
}

var a = 0;

ajax( "..pre-cached-url..", result );
a++;
```

Will this code print `0` (sync callback invocation) or `1` (async callback invocation)? Depends... on the conditions.

You can see just how quickly the unpredictability of Zalgo can threaten any JS program. So the silly-sounding "never release Zalgo" is actually incredibly common and solid advice. Always be asyncing.

What if you don't know whether the API in question will always execute async? You could invent a utility like this `asyncify(..)` proof-of-concept:

```js
function asyncify(fn) {
	var orig_fn = fn,
		intv = setTimeout( function(){
			intv = null;
			if (fn) fn();
		}, 0 )
	;

	fn = null;

	return function() {
		// firing too quickly, before `intv` timer has fired to
		// indicate async turn has passed?
		if (intv) {
			fn = orig_fn.bind.apply(
				orig_fn,
				// add the wrapper's `this` to the `bind(..)`
				// call parameters, as well as currying any
				// passed in parameters
				[this].concat( [].slice.call( arguments ) )
			);
		}
		// already async
		else {
			// invoke original function
			orig_fn.apply( this, arguments );
		}
	};
}
```

You use `asyncify(..)` like this:

```js
function result(data) {
	console.log( a );
}

var a = 0;

ajax( "..pre-cached-url..", asyncify( result ) );
a++;
```

Whether the Ajax request is in the cache and resolves to try to call the callback right away, or must be fetched over the wire and thus complete later asynchronously, this code will always output `1` instead of `0` -- `result(..)` cannot help but be invoked asynchronously, which means the `a++` has a chance to run before `result(..)` does.

Yay, another trust issued "solved"! But it's inefficient, and yet again more bloated boilerplate to weigh your project down.

That's just the story, over and over again, with callbacks. They can do pretty much anything you want, but you have to be willing to work hard to get it, and oftentimes this effort is much more than you can or should spend on such code reasoning.

You might find yourself wishing for built-in APIs or other language mechanics to address these issues. Finally ES6 has arrived on the scene with some great answers, so keep reading!

## Review

Callbacks are the fundamental unit of asynchrony in JS. But they're not enough for the evolving landscape of async programming as JS matures.

First, our brains plan things out in sequential, blocking, single-threaded semantic ways, but callbacks express asynchronous flow in a rather nonlinear, nonsequential way, which makes reasoning properly about such code much harder. Bad to reason about code is bad code that leads to bad bugs.

We need a way to express asynchrony in a more synchronous, sequential, blocking manner, just like our brains do.

Second, and more importantly, callbacks suffer from *inversion of control* in that they implicitly give control over to another party (often a third-party utility not in your control!) to invoke the *continuation* of your program. This control transfer leads us to a troubling list of trust issues, such as whether the callback is called more times than we expect.

Inventing ad hoc logic to solve these trust issues is possible, but it's more difficult than it should be, and it produces clunkier and harder to maintain code, as well as code that is likely insufficiently protected from these hazards until you get visibly bitten by the bugs.

We need a generalized solution to **all of the trust issues**, one that can be reused for as many callbacks as we create without all the extra boilerplate overhead.

We need something better than callbacks. They've served us well to this point, but the *future* of JavaScript demands more sophisticated and capable async patterns. The subsequent chapters in this book will dive into those emerging evolutions.
