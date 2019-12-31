# 需求

```html
1. 怎么实现通过一个url打开app?
2. 怎么打开指定的app页面?
```

## 初步实现方案 - URL Scheme协议

### URL Scheme使用场景介绍

```html
1.通过小程序，利用Scheme协议打开原生app
2.H5页面点击锚点，根据锚点具体跳转路径APP端跳转具体的页面
3.APP端收到服务器端下发的PUSH通知栏消息，根据消息的点击跳转路径跳转相关页面
4.APP根据URL跳转到另外一个APP指定页面
5.通过短信息中的url打开原生app
```

### URL Scheme基础介绍

```javascript
1. 什么是URL Scheme?

- android中的scheme是一种页面内跳转协议，是一种非常好的实现机制，通过定义自己的scheme协议，可以非常方便跳转app中的各个页面

2. Scheme链接格式样式

- 样式：[scheme]://[host]/[path]?[query]
```

### URL Scheme如何使用

1. 设置Scheme

- 在AndroidManifest.xml中对标签增加设置Scheme

![scheme](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/scheme_and.jpg)

2. 调用方式

```javascript
- 原生调用
    Intent intent = new Intent(Intent.ACTION_VIEW,Uri.parse("gotocash://gotocash:8888/from?type=yangchong"));
    startActivity(intent);
- 网页调用
    <a href="gotocash://gotocash:8888/from?type=yangchong">打开GotoCash app</a>

3. Scheme在短信息中注意要点

 - 设置android:scheme="http"或者android:scheme="https"后，点击短信息或者h5页面，发现没有跳到指定的页面，反而打开的是网页链接。
```

### 调起app后怎么打开指定页面

```javascript
1. 尝试一: react navigation + scheme 配置打开指定路由  // https://reactnavigation.org/docs/en/next/deep-linking.html#uri-prefix

2. 尝试二: react native Linking + scheme  // https://reactnative.cn/docs/linking/#docsNav   

Linking中的坑和用法

1. https://www.jianshu.com/p/351c2f690569
```
