### redux 
#### 背景
redux: 是js状态容器, 提供可预测的状态管理。

- action: 行为类型
- dispatch: 发起操作
- reducer: 更改state-tree 节点
- middleware: 中间件

store 容器内有一个对外的状态树简称 state-tree。 如何更改状态树: redux 设计了纯函数reducer利用reducer 来change state-tree 的tree节点 。reducer纯函数的执行 依赖 dispatch(执行) 相应的action ,从而达到更新状态树。

action --(dispatch)--> reducer ----> state tree(更新状态树)
action --(dispatch)--> middleware ---> reducer ---> state tree

middleware: 就是在dispatch -> action 中间进行了额外的处理。

```
实现一个基础版本Redux
1. 实现 2 个api: 对外提供state-tree 和 对外的方法 dispatch
2. 实现中间件设计
3. 实现异步请求
```

#### 1. 实现 同步api： const { dispatch, state } = store(reducers)
```

const store = function store(reducers){
  const state = {}
  function initState(){
      for( let keys in reducers){
        state[keys] = reducers[keys](undefined,{type:Symbol('hello world')}) 
      }
  }

  initState() // 初始化 state-tree

  const next = function dispatch(action){
    if(!action.type) throw '定义的action需要有type'
    for( let keys in reducers){
      state[keys] = reducers[keys](state[keys],action)
    }
  }

  return { // 对外api
    state,
    dispatch: next
  }   
}

// 演示: demo
const loginState = {
  isLogin: false,
  token:'',
  type:'init or default'
}
const getLoginReducer =  (state = loginState, action) => {
    switch(action.type){
      case 'LOGIN_INIT':
        return {...loginState}
      case 'LOGIN_SUCCESS':
        return { ...state, isLogin: true, ...action.payload} 
      case 'LOGIN_FAIL':
        return { ...state, isLogin: false, token: action.token}  
      default:
        return {...loginState}    
    }
}

const initReducer = {
  loginReducer: getLoginReducer
}

const stores = store(initReducer)

stores.dispatch({typex:'LOGIN_SUCCESS',payload:{type:"login_success",accounbt:'wangdashi1'}})

console.log(stores.state)

// 打印结构如下:
{ loginReducer:
   { isLogin: true,
     token: '',
     type: 'login_success',
     accounbt: 'wangdashi1' } }
```
到目前为止我们实现了同步的redux , 接下来实现 中间件设计。

#### 简易版redux 中间件
中间件: middleware 
1. 实现一个打印中间件
2. 实现中间件串联

##### 实现一个打印中间件 logger

```
const store = function store(reducers){
  const state = {}
  function initState(){
      for( let keys in reducers){
        state[keys] = reducers[keys](undefined,{type:Symbol('hello world')}) 
      }
  }
  initState() // 初始化 state-tree

  const next = function dispatch(action){
    if(!action.type) console.error('定义的action需要有type')
    changeState(action)
  }
  const dispatchLogger = function dispatch(action){ // 打印中间件
    console.log('start:',dispatch)
    try{
      next(action)
    }catch(err){
      console.error('报错:',err)
    }
  }

  const changeState = (action) => {
    for( let keys in reducers){// 每次都要把所有的函数 的分支都跑到
      state[keys] = reducers[keys](state[keys],action)
    }
  }
  return {
    state,
    dispatch: dispatchLogger
  }   
}
```
>  上述代码虽然实现了日志打印，但是你会发现耦合度过高，并且实现都是在store 内部实现这样对于一个设计来说不友好，然后我们把打印的逻辑做抽离

``` 
const store = function store(reducers, middleware){
  const state = {}
  function initState(){
      for( let keys in reducers){
        state[keys] = reducers[keys](undefined,{type:Symbol('hello world')}) 
      }
  }
  initState() // 初始化 state-tree

  const dispatch = (action) => {
    for( let keys in reducers){// 每次都要把所有的函数 的分支都跑到
      state[keys] = reducers[keys](state[keys],action)
    }
  }

  const extendDispatch = function (action){
    if(!action.type) console.error('定义的action需要有type')
    middleware.logger(dispatch)(action)
  }
  return {
    state,
    dispatch: extendDispatch
  }   
}

const loggerMiddleware = (next) => (action) => { 
  console.log('start:',action)
  try{
    next(action)
  }catch(err){
    console.error('报错:',err)
  }
}

const middleware = {
  logger: loggerMiddleware
}
```

