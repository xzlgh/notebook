
# 测试
## 单元测试
### 渲染UI组件
#### 浅渲染UI组件
##### 出现错误的测试文件：
> 文件名：app.test.js
> 文件内容：
```
import React from 'react'
import { shallow } from 'enzyme'
import App from './App'

describe('Dom node shallow rendering', () => {
  it('renders without crashing', () => {
    shallow(<App />)
  })
})

- 引入的App文件为入口文件
- 引入的enzyme为测试框架，shallow是框架提供的测试框架中测试组件的函数
```

##### 出现的报错信息如下：
```
Summary of all failing tests
 FAIL  client/src/App.test.js
  ● Test suite failed to run

    /home/qydev/workspace/jpmanagement/node_modules/antd/es/input/index.js:1
    ({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,global,jest){import Input from './Input';
                                                                                                    ^^^^^

    SyntaxError: Unexpected identifier

      at new Script (vm.js:79:7)
      at Object.<anonymous> (client/src/BaseComponents/Search/index.js:1:178)
      
```

##### 分析\解决问题问题：
```
1. 错误信息提示Input组件错误，
    - 找到client/src/BaseComponents/Search/index.js文件，文件里面引入了antd框架的Input组件，猜测框架影响，注释引用。
    - 紧接着另一个引用框架中的组件文件也报错，错误信息相同，提示文件和组件的位置不同，再次注释，还是会报错（只要有引用了框架的组件都会出现同样的错误）
    - 出现上述情况，猜测是少了什么库或者是什么插件库，上网百度查找人家的解决方式，没有一个是和自己情况相同的。
    - 解决时间过长，询问身边同事，未能解决，紧接着猜测是配置中的问题，但是目前自己对babel以及webpack不够熟悉，向屈洋请教。
    - 查看babel配置（.babelrc）文件，文件内容如下：
    {
      "presets": [ "es2015", "stage-0", "react"],
      "plugins": [
            "transform-react-jsx",
            "transform-decorators-legacy",
            [
    		    "import",
    			    {
    				    "libraryName": "antd",
    				    "libraryDirectory": "es",
                        "style": "css"
    			    }
    		]
        ],
        "env": {
            "test": {
              "plugins": [
                [ "babel-plugin-webpack-alias", { "config": "./client/config/webpack.config.unit.js" } ]
                ]"libraryDirectory": "es",
            }
        }
    }
    - 删除第9行的："libraryDirectory": "es"
2. 问题得到解决。
```
##### 总结： 对babel掌握的知识不够，基础知识需要加强。



















