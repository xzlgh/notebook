# form表单使用技巧汇总

## Input

### 关联文本和input的两种关联方式
目的：
> 实现用户点击文字，则checkbox能够自动选中和取消

1. 传统关联，使用for的关联方式：

```html
<input id="test" type="checkbox">
<label for="test">测试</label>
```

2. 利用form表单元素来进行关联

```html

<label><input type="checkbox" />测试</label>
```
第二种方式值得注意的是：不要再label标签里面放置其他非form相关标签，例如：

```html
<label>
    我是radio
    <input id="test" type="radio"><a>跳转</a>
</label>
```
上面这种方式不推荐，推荐的做法是下面这种：

```html
<label>
    <input id="test" type="radio">
    我是radio
</label>
<a>跳转</a>
```

### 自定义单选多选样式

IE浏览器不支持通过appearance: none来去除input的默认样式。所以如果要自定义单选多选样式，且必须兼容IE，可以通过下面的方式来实现：
1> 首先让input隐藏，display: none
2> 使用label的伪元素样式来实现自定义的单选多选样式


