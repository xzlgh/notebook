
# react-native从搭建到运行

## 前言

```javascript
开始之前我们先来了解两个词汇React.js和React Native

React.js: Node.js的一个UI框架，可以对UI代码进行封装，实现UI组件化

React Native: 也是Node.js的一个UI框架，是基于React.js实现的

React Native跟React.js的本质区别在于在渲染时，ReactJs是驱动html dom渲染；React Native是驱动android/ios原生组件渲染
```

## 一、环境搭建

### 安装清单

```javascript
1.Node

2.Watchman: Facebook 提供的监视文件系统变更的工具。安装此工具可以提高开发时的性能（packager 可以快速捕捉文件的变化从而实现实时刷新）。

3.Yarn: Facebook 提供的替代 npm 的工具，可以加速 node 模块的下载。React Native 的命令行工具用于执行创建、初始化、更新项目、运行打包服务（packager）等任务。

4.React Native的命令行工具(react-native-cli): Node.js的一个插件，可用于命令行执行React Native命令和RN应用启动时检查服务端js文件是否更新,以及进行打包服务。
```

#### 步骤1： 下载Node、watchman并配置环境

```javascript
##### 通过安装Homebrew来安装node环境

1. 安装Homebrew
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
中途根据提示输入mac密码就OK
2. 检测brew是否安装成功
brew -v
3. 安装node
brew install node或者sudo brew install node sudo (使用admin权限)
4. 检测node是否安装成功
node -v
5. 安装watchman
brew install watchman

#### 步骤2: 安装yarn和react-native-cli

1. 通过npm安装
npm install -g yarn react-native-cli

2. 设置镜像源
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```

## 二、 创建项目

### 步骤1: 用命令行指令初始化RN项目

```javascript
1. 切换到保存项目的目录
2. react-native init Demo --verbose --version 0.59.9

可以使用react-native init Projectname初始化一个RN项目,可带附加参数：
--verbose 设置初始化时显示详情，比如安装了什么模块，进度如何等等。
--version Versioncode 设置初始化的RN版本，react-native更新得比较快，若不加此参数，会下载最新的react-native
```

### 步骤2: 查看项目目录

```javascript
一个完整的RN项目目录
![目录图](http://gitlab2018/caiqingfeng/res/raw/master/font/zym/share/src.png)
```

```html
android/: Android 工程目录，<font color=red>可用AS等IDE打开</font>，主要用于原生库、原生View的编写桥接、以及 ReactNative 第三方库的 link。

ios/: iOS原生代码目录，主要用于原生库、原生View的编写桥接、以及 ReactNative 第三方库的 link。

index.js/: 设备运行项目的入口文件。有些RN项目下面没有<font color=red>index.js</font>但有index.android.js和index.ios.js文件，它们的作用都是一样的。React Native会根据运行的平台在index.android.js和index.ios.js中进行选择.

package.json: npm 的包管理文件，功能类似 Android 的 gradle，iOS 的 cocoaPods,编辑该文件,可以发现跟maven的pom文件很像，具体的用法以后将会继续写明。。

node_modules/: 执行 npm install 之后生成，包含 npm 根据 package.json 下载的第三方包的源代码。
```

## 三、 运行项目

### 真机调试运行RN项目

```javascript
1. 用USB线连接真机与电脑环境
手机上选择文件传输模式并打开手机的系统的调试模式
使用adb指令进行查看验证手机是否正确连接
在命令行下输入：adb devices
若显示no devices,进行adb重启adb kill-server之后adb start-server再进行查看。

2. 在项目目录下执行react-native run-android
```
