# 使用js来实现bind功能

*在js的世界中，bind函数是我们常用的Function.prototype上的一个方法。自己去实现一遍它的功能，可以使我们对于this指向问题的理解更加深刻。*

对于总也搞不清楚this指向问题，一直处于模糊状态。通过自己动手实现，完全解决了我的这个问题，对于this指向的理解也更加深入。现在我们开始动手实践，如何去实现一个bind函数吧。

<b>首先我们想要实现某一个函数的功能，我们需要知道这个函数是用来做什么的？它能做什么事情？</b>

### bind功能描述

bind() 方法<b>创建一个新的函数</b>，在 bind() 被调用时，这个<b>新函数</b>的 <b>this</b> 被指定为 bind() 的<b>第一个参数</b>，而其余参数将作为新函数的参数，供调用时使用。<b>返回一个</b>原<b>函数</b>的拷贝，并拥有指定的 this 值和初始参数。


### 实现步骤

通过对上述功能描述的分析，我们可以看到，得到如下几条重要信息：
1. 创建一个新的函数
2. 新函数的this指向bind()方法的第一个参数
3. 返回一个函数，返回的函数拥有指定的this值和初始参数

根据上述内容我们可以得到如下代码

> myBind.js

```js
Function.protoptype.myBind = function(thisArg) {
    // 拿到传入的参数,把arguments变成一个真正的数组
    // 可以通过Function.prototype验证arguments是否是真正的数组.arguments只是一个类数组,实际为object类型
    const args = Array.prototype.slice.call(arguments, 1) 
    const self = this
    // 创建一个函数
    var bound = function() {
        // 此函数的this指向thisArg,这里利用apply来扩展this指向
        return self.apply(thisArg, args)
    }

    // 返回一个函数
    return bound
}

// 验证myBind函数
var obj = {
    name: 'obj'
}

var module = {
    name: 'module',
    getName: function(param) {
        console.log(param)
        return this.name
    }
}

var bound = module.getName.myBind(obj, 'test')
console.log(bound()) 
// 输出：
// >: test
// >: obj

// 验证bind函数
var obj1 = {
    name: 'obj'
}

var module1 = {
    name: 'module',
    getName: function(param) {
        console.log(param)
        return this.name
    }
}

var bound1 = module1.getName.bind(obj, 'test')
console.log(bound1())

// 输出：
// >: test
// >: obj
```

从上述输出可以看出基础用法时，原生bind方法，和我们自己实现的myBind输出结果是一致。是不是表示就这样已经就可以了呢？其实不是，我们可以看到上述代码非常简单，细节上面一点都没做处理，真正的bind源码是使用c++实现的，这里只是模拟它的基础功能，我们随后还需要做哪些处理呢？继续往下修改代码。

我们可以从上述代码进行思考，bind()如果不是一个函数调用bind方法会是什么样的效果？

> 验证一下如果不是函数调用bind方法

```js
var authBindObj = {
    name: 'authBindObj'
}

var authObj = {
    name: 'authObj'
}

var boundObj = authObj.bind(authBindObj)
```

当我们运行这段代码时,会出现如下错误提示:

```node
var boundObj = authObj.bind(authBindObj)
                       ^

TypeError: authObj.bind is not a function // 这一句明确告诉我们authObj.bind不是一个函数
    at Object.<anonymous> (E:\study\js\deepStudyJs\sourceCodeImplement\bind1.js:54:24)
    at Module._compile (module.js:653:30)
    at Object.Module._extensions..js (module.js:664:10)
    at Module.load (module.js:566:32)
    at tryModuleLoad (module.js:506:12)
    at Function.Module._load (module.js:498:3)
    at Function.Module.runMain (module.js:694:10)
    at startup (bootstrap_node.js:204:16)
    at bootstrap_node.js:625:3
```

我们可以通过去浏览器开发工具上面尝试点一下Function.prototype对象里面有的方法,以及我们的Object上的方法,可以看到,只有Function对象上面才存在bind方法,所以我们将之前写的myBind()方法进行改造一下