##### 实现中间件串联
以上代码只是实现了一个中间件的逻辑, 事实上我们会遇到多个中间件串联的处理场景,
如果使用2个中间件: loadingMiddleware 和 loggerMiddleware 

```
const store = function store(reducers, middleware){
  const state = {}
  function initState(){
      for( let keys in reducers){
        state[keys] = reducers[keys](undefined,{type:Symbol('hello world')}) 
      }
  }
  initState() // 初始化 state-tree

  const dispatch = (action) => {
    for( let keys in reducers){// 每次都要把所有的函数 的分支都跑到
      state[keys] = reducers[keys](state[keys],action)
    }
  }

  const extendDispatch = function (action){
    if(!action.type) console.error('定义的action需要有type')
    const { logger, loading } = middleware 
    logger(loading(dispatch))(action) // 这里就是实现了中间件的组合, 

    // logger((action) => { return dispatch(action)})(action)

    // const fn = (action) => { return dispatch(action)}
    // const loggerMiddleware = (next) => (action) => { 
    //   console.log('start:',action)
    //   try{
    //     return fn(action)
    //   }catch(err){
    //     console.error('报错:',err)
    //   }
    // }(fn)(action)
  }
  return {
    state,
    dispatch: extendDispatch
  }   
}

const loggerMiddleware = (next) => (action) => { 
  console.log('start:',action)
  try{
   return next(action)
  }catch(err){
    console.error('报错:',err)
  }
}

const loadingMiddleware = (next) => (action) => {
  switch(action.type){
    case 'START':
      console.log('开始loading')
      break;
    case 'END':
      console.log('关闭loading')
      break;
    default:
      console.log('loading默认状态')    
  }
  return next(action)
}

const middleware = {
  logger: loggerMiddleware,
  loading: loadingMiddleware
}


const loginState = {
  isLogin: false,
  token:'',
  type:'init or default'
}
const getLoginReducer =  (state = loginState, action) => {
    switch(action.type){
      case 'LOGIN_INIT':
        return {...loginState}
      case 'LOGIN_SUCCESS':
        return { ...state, isLogin: true, ...action.payload} 
      case 'LOGIN_FAIL':
        return { ...state, isLogin: false, token: action.token}  
      default:
        return {...loginState}    
    }
}

const initReducer = {
  loginReducer: getLoginReducer
}

const stores = store(initReducer,middleware)

stores.dispatch({type:'LOGIN_SUCCESS',payload:{type:"login_success",account:'wangdashi1'}})
// console.log(stores.state)
stores.dispatch({type:'START'})
stores.dispatch({type:'END'})
// 实现一个logger 中间件

// 下面是输出结构
start: { type: 'LOGIN_SUCCESS',
  payload: { type: 'login_success', account: 'wangdashi1' } }
loading默认状态
start: { type: 'START' }
开始loading
start: { type: 'END' }
关闭loading

```
ps. 但是中间件执行顺序整合上面代码 可读性以及拓展性不友好,源码中有提供applyMiddleware 这个方法 进行了整合

```

const store = function store(reducers, middleware){
  ...
  const extendDispatch = function (action){
    if(!action.type) console.error('定义的action需要有type')
    middleware(dispatch)(action)
  }
}
[logger,loadding]
const applyMiddleware = (...arg) => {
  const temp = arg.slice()
  if(temp.length ===1 ) return temp[0]
  // a: total  , b: current 
  return temp.reduce((a, b) => (...args) => a(b(...args)))
}
// (dispatch) => a(b(c(dispatch)))

const middles = applyMiddleware(loggerMiddleware,loadingMiddleware) // 修改 俩个中间件的顺序 完美切换
const stores = store(initReducer,middles)

const middles1 = applyMiddleware(loggerMiddleware,loadingMiddleware)
// start: { type: 'LOGIN_SUCCESS',
//   payload: { type: 'login_success', account: 'wangdashi1' } }
// loading默认状态
// start: { type: 'START' }
// 开始loading
// start: { type: 'END' }
// 关闭loading

const middles2 = applyMiddleware(loadingMiddleware, loggerMiddleware)
// loading默认状态
// start: { type: 'LOGIN_SUCCESS',
//   payload: { type: 'login_success', account: 'wangdashi1' } }
// 开始loading
// start: { type: 'START' }
// 关闭loading
// start: { type: 'END' }

其实就是 哪个中间件先后执行问题
```
> 到这一步实现了 中间件组合执行，上述完成了redux 大部分的功能， 但是还有最重要的，如何在redux 处理异步请求, 从上述实现可以发现我们要实现异步操作就要从中间件入手。
- redux中间件比较常用的处理方式: redux-saga, redux-thunk
- 我们项目中用的是: redux-saga 模版配置写一大堆比较头疼,实现原理有点复杂，这里就不介绍了-😯 
- redux-thunk 源码实现比较简单,下面就以这个中间件简化版介绍-😯

