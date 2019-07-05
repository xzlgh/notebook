# 环境搭建

## 基础环境搭建

### vue-cli 脚手架搭建项目

#### vue-cli2.×搭建项目
```
- 安装node.js以及npm,安装完成后，为npm换源，更换下载内容的源镜像，可以使用淘宝的镜像。在命令行工具中执行下面内容：
alias cnpm="npm --registry=https://registry.npm.taobao.org --cache=$HOME/.npm/.cache/cnpm --disturl=https://npm.taobao.org/dist --userconfig=$HOME/.cnpmrc"
另外一种方式：npm install --registry=https://registry.npm.taobao.org

- 安装vue-cli: cnpm install vue-cli -g

- 初始化一个项目（普通的webpack项目）: vue init webpack demo2(项目名称)

- 启动项目：npm run dev

- 打包项目：npm run build

- 安装vue-router路由：npm install vue-router --save(保存路由) 安装过程中会看到路由的一些配置

- 安装vuex: npm install vuex --save
```