```js
Function.prototype.myBind = function(thisArg) {
    // ...
    const self = this

    // 增加这部分代码,如果不是函数那么就抛出一个异常,告诉开发人员,myBind只能被函数调用
    if (typeof self !== 'function') {
        throw new TypeError(`${self} is not function!`)
    }

    const bound = function() {
        // ...
    }
    // ...
}
```

增加了函数校验之后，我们的方法就可以跟源码中的bind一样，实现以上错误提示的功能

#### 以上是bind的基本功能实现，接下来我们继续看偏函数

在bind()方法中，有另外一个简单的用法 —— 预设初始值。也就是[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)上描述的偏函数。

bind()方法的预设初始值使用方法如下：

```js
// 偏函数
function add(x, y) {
    return x + y
}

var boundAdd = add.bind(null, 10)

console.log(boundAdd(8)) 
// 输出：
// >: 18
```

上述代码中，add需要接收两个参数，但是我们在调用boundAdd的时候，只传了一个参数，输出的结果为18。是因为在调用bind()方法的时候，给了一个参数10。10这个参数就是boundAdd的预设初始值。也就是传递给add函数时x的值。我们当前的myBind的实现达不到这个效果。myBind输出的结果为：

```js
var myBoundAdd = add.myBind(null, 10)
console.log(myBoundAdd(8)) // NaN
```
自定义的myBind输出的是NaN，为什么会出现这种情况呢？我们可以通过在add函数中打印出x和y的值，看到为什么结果为NaN。add函数中接收到的参数x为10，y为undefiend。10+undefined = NaN是成立的。

这里可能会有一个疑问，为什么我的8没有传递过去。是因为我们的代码中并没有实现参数的二次传递。所以需要修改我们实现的代码

```js
Function.prototype.myBind = function(thisArg) {
    // ...
    var bound = function() {
        // 此函数的this指向thisArg,这里利用apply来扩展this指向
        return self.apply(
            thisArg, 
            // 将args改成下面的代码
            // 此时的arguments是调用绑定后的函数传递进来的参数,args是bind()调用时传递的参数。
            // 希望达到预设初始值,需要将两次传递的参数进行拼接。统一在调用时再传递过去。
            args.concat(Array.prototype.slice.call(arguments)
        )
    }

    // 返回一个函数
    return bound
}
```

此时再验证我们的myBind()的偏函数功能，则已经能够正确输出`18`这个最终结果。

#### new的情况下，函数bind后，实例对象指向实例本身，而不会改变this指向
原生bind的使用效果：

```js
function bar() {
    console.log(this.name, arguments)
}

bar.prototype.name = 'bar'

var foo = {
    name: 'foo'
}

var bindFoo = bar.bind(foo, 10, 20)
new bindFoo();  // bar { '0': 10, '1': 20 }
bindFoo(); // foo { '0': 10, '1': 20 }
```
上述代码中，new bindFoo()打印出来的是bar，是它本身的实例对象，而不是foo。我们现在来实现一下这个功能。

首先思考一个问题，我们怎么判断当前的绑定函数调用方式是直接调用还是通过new来调用的？

我们可以单独看new的过程,实际上内部是下面这样的：
```js
var zhangsan={};
zhangsan.__proto__=Person.prototype;
Person.call(zhangsan);
```
我们需要关注的是原型链。

