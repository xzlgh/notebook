# app启动过程剖析

## 启动分析

问题：用户在手机桌面点击 app图标开始，整个启动过程经过哪几个关键阶段，又会给用户带来哪些体验问题？

1. 启动过程分析

![启动过程图](https://static001.geekbang.org/resource/image/0d/43/0da2051f1f8d182a531063eb202abf43.png)

我以peso2go为例子，用户从桌面点击图标开始，会经过4个关键的过程

- T1 预览窗口显示。系统在拉起peso2go进程之前，会先根据peso2go的 Theme 属性创建预览窗口。如果我们禁用预览窗口或者将预览窗口指定为透明，用户在这段时间内依然看到的是桌面

- T2闪屏显示。 在peso2go进程和闪屏窗口创建完毕，并且完成一系列的inflate view 、 onmeasure 、 onlayout 等准备工作后，用户终于可以看到启动页的图片了

- T3主页显示。 在完成主窗口的创建和页面显示的准备工作后，用户可以看到peso2go的主界面

- T4界面可操作。 在启动完成后，peso2go有很多操作需要完成，获取状态、还款信息、配置信息等，在这些工作完成后，用户才能愉快的贷款了

2. 启动问题分析

从启动流程的4个阶段来看，我们可以推测用户可能会出现3个问题

- 问题1：点击图标很久都不响应

如果我们禁用了预览窗口或者指定了透明皮肤，用户在点击图标后会停留在桌面好几秒时间，这样会让用户误以为点击图标没反应，app打不开，体验很不好，在低端机型更加明显。

- 问题2：首页显示太慢

现在应用启动流程越来越复杂，闪屏广告、热修复框架、插件化框架等等，所有准备工作都在启动阶段完成，上面T3时间会是用户的噩梦，有时候得长达十来秒才能从打开app到看到应用首页。

- 问题3： 首页显示后无法操作

既然首页显示那么慢，那我能不能把尽量多的工作都通过异步化延后执行呢？很多应用的确是这么做的，但这样做会造成两种后果，要么首页白屏，要么首页出现后无法操作。

所以启动优化，要从用户真实的体验区出发，要着眼用户点击图标到可操作的主页的整个过程。


## 优化

1. 优化工具

工欲善其事必先利其器”，我们需要先找到一款适合做启动优化分析...

systrace + 函数插桩

2. 优化方式

- 闪屏优化
推荐 Android 6.0 或者 Android 7.0 以上才启用“预览闪屏”方案，让手机性能好的用户可以有更好的体验。

- 业务梳理
启动过程正在运行的每一个模块，哪些是一定需要的、哪些可以砍掉、哪些可以懒加载。

- 线程优化
线程的优化主要在于减少 CPU 调度带来的波动，让应用的启动时间更加稳定。

- GC 优化
启动过程，要尽量减少 GC 的次数，避免造成主线程长时间的卡顿
