# React Native Bug汇总

---

## android原生环境bug汇总

### 同一个接口偶现同时请求多次

#### 用户第一次安装点击Home键，再次打开App，会重新开启App

> 错误体现：用户进入首页，多次调用接口，出现多个页面压盖的情况

#### 分析\解决

```md

1. 不断调试，发现规律 —— 第一次安装App的时候必现，之后如果退出了App（而不是点击物理Home键），则之后不会再出现；
2. 网上查找解决方案，定位出现问题原因在android系统上，有两种解决方案：
  a、修改reactnative源码
  b、修改android原生代码
3. 这里选择第二种方式，在AndroidManifest中修改MainActivity的launchMode为SingleTask.一般在原生的Android编写中，都会进行此项设置，但是我们的项目中使用的是reactnative,所以忘记对此项进行设置

  android:launchMode="singleTask">

参考链接： https://blog.csdn.net/Ghost_tal/article/details/89476644
```