```js
function Person() {
    this.name = 'person'
}

var aPerson = new Person()

console.log(aPerson instanceof Person) // true
console.log(aPerson instanceof Object) // true
```
上述代码我们可以看到，new出来的实例对象，它的instanceof是它的构造函数。我们回到我们现在完整的myBind函数。
```js
Function.prototype.myBind = function(thisArg) {
    // 拿到传入的参数,变为真正的数组
    const args = Array.prototype.slice.call(arguments, 1) 
    const self = this

    if (typeof self !== 'function') {
        throw new TypeError(`${self} is not function!`)
    }

    // 创建一个函数
    var bound = function() {
        // 此函数的this指向thisArg,这里利用apply来扩展this指向
        console.log(this instanceof bound)
        return self.apply(
            thisArg, 
            args.concat(Array.prototype.slice.call(arguments))
        )
    }

    // 返回一个函数
    return bound
}

var obj = {
    food: 'rice'
}

function food() {
   console.log(this.food)
}

food.prototype.food = 'noodles'

var boundTest = food.myBind(obj, 'test')
new boundTest()
boundTest()
// 输出：
// >: true  重点关注这个true的值，我们可以看到new的情况下，bound中的this instanceof bound输出为true
// >: rice
// >: false 当成普通函数调用时，this instaceof bound 得到的为false
// >: rice
```
当我们myBind()之后，new的是bound函数，我们可以打印一下，我们调用myBind后的函数boundTest，myBind()方法中的bound就是new的构造函数。这里可能会有点绕，但是你要是细心，跟着代码的执行思路去梳理，是可以自己看明白的，相信自己。

通过上述代码输出结果，我们可以很清晰的区分出，使用new调用和普通函数调用之间的差别。现在我们可以开始对我们的代码进行改造了。

```js
Function.prototype.myBind = function(thisArg) {
    // 拿到传入的参数,变为真正的数组
    const args = Array.prototype.slice.call(arguments, 1) 
    const self = this

    if (typeof self !== 'function') {
        throw new TypeError(`${self} is not function!`)
    }

    // 创建一个函数
    var bound = function() {
        // 此函数的this指向thisArg,这里利用apply来扩展this指向
        return self.apply(
            this instanceof bound ? this : thisArg, 
            args.concat(Array.prototype.slice.call(arguments))
        )
    }

    // 我需要把self上面的prototype对象赋值给bound.prototype
    bound.prototype = self.prototype

    // 返回一个函数
    return bound
}

// 验证new的情况
var obj = {
    food: 'rice'
}

function food() {
   console.log(this.food)
}

food.prototype.food = 'noodles'

var boundTest = food.myBind(obj, 'test')
new boundTest() // noodles
boundTest() // rice

var sourceBound = food.bind(obj, 'source')
new sourceBound() // noodles
sourceBound() // rice

// 对比两个输出结果，符合我们预期。
```
到这里我们基本实现了new的情况下指向实例对象本身，普通函数调用的情况下，this指向给定的对象。

是不是到这里就结束了呢？我们的bind基本就实现完了呢？其实不是，我们还需要进一步的进行优化。

#### 箭头函数的
箭头函数的this没有prototype,箭头函数的this永远都是指向所在的作用域。我们在前面的代码中，有一句代码是：
```js
// ...
    bound.prototype = self.prototype
// ...
```
这里并没有做箭头函数的考虑，如果self是箭头函数中的this，那么self.prototype就是一个undefined。我们需要对这种情况进行过滤。我们可以对我们的myBind()进行最后的改造：
```js
Function.prototype.myBind = function(thisArg) {
    // 拿到传入的参数,变为真正的数组
    const args = Array.prototype.slice.call(arguments, 1) 
    const self = this

    if (typeof self !== 'function') {
        throw new TypeError(`${self} is not function!`)
    }

    // 创建一个函数
    var bound = function() {
        // 此函数的this指向thisArg,这里利用apply来扩展this指向
        return self.apply(
            this instanceof bound ? this : thisArg, 
            args.concat(Array.prototype.slice.call(arguments))
        )
    }

    if (self.prototype) {
        // 我需要把self上面的prototype对象赋值给bound.prototype
        bound.prototype = self.prototype
    }
    
    // 返回一个函数
    return bound
}
```
这边避免了使得bound.prototype是一个undefined。到这里本篇文章也将结束了，通过一路的推敲，一边验证，一边实践，我也对bind函数有了更加深入的了解。如有更多功能点未覆盖到，可以自行推敲。整个实践过程，作为自己的心得，分享给有需要伙伴。如果有不合理或错漏的地方，欢迎指正。


