### 一个console.log引发的血案
#### bug描述：
##### 模拟器启动App，进入启动页后页面就闪退，没有任何报错提示，也还没连接debuger-Ui 一直找不到问题所在，模拟器启动不起来，但是手机可以启动，只是启示时间很长
##### 解决过程：
- 注释自己猜测会出现bug的代码，注释了获取原生versioncode代码，App能起起来,以为是获取版本code值报错，一点一点从调用开始往源头找，有时出现这种问题，有时候不出现。打印具体的版本号，没有出现问题。猜测可能方向错了
- 开始从提交的代码着手，一个版本一个版本的回退，当我回退到同事合并我的代码的版本的时候，不能启动，但是往前他的代码的版本可以正常启动；我于是再往前，启动我提交的代码，能正常启动；唯有合并后的代码不能启动，猜测合并代码的时候出现了问题。
- 开始注释合并的代码，终于找到罪魁祸首：一个consol.log()函数，
- console.log()出现的位置：App.ts组件中，的router中绑定了ref，在ref函数里面console.log()造成。
```
import React from 'react'
import { Component } from 'react';
import SplashScreen from 'react-native-splash-screen'
import {
  Platform,
} from 'react-native';
import { AppRegistry, View } from 'react-native';
import { Provider } from "mobx-react"
import Router from './client/router'
import moment from 'moment'
import 'moment/locale/zh-cn'

import RootStore from './client/stores/root'
import NavigationService from './client/router/NavigationService';
import 'app/client/Global'
moment.locale('en-ph')

const instructions = Platform.select({
  ios: 'Press Cmd+R to reload,\n' +
    'Cmd+D or shake for dev menu',
  android: 'Double tap R on your keyboard to reload,\n' +
    'Shake or press menu button for dev menu',
});
const rootStore = new RootStore()



export default class App extends Component<any> {
  componentDidMount() {
    SplashScreen.hide();
  }
  render() {
    return (
      <Provider {...rootStore}>
        <Router
          // persistenceKey={"NavigationState"}
          ref={(navigatorRef: any) => {
            // 错误在这里
            console.log(navigatorRef)
            NavigationService.setTopLevelNavigator(navigatorRef);
          }}
        />
      </Provider>
    )
  }
}
```
- 分析： 因为此处的Router相当于整个App，太庞大了，不能这么做。打印出来非常的吃内存。



