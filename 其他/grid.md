# Grid 布局

## 基本概念
网格布局:Flex 布局是轴线布局，只能指定"项目"针对轴线的位置，可以看作是一维布局。Grid 布局则是将容器划分成"行"和"列"，产生单元格，然后指定"项目所在"的单元格，可以看作是二维布局。Grid 布局远比 Flex 布局强大

在父容器 设置 display:grid  容器里的项目就会按照网格布局 ,  也可以设置inline-grid 容器内还是网格布局，但是父元素变为行内元素 

## 实例

###  容器属性 
![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032506.png]
```
长度单位可以使用px 也可以是用百分比 
#container{
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
}
```
repeat

```
#container{
  display: grid;
  grid-template-columns:repeat(3,100px);
  grid-template-rows: repeat(3,100px);
}
```
autofill 关键字  表示自动填充  一行或者一列放入尽可能多的项目
![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032508.png]

```
#container{
  display: grid;
  grid-template-columns:repeat(autofill,100px);
  grid-template-rows: repeat(3,100px);
}
```

fr 关键字 类似与 flex:1   表示比例关系 
```
.container {
  display: grid;
  grid-template-columns: 150px 1fr 2fr;
}
```


minmax 函数 
以下代码就表示给第三列一个最小宽度 

```
.container {
  display: grid;
  grid-template-columns: 1fr 1fr minmax(100px, 1fr);
}
``` 

行间距 grid-row-gap 
列间距 grid-column-gap 
间距 grid-gap 
这里只是行列间距 项目与容器是不会有间距的  
```
grid-gap: <grid-row-gap> <grid-column-gap>;
```
```
#container{
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-row-gap: 40px;
  grid-column-gap: 30px;
}
```

grid-auto-flow 相当于 flex-direction 
这个属性来决定先排满一行 还是  一列 

![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032506.png]

![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032512.png]



justify-items:start | end  | center| stretch 

algin-items :start | end  | center| stretch 

place-items 属性是align-items属性和justify-items属性的合并简写形式。
用来改变单元格区域内容的位置 
```
place-items: <align-items> <justify-items>;
``` 

![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032516.png]

![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032517.png]


justify-content : start | end |  center | stetch |  space-around  |  space-evenly | space-between 
align-content : : start | end |  center | stetch |  space-around  |  space-evenly | space-between 

用来改变整个内容区域在容器里的位置 

```
place-content :  <align-content> <justify-content>
```

![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032519.png]

![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032518.png]


### 项目属性 
grid-column-start属性：左边框所在的垂直网格线

grid-column-end属性：右边框所在的垂直网格线

grid-row-start属性：上边框所在的水平网格线

grid-row-end属性：下边框所在的水平网格线

以上项目属性可以改变项目在容器中的位置

```
#container{
  display: grid;
  grid-template-columns: 100px 100px 100px ;
  grid-template-rows: 100px 100px 100px;
}

.item-1 {
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 2;
  grid-row-end: 4;
}

```


![avatar][https://www.wangbase.com/blogimg/asset/201903/bg2019032527.png]