#### 如何实现异步操作
ps. 这里采用thunk 中间件的设计
```js

// thunk 简化板中间件
const thunkMiddle = (next) => (action) => {
  if(typeof action === 'function'){// 就多了这个东西
      return action(next)
  }
  return next(action)
}

store.dispatch((dispatch) => {
  setTimeout(() => {
    dispatch({type:'LOGIN_FAIL',payload:{name:'xxx',token:'xxx'}})
  },0)
})

console.log(stores.state,'sync') // 同步打印

setTimeout(()=>{                 // 异步打印
  console.log(stores.state,'async')
},0)

```
thunk 中间件到底是什么操作, 就是改写action 对象为函数 , 在函数中处理你的异步操作 ,等待异步操作成功或失败后,然后才去执行你的dispatch 

到目前为止我们已经实现了: 
1. redux 的基础功能, 实现了数据更新
2. 但是搭配react ,如何更新了数据同时更新dom节点呢?

参考react-redux,api 这边设计了2个api: Provider, connect

Provider: 实现数据的传递
Connect: 实现组件的更新
```js
const { Provider, Consumer } =  React.createContext()
class Providerx extends React.Component{
  render(){
    return <Provider value={this.props.stores}>{this.props.children}</Provider>
  }
}

export const Connect = () => (Target) =>{
  return class XX extends React.Component{
    componentDidMount(){
      this.props.subscribe(()=>{// 存储更新组件的方法
        this.setState({})
      })
    }
    render(){
      return <><Target {...this.props}/></>
    }
  }
}
class Children extends React.Component{
  
  startLoading = () => {
    this.props.dispatch({type:'LOGIN_SUCCESS',payload:{name:'xxx',age:224}})
  }
  endLoading = () => {
    this.props.dispatch({type:'LOGOUT',payload:{name:'x',age:100}})
  }
  asyncClick = () => {
    this.props.dispatch((dispatch)=>{
      setTimeout(()=>{
        dispatch({type:'LOGIN_INIT',payload:{name:'init',age:1}})
      },1000)
    })
  }
  render(){
    return(
      <div>
        <p onClick={this.startLoading}>点击登陆成功</p>
        <p onClick={this.endLoading}>点击退出登陆</p>
        <p onClick={this.asyncClick}>异步初始化延迟1s执行</p>
        年龄:{this.props.state.loginReducer.age}<br/>
        姓名:{this.props.state.loginReducer.name}
      </div>
    )
  }
}

const FKK = connect()(Children)
export default () => {
  return (
    <Providerx stores={store(initReducer,middles)}>
        <Consumer>
          {
            (props) => {
                return <FKK {...props}/>
            }
          }
        </Consumer>
    </Providerx>
  )
}


// store 更改
export const store = function store(reducers, middleware){
  const state = {}
  const listern = []
  function initState(){
      for( let keys in reducers){
        state[keys] = reducers[keys](undefined,{type:Symbol('hello world')}) 
      }
  }
  // 注册监听
  function subscribe(fn){ // 用于存储 监听的事件
    listern.push(fn)
    console.log(listern,'fn')
  }
  initState() // 初始化 state-tree
  const dispatch = (action) => {
    for( let keys in reducers){// 每次都要把所有的函数 的分支都跑到
      state[keys] = reducers[keys](state[keys],action)
    }
    listern[0]() // 新增 每次触发dispatch 执行listern[0]() 更新操作 
  }
  const extendDispatch = function (action){
    middleware(dispatch)(action)
  }
  return {
    state,
    dispatch: extendDispatch,
    subscribe:subscribe
  }   
}
const loggerMiddleware = (next) => (action) => { 
  console.log('start:',action)
  try{
   return next(action)
  }catch(err){
    console.error('报错:',err)
  }
}
const loadingMiddleware = (next) => (action) => {
  switch(action.type){
    case 'START':
      console.log('开始loading')
      break;
    case 'END':
      console.log('关闭loading')
      break;
    default:
      console.log('loading默认状态')    
  }
  return next(action)
}
const loginState = {
  isLogin: 'xx',
  token:'',
  type:'init or default'
}
const getLoginReducer =  (state = loginState, action) => {
    switch(action.type){
      case 'LOGIN_INIT':
        return {...loginState,...action.payload}
      case 'LOGIN_SUCCESS':
        return { ...state, isLogin: true, ...action.payload} 
      case 'LOGOUT':
          return { ...state, isLogin: false, type:'logout',...action.payload}   
      case 'LOGIN_FAIL':
        return { ...state, isLogin: false, token: action.token,...action.payload}  
      default:
        return {...loginState}    
    }
}
// 同步change action
const requestMiddle = (next) => (action) => {
  if(typeof action === 'function'){// 
      return action(next)
  }
  return next(action)
}
export const initReducer = {
  loginReducer: getLoginReducer
}
const applyMiddleware = (...arg) => {
  const temp = arg.slice()
  if(temp.length ===1 ) return temp[0]
  return temp.reduce((a, b) => (...args) => a(b(...args)))
}
export const middles = applyMiddleware(requestMiddle, loadingMiddleware , loggerMiddleware)

```
好了到这里就实现了redux + react-redux 的所有功能是不是卧槽
当然这里对上述进行总结:

