# monitor

## 启动 Hierarchy Viewer

```html
1. 将设备连接到计算机。如果设备显示对话框提示您允许 USB 调试吗？，请点按确定。
2. 在 Android Studio 中打开您的项目，在您的设备上编译并运行它。
3. 启动 Android Device Monitor。Android Studio 可能会显示 Disable adb integration 对话框，因为一次只能有一个进程可以通过 adb 连接到设备，并且 Android Device Monitor 正在请求连接。
图 1 演示了 Android Device Monitor 中最初显示的内容。
4. 在菜单栏中，依次选择 Window > Open Perspective，然后点击 Hierarchy View。
您应该会看到类似于图 2 所示的排列方式。如果没有看到，请依次选择 Window > Reset Perspective 以返回默认布局。
5. 在左侧的 Windows 标签中双击应用的软件包名称。这会使用应用的视图层次结构填充相关窗格
```

![图1](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/monitor/m-1.jpg)
（图1）
![图2](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/monitor/m-2.jpg)
（图2）

## 熟悉相关工具

```html
1. Hierarchy Viewer 提供了以下窗格（如图 2 所示）：
Tree View（中央）：显示视图层次结构的树状视图。您可以使用鼠标和底部的缩放控件来拖动和缩放树。每个节点都指示它的 View 类名和 ID 名称。
Tree Overview（右上方）：可让您一览应用的完整视图层次结构。移动灰色矩形可更改 Tree View 中可见的视口。
Layout View（右下方）：显示布局的线框视图。当前所选视图的轮廓为红色，其父视图为浅红色。
在此处点击某个视图也会在 Tree View 中选择该视图，反之亦然。
2. 要查看有关视图的详细信息，包括其度量、布局和绘制时间（如图 3 所示），请在 Tree View 中点击该视图。双击该视图可查看其放大图片。
3. 要查看视图的属性，请点击左窗格中的 View Properties 标签，如图 4 所示。
```

![图3](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/monitor/m-3.jpg)
（图3）
![图4](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/monitor/m-4.jpg)
（图4）

## 分析布局

1. 现在您已经了解了该工具的用法，可以使用它来分析视图层次并解读结果了。

```html
在 Tree View 或 Layout View 中，点击要分析其子级的视图节点。
要开始分析，请点击 Tree View 顶部的 Obtain layout times 图标 。
对于大型视图层次结构，分析可能需要几秒钟。


所选节点的每个子级都有三个圆点，可以是绿色、黄色或红色。

- 左侧圆点表示渲染管道的绘制过程。
- 中间圆点表示布局阶段。
- 右点圆点表示执行阶段。
```

![图5](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/monitor/m-5.jpg)
（图5）
![图6](https://gitlab2018.com/caiqingfeng/res/raw/master/font/zym/share/app/monitor/m-6.jpg)
（图6)

