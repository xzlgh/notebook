### 存储基础技巧

> 推荐链接: https://blog.csdn.net/a727911438/article/details/54290931

#### store
```
从后端接收过来的数值，如果是数字，当我第一次登录保存到store里面后，第一次取得的值为number类型，
但是后面取得的值就是string类型，为了保证判断时候的正确性，在判断前最好自己统一转换成number类型，或者是转换成string类型，保证运行的正确性。
```

#### 本地存储
```
* localStorage和sessionStorage；此外还有Web SQL 是h5的产物
* cookie 一些老旧版本浏览器的产物。
```

思考：
```
* localStorage除了使用字符串的方式来存储json对象，还可以使用什么方式存储一个json对象？
 > 可以将json数据转成数组

注： 在vuex中是可以直接存储 json 对象的，但是localStorage 是不能直接存储的，
结合使用的话，就需要考虑到json对象不能直接存储在localStorage里面。

* 如果不使用localStorage来辅助存储数据，还可以使用什么方式来永久保存数据？
cookie \ Web SQL
```

localStorage存储json对象：
```
localStorage不会将json对象自动转换成字符串，需要手动转换成字符串后，再存储
```

localStorage和sessionStoage的区别在哪里？
```
localStorage: 域内安全、永久保存。即客户端或浏览器中来自同一域名的所有页面都可访问localStorage数据且数据除了删除否则永久保存，但客户端或浏览器之间的数据相互独立。

sessionStorage: 会话控制、短期保存。会话概念与服务器端的session概念相似，短期保存指窗口或浏览器或客户端关闭后自动消除数据。
但是如果我们没有关闭浏览器，即使刷新页面，数据还是存在，只有关闭了浏览器之后，重新打开，数据才会消失。
```

localStorage和sessionStorage的缺陷在哪？
```
存在兼容问题：本地存储是h5的新特性，在一些老旧版本的浏览器中，可能不兼容。
如果要解决这种兼容问题，可以选择使用cookie来替代 localStorage和sessionStorage
```

如果以上的locaStorage和cookieStorage或者说Web SQL都不能使用的时候，那么还可以上传服务器，请求服务器保存数据。

#### cookie有那些缺陷
```
cookieStorage
* 有存储时间的限制
* 有存储大小的限制
```













