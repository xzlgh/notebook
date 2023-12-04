# 单元测试bug记录

## react17 + umi 3.x + @testing-library/react  + ts 单元测试

### 问题一

#### 项目中使用了ResizeObserver全局对象，写单元测试的时候出现报错
<b>报错信息如下：</b>
```shell
src/**/**.../**/index.test.tsx
Attachment
✕ renders without errors (262 ms)

● Attachment › renders without errors
ReferenceError: ResizeObserver is not defined
  13 |
  14 |   const [resizeObserver] = useState((): ResizeObserver => {
> 15 |     return new ResizeObserver(run);
     |     ^
  16 |   });
  17 |
  18 |   useMount((): void => {
....
```
报错信息提示，new ResizeObserver is not defined，提示未定义

<b>分析以及解决方案</b>

这个错误通常是由于缺少 ResizeObserver 的全局定义引起的。ResizeObserver 是一个用于监视元素大小变化的 Web API，但在某些测试环境中可能没有完全实现或被定义。
要解决这个问题，你可以使用一个 polyfill 来提供 ResizeObserver 的定义。以下是使用 resize-observer-polyfill 这个常见的 polyfill 的示例：

1. 首先，安装 resize-observer-polyfill 作为开发依赖项：

npm安装

```shell
npm install --save-dev resize-observer-polyfill
```

或者使用 Yarn：

```shell
yarn add --dev resize-observer-polyfill
```

2. 在你的测试文件的顶部添加以下导入语句：

```tsx
import 'resize-observer-polyfill/dist/ResizeObserver.global';
```
这将在测试环境中提供 `ResizeObserver` 的全局定义。

3. 现在重新运行测试，ResizeObserver 应该能够被正确定义，而不会再出现 ReferenceError。


### 问题二

#### 项目中使用了原生js去操作canves 

<b>报错信息如下:<b/>

```shell
Attachment › renders correct number of attachments
TypeError: Cannot set properties of null (setting 'font')

  19 |   const context = document.createElement('canvas').getContext('2d') as CanvasRenderingContext2D;
  20 |
> 21 |   context.font = `${fontWeight} ${fontSize} ${fontFamily}`;
     |   ^
  22 |
  23 |   if (context.measureText(text).width > width) {
  24 |     const left = initLeftRange(text, startIndex, ellipsisPosition);
```
根据上述的报错信息，这里提示font是一个空

<b>分析以及解决方案</b>

这个错误是由于在测试环境中，document.createElement('canvas').getContext('2d') 返回的 CanvasRenderingContext2D 上下文对象是 null，因此无法为其属性设置值。

为了解决这个问题，你可以使用一个 polyfill 来提供 CanvasRenderingContext2D 的定义。以下是使用 jest-canvas-mock 这个常见的 polyfill 的示例：

1. 首先，安装 jest-canvas-mock 作为开发依赖项：

```shell
npm install --save-dev jest-canvas-mock
```

或者使用 Yarn：

```shell
yarn add --dev jest-canvas-mock
```

2. 在你的测试文件的顶部添加以下导入语句：

```tsx
import 'jest-canvas-mock';
```

这将在测试环境中提供 `CanvasRenderingContext2D` 的定义以及其他与 Canvas 相关的全局变量。

3. 现在重新运行测试，应该不再出现关于 CanvasRenderingContext2D 的错误。

