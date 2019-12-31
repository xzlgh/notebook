# 性能监控

## app 摇一摇 Show FPS Monitor 观测性能

![操作图](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/13.png)
![性能监控图](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/12.png)

1. JS 帧率(JavaScript 线程)
    - 业务逻辑是运行在 JavaScript 线程上的。这是 React 应用所在的线程，也是发生 API 调用，以及处理触摸事件等操作的线程。更新数据到原生支持的视图是批量进行的，并且在事件循环每进行一次的时候被发送到原生端，这一步通常会在一帧时间结束之前处理完（如果一切顺利的话）。如果 JavaScript 线程有一帧没有及时响应，就被认为发生了一次丢帧。 例如，你在一个复杂应用的根组件上调用了this.setState，从而导致一次开销很大的子组件树的重绘，可想而知，这可能会花费 200ms 也就是整整 12 帧的丢失。此时，任何由 JavaScript 控制的动画都会卡住。只要卡顿超过 100ms，用户就会明显的感觉到。

2. UI 帧率(主线程)
    - 不会被 JavaScript 线程上的掉帧所影响。
    - 当 JavaScript 线程卡住的时候，你仍然可以欢快的上下滚动ScrollView，因为ScrollView运行在主线程之上（尽管滚动事件会被分发到 JS 线程，但是接收这些事件对于滚动这个动作来说并不必要）。

## systrace调试性能

1. Collecting a trace
    - 连接手机设备到电脑，打开手机调试。
    - 打开要查看性能的app页面
    - <path_to_android_sdk>/platform-tools/systrace/systrace.py --time=10 -o trace.html sched gfx view -a <your_package_name>

2. Reading the trace
    - 全貌图
    ![概略图](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/17.jpg)
    - cpu图
    ![cpu图](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/cpu.jpg)
    - ui thread
    ![uithread](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/19.jpg)
    - render thread
    ![renderthread](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/20.jpg)