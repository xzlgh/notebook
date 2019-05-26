# CSS的妙用

### ios系统中元素被触摸时产生的半透明灰色遮罩怎么去掉？
ios用户点击一个链接，会出现一个半透明灰色遮罩, 如果想要禁用，可设置-webkit-tap-highlight-color的alpha值为0，也就是属性值的最后一位设置为0就可以去除半透明灰色遮罩。

```
{-webkit-tap-highlight-color: rgba(0,0,0,0;)}
```

### webkit表单元素的默认外观怎么重置？

```
.css{-webkit-appearance:none;}
```

### 禁止文本缩放

当移动设备横竖屏切换时，文本的大小会重新计算（或者在内嵌浏览器中设置字体的大小），进行相应的缩放，当我们不需要这种情况时，可以选择禁止：

```
html {
　　-webkit-text-size-adjust: 100%;
}
```

#### 如何禁止保存或拷贝图像（IOS）
通常当你在手机或者pad上长按图像 img ，会弹出选项存储图像 或者拷贝图像，如果你不想让用户这么操作，那么你可以通过以下方法来禁止：
img { -webkit-touch-callout: none; }

#### ==摇一摇功能==
HTML5 deviceMotion：封装了运动传感器数据的事件，可以获取手机运动状态下的运动加速度等数据。


#### android 上去掉语音输入按钮
input::-webkit-input-speech-button {display: none}

### 搭配
```
> backbone

> underscore

https://daneden.github.io/animate.css/          css样式
```
