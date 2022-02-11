原文链接：[https://www.jianshu.com/p/417a165ca9d7](https://www.jianshu.com/p/417a165ca9d7)

React Native--搭建本地Code Push Server实现高效热更新
=========================================

 [![96](//upload.jianshu.io/users/upload_avatars/877439/b5a70d3e-e99a-4a88-938c-5ea942e692e0.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96)](/u/b534ce5f8fae) 

[青苹果园](/u/b534ce5f8fae)

0.4 2018.07.03 10:57\* 字数 1305

![](//upload-images.jianshu.io/upload_images/877439-f9a0f09c0c7fa74b.jpeg)

[CodePush](https://github.com/Microsoft/react-native-code-push) 简介
------------------------------------------------------------------

> CodePush是一个微软开发的云服务器。通过它，开发者可以直接在用户的设备上部署手机应用更新。
> 
> CodePush相当于一个中心仓库，开发者可以推送当前的更新（包括JS/HTML/CSS/IMAGE等）到CoduPush，然后应用将会查询是否有更新。

热更新的原理大致可以的理解为这样，由于 React Native 会将所有需要加载的 js 文件都打包在一个 bundle 文件中，而 app 运行时会加载该文件。所以，如果要升级 app，一个可行的思路就是动态替换该 bundle 文件，然后重启该 app 即可（如果修改了底层 native 代码则需要重新安装该 app ）。

实际上在开发时使用的更新模式就是上面所说的这种，直接替换 bundle 文件。开发环境时使用调试工具可以 Reload JS（替换 bundle 文件），但是在生产环境却不存在该调试工具，需要自己手动实现动态替换 bundle 的功能，而 CodePush 就是实现了该功能的一个工具。

然而 CodePush 服务器是在国外的，国内使用的话速度并不理想，所以自建本地CodePush 服务是最理想的。

自建 CodePush 服务
--------------

CodePush 服务主要分为三个部分：服务端、客户端、React Native 项目。

### 一、服务端

服务端需要使用 [code-push-server](https://github.com/lisong/code-push-server) 和[MySQL](https://dev.mysql.com/downloads/mysql/5.6.html#downloads) 所以需要先将这两个安装好。

#### 1\. 安装MySQL

*   官网下载后直接双击安装，[下载地址](https://dev.mysql.com/downloads/mysql/5.6.html#downloads)；
*   打开“系统设置”，看到最新安装的MySQL，进入并启动服务。

#### 2\. 安装 [code-push-server](https://github.com/lisong/code-push-server)

作者发布了两种安装方式（npm安装或源码安装），在此我推荐使用源码安装，为后期我们要基于这个服务修改自己的网页，源码安装方便些。

*   (1)、下载code-push-server代码

    // clone代码
    git clone https://github.com/lisong/code-push-server.git
    
    // 进入项目并安装资源
    cd code-push-server && npm install
    

*   (2)、修改config/config.js 文件，在 db 对象中添加数据库信息，参考如下：

     db: {
        username: process.env.RDS_USERNAME || "root", // 数据库账户
        password: process.env.RDS_PASSWORD || "root", // 数据库账户密码
        database: process.env.DATA_BASE || "codepush", // 新建的数据库表名
        host: process.env.RDS_HOST || "127.0.0.1",
        port: process.env.RDS_PORT || 3306,
        dialect: "mysql",
        logging: false
      },
    

*   (3)、创建数据库表

    // 初始化mysql数据库
    ./bin/db init --dbhost localhost --dbuser root --dbpassword 数据库密码
    
    eg..
    ./bin/db init --dbhost 127.0.0.1 --dbuser root --dbpassword  root
    

![](//upload-images.jianshu.io/upload_images/877439-b744444841ffde45.png)

*   (4)、配置打包后的，bundle存储地址，这里配置loal本地也可以配置[qiniu](http://www.qiniu.com/)和[OSS](https://www.aliyun.com/product/oss)

创建`storage`和`data`文件夹，用来保存打包好的资源，供用户更新下载，`downloadUrl`地址必须为服务器所在的地址，不然用户无法下载到包。主要配置如下：

       // 如果存储类型“storageType”为“qiniu”如果更新包放在七牛，需要配置相关信息 (http://www.qiniu.com/) 。
      qiniu: {
        accessKey: "",
        secretKey: "",
        bucketName: "",
        downloadUrl: "" //文件下载域名地址
      },
    
      //阿里云存储配置 当storageType为oss时需要配置
      oss: {
        accessKeyId: "",
        secretAccessKey: "",
        endpoint: "",
        bucketName: "code-push-server",
        prefix: "storage", // Key prefix in object key
        downloadUrl: "https://code-push-server.oss-cn-shenzhen.aliyuncs.com/storage", // binary files download host address.
      },
    
      //文件存储在本地配置 当storageType为local时需要配置
      local: {
        storageDir: "/Users/lisilong/Desktop/workspaces/storage",
        //文件下载地址 CodePush Server 地址 + '/download' download对应app.js里面的地址
        downloadUrl: "http://localhost:3000/download",
        // public static download spacename.
        public: '/download'
      },
    
      jwt: {
        // 登录jwt签名密钥，必须更改，否则有安全隐患，可以使用随机生成的字符串
        // Recommended: 63 random alpha-numeric characters
        // Generate using: https://www.grc.com/passwords.htm
        tokenSecret: 'INSERT_RANDOM_TOKEN_KEY'
      },
      
      common: {
        dataDir: "/Users/lisilong/Desktop/workspaces/data",
        //选择存储类型，目前支持local,oss,qiniu,s3配置
        storageType: "local"
      },
    

*   (5)、改好之后，启动服务

    // 在根目录中执行
    ./bin/www
    

![](//upload-images.jianshu.io/upload_images/877439-6ab0dc8a040d4a10.png)

在浏览其中输入：[http://127.0.0.1:3000](http://127.0.0.1:3000) 能加载到CodePushServer登录界面即表示启动完成。

二.客户端
-----

### 1\. 客户端需要安装 code-push-cli [参考文档](https://github.com/lisong/code-push-server/blob/master/docs/react-native-code-push.md)

    npm install -g code-push-cli
    

### 2\. 登录code-push-server，使code push和自建的服务器关联

执行命令查看当前是否登录，因为是新服务，所以要先保证没有别的账号正在登录

     code-push whoami
    

如果报错如下，表示没有登录

    [Error]  You are not currently logged in. Run the 'code-push login' command to authenticate with the CodePush server.
    

如果没有报错 并且显示邮箱账号，则表示已经登录账户，则我们要先注销当前账号

    code-push logout
    

成功注销后执行登录指令，浏览器会自动打开本地服务登录页面，命令行中会提示输入key。默认账号和密码为： admin 123456， 登录后获取token 并复制token到命令行中，并回车确认

    code-push login http://localhost:3000
    
    //提示此表示登录成功
    Successfully logged-in…… 
    

### 3\. 创建应用，获取 DeploymentKey

    Usage: code-push app add <appName> <os> <platform>
    选项：
      -v, --version  显示版本号  [布尔]
    
    示例：
      app add MyApp ios react-native      Adds app "MyApp", indicating that it's an iOS React Native app
      app add MyApp windows react-native  Adds app "MyApp", indicating that it's a Windows React Native app
      app add MyApp android cordova       Adds app "MyApp", indicating that it's an Android Cordova app
    
    e.g..
    code-push app add ReactNativeCodePushDemo-ios ios react-native
    

结果如下：

    │   Name     │ Deployment Key                        │
    ├────────────┼───────────────────────────────────────┤
    │ Production │ EoQ6vVQ19YYXH18JxnoOVDsYtAcT4ksvOXqog │
    ├────────────┼───────────────────────────────────────┤
    │ Staging    │ YO4pnZs4ePEG2F8p7dPWnS3oHDg74ksvOXqog │
    

其中Production对应的是生产的Deployment Key，Staging是开发时使用。

**可以通过命令行查看**，[更多相关命令，请查阅官方文档](https://github.com/Microsoft/react-native-code-push/blob/master/docs/setup-ios.md)

    code-push deployment ls XunHuiFinance-ios -k
    

![](//upload-images.jianshu.io/upload_images/877439-04591646072406f6.png)

三、React Native 项目端
------------------

### 1\. 安装 react-native-code-push

    // 项目中导入CodePush代码
    npm install --save react-native-code-push
    // 关联我们的项目
    react-native link react-native-code-push
    

### 2.可以把检测更新的入口，添加到componentDidMount方法中：

    componentDidMount() {
        CodePush.sync({
            //启动模式三种：ON_NEXT_RESUME、ON_NEXT_RESTART、IMMEDIATE
            installMode: CodePush.InstallMode.ON_NEXT_RESTART,
            // 苹果公司和中国区安卓的热更新，是不允许弹窗提示的，所以不能设置为true
            updateDialog: false  
        });
    }
    

### 3\. Android端配置

*   (1)、settings.gradle加入：

    include ':react-native-code-push'
    project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
    

*   (2)、build.gradle修改：

    apply from: "../../node_modules/react-native-code-push/android/codepush.gradle"
    
    dependencies {
        compile fileTree(dir: "libs", include: ["*.jar"])
        compile "com.android.support:appcompat-v7:23.0.1"
        compile "com.facebook.react:react-native:+"  // From node_modules
        compile project(':react-native-code-push')
    }
    

*   (3)、MainApplication文件下修改

     private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
    
       @Override
       protected String getJSBundleFile() {
            return CodePush.getJSBundleFile();
       }
        
        @Override
        public boolean getUseDeveloperSupport() {
          return BuildConfig.DEBUG;
        }
    
        //第一个参数是刚刚申请的key（可以根据环境配置）
        //第三个参数是服务器的URL
        @Override
        protected List<ReactPackage> getPackages() {
          return Arrays.<ReactPackage>asList(
              new MainReactPackage(),
              new CodePush(" nJ3oSQmb64bxRqTP9mwMhZuZLIm94ksvOXqog ", MainApplication.this, BuildConfig.DEBUG,"http://你的IP:端口/")
          );
        }
    

*   (4)、修改版本号

将 android/app/build.gradle 中的 `android.defaultConfig.versionName` 改成3位数的版本号（默认是1.0，但是codepush需要三位数）。

    android{
        defaultConfig{
            versionName "1.0.0"
        }
    }
    

### 4\. iOS端配置

*   (1)、info.plist配置

![](//upload-images.jianshu.io/upload_images/877439-bcf491ab1628d1e3.png)

1

![](//upload-images.jianshu.io/upload_images/877439-ea2e36e1bb7d75c7.png)

2

CodePushDeploymentKey 即为我们注册APP时获得的key，更加开发还是生产来分别设置；CodePushServerURL对应的是我们的bundle更新包的下载地址。这里因为用的是真机调试，所以配置了服务器的ip地址。

*   (2)、开发阶段可以先直接使用CodePush:

![](//upload-images.jianshu.io/upload_images/877439-3f3bc78525799752.png)

*   (3)、改成3位数的项目版本号

四、发布更新
------

### 发布更新：

    // 可以使用code-push release-react --help查看语法
    code-push release-react --help
    
    / 发布命令（打包文件并上传到服务器）
    $ code-push release-react <appName> <OS> <updateContents> <deploymentNmae> <description> <disabled> <mandatory>
    <appName> //必须 app名称
    <OS> //必须 发布平台iOS/Android
    <updateContents> //非必须 Bundle文件所在目录
    <targetBinaryVersion> //非必须 需要热更的app 版本
    <deploymentNmae> //必须 需要发布的部署
    <description> //非必须 描述 (更新客户端不可见必须有"hide"  eg: --description "hide xxxx")
    <disabled> //非必须 该版本客户端是否可以获得更新,默认为false
    <mandatory> //非必须  如果有则表示app强制更新
    
    code-push release-react ReactNativeCodePushDemo-ios ios -t "1.0.0" --des "测试热更 新" -d Staging
    

发布成功后，可以在文件夹中看到，等待被用户下载的bundle文件。

![](//upload-images.jianshu.io/upload_images/877439-53a2b9d4842eec41.png)

### 查看历史版本

    // code-push deployment history <应用名> Staging/Production
    code-push deployment history ReactNativeDemo-ios Staging
    

### 清空历史版本

    code-push deployment clear ReactNativeDemo-ios Staging
    

### 案例：

*   运行项目；
*   修改RN项目；
*   执行发布更新；
*   点击`Press for dialog-driven sync`按钮，弹框提示更新；
*   点击“后台更新”，即完成更新后刷新界面。

![](//upload-images.jianshu.io/upload_images/877439-3b7395c7ce012717.jpeg)

效果图

参考文章：  
[lisong的github](https://github.com/lisong/code-push-server)  
[Microsoft Code Push](https://github.com/Microsoft/code-push/blob/master/cli/README-cn.md#%E5%8F%91%E5%B8%83%E6%9B%B4%E6%96%B0-general)  
[Bloodline's Blog](http://ibloodline.com/articles/2017/10/20/code-push.html)  
[孜孜不倦的blog](https://blog.xgy666.cn/2017/03/21/using-code-push-to-build-rn-hot-update/)  
[花儿的爸爸](https://www.jianshu.com/p/eb7fdee307dc)  
[https://www.jianshu.com/p/ca4beb5973bb](https://www.jianshu.com/p/ca4beb5973bb)