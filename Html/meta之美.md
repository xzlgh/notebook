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