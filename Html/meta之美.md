# meta之美

## meta可以做的事

### 设置视口

```
// 设置理想视口
<meta name="viewport" content="width=device-width" /> 

width: 设置布局视口宽度，一般为device-width;
initial-scale: 初始缩放比例。1即100%， 2即200%；
maximum-scale: 最大缩放比例；
minimum-scale: 最小缩放比例；
user-scalable: 是否禁止用户进行缩放。
```

### 关闭页面上面恶心的广告

通过百度手机打开网页时，百度可能会对你的网页进行转码，往你页面贴上它的广告，非常之恶心。不过我们可以通过这个meta标签来禁止它：

```
<meta http-equiv="Cache-Control" content="no-siteapp" />
```

### 关闭手机号码识别（IOS）

在 iOS Safari （其他浏览器和Android均不会）上会对那些看起来像是电话号码的数字处理为电话链接，比如：

-	7位数字，形如：1234567

-	带括号及加号的数字，形如：(+86)123456789

-	双连接线的数字，形如：00-00-00111

-	11位数字，形如：13800138000

可能还有其他类型的数字也会被识别。我们可以通过如下的meta来关闭电话号码的自动识别：


```
<meta name="format-detection" content="telephone=no" />
```

#### 开启电话功能：

```
<a href="tel:123456">123456</a>
```

#### 开启短信功能：

```
<a href="sms:123456">123456</a>
```

### 邮箱识别（Android）

与电话号码的识别一样，在安卓上会对符合邮箱格式的字符串进行识别，我们可以通过如下的meta来管别邮箱的自动识别：

```
<meta content="email=no" name="format-detection" />
```

同样地，我们也可以通过标签属性来开启长按邮箱地址弹出邮件发送的功能：

```
<a mailto:dooyoe@gmail.com">dooyoe@gmail.com</a>
```

