# CSS的妙用



#### webkit表单元素的默认外观怎么重置？

```
.css{-webkit-appearance:none;}
```

#### 禁止文本缩放

当移动设备横竖屏切换时，文本的大小会重新计算（或者在内嵌浏览器中设置字体的大小），进行相应的缩放，当我们不需要这种情况时，可以选择禁止：

```
html {
　　-webkit-text-size-adjust: 100%;
}
```

#### 移动端禁止选中内容
如果你不想用户可以选中页面中的内容，那么你可以在css中禁掉。
```
.user-select-none {
  -webkit-user-select: none;
｝
```

#### 如何禁止保存或拷贝图像（IOS）
通常当你在手机或者pad上长按图像 img ，会弹出选项存储图像 或者拷贝图像，如果你不想让用户这么操作，那么你可以通过以下方法来禁止：
img { -webkit-touch-callout: none; }

#### ==摇一摇功能==
HTML5 deviceMotion：封装了运动传感器数据的事件，可以获取手机运动状态下的运动加速度等数据。


#### android 上去掉语音输入按钮
input::-webkit-input-speech-button {display: none}

#### 搭配
```
> backbone

> underscore

https://daneden.github.io/animate.css/          css样式
```

#### 使用一个div模拟textarea实现高度自适应
```
使div可编辑，设置contenteditable属性
html: 
  <div contenteditable="true"></div>

css样式固定宽度：
  div {
    width: 100px; // 固定宽度
    min-height: 30px; //设定最小高度
    border: 1px solid #eee; //设置边框
    outline: none; //去除文本框选中时的蓝色边框
  }

注：这种方式有一点缺陷,就是必须是固定的宽度，不能够是百分比。或者是可伸缩宽度。可编辑元素浮动式依然有效。
```

#### 限制输入框不能输入中文
```
当匹配到中文时，替换成''空字符： 
  <input onkeyup="value=value.replace(/[\u4E00-\u9FA5]/g,'')"/>
```