对于redux 我们需要关注 几个重点的东西
state: 对外提供的状态树
dispatch: 行为操作, 发起action 触发reducer更新state-tree 
subscribe: 对外进行事件注册, 当触发dispatch 时 执行注册的事件
reducer: 纯函数,接受preState和 action 进行更新 state-tree
middleware: 中间件, 可以理解对初始dispatch 进行一些列增强: extendDispatch ,当然实现过程也是比较巧妙的, 函数科里化


---
### redux 简化源码实现
> 核心代码介绍
``` api
  createStore,       创建store
  combineReducers,   reducer更改state-tree
  applyMiddleware,   中间件
  compose            科里化组合
```
#### createStore 创建store

```
export default function createStore(reducer, preloadedState, enhancer) {
  if(typeof enhancer === 'function'){ // 中间件
    return enhancer(createStore)(reducer, preloadedState)
  }
  let currentReducer = reducer        // 当前reducer
  let currentState = preloadedState   // 当前state
  let currentListeners = []           // 当前的监听
  let nextListeners = currentListeners

  function getState() {      // 返回当前state
    return currentState
  }

  function subscribe(listener) {     // 将 listener 添加到监听数组 返回 一个移除函数 把数组中进行移除 
    nextListeners.push(listener)
    return function unsubscribe() {
      const index = nextListeners.indexOf(listener)
      nextListeners.splice(index, 1)
    }
  }

  function dispatch(action) {// 
    currentState = currentReducer(currentState, action)  // 更新state-tree 

    const listeners = (currentListeners = nextListeners) // 执行subscribe 函数 组册的listener 
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }
    return action
  }

  dispatch({ type: 'ActionTypes.INIT' }) // 初始化state-tree
  return {
    dispatch,
    subscribe,       // 监听行为
    getState
  }
}
```
#### combineReducers 更新state-tree
```
export const combineReducers = function combineReducers(reducers) {
  // key - value 过滤value 非 function 避免干扰就忽略 
  const finalReducerKeys = Object.keys(reducers)
  return function combination(state = {}, action) {
    let hasChanged = false
    const nextState = {}
    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i]
      const previousStateForKey = state[key]
      const nextStateForKey = reducers[key](previousStateForKey, action)
      nextState[key] = nextStateForKey
      hasChanged = hasChanged || nextStateForKey !== previousStateForKey
    } 
    return hasChanged ? nextState : state
  }
}
```
#### applyMiddleware and compose

```
export const applyMiddleware = function applyMiddleware(...middlewares) { 
  return createStore => (...args) => { // 创建store
    const store = createStore(...args)

    let dispatch = () => {}
    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args)
    }
    const chain = middlewares.map(middleware => middleware(middlewareAPI))
    dispatch = compose(...chain)(store.dispatch)
    return {
      ...store,
      dispatch
    }
  }
}

export const compose = function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }
  if (funcs.length === 1) {
    return funcs[0]
  }
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```
#### loading
```
function loading(){
  return dispatch => next => action => {
    swich(action){
      case '_SUCCESS':
        dispatch({type:'loading'})
        break;
      default:
        dispatch({type:'init'})
        break;
    }
    return next(action)
  }
}
```
1. 代码隔离 + 毕包
2. 各种兼容设计

实现地址 [demo](https://github.com/onionRunning/redux_router.git)


### 参考文章
1. [redux中间件的原理](https://www.cnblogs.com/wshiqtb/p/7909770.html)
2. 源码
