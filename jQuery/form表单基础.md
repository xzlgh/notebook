### jQuery基础用法技巧

##### checkbox输入框

###### 检查选中状态
```
1、jquery.is(':checked')：来检查，返回boolean值，true表示选中。false表示没选中。（推荐）
2、attr('checked') jquery1.6+版本返回：'checked'或者'undefine';jquery1.5-返回：true,false,使用1.12+版本一直显示的是undefine，推荐第一种方式判断
3、jquery.prop('checked')： 1.6+版本返回：true/false。
```

###### 改变选中状态
```
jquery.attr('checked',true)/jquery.attr('checked',false)： 这种方式有时虽然属性值改变了，但是页面上的checkedbox并没有改变选中状态。这种情况建议使用prop来代替attr
```















