# JS对象和JSON对象

## JS对象

js对象是字面量，其key可以不用引号引起来。js对象是一种key-value形式的数据格式 —— eg: {a: 'a'}

## JSON对象

json对象是js语言特有的，json对象不需要再去实例化一个对象，json对象也是使用key-value形式的数据格式，但是它的key需要用引号引起来

## 为什么要进行json字符串和jsond对象的转换？

  因为在网络传输中，为了方便进行传输，一般都是选择使用字符串转换。

### 网络传输中为什么使用json格式呢？

  因为这种json是一种国际标准，可以规范我们的代码。

### json对象和json协议有什么区别

  json协议，只要我们将数据转换成这个格式，就能传输，实际上是一个共同约定的一种规则。