- 关键词: tslint, jslint, eslint, 项目配置
---

> javascript 代码校验工具

### jslint eslint tslint 区别?
```
1. jslint 比较老的工具 ,不支持自定义配置(没有使用过)
2. eslint 插件化的 javascript/typescript 代码检测工具
3. tslint 检测 typescript 语法工具

ps. typescript 官网表示全面拥抱 eslint , eslint > tslint (star 3:1)
```
---

### 项目工程中如何去配置你的检测工具 eslint / tslint ?


#### 1. eslint 使用

```
全局安装: sudo npm i eslint -g
project下: eslint --init  
> .eslintrc.*
{
    "rules":{
        // ...
    }
}

ps: .eslintrc.* ( .eslintrc/ .eslintrc.json)

> 也可以是 package.json

{
   ...
   "eslintConfig": {
        "dev": {
            "browser": true,
            "node": true
        }
    },
    ... 
}

> 也可以支持:yaml/yml 格式


> 如果一个目录下有多个配置文件，ESLint 只会使用一个 并且具有优先级

优先级从上往下
1. .eslintrc.js   
2. .eslintrc.yaml
3. .eslintrc.yml
4. .eslintrc.json (常用)
5. .eslintrc      (常用)
6. package.json

ps. 具体的项目中需要的配置需要看你的需求去配置，这里就不过多说明。

```


#### 2. tslint 使用

```
TSLint will be deprecated some time in 2019. See this issue for more details: Roadmap: TSLint → ESLint. If you're interested in helping with the TSLint/ESLint migration

tslint 停止维护? 需要代码检测工具迁移 tslint ---> eslint  (😯😯😯)
还是大体介绍一波尊重下这个,目前我们使用的项目大多用到了这个 tslint 歪日慌的一匹

- tslint.json
{
    rules:{
        ...
    }
}

- tsconfig.json
{

}
> ps. 无论是 tslint 还是 eslint 都可以在各自的文件夹内设置自己的规则, a,b 俩个文件可以设置不同的规则

```

### 代码检测工具如何工作
> 项目中配置了无论是tslint还是eslint 到底怎么才算生效，或者说这个检测工具什么时候工作,大家有没有思考过...

1. vscode 编辑器有提示
这个方式是最直观的体现了,就以tslint 为例子

