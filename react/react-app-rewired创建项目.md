# 搭建项目

## 项目所需依赖

```json
"dependencies": {
    // 前三个create-react-app创建项目是默认生成
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.3.2",
    "@testing-library/user-event": "^7.1.2",
    "@types/history": "^4.7.5",
    "@types/react-router-dom": "^5.1.3",
    "babel-plugin-transform-decorators-legacy": "^1.3.5",
    "customize-cra": "^0.9.1",
    "mobx": "^5.15.4",
    "mobx-react": "^6.1.7",
    "mobx-react-router": "^4.1.0",
    "node-sass": "^4.13.1",
    "react": "^16.12.0",
    "react-dom": "^16.12.0",
    "react-router": "^5.1.2",
    "react-router-dom": "^5.1.2",
    "react-scripts": "3.3.1"
},
"devDependencies": {
    "@babel/core": "^7.8.4",
    "@babel/plugin-proposal-decorators": "^7.8.3",
    "babel-loader": "^8.0.6",
    "postcss-px-to-viewport": "^1.1.1",
    "react-app-rewired": "^2.1.5",
    "typescript": "^3.7.5"
},
```

### 搭建项目详细过程

#### 创建项目 npx create-react-app react-demo

生成目录：

```js
—— public
|
    — favicon.ico
    — index.html
    — logo192.png
    — logo512.png
    — manifest.json
    — robots.txt
—— src
|
    — App.css
    — App.js
    — App.test.js
    — index.css
    — logo.svg
    — serviceWorker.js
    — setupTests.js
— .gitignore
— package.json
— READNE.md
— yarn.lock
```

#### 清理一下自动生成的文件

- App.js文件，只需要留下基础内容即可

```javascript
import React from 'react';

function App() {
  return (
    <div className="App">
    </div>
  );
}

export default App;
```

- 删除App.css
- 删除public/logo192.png和public/logo512.png
- 修改public/manifest.json文件内容

```json
{
    ...
    "icons": [
    {
      "src": "favicon.ico",
      "sizes": "64x64 32x32 24x24 16x16",
      "type": "image/x-icon"
    },
    // 删除这部分配置
    // {
    //   "src": "logo192.png",
    //   "type": "image/png",
    //   "sizes": "192x192"
    // },
    // {
    //   "src": "logo512.png",
    //   "type": "image/png",
    //   "sizes": "512x512"
    // }
    ...
  ],
}
```

#### 安装react-app-rewired 和 customize-cra

react-app-rewired 为了不eject, 就能够扩展项目配置，这个库可以通过根目录下的config-overrides.js文件进行扩展，不需要暴露
customize-cra 与react-app-rewired配合，可以方便的对项目做打包配置

安装完成后，根目录下面创建一个config-overrides.js文件，此文件用于扩展项目的配置，customize-cra库里面，社区维护了一些封装好的配置方法，我们可以通过这些方法，方便的对webpack\jest\webpackServer等配置进行扩展。

config-overrides.js文件

```javascript
const {
    override,
    addWebpackAlias,
    addDecoratorsLegacy,
    overrideDevServer,
    watchAll
} = require("customize-cra")

const path = require('path')

// 移动端启用px自动转换单位vw插件库配置， 移动端需要添加，如果不是移动端则不用，这里需要yarn add postcss-px-to-viewport
const postcssPxToViewport = require("postcss-px-to-viewport")

const postcssConfig = postcssPxToViewport({
    unitToConvert: "px",
    viewportWidth: 750,
    viewportHeight: 1334,
    unitPrecision: 5,
    propList: ["*"],
    viewportUnit: "vw",
    fontViewportUnit: "vw",
    selectorBlackList: [],
    minPixelValue: 1,
    mediaQuery: false,
    replace: true,
    exclude: [],
    landscape: false,
    landscapeUnit: "vw",
    landscapeWidth: 568
})

module.exports = {
    webpack: override(
        addPostcssPlugins([postcssConfig]), // px转vw
        addWebpackAlias({
            "@": path.resolve(__dirname, 'src') // 配置别名
        }),
        addDecoratorsLegacy(), // 启用es7装饰器语法， 需要安装babel-plugin-transform-decorators-legacy
    ),
    devServer: overrideDevServer(
        watchAll() // 配置服务热更
    )
}
```

这里涉及到需要我们通过babel来解析es6和es7的语法，所以我们这里还需要安装babel的依赖，待会我们再安装

- 修改package.json文件

```json
{
    // ...
    "scripts": {
        // - "start": "react-scripts start",
        "start": "react-app-rewired start",
        // - "build": "react-scripts build",
        "build": "react-app-rewired build",
        // - "test": "react-scripts test",
        "test": "react-app-rewired test",
        // - "eject": "react-scripts eject"
        // "eject": "react-app-rewired eject"
    },
    // ...
}

```

