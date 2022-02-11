原文链接：[https://zhuanlan.zhihu.com/p/35407642](https://zhuanlan.zhihu.com/p/35407642)

Webpack 4进阶--从前的日色变得慢 ，一下午只够打一次包
================================

> 从前的日色变得慢，车，马，邮件都慢，一生只够爱一个人 -- 《从前慢》

近期在团队项目里把Webpack升级到4.4.1，过程中发现现存的升级文档十分有限，踩了不少坑，好在升级之后提升还算显著，production场景下第三方依赖打包速度提升76%，development场景下本地服务首次启动提升效果约46%，再次启动提升效果上升至63%。这里将这次升级过程中的点滴分享出来，希望对大家有所帮助。

**理论部分**
--------

[Webpack 4](http://link.zhihu.com/?target=https%3A//medium.com/webpack/webpack-4-released-today-6cdb994702d4)发布之后，议论最多的两大特性，其一是零配置，其二是速度快（号称提速上限98%）。听起来十分美妙，在实地测试之前，首先从理论上分析一下可能性。

零配置
---

一言以蔽之，约定优于配置。通过mode属性将开发/生产（development/production）环境中常用的功能设置好默认值，用户即来即用。

打包速度快
-----

1\. Optimization

Webpack 4取消了四个常用的用于性能优化的plugin（UglifyjsWebpackPlugin，CommonsChunkPlugin，ModuleConcatenationPlugin，NoEmitOnErrorsPlugin），转而提供了一个名为optimization的配置项，用于接手以上四位的工作。

![](https://pic1.zhimg.com/v2-ee213b1ff1ceead751e2ea1cb27bb060_b.jpg)

![](https://pic1.zhimg.com/v2-ee213b1ff1ceead751e2ea1cb27bb060_b.jpg)

_注：UglifyjsWebpackPlugin并不执行tree shaking操作，这里为了介绍sideEffects，故而将关系紧密的两者放在一起介绍了_

*   Tree Shaking & Minimize

_废弃插件：UglifyjsWebpackPlugin_

_新增属性：sideEffects，minimize等_

**Tree shaking**一直是一个美丽而遥不可及的话题。影响tree shaking的根本原因在于side effects（副作用），其中最广为人知的一条side effect就是动态引入依赖的问题。

得益于ES6的模块化实现思路，所有的依赖必须位于文件顶部，静态引入（然而import（）的出现打破了这个规则），Webpack可以在绘制依赖图的时候进行静态分析，从而将真正被引用的exports添加到bundle文件中，减少打包体积。然而很多热度较高的第三方库为了考虑兼容性往往采用UMD实现，而其所支持的动态引入依赖的功能则导致真实的依赖图可能要到运行时才能确定，使得静态分析难以发挥真正威力，tree shaking采用了保守策略，导致我们发现没有被用到的方法依然出现在了bundle文件中。一个好消息是许多第三方库相继推出了es版，配合tree-shaking食用，口感更佳，这也是官方号称提速98%的重要前提之一（冷漠脸）。坏消息是ES6其实也提供import（）方法支持动态引入依赖，所以以下写法其实也是完全行的通的。。。还记得那些年我们追过的沈佳宜说过的话么，“人生本来就有很多事情是徒劳无功的啊”。

    if(Math.random() > 0.5) {
        import('./a.js').then(() => {
            ...
        })
    } else {
        import('./b.js').then(() => {
            ...
        })
    }
    

除此以外，为了防止用户不小心修改输出元素的属性，有些库会将最终的输出元素用Object.freeze方法包裹起来，这也属于side effects之一，同样也会对tree shaking产生影响。

回到Webpack 4，官方提供了sideEffects属性，通过将其设置为false，可以主动标识该类库中的文件只执行简单输出，并没有执行其他操作，可以放心shaking。除了可以减小bundle文件的体积，同时也能够提升打包速度。为了检查side effects，Webpack需要在打包的时候将所有的文件执行一遍。而在设置sideEffects之后，则可以跳过执行那些未被引用的文件，毕竟已经明确标识了“我是平民”。因此对于一些我们自己开发的库，设置sideEffects为false大有裨益。

**Minimize**属性就没啥可多说的了，混淆压缩文件。

*   Scope hoisting

_废弃插件：ModuleConcatenationPlugin_

_新增属性：concatenateModules_

    //开启前
    [
        /* 0 */
        function(module, exports, require) {
            var module_a = require(1)
            console.log(module_a['default'])
        }
        
        /* 1 */
        function(module, exports, require) {
            exports['default'] = 'module A'
        }
    ]
    
    //开启后
    [
        function(module, exports, require) {
            var module_a_defaultExport = 'module A'
            console.log(module_a_defaultExport)
        }
    ]
    

**concatenateModules**被开启之后，可以看出bundle文件中的函数声明变少了，因而可以带来的好处，其一，文件的体积比之前更小了，其二，运行代码时创建的函数作用域变少了，开销也随之变少了。不过scope hoisting的效果同样也依赖于静态分析，无奈命不由我。

*   Code splitting

_废弃插件：CommonsChunkPlugin_

_新增属性：splitChunks，runtimeChunk， occurrenceOrder等_

**splitChunks**在Webpack 4里取代了我们熟悉的CommonsChunkPlugin。谈到这一处改动的时候不知道你有没有发现其中的端倪，这是否意味着DllPlugin和CommonsChunkPlugin（splitChunks）可以共存了呢？

在Webpack 4之前，两者并不能一起使用，原因有二。

*   一个相对没那么重要的原因是DllPlugin服务的目标场景是develop环境，因为第三方依赖（输出文件暂称为vendors）的变更频率较低，故而在每次启动本地服务或者rebuild的时候将第三方依赖重新打包一次实际上是一种浪费。通过DllPlugin，将第三方依赖的打包过程从业务代码的打包过程中独立出来，可以大大缩短develop环境下的启动时间。同时通过设置hash值，也可以充分的利用浏览器对这部分文件的缓存，提升加载效率。而在对加载效率更为苛刻的production环境，DllPlugin打包出的文件则稍显笨重，很多重复的内容被多次打包进了bundle文件。在这种场景下，CommonsChunkPlugin被视为更好的选择，因为我们不需要为打包时间操心过多，加载效率是我们唯一需要关注的内容。所以在webpack的开发者看来，这两者如同“I have an apple，I have a pen，Ah~~ Apple pen”一样，实际上并不存在什么交集。
*   因此也引出了二者不兼容更为重要的第二个原因，[没人实现](http://link.zhihu.com/?target=https%3A//github.com/webpack/webpack/issues/5447)。

这块功能实际上通过CommonsChunkPlugin设置两个entry point也可以实现，一个作为业务代码的入口，一个作为vendors的入口。不过存在两个问题，第一个问题是，尽管vendors被单独设置了entry point，但是在每次启动本地服务的时候，尽管打包的结果不变，hash值不变，浏览器的缓存文件也被充分利用了，它的打包过程依然会执行，所以启动时间并不会缩短，第二个问题是，许多人在使用CommonsChunkPlugin的时候并没有注意到Webpack会将runtime一起打包进vendors文件，所以每次启动的时候，尽管你并没有修改任何第三方依赖，但是vendors文件的hash值却变了，导致浏览器缓存实际上并没有被利用起来。要解决这个问题，需要配置CommonsChunkPlugin将runtime单独打包成一个文件。

然而到了Webpack 4，在CommonsChunkPlugin变成splitChunks之后，出于某些未知的原因，两者兼容性的问题被解决了。。。Happy coding。

**runtimeChunk**之所以被单独设置为一个配置项，应该就是为了主动帮助用户避免上文所述的问题吧。

**occurrenceOrder**应用的场景是如果不手动设置chunk的名字，而采用默认值的话，Webpack将会用更短的名字去命名引用频度更高的chunk。

*   noEmitOnErrors

_废弃插件：NoEmitOnErrorsPlugin_

_新增属性：noEmitOnErrors_

**noEmitOnErrors**用于在编译出现错误时跳过输出阶段。

2\. New Plugin

Webpack 4同时实现了一套新的plugin机制，与性能相关的改进点是消除了对arguments的滥用。如同我们推崇开发时定义类型，从而可以避免JIT过程中产生过多的重载函数，以及降低重新编译的概率。

**实践部分**
--------

讲了这么多，最后分享一下我的实操经历。Webpack 4为用户描绘的场景固然美好，然而带来便利的同时也给开发者留下了不少麻烦。首当其冲的就是兼容性的问题，很多我们常用的loader，plugin尚未对这次升级做好准备，找到合适的替代工具以及积极改造自研的工具将成为升级过程中一场重要战役。接下来我会针对在这次项目升级中我所遇到的兼容性问题以及最终采用的解决方案做一个总结，常规的Webpack 4配置可以在[官方demo](http://link.zhihu.com/?target=https%3A//github.com/webpack/webpack/tree/master/examples)中找到答案。

1\. CommonsChunkPlugin + DllPlugin

Nothing special，主要还是一个分类问题，如何识别存在公共依赖的第三方依赖，并将其分配到不同的entry中。例如antd和react都依赖了react，则应该将两者分配到不同的entry中。以及如何均匀的分配依赖到不同的entry中，使得打包之后的每个entry大小相近。可以说十分考验一名配置工程师的功力和对源码库的了解程度。

2\. Ts-loader

因为awesome-typescript-loader（ATL）还没有合并支持Webpack 4的pr。所以ts-loader是ts爱好者们目前最好的选择。曾经ATL之所以能够战胜ts-loader，成为不少人的选择，原因有二，其一是ATL会新开一个独立的进程执行类型检查操作，因此不会影响编译时间，其二是ts的编译结果会被缓存，rebuild场景下可以提速。目前ts-loader也已经支持这两方面功能了，所以替换时并不需要担心。

    module: {
      rule: {
        test: /\.tsx?$/,
        use: [
          'cache-loader',
          {
            loader: 'thread-loader',
            options: {
              workers: require('os').cpus().length - 1,
            }
          },
          {
            loader: 'ts-loader',
            options: {
              happyPackMode: true,
              transpileOnly: true
            }
          }
        ]
      }
    }
    
    plugins: [
      new ForkTsCheckerWebpackPlugin()
    ]
    

*   ForkTsCheckerWebpackPlugin用于新建进程执行类型检查，为此你需要关闭ts-loader自身的类型检查功能，即设置transpileOnly为true。
*   thread-loader允许新建一个worker进程去分担一些昂贵的loader操作；cache-loader则可以将loader的运行结果缓存在本地。然而两者同时也会带来额外的开销（进程管理，I/O操作），自行评估后使用。

3\. MiniCssExtractPlugin

通过名字不难猜出它的功能，由于ExtractTextWebpackPlugin尚不支持Webpack 4，而且未来很可能被吸收为配置项，MiniCssExtractPlugin可以作为过渡期的一个选择。除了常规的css抽取合并功能外，它还会在合并时清理重复的css副本，而这也是ExtractTextWebpackPlugin尚未实现的功能，所以理论上css的打包效果更优。

4\. InlineChunkWebpackPlugin（Webpack 4尚未支持）

虽然Webpack 4尚未支持这个插件，但还是把它加在了这里，只是因为它确实有用。上文说到通过配置runtimeChunk为true，可以将运行时打包成独立的chunk，然而这个chunk体积很小，单独占用一个http请求稍显浪费，inline显然是更好的选择。InlineChunkWebpackPlugin可以帮助我们将指定的chunk通过inline的形式写入index.html文件。在Webpack 4尚不支持的情况下，只好在http和ctrl + a&ctrl + c&ctrl + v中选择一个更合适您口味的方法了。

5\. CleanWebpackPlugin

首先我要说明，这是一个玄学plugin，用或不用完全取决于脸黑不黑，手脏不脏。用处就是可以在打包前清理指定目录的文件，譬如说旧的bundle文件。开始我也不信，后来的结果你们也看到了。

* * *

最后秀一下数据吧

在展示最终结果之前需要声明的一点是，由于升级Webpack的同时，还解决了诸多兼容性问题，所以最终结果的表现无论优劣，都不仅仅是Webpack的功过，loader以及plugin替换带来的性能影响同样不可忽略。至于如何到达提速98%，如果所有依赖全部更新成为es版本的话。。。

1.  DllPlugin + CommonsChunkPlugin对第三方依赖打包场景（production场景）

Webpack 3.8.1的打包时长为57411ms，Webpack 4的打包时长为13959ms，提升效果约76%，详情如下图所示。

![](https://pic3.zhimg.com/v2-e975952399b4a5f92cadaee19225af36_b.jpg)

![](https://pic3.zhimg.com/v2-e975952399b4a5f92cadaee19225af36_b.jpg)

Webpack 3.8.1

![](https://pic2.zhimg.com/v2-73d66ba9ff8ed11844920f1e2299baa9_b.jpg)

![](https://pic2.zhimg.com/v2-73d66ba9ff8ed11844920f1e2299baa9_b.jpg)

Webpack 4.4.1

2\. 本地启动（development场景）

Webpack 3.8.1的启动时长（仅包含业务代码打包过程）为42890ms，Webpack 4的首次启动（cache文件尚未产生）时长为23017ms，Webpack 4的再次启动（cache文件已经存在，并非watch模式下的rebuild场景）时长为15827ms，首次启动提升效果约46%，再次启动提升效果上升至63%，详情如下图所示。

![](https://pic3.zhimg.com/v2-34d0ec4400b6d9e98bf2948954c07f02_b.jpg)

![](https://pic3.zhimg.com/v2-34d0ec4400b6d9e98bf2948954c07f02_b.jpg)

Webpack 3.8.1

![](https://pic4.zhimg.com/v2-586feb58f08e9f90439d71ccb119073b_b.jpg)

![](https://pic4.zhimg.com/v2-586feb58f08e9f90439d71ccb119073b_b.jpg)

Webpack 4.4.1（首次启动，无缓存）

![](https://pic3.zhimg.com/v2-f126d9f172b650ca37b9d4801c9038de_b.jpg)

![](https://pic3.zhimg.com/v2-f126d9f172b650ca37b9d4801c9038de_b.jpg)

Webpack 4.4.1（非首次启动，有缓存）

结束语
---

在不纠结究竟是Webpack还是替换loader&plugin的功劳，以及升级过程中遭遇的懵逼，躁郁，崩溃的情况下，这次升级还是为项目带来了正反馈。如果你也是一名追求极致开发体验的配置工程师的话，这次Webpack升级还是值得尝试的。最后希望文章中的内容能够有所帮助。