![1](https://gitlab2018.com/caiqingfeng/res/raw/master/font/wangcong/share/07/demo.jpeg)

![2](https://gitlab2018.com/caiqingfeng/res/raw/master/font/wangcong/share/07/demo2.jpeg)

然后我们打开我们code时，会有这些错误，你知道后然会去会改。

ps. 这个红色错误是怎么产生的, vscode 为什么能够把不符合tslint规则的代码打印？

- vscode 内部有代码检测机制根据你的tslint规则, 然后匹配相应的code报红,以及提示


2. 命令行操作打印log

如果说编辑器没有提示功能,然后我们需要用命令行跑你的项目工程然后输出结果,其实不是很直观。下面以tslint介绍为例子

- 全局安装tslint 检测你的工程code
项目工程下: tslint --project tsconfig.json --config tslint.json

![img](https://gitlab2018.com/caiqingfeng/res/raw/master/font/wangcong/share/07/demo3.jpeg) 

> 我觉得不是很直观要是能输出表格排版并且整理.

- 项目工程下安装过跑你的code
项目工程下: ./node_modules/.bin/tslint --project tsconfig.json --config tslint.json

ps. 以上俩中方式就是输出了相应结果，但是打印的日志不是特别直观。

3. gitHooks.pre-commit 错误会拦截提交操作
本地项目工程中需要安装:tslint

进入工程中: cd .git/hooks  然后修改 pre-commit.sample

```sh
#!/bin/bash
TSLINT="$(git rev-parse --show-toplevel)/node_modules/.bin/tslint"
for file in $(git diff --cached --name-only | grep -E '\.ts$')
do
        git show ":$file" | "$TSLINT" "$file"
        if [ $? -ne 0 ]; then
                exit 1
        fi
done
```
然后修改 pre-commit.sample名字为 pre-commit (注意: 如果你是重新创建pre-commit 需要添加权限)
在git commit 操作的时候 会先检查你的工程代码是否符合tslint 规范 ,只要你的code有一处不符合规范的就提交不了, 是否太强制了。(eg:如果有些类型实在是没有,any可以稍微替换?)

![img2](https://gitlab2018.com/caiqingfeng/res/raw/master/font/wangcong/share/07/demo4.jpeg) 
![img3](https://gitlab2018.com/caiqingfeng/res/raw/master/font/wangcong/share/07/demo5.jpeg) 

ps. .git文件 里面还有其他的文件具体感兴趣自行探索, husky 也可以自行了解

---

### 基于npm 自定义一个代码检测工具(基于tslint)

> 如果有一个工具对现有代码流程无影响，并且又可以格式化输出结果,发现问题就去修改 貌似会更好
- 先定义api
```
安装
sudo npm i min-codecheck -g

min-codecheck s    // 扫描当前目录代码,然后输出统计结果
min-codecheck c    // 检测代码是否符合tslint 的要求,然后输出不符合要求的代码位置,也有统计
```
#### 如何实现
```
1. 实现代码扫描
2. 实现tslint代码检测
3. min-codecheck s/c 各自的api 如何实现
4. sudo npm i min-codecheck -g 怎么实现
```

##### step1 实现代码扫描  核心代码

```js
// 获取当前目录下的文件名,统计特定后缀的文件( 分类 )然后统计个数 以及行数
const getFilesName = (startPath) => {  // 获取文件名 以及 文件的代码行数
    let result=[]
    const finder = ( path )=> {
        let files=fs.readdirSync(path)
        files.forEach((temp) => {
            if(ignoreDir.includes(temp))return // 过滤指定文件 like: node_modules
            let fPath=join(path,temp)
            let stats=fs.statSync(fPath)
            if(stats.isDirectory()) finder(fPath) // 如果是文件夹 进行递归
            if(stats.isFile()) result.push({name:fPath, line:getLine(fPath)}) // 文件添加
        })
    }
    finder(startPath)
    return result
}

// 获取代码行数
const getLine = async(path) => {
    let rep = await fs.readFileSync(path)
    rep = rep.toString()
    let lines = rep.split("\n")
    return lines.length
}

fs 模块: readFileSync , readdirSync 

```

##### step2 实现 tslint 规则检查你的 核心代码

```js
const tslint = require("tslint")

const getLinterResult = (spath, rootPath) =>{
    const fileContents = fs.readFileSync(spath, "utf8") 
    const linter = new tslint.Linter({})
    const name = path.join(__dirname,'../','tslint.json')   // 比较坑路径需要注意
    const configuration = tslint.Configuration.findConfiguration(name, spath).results
    linter.lint(spath, fileContents,configuration)
    const result = linter.getResult()  // tslint 检测你的code 

    const temp = []
    for(let i =0 ; i < result.failures.length ; i++){
        const item = result.failures[i]
        if(InRule.includes(item.ruleName)){
            temp.push([item.fileName.replace(rootPath+'/',''), reflectWarn[item.ruleName],item.startPosition.lineAndCharacter.line + 1])
        }        
    }
    return temp
}
```
##### step3 min-codecheck s / min-codecheck s  怎么对应上执行对应的api

在你的项目工程 根目录下新建 bin/command.js代码如下:
```js
#! /usr/bin/env node  // 必须要加
const program = require('commander')
const { getCodeSum } = require('../scan/index')
const { checkFilesCode } = require('../checkRule/check')

program
  .version(require('../package').version)
  .command('--version')
  .alias('-v')
  .action(function(type, name){
    console.log('当前版本号',require('../package').version)
  })

program
  .command('--list','list')
  .alias('-l','l')
  .action(function(type, name){
    console.log('min-codecheck c : 检测你的ts版本规则')
    console.log('min-codecheck s : 扫描你的当前目录文件夹')
    console.log('min-codecheck v : 查看当前版本号')
  })

program
  .command('scan')
  .alias('s')
  .action(function(type, name){
    getCodeSum(process.cwd())    //process.cwd() 当前执行目录的地址
  })

program
  .command('clean')
  .alias('c')
  .action(function(type, name){
    checkFilesCode(process.cwd())
  })  

program.parse(process.argv) //解析命令行


##### node 获取地址的三种方法
console.log('__dirname : ' + __dirname)       // 运行文件所在的目录(比如你的 node 安装的地址)
console.log('resolve   : ' + resolve('./'))   // 当前命令所在的目录
console.log('cwd       : ' + process.cwd())   // 当前命令所在的目录

```

##### step4 npm 发布code 实现: sudo npm i min-codecheck -g

```
npm login
npm publish
    每次发版本需要update 你的version   

修改packge.json

"bin": {
    "min-codecheck": "./bin/command.js"  // key: 是你全局使用的命令 value: 指向你的文件地址
},

全局能够使用就是加了软连接操作(环境变量配置), 
/usr/local/bin/min-codecheck -> /usr/local/lib/node_modules/min-codecheck/bin/command.js
```
[github 地址](https://github.com/onionRunning/min-codecheck)
[min-codecheck 地址](https://www.npmjs.com/package/min-codecheck)

### tslint 迁移 eslint 

```
- 可以设置基础代码风格，空格 换行 ... 加入到git commit 中 ,当然 pre-commit 需要去配置拦截
- code使用:

eslint --init
? How would you like to use ESLint? To check syntax and find problems
? What type of modules does your project use? JavaScript modules (import/export)
? Which framework does your project use? React
? Does your project use TypeScript? Yes
? Where does your code run? Browser
? What format do you want your config file to be in? JSON

根目录下默认生成: .eslintrc.json
{
    "env": {
        "browser": true,
        "es6": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:@typescript-eslint/eslint-recommended"
    ],
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly"
    },
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 2018,
        "sourceType": "module"
    },
    "plugins": [
        "react",
        "@typescript-eslint"
    ],
    "rules": {            // 定义规则
        "no-var":2
    }
}

package.json
{   
    ...
    "eslint": "eslint src --ext .tsx && eslint src --ext .ts" // 检查src 目录下的相应的文件
    ...
}
如果你要直接在vs中展示错误:

1. 下载eslint 插件
2. 打开设置中的 setting.json 然后加入下列代码   
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "typescript"
    ]
```
![img4](https://gitlab2018.com/caiqingfeng/res/raw/master/font/wangcong/share/07/demo6.jpeg) 

参考地址:
1. [eslint 官网地址](https://cn.eslint.org/docs/rules/)
2. [React Native工程中TSLint静态检查工具的探索之路](https://tech.meituan.com/2019/01/17/exploring-the-tslint-static-checking-tool-on-the-react-native-project.html)