## docker 搭建Sentry服务
一. 安装docker && docker-compose
  - 使用的是mac的话，自己去官网下载镜像文件https://hub.docker.com/editions/community/docker-ce-desktop-mac
  - 使用Ubuntu 
    - 安装docker http://www.runoob.com/docker/ubuntu-docker-install.html
    - 安装docker-compose
        - 没有pip `sudo apt-get install python-pip`
        - `sudo pip install docker-compose`

二. 本地搭建sentry服务
  - git clone https://github.com/getsentry/onpremise.git
    1. `docker volume create --name=sentry-data && docker volume create --name=sentry-postgres` - Make our local database and sentry volumes
        Docker volumes have to be created manually, as they are declared as external to be more durable.
    2. `cp -n .env.example .env` - create env config file
    3. `docker-compose build` - Build and tag the Docker services
    4. `docker-compose run --rm web config generate-secret-key` - Generate a secret key.
        生成的密钥添加到 docker-compose.yml中， 在 environment: 下添加
        `SENTRY_SECRET_KEY: 添加你生成的密钥字符串`.
    5. `docker-compose run --rm web upgrade` - Build the database.
        Use the interactive prompts to create a user account.（安装期间需要输入邮箱和密码， 用于后面的服务登录）
    6. `docker-compose up -d` - Lift all services (detached/background mode).
    7. Access your instance at `localhost:9000`!
    8. 登录之后会要求你输入错误信息所上传的ip地址 && admin 邮箱地址
三. 创建监控项目（ps:演示的是native项目）
   1. cd到你的native项目 安装原生依赖，用于上报手机客户端信息 `npm install react-native-sentry --save`
   2. 本文档是本地部署sentry服务，不用上传到sentry官网服务器上。需要手动写入配置 react-native-sentry
        - android/setting.gradle  
        ```
        include ':react-native-sentry'
        project(':react-native-sentry').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-sentry/android')
        ```
        - android/app/build.gradle  
        *若项目结构和RN初始化出来的一致，可以使用以下命令在每次构建时上次sourcemap，本项目不使用该方式*
        *apply from: "../../node_modules/react-native-sentry/sentry.gradle" 加入依赖*
        ```
            dependencies {
                compile project(':react-native-sentry')
            }
        ```
        - android/app/src/main/java/com/reactnativedemo/MainApplication.java

        ```
        import io.sentry.RNSentryPackage;
        public class MainApplication extends Application implements ReactApplication {
        private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
            @Override
            protected List<ReactPackage> getPackages() {
            return Arrays.asList(
                new MainReactPackage(),
                // 插入下方代码
                new RNSentryPackage(MainApplication.this)
            );
            }
        };
        }
        ```

   3. 进入你搭建的Sentry服务 为你需要监控的项目创建一个项目名字，进入项目之后在设置中找到 Client Keys(DSN) 复制当前DSN
   4. 在你的native项目中 找到入口文件 // index.js  写下
        ```
            import { Sentry } from "react-native-sentry";
            Sentry.config("复制的DSN地址").install();
        ```
   *运行你的native项目，目前可以监控到错误了，但是没有显示错误具体的哪行代码，接下来我们需要生成一个sourcemap文件来对于源码进行一个映射，使他可以解析出源码的具体位置*

   5. 全局安装 sentry-cli `sudo npm install -g @sentry/cli --unsafe-perm`

   6. 创建一个sentry配置文件，配置你的sentry服务器信息 
        `mkdir .sentry.properties` 在里面写入： 
        ```
           # .sentry.properties
            defaults.url=自己的后端服务地址
            defaults.org=项目所在组织
            defaults.project=项目名
            auth.token=（你的sentry服务地址/api, 创建一个Auth Tokens,之后复制写在这里）
            cli.executable=/usr/local/bin/sentry-cli 
        ```
    7. 进入native项目，打包生成sourcemap文件
        ```
        react-native bundle 
            --platform android 
            --dev false 
            --entry-file index.js 
            --bundle-output （输出bundle文件路径）/index.android.bundle 
            --sourcemap-output （输出sourcemap文件路径）/index.android.map
        ```
     8. 上传sourcemap文件, 在你native项目中，终端输入
        `export SENTRY_PROPERTIES=.sentry.properties`
        ```
        sentry-cli releases 
            files RELEASE_NAME 
            upload-sourcemaps 
            --dist DISTRIBUTION_NAME 
            --strip-prefix /path/to/project/root 
            --rewrite 
            （输出bundle文件路径）/index.android.bundle （输出sourcemap文件路径）/index.android.map
        ```
        如果一切顺利，那么 bundle文件和map 文件都已上传到你的sentry服务中，
        终端输入`sentry-cli releases files VERSION(ps：这里写你项目的名称-版本) list`检验
        也可以在你的 sentry服务上进入你的创建的项目，找到Releases下的版本 Artifacts 中会躺着2个文件 `~/index.android.bundle && ~/index.android.map`
    9. 进入你的native项目打包当前apk， 安装到手机上。那么ok，一旦有错误就可以在你的sentry服务器上可以看到你的错误信息定位到代码具体哪一行