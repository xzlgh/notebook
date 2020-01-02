# react-navigation隐藏底部tabbar

## 隐藏底部TabBar的两种方式

### 场景描述

> navigation版本号为：4.x

将需要隐藏底部Tab的页面放在与Tab堆栈同一级别的目录,例如：

我当前有Route的index.js文件，我有页面APage和页面BPage,还有BPage的子页面BChildPage

```tsx
// 启动页BootPage
import BootPage from 'BootPage'
import APage from 'APage'
import BPage from 'BPage'
import BChildPage from 'BChildPage'

import { createAppContainer, createSwitchNavigator } from 'react-navigation'
import { createBottomTabNavigator } from 'react-navigation-tabs'
import { createStackNavigator } from 'react-navigation-stack'

// 创建B位置的tab堆栈
const BStack = createStackNavigator({
  BPage: BPage,
  BChildPage: BChildPage
})

// 底部tab堆栈
const TabBottom = createBottomTabNavigator({
  APage: APage,
  BStack: BStack
})

// switch堆栈
const App = createSwitchNavigator({
  bootPage: BootPage,
  home: TabBottom
})

export default createAppContainer(App)
```

如上述文件中的堆栈关系，我如果希望在BChildPag页面隐藏底部的Tab,

### 处理办法

#### 第一种处理办法

将BChildPage放置的位置放在与Tab同级,调整路由设置如下

```tsx
import BootPage from 'BootPage'
import APage from 'APage'
import BPage from 'BPage'
import BChildPage from 'BChildPage'

import { createAppContainer, createSwitchNavigator } from 'react-navigation'
import { createBottomTabNavigator } from 'react-navigation-tabs'
import { createStackNavigator } from 'react-navigation-stack'

// 底部tab堆栈
const TabBottom = createBottomTabNavigator({
  APage: APage,
  BPage: BPage
})

// 把BChildPage提到与Tab同一个层级的堆栈中
const HometStack = createStackNavigator({
  Tab: TabBottom,
  BChildPage: BChildPage
})

// switch堆栈
const App = createSwitchNavigator({
  bootPage: BootPage,
  home: HometStack
})

export default createAppContainer(App)
```

#### 第二种处理办法

手动隐藏Tab，通过设置BChildPage页面所在堆栈navigationOptions的tabBarVisible属性为false，隐藏TabBar。

1> 具体实现如下：

```tsx
// 启动页BootPage
import BootPage from 'BootPage'
import APage from 'APage'
import BPage from 'BPage'
import BChildPage from 'BChildPage'

import { createAppContainer, createSwitchNavigator } from 'react-navigation'
import { createBottomTabNavigator } from 'react-navigation-tabs'
import { createStackNavigator } from 'react-navigation-stack'

// 创建B位置的tab堆栈
const BStack = createStackNavigator({
  BPage: BPage,
  BChildPage: BChildPage
})

BStack.navigationOptions = ({navigation}) => {
  let tabBarVisible = true
  if (navigation.state.index > 0) {
    tabBarVisible = false
  }
  return {tabBarVisible}
}

// 底部tab堆栈
const TabBottom = createBottomTabNavigator({
  APage: APage,
  BStack: BStack
})

// switch堆栈
const App = createSwitchNavigator({
  bootPage: BootPage,
  home: TabBottom
})

export default createAppContainer(App)
```

2> 也可以直接在BStack堆栈的NavigationOptions里面直接写配置信息

```tsx
...
// 创建B位置的tab堆栈
const BStack = createStackNavigator({
  BPage: BPage,
  BChildPage: BChildPage
}, {
  navigationOptions = ({navigation}) => {
    let tabBarVisible = true
    if (navigation.state.index > 0) {
      tabBarVisible = false
    }
    return {tabBarVisible}
  }
})
...
```

3> 还可以直接在BChildPage页面上去修改配置

```tsx
// BChildPage.tsx
import React from 'react';
import {View, Text} from 'react-native';

class BChildPage extends React.Component {
  static navigationOptions = ({ navigation }) => {
    let tabBarVisible = true
    if (navigation.state.index > 0) {
      tabBarVisible = false
    }
    return {tabBarVisible}
  };

  render() {
    return (
      <Text>demo</Text>
    )
  }
}
export default BChildPage
```
