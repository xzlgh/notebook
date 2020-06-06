# apply & call

我之前有一篇文章写了我们怎么去利用apply实现bind方法。我们知道bind、apply和call这三者都是改变this指向的过程,今天我们来讨论一下如何去实现apply & call。

## apply()

"换汤不换药" —— 这里我们还是看看apply是怎么用的，它有什么样的功能。

<b>功能</b>

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)上的定义：apply() 方法调用一个具有给定this值的函数，以及作为一个数组（或类似数组对象）提供的参数。

<b>params</b>

-  thisArg  给定的this值
-  [argsArray] 数据类型为Array，表示函数的参数，该参数为可选参数




