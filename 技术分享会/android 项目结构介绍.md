
# 项目目录简介

## 1>.gradle文件夹包含的是gradle工具的各个版本

## 2>.idea文件夹包含了开发所需的各种环境

## 3>app文件夹包含了我们要开发的app所有

1. AndroidManifest.xml：APP的配置信息
2. java：主要为源代码和测试代码
3. res：主要是资源目录，存储所有的项目资源
4. drawable：存储一些xml文件，-*dpi表示存储分辨率的图片，用于适配不同的屏幕。

- mdpi:320x480
- hdpi:480x800、480x854
- xhdpi:至少960x720
- xxhdpi:1280x720
- layout：存储布局文件
- mipmap：存储原声图片资源
- values：存储app引用的一些值（colors.xml：  存储了一些color的样式 ,dimens.xml：存储了一些公用的dp值
, strings.xml： 存储了引用的string值, styles.xml：   存储了app需要用到的一些样式）

- Gradle Scripts:build.gradle为项目的gradle配置文件

## 4>gradle 文件夹包含了 gradle 工具的打包

## 5>build.gradle （主目录下的）文件是最顶层的构建文件，这里配置所有模块通用的配置信息。注意与 模块下的 build.gradle 文件区分

## 6>gradle.properties 文件， gradle 的相关配置

## 7>gradlew.bat 是 windows 下的批处理文件

## 8>local.properties 文件保存 Android SDK 所在的路径

## 9>setting.gradle 文件在初始化过程中被执行，一个 Gradle 构建通常包括三个阶段：初始化，配置，和执行
