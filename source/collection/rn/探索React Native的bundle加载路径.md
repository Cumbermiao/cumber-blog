原文链接：[https://www.jianshu.com/p/7c020c3e360a](https://www.jianshu.com/p/7c020c3e360a)

探索React Native的bundle加载路径
=========================

 [![96](//upload.jianshu.io/users/upload_avatars/548793/0ff4456d-450a-40ea-b4ba-ceb0ee51b732.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96)](/u/b92ab7b3ab48) 

[羽纱](/u/b92ab7b3ab48)

0.3 2017.04.01 18:06\* 字数 1229

我们都知道在Debug模式下，会去加载JS Server服务的bundle。在Release模式下会去加载本地的bundle，原生中是如果确定bundle的加载路径的？什么时候会在Debug模式下加载本地的bundle？在下文中会分别对iOS和Android原生代码的中如何确定bundle加载路径进行分析。

### iOS的bundle加载路径确认以及遇到的飘红(RCTRedBox)

* * *

**第一步：packageHost的确定**  
在Debug模式下会去`guessPackagerHost`，没错，就是去猜packageHost，猜的步骤如下：

*   查询是否有ip.txt文件在bundle中，有的话就把ip.txt的内容读出来，没有的话就是用localhost。
*   然后是用host和port拼成`http://localhost:8081/status`发送请求，如果返回的是`packager-status:running`.代表packagerHost是可用的，会返回这个host，否则返回null。

在Rlease模式下如果没有设置host在偏好设置中，则会返回null。

**第二步：bundle路径（jsCodeLocation）的确定**  
如果第一步的host不为null，则会拼成：`http://localhost:8081/index.ios.bundle?platform=ios&dev=true&minify=false`的url。如果第一步的host为空，则会去加载mainBundle（可以简略理解成本地的）中的main.jsbundle。

> 注意：如果本地没有main.jsbundle则会返回null，这个会导致后面飘红。

**第三步：bridge去加载jsCodeLocation**  
前话：bundleURL不存在或加载过程中失败的飘红都是从这里出来的。

    可以在这里打断点：
    RCTBatchedBridge.m->(void)stopLoadingWithError:
    

1、第一个遇到的错误：

    //RCTBatchBridge.m -> (void)loadSource:_onSourceLoad:onProgress
    bundleURL must be non-nil when not implementing loadSourceForBridge
    

如果飘了这个错误，证明bundleURL为空，这是就是第二步注意事项提到的，这时可以确定的是它连不上JS Server才导致它试图去找本地的main.jsbundle，但是很遗憾，本地也没找到，那么可以按以下步骤检查：

*   是否开了JS Server
*   是否科学上网了。
*   是否在同一个局域网内。
*   是否需要指定host（通过ip.txt），而不能使用localhost。
*   或者你指定了ip（通过ip.txt），但是却重启路由之类的，导致ip改了。

2、JS端的堆栈信息打印  
会把文件名，方法名，行号打印出来，最后会把错误描述在最上面。  
遇到这种错误是因为JS Server检查出了js方的错误，并返回了错误信息。此时对着错误调就好了，比较简单，调好后再reload。

![](//upload-images.jianshu.io/upload_images/548793-929bf909c25b564f.png)

Paste\_Image.png

3、网络错误，如果确定网络正常，并且第1个错误的检查点都检查了，可以多刷几次。因为可能是超时，或偶尔出现的500。

    Could not connect to development server
    Ensure the following
    - Node server is running and available on the same network - run 'npm start' from react-native root
    - Node server URL is correctly set in AppDelegate
    
    URL: *****
    

![](//upload-images.jianshu.io/upload_images/548793-12c28dab54510a6d.png)

Paste\_Image.png

4、Remote Debug JS 连接不上的错误  
这种错误会报到RCTWebSocketExecutor上去，很有可能是在Debug模式下开了Remote Debug JS，却没开JS Server，然后加了到了本地的bundle。这时候需要打开JS Server，然后重启应用，关闭Remote Debug JS即可。

    Connection to http://localhost:8081/debugger-proxy?role=client timed out.Are you running...
    

![](//upload-images.jianshu.io/upload_images/548793-fb0947a173ed66a4.png)

Paste\_Image.png

**总结**  
由上可以知道在Debug模式下，iOS会先会尝试连接它自己guess的host（使用ip.txt指定了，不然默认为localhost），如果没有获取到则会去尝试加载本地的main.jsbundle，当本地也没有bundle时就会报错了（本地的main.jsbundle何时存在可以参考这篇文章：[探索react-native run-ios(android)](https://www.jianshu.com/p/088be846270d)）。在Release模式下是不会去猜host的，直接去加载本地的jsbundle。

### Android的bundle加载路径确认以及遇到的飘红

* * *

Android的bundle路径尝试方式与iOS完全相反，它的大致思路是无论是Release还是Debug模式下都尝试去加载本地的index.android.bundle，当在Debug模式下加载不到时会直接飘红，然后我们需要摇出开发者选项设置ip和端口，Reload后才能去加载JS Server的bundle。

因为Android的自动打包bundle与iOS不一样（可以参考这篇文章：[探索react-native run-ios(android)](https://www.jianshu.com/p/088be846270d)），所以如果想在Debug模式下做原生开发，又不想开JS Server时，可以打个bundle放到assets下，这样它就不会去加载JS Server中的去了。

### 在项目中的应用

* * *

1、在混合开发时，可以给iOS和Android原生都打bundle包，这样原生就算在Debug模式下不开JS Server也不会有问题。不过这时js开发必须把Android中的index.android.bundle删了才能加载JS Server中的内容。  
2、ios可以在项目中加入一个ip.txt文件（指定JS Server）然后再重编来重定向JS Server。