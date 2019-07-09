## 安装

现在node里面默认有npm，因为在前端的打包上线，环境配置等工作中，都有node的身影，这里推荐直接安装node。

> node官网：https://nodejs.org/zh-cn/
> 在官网下载长期维护版本的对应安装包\源码包

注: 选择长期支持的版本是因为这种版本会更加稳定，相对来说bug较少。


## 为npm更换镜像源
### 这里选用淘宝的镜像源
```
- 1. npm install -g cnpm --registry=https://registry.npm.taobao.org   安装一个新的cnpm，为它设置镜像源为淘宝镜像
- 2. npm config set registry https://registry.npm.taobao.org 直接更换npm的镜像源
- 验证换源是否成功: npm config get registry
```
npm换源后,使用效果和使用cnpm是一样的!

### npm常用命令
```
- npm init 初始化
- npm i || npm install + 具体的包名   下载相关的库和第三方插件等
```