#### 安装babel

> yarn add @babel/core babel-loader @babel/plugin-proposal-decorators babel-plugin-transform-decorators-legacy

#### 安装ts以及react,react-dom,react-router,react-router-dom,mobx,mobx-react

> yarn add typescript react react-dom react-router react-router-dom mobx mobx-react mobx-react-router  
@types/history @types/react-router-dom

至此安装完成所有需要的依赖

*如果需要使用sass,可以直接安装node-sass依赖库，即可直接使用sass,如果使用的是less,则需要在config-overrides.js文件中添加less的配置。*

#### 项目目录调整

目标项目结构

```md
- src
|
    - Components  // 放置公用组件
    - Pages // 放置页面
    - Store // 放置store数据
    - Routes // 放置路由配置
```

#### 配置项目别名

- 新建tsconfig.json,添加如下内容：

```json
{
  "extends": "./paths.json",
  "compilerOptions": {
    "target": "es5",
    "lib": [
      "dom",
      "dom.iterable",
      "esnext"
    ],
    "allowJs": true,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react",
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  },
  "include": [
    "src"
  ]
}
```

- 根目录下创建一个新的paths.json文件，填充以下内容，用以配合webpack配置别名

```json
{
    "compilerOptions": {
      "baseUrl": ".",
      "paths": {
        "@Components/*": ["src/Components/*"],
        "@Pages/*": ["src/Pages/*"],
        "@Router/*": ["src/Router/*"],
        "@Store/*": ["src/Store/*"],
        "@Server/*": ["src/Server/*"],
        "@Utils/*": ["src/Utils/*"],
        "@/*": ["src/*"]
      }
    }
}
```

`@Pages`要起效需要在webpack的扩展配置里面添加路径，即在config-overrides.js文件中添加如下内容

```js
const {
    override,
    addWebpackAlias,
    addDecoratorsLegacy,
    // ...
} = require("customize-cra")

const path = require('path')

// ....

module.exports = {
    webpack: override(
        // ...
        addWebpackAlias({
            "@": path.resolve(__dirname, 'src'), // 配置别名
            "@Pages": path.resolve(__dirname, 'src/Pages') // 增加这一句，表示的意思为`@Pages`在src/Pages目录
        }),
        addDecoratorsLegacy(), // 启用es7装饰器语法， 需要安装babel-plugin-transform-decorators-legacy
    ),
    // ...
}
```

#### 嵌入mobx到项目中

- src/App.js文件修改为如下

```tsx
import React from 'react';
import { Provider } from 'mobx-react';

import RootStore from './Store'

const rootStore = new RootStore()

function App() {
  return (
    <Provider {...rootStore}>
      <div></div>
    </Provider>
  );
}

export default App;
```

- src/Store目录下新增一个index.ts文件和demo.ts文件，文件内容如下

idnex.ts文件

```ts
import Demo from './demo'

class RootStore {
    demo: Demo

    constructor() {
        this.demo = new Demo()
    }
}

export default RootStore
```

demo.ts文件

```ts
import { observable } from "mobx"

class Demo {
    @observable _name: string = 'test'
}

export default Demo
```

#### 嵌入mobx

- 修改App.js文件

```jsx
import React from 'react';
import { Provider } from 'mobx-react';
import RootStore from './Store'

// 新增代码
import { createBrowserHistory } from 'history'
import { RouterStore, syncHistoryWithStore } from 'mobx-react-router'
import { Route, Router } from 'react-router-dom'
import Routes from './Routes'

// 新增
const browserHistory =  createBrowserHistory()
const routeStore = new RouterStore()
const history = syncHistoryWithStore(browserHistory, routeStore)


const rootStore = new RootStore()

function App() {
  return (
    <Provider {...rootStore}>
      {
        // 修改内容
      }
      <Router history={history}>
        <Route component={Routes}/>
      </Router>
    </Provider>
  );
}

export default App;
```

- Routes目录下面新增加index.tsx文件， 文件内容如下：

```tsx
import React from 'react'
import {Route, Switch, Redirect} from 'react-router-dom'
import Home from '@Pages/Home' // 自定义的Home页面

class Router extends React.Component {
    render() {
        return (
            <>
                <Switch>
                    <Route path="/" exact component={Home}/>
                    <Route path="/home" component={Home}/>
                    <Redirect path="/*" to="/"/>
                </Switch>
            </>
        )
    }
}

export default Router
```

至此环境搭建内容基本结束，如果需要其他的一些依赖库，可以自行添加




