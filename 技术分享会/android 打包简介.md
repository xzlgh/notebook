### react-native android端 gradle 打包配置代码

### 主要介绍一些配置文件的用法

> 配置项经常会接触到，所以我们应该对里面的配置信息有一些了解

![img](https://raw.githubusercontent.com/onionRunning/daily_record/master/asset/summary/font/gradle.jpeg)

> 比较重要的文件

- android
    - settings.gradle
    - build.gradle
    - gradle.properties
    - app
        - build.gradle
        - build
            - outputs
                - apk (打包apk的路径)

android/settings.gradle     
> 描述本项目的依赖(会引入其他文件)
```
rootProject.name = 'fine2e' // 项目名
include ':app'

include ':detox'
project(':detox').projectDir = new File(rootProject.projectDir, '../node_modules/detox/android/detox')
// react-native link
```

android/build.gradle
> 配置你的软件版本信息、引用库的配置，以及编译所需的配置(各种配置)
```
buildscript {
    ext {
        buildToolsVersion = "28.0.3"  
        minSdkVersion = 18               
        compileSdkVersion = 28
        targetSdkVersion = 28
        supportLibVersion = "28.0.0"
    }
    ext.kotlinVersion = '1.3.41'                   
    ext.detoxKotlinVersion = ext.kotlinVersion     

    // 定义的全局的变量 (会在其他地方使用, eg: android/app/buidle.gradle)

    repositories { // repositories闭包  配置远程仓库
        google()
        mavenCentral()                              

        //代码托管库：设置之后可以在项目中轻松引用jcenter上的开源项目
        jcenter{url "http://jcenter.bintray.com/"}   
    } 
    
    // dependencies闭包        配置构建工具
    dependencies {
        // gradle插件版本3.4.1 用于构建 android
        classpath 'com.android.tools.build:gradle:3.4.1'

        // kotlin 一门语言可用于开发android app  ，可以java 在同一工程中进行联合调试
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlinVersion"  
    }
}

allprojects {
    repositories {
        mavenLocal()
        google()
        jcenter{url "http://jcenter.bintray.com/"}

        maven {
            url "$rootDir/../node_modules/react-native/android"
        }
        maven {
            url "$rootDir/../node_modules/detox/Detox-android"  
        }
    }
}


buildScript: gradle脚本执行所需依赖
allprojects: 是项目本身需要的依赖
```

android/app/build.gradle

> android 下的 build.gradle(配置整个工程) ， android/app 下的 build.gradle(配置app)

```

- 1、apply plugin
引入相应的插件

- 2、android{}闭包
    - signingConfigs 闭包（密钥签名）
    - compileSdkVersion  sdk 版本号
    - buildToolsVersion  构建工具版本号
    - defaultConfig{}闭包   安装包的一些信息
    - buildTypes{}闭包      打包构建时的信息
    - lintOptions{}闭包：   代码扫描分析

- 3、dependencies{}闭包： react-native link
项目的依赖关系
```

```
apply plugin: "com.android.application"
import com.android.build.OutputFile


project.ext.react = [
    entryFile: "index.js"     // 根目录下需要打包的js 入口文件
]

apply from: "../../node_modules/react-native/react.gradle"


// 定义常量
def enableSeparateBuildPerCPUArchitecture = false
def enableProguardInReleaseBuilds = false

android {

    compileSdkVersion rootProject.ext.compileSdkVersion  // gradle 编译你应用的android sdk版本 (28)
    buildToolsVersion '28.0.3'         // 构建工具的版本

    // 设置JDK1.8
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }

    // 设置是 比较重要了
    defaultConfig {
        applicationId "com.teste2e"                              // 项目的包名
        minSdkVersion rootProject.ext.minSdkVersion              // 项目兼容的最低sdk版本
        targetSdkVersion rootProject.ext.targetSdkVersion        // 项目当前sdk版本
        versionCode 1                                            // 版本code,每次打包上线递增
        versionName "v1.0"                                        // 版本名称,展示在应用市场

        
        testBuildType System.getProperty('testBuildType', 'debug')
        //Instrumentation单元测试
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }
    splits {
        // 根据abi打出相关的包，避免把全部的都打进去，apk太大
        abi { 
            reset()
            enable enableSeparateBuildPerCPUArchitecture
            universalApk false  
            include "armeabi-v7a", "x86", "arm64-v8a", "x86_64"
        }
    }
    
    signingConfigs {
        // ps. 这一块是你的打包配置密钥 我这边是直接复制 detox demo项目的配置文件 ,看你个人的需要
        release {
            // 签名配置
            storeFile file("keystore.jks") 
            storePassword "12345678"
            keyAlias "key0"
            keyPassword "12345678"
        }
    }
    // 指定生成安装文件的配置，常有两个子包:release,debug
    buildTypes {
        release {
            minifyEnabled true      // 代码混淆(无用代码删除)

            // (代码混淆规则) release的Proguard默认为Module下的proguard-rules.pro文件
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            
            proguardFile "${rootProject.projectDir}/../node_modules/detox/android/detox/proguard-rules-app.pro"
            
            // 签名信息
            signingConfig signingConfigs.release
        }

        majiabaoOne.initWith(buildTypes.release)
        majiabaoOne{
            applicationIdSuffix ".majiabaoOne" 
        }
    }

    productFlavors{ // 多渠道打包
        huawei{
            applicationId "com.huawei.com"
        }
        xiaomi{

        }
    }

    // 可以设置 包名
    applicationVariants.all { variant ->
        variant.outputs.each { output ->
            def versionCodes = ["armeabi-v7a":1, "x86":2, "arm64-v8a": 3, "x86_64": 4]
            def abi = output.getFilter(OutputFile.ABI)
            if (abi != null) {  // null for the universal-debug, universal-release variants
                output.versionCodeOverride =
                        versionCodes.get(abi) * 1048576 + defaultConfig.versionCode
            }
        }
    }

    // 代码扫描分析
    lintOptions {
        //即使报错也不会停止打包
        abortOnError false
        //打包release版本的时候是否进行检测
        checkReleaseBuilds false
    }
}

//指定当前项目的所有依赖关系：本地依赖、库依赖、远程依赖 react-native link
dependencies {
    //本地jar包依赖
    implementation fileTree(include: ['*.jar'], dir: 'libs')

    implementation 'com.android.support:appcompat-v7:27.1.1'
    //远程依赖
    implementation 'com.android.support.constraint:constraint-layout:1.1.2'
    testImplementation 'junit:junit:4.12'
    //声明测试用例库
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}
}

task copyDownloadableDepsToLibs(type: Copy) {
    from configurations.compile
    into 'libs'
}
```


### 更加优雅打主包和打马甲包  或多渠道打包
- 渠道包 发布在不同渠道平台(google play)的安装包(主要是不同市场渠道要有统计)
- 马甲包 与主包的区别颜色，或图片 logo 或启动页(其他与主包完全一样)


多渠道打包(Gradle和Maven多渠道打包)
``` 

1. 在Manifest的application节点下, 添加
<meta-data 
  android:name="CHANNEL_NAME" 
  android:value="${CHANNEL_NAME_VALUE}" />

2. android/app/build.gradle

defaultConfig {
    ...
    flavorDimensions:"versionCode"
}

productFlavors {
    app_baidu {
        applicationId = "com.demo.baidu"
        manifestPlaceholders = [
                CHANNEL_VALUE: "app_baidu", // 对应的渠道名称
                JPUSH_PKGNAME: "",  // 你的appid
                JPUSH_APPKEY : "", //JPush上注册的包名对应的appkey.
                JPUSH_CHANNEL: "", //暂时填写默认值即可.
        ]
    }
    
}

```

马甲包 
```
defaultConfig{
    ...
    manifestPlaceholders = [APP_NAME: 'com.xxx1'] // 配置主包的应用名称
}

buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.releaseConfig
        }
        // 第一个马甲包
        majiabaoOne.initWith(buildTypes.release) // 以release的配置来初始化马甲包配置
        majiabaoOne {
            applicationIdSuffix ".majiabaoOne" // 原有的app_id 加上后缀
            manifestPlaceholders = [APP_NAME: 'com.xxxx1.mjmj'] // 配置马甲包的应用名称
        }
}

AndroidManifest.xml

<application android:label="${APP_NAME}"/>


修改icon and 启动页
android/app/src/mjmj  马甲包后缀命名的文件夹

src
    - main
        -res
    - mjmj
        -res

按照上述方式 一一对应添加        

```

> ps. 目前菲律宾app 处理的方式是直接用打渠道包的方式 打包打不同的包（因为现有的马甲包的版本和code 是不一样的）


### 常用的打包/运行 相关的命令基本解释

> react-native run-android

编译并打包一个用来debug的apk，并安装到模拟器或者手机。
debug版本的apk，每次运行的需要从pc上加载你所写的js代码,所有会有一个js server服务

react-native 0.59.9 版本
```
info Starting JS server.../info JS server already running. 
info Installing the app... 


1. node node_modules/react-native/local-cli/cli.js start
2. cd android && ./gradlew app:installDebug
3. adb reverse tcp:8081 tcp:8081
4. adb -s ${device} install ${args.appFolder}/build/outputs/apk/${args.appFolder}-debug.apk
5. adb -s ${device} shell am start -n ${packageNameWithSuffix}/${packageName}.${mainActivity}
```

> gradlew clean vs gradlew build

```
gradlew clean 清除工程目录下的build文件夹 (android/app/build)


打包命令
- gradlew build  把debug、release、check 环境的包都打出来
- 指定打包: 
    gradlew assembleRelease 
    gradlew assembleDebug 
    gradlew assembleCheck

- app/outputs/apk
    - debug
    - release
    - check


- 如何增加渠道打包: gradlew assembleRelease  // 指定release

- app/outputs/apk
    - baidu
        - release
    - google
        - release     

- 打马甲包  是在渠道之下，基于你的base

- app/outputs/apk
    - baidu
        - release
        - majiabaoOne
    - google
        - release  
        - majiabaoOne
```


> npm run bundle
```
react-native bundle --platform android --dev false --entry-file index.js  --bundle-output android/app/src/main/assets/index.android.bundle  --assets-dest android/app/src/main/res/

```


> react-native run start vs react-native run start --reset-cache
```
前者启动服务,生成一个打包js(监听本地的js 文件)，然后与在虚拟机上安装的debug apk关联起来
后者比前者多了清空缓存的功能
```


### 介绍现有的打包脚本,以及gradlew 部分


```
pkg/js
...


package.json

{
    ...
    "scripts":{
        ...
        "dev:android-peso2go": "react-native run-android --variant=peso2goDebug",
        "dev:android-jetpeso": "react-native run-android --variant=jetpesoDebug",

        "build-android-peso2go": "cd android && ./gradlew assemblePeso2goRelease",
        "build-android-jetpeso": "cd android && ./gradlew assembleJetpesoRelease"
    }
}

android/app/build.gradle

android{
    ...
    flavorDimensions 'api'
    productFlavors {
        peso2go{
            applicationId "com.peso2go"
            versionCode 20
            versionName "v1.9.4"
        }
        jetpeso{
            applicationId "com.peso2go.mjOne"
            versionCode 5
            versionName "v1.0.3"
        }
    }
}

```



参考文章:
1. [史上最全Android build.gradle配置详解](https://www.jianshu.com/p/c11862136abf)
2. [Android Studio Gradle配置详解](https://blog.csdn.net/lylodyf/article/details/81194163)
3. [Android多渠道打包方式](https://www.jianshu.com/p/552c8f5ce203)
4. [官网介绍配置项](http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html)

5. [react-native run-android 详细过程](http://blog.hacktons.cn/2018/04/28/react-native-startup/)