# 概要

RN主要的通信在于java与js之间，平常我们写的jsx代码最终会调用到原生的View

## 通信

我们所说的［通信］，指的是RN中Java和js的通信，也就是js部分中的那些jsx代码是如何转化成一个java层真实的view和事件的，java层又是如何调用js来找出它所需要的那些view和事件的。

简单的说，RN的两端通信靠的是一张配置表，java端和js端持有同一张表，通信的时候就是靠这张表的各个条目的对应来进行的。

![结构图](http://gitlab2018/caiqingfeng/res/raw/master/font/zym/share/11.png)

- 大致的就是和上面这张图一样，两端各持有一份相同的config，config中有一些已经注册的模块，两端的通信就是通过传输这样的“A”，“B”或者”C”来实现的。这个config对应到RN的代码是NativeModuleRegistry和JavaScriptModuleRegistry。如果大家想象不出来的话我可以给大家打个比喻，java端和js端的通信就好比两个语言不一样的人在对话，而这个config，也就是注册表就相当于两个翻译，有了翻译两个语言不通的人才能正常交流。那这两张表是如何生成的呢？还是让我们从代码中寻找答案吧。

(1) 在程序启动的时候，也就是ReactActivity的onCreate函数中，我们会去创建一个ReactInstanceManagerImpl对象

(2) 通过ReactRootView的startReactApplication方法开启整个RN世界的大门

(3) 在这个方法中，我们会通过一个AsyncTask去创建ReactContext

(4) 在创建ReactContext过程中，我们把我们自己注入(MainReactPackage)的和系统生成(CoreModulesPackage)的package通过processPackage方法将其中的各个modules注入到了对应的Registry中

(5) 最后通过CatalystInstanceImpl中的ReactBridge将java的注册表通过jni传输到了JS层。

这样，js层就获取到了java层的所有接口和方法

## 通讯机制

![通讯框架图](https://upload-images.jianshu.io/upload_images/2979409-1d50d6b25bd280bf.png?imageMogr2/auto-orient/)

### Java层，这块的实现在ReactAndroid中

- ReactContext: android 上下文子类，包含一个CatalystInstance实例，用于获取NativeModule、JsModule、添加各种回调等
- ReactInstanceManager:管理CatalystInstance的实例，处理RNRootView，启动JS页面，管理生命周期
- CatalystInstance:通讯的关键类，提供调用JSModule也支持JS调用Native Module，与Bridge进行交互，对开发者不可见

### C++层，这块实现在ReactCommon中，供Android与iOS使用

- NativeToJsBridge : native与JS的桥接，负责调用JS Module、回调Native（调用JsToNativeBridge）、加载JS代码（调用JavaScriptCore）
- JsToNativeBridge : 调用Native Module的方法
- JSCExecutor : 加载/执行JS代码（调用JavaScriptCore）、调用JS Module、回调native、性能统计等，都是比较核心的功能

### JS层，RN JS相关的实现

- MessageQueue : 管理JS的调用队列、调用Native/JS Module的方法、执行callback、管理JS Module等
- JavaScriptModule : 代指所有的JSModule实现，在java层中也有对应的代码（都是interface），使用动态代理调用，统一入口在CatalystInstance中

## js调用native

![通讯时序图](https://upload-images.jianshu.io/upload_images/2979409-ba6dfb37454f8037.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

(1) JS将方法的对应参数push到MessageQueue中，等java端事件传递

(2) Java端事件触发以后，JS层将MessageQueue中的数据通过C层传递给java层

(3) C层调用一开始注册在其中的NativeModulesReactCallback

(4 然后通过JavaRegistry这个Java注册表拿到对应的module和method

(5) 通过反射执行方法。

<!-- ## native调用js

![通讯时序图](https://upload-images.jianshu.io/upload_images/2979409-c39bcbcfe1484399.png?imageMogr2/auto-orient/) -->

### JS 调用 native

- native 处理
    1. application中添加 package
    ![package图](http://gitlab2018/caiqingfeng/res/raw/master/font/zym/share/1.jpg)
    2. package中创建 module
    ![module图](http://gitlab2018/caiqingfeng/res/raw/master/font/zym/share/2.jpg)
    3. module中定义js可调用的方法   @ReactMethod
    ![method图](http://gitlab2018/caiqingfeng/res/raw/master/font/zym/share/3.jpg)
- js 处理
    1. 通过NativeModules.module名称.方法名调用native方法
    ![js图](http://gitlab2018/caiqingfeng/res/raw/master/font/zym/share/6.jpg)