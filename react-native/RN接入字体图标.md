## 插件使用

### 原生字体图标使用

#### 原生字体图标库-react-native-vector-icons

> 该插件库区分ios和android,这里主要描述的是android

- npm i --save react-native-vector-icons 安装原生字体图标库

- react-native link react-native-vector-icons   自动链接该插件库(也可以手动link,但是不了解原生建议直接自动link)

- 打开android -> app -> build.gradle文件,查看里面的buildscript对象

```Android
buildscript {
    ext {
        <!-- 看buildToolsVersion的版本号,此版本号要与node_modules里面的一致 -->
        buildToolsVersion = "28.0.2"
        minSdkVersion = 16
        compileSdkVersion = 28
        targetSdkVersion = 27
        supportLibVersion = "28.0.0"
    }
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.2.1'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
```

- 打开node_modules -> react-native-vector-icons -> android -> build.gradle文件, 找到BulidToolsVision配置项,将此项改成和上面android里面的配置一致对应,即可完成配置

- 完成配置后,可以开始创建自定义字体图标

- 去阿里巴巴矢量图标库可以下载字体图标,将下载的文件夹里面的.ttf文件拷贝到Android -> app -> src -> main -> assets -> fonts文件夹下面

- 在src下面创建一个Icon文件夹,设置自定义图标,且创建两个文件

```js
>  index.js文件

import FontAwesome from 'react-native-vector-icons/FontAwesome'
import {OIcon} from './iconFont'
import React, {Component} from 'react'
import PropTypes from 'prop-types'

const iconMap = {
  fontAwesome: FontAwesome, // 原来原生的图标名称
  icon: OIcon // 自定义图标名称
}

class Icon extends Component {

  render() {
    const {name, size, color} = this.props
    if (!name.includes('|')) {
      throw new Error('name 解析错误！')
      return null
    }
    let nameArr = name.split('|')
    let fontlib = nameArr[0]
    let font = nameArr[1]
    let CustomIcon = iconMap[fontlib]
    if (!CustomIcon) throw new Error('没有找到匹配的font库，请review代码！')
    return (
      <CustomIcon name={font} size={size} color={color}/>
    )
  }
}

Icon.propTypes = {
  name: PropTypes.string.isRequired,
  size: PropTypes.number.isRequired,
  color: PropTypes.string.isRequired
}

export {Icon}


> iconFont文件:

import {createIconSet} from 'react-native-vector-icons'

const glyphMap = {
 // 这里的数值是Unicode里面的十六进制值转换成十进制后的值,hom是自定义的关键词,用来确定图标名称
  home: 59137,
  setting: 59025
}
// 这里的iconfont要和iconfont.ttf文件对应
const OIcon = createIconSet(glyphMap, 'iconfont', 'iconfont.ttf')

export {OIcon}

```

- 使用自定义的字体图标

```ts
> 在组件中引入刚刚创建的Icon组件,直接使用

import {Icon} from '../../Icon'


export default demo extends Component {
  render() {
    return (
      // name是自定义图标名称, size图标大小, color图标颜色
      <Icon name={'icon|home'} size={200} color={"pink"}/>
    )
  }
}

```

- 重启项目,至此,引入自定义的字体图标已经完成

---
