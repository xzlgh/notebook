# react-router

## url变了,页面没变化,手动刷新才变化:

> 库: react-router 4.0以上版本;

> 出现这种问题的原因是因为代码中包了两层的Router造成的,我们在使用时,只需要保证最外层有一个Router组件即可.

> 参考链接: https://segmentfault.com/q/1010000011157471

> 实现代码:

> 未修改前:

> Router文件(关键代码)

```
class Layout extends Component {
  render() {
    return (
        <Router>
          <Switch>
            <Route exact path="/" component={createComponent(Home)} />
            <Route path="/page1" component={createComponent(Pages1)} />
            <Route path="/counter" component={createComponent(Counter)} />
            <Route path="/userInfo" component={createComponent(UserInfo)} />
          </Switch>
        </Router>
    )
  }
}
```

> Nav文件(关键代码)

```
class Nav extends Component {
  render() {
    return (
      <div>
        <ul>
          <li><Link to="/">首页</Link></li>
          <li><Link to="/page1">page1</Link></li>
          <li><Link to="/counter">Counter</Link></li>
          <li><Link to="/userInfo">UserInfo</Link></li>
        </ul>
      </div>
    )
  }
}
```

> app文件(关键代码)

```
export default class App extends Component {
  render() {
    return (
      <div>
        <Nav />
        <Layout />
      </div>
    )
  }
}
```

> index文件(关键代码)

```
function renderWithHotReload() {
  ReactDom.render(
    <AppContainer>
      <Provider store={store}>
        <Router>
          <App />
        </Router>
      </Provider>
    </AppContainer>,
    document.getElementById('app')
  );
}
```

> 解决办法: 只需要去掉router文件中最外层的包裹的Router组件即可.
