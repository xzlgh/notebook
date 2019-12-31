# ReactNative - JS渲染成Android控件

ReactNative开发中,我们都知道敲入 react-native run-android,神奇的JS代码就被编译成Android可以理解的View了,不得不惊叹ReactNative隐藏的神秘的力量，所以本文分析一下一个Hello,World的JS代码是怎么变成Android控件的.

简单看下JS代码

![图片1](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/1.jpg)

## View的创建

首先还是需要从JS中找入口就是说JS的view是怎么被创建？

源码找到UIManager.createView UIManager.updateView UIManager.manageChildren

而UIManager对应的Android源码类是UIManagerModule.java，举个例子看看createView：

![图片2](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/2.jpg)

看到@ReactMethod这个注解就对了，表明这个是个供JS调用的java方法。所以说我们的每一个JS代码中写的控件最终都是通过这个入口createView创建一个个Android能够识别的View，具体的进一步代码实现如下：

![图片3](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/3.jpg)

而mNativeViewHierarchyOptimizer.handleCreateView这个方法最后是调用UIViewOperationQueue.enqueueCreateView：

![图片4](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/4.jpg)
![图片5](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/5.jpg)

最后是将一个个的CreateViewOperation对象加入到列表中去，所以这里总结一下流程：

1. JS调用UIManager.createView
2. Android 调用UIManagerModule.createView
3. Android 调用UIImplementation.createView
4. Android 调用UIViewOperationQueue.enqueueCreateView
5. Android 调用CreateViewOperation.execute

## View的显示 - Yoga

Yoga是实现Flexbox的跨平台布局引擎 -  https://github.com/facebook/yoga