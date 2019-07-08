
## form表单中的button按钮点击事件点击一次触发了两次。
### 发生场景

使用原生的ajax请求数据时，表单中button按钮，当被按下的时候，接收到的数据被重新刷新掉了。

### 分析\原因
每一个标签元素都有一个默认事件，如果button是放在一个form里面，会默认为是submit按钮，所以会执行一次它的默认事件，重新提交刷新了一次页面，接受数据被覆盖。

### 解决办法：
1. 设置button的类型type="button"可以解决这个问题；
2. 可以在js中阻止默认事件（event.preventDefault()）。

## 阻止输入框自动填充上一次填写内容

### 出现自动填充的情况
```
1. 在用户和密码包含在form的情况下，同时包含一个 type="text"和 type="password"元素且不包含readonly和disabled属性，
两个元素可以没有name和id属性
2. 在没有form的情况下，同时包含一个 type="text"和 type="password"元素且不包含readonly和disabled属性，
且两个元素至少都有name或id属性（但name和id可以是任意值但不能是空或空格
```
### 使用场景
```
登录时,我们一般不希望浏览器自动帮我们填充上用户名和密码:
1. 自动填充会出现用户登录不安全,存在安全隐患
2. 自动填充的用户名和密码,会使得输入框背景变成黄色,非常影响美观
```

### 通用解决办法
```
1. 在表单标签上面加上:  autocomplete="off" 属性,密码标签上面加上 autocomplete="new-password", 如下:

<input type="text" autocomplete="off" name="userName"/>
<input type="password" autocomplete="new-password" name="password"/>

 注: 这种解决方式存在兼容性问题.尤其是360浏览器上面.虽然不会自动填充了,但是还是会提示用户名去填充
```





