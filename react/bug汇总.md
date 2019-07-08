## bug汇总

##### 当使用a标签去新开一个窗口的时候，url上面为：Bank:about，不会跳转到指定的页面
分析：
```
- 传递的url是否正确
- 当我直接使用的是pgitlab+指定位置访问的时候可以正常访问，但是使用具体的ip来访问的话则会出现这个问题
- 猜测：在img标签上面复制url的时候，url给错了。
- 打印出window.location.origin和window.location.host发现里面的值是不同的,host只有域名\ip没有协议，origin会把协议一并给出
- 获取当前的网络协议和ip直接使用host会出现这种情况，但是换成window.location.origin则可以正常执行，不会出现这种问题。
- 当我们在浏览器的url上面直接使用url访问的时候，host获取到的值是不携带协议的（eg: xxx.xxx.xxx.xxx:30016）;origin获取到的值是携带协议的：（eg: http://xxx.xxx.xxx.xxx:30016）
```




















