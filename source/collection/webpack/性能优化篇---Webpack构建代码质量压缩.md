原文链接：[https://segmentfault.com/a/1190000018644992?utm\_source=tag-newest](https://segmentfault.com/a/1190000018644992?utm_source=tag-newest)

性能优化篇---Webpack构建代码质量压缩
=======================

*   [Webpack构建速度优化](https://segmentfault.com/a/1190000018493260)基本优化完毕，接下来考虑的就是：线上代码质量的优化，即如何使用webpack构建出高质量的代码
*   **Webpack构建流程：**初始化配置参数 -> 绑定事件钩子回调 -> 确定Entry逐一遍历 -> 使用loader编译文件 -> 输出文件

### 提纲

*   本次优化构建代码质量基本技术：

1.  reactRouter按需加载；
2.  公共代码提取，以及代码压缩；
3.  CDN接入；
4.  开启gzip压缩；
5.  接入treeShaking，剔除无用代码
6.  开启Scope Hoisting

*   **（生产环境代码构建）为实时查看每次配置后代码构建情况，使用Webpack监听文件避免每次手动build，并且开启webpack-jarvis，实时查看构建分析，**`npm i -D webpack-jarvis`。
*   开启监听模式

    watch: true,
    watchOptions: {
        ignored: /node_modules/, // 忽略监听文件
        aggregateTimeout: 300,  //文件变动后多久发起构建
        poll: 1000,  //每秒询问次数，越小越好
    }

### 一、react-router4实现按需加载

*   单页应用按需加载一般原则：
    
    1.  将网站划分成一个个小功能，在按照每个功能的相关度将他们分成几个类；
    2.  将没一个类合并成一个chunk，按需加载对应的代码；
    3.  不可将用户首次进入网站时需要看到画面的对应功能Chunk按需加载；
*   被分割出去的代码的加载需要一定的触发时机，即当用户操作了或者即将操作对应功能时再去加载对应的代码（默认使用`react-router`按需加载的触发条件是路由的变化）
*   **实现条件：**
    
    1.  使用插件：`npm i react-loadable`;
    2.  配合bable插件`npm i @babel/plugin-syntax-dynamic-import`;
*   代码示例：

    // .bablerc
    {
      "plugins": ["@babel/plugin-syntax-dynamic-import"]
    }
    
    // 示例代码
    Loadable({
      loader: () => import('./component'), //按需加载组件
      loading: Loading, //处理组件加载的loading、error等
      delay: 300  //延迟加载避免loading的闪烁问题
    });
    
    // Loading组件自定义
    // 接受三个props，其中pastDelay：等待时触发；timedOut：超时时触发超过delay；error：出错触发默认为200ms
    const Loading = ({ pastDelay, timedOut, error }) => {
        if (pastDelay) {
          return <Spin spinning tip="Loadding..." ><div style={{height: 300}} /></Spin>
        } else if (timedOut) {
          return <Spin spinning tip="Taking a long time..." ><div style={{height: 300}} /></Spin>
        } else if (error) {
          return <div>Error!</div>;
        }
        return null;
    };

* * *

### 二、提取公共代码`webpack.optimization`

    optimization: {
        splitChunks: {
          chunks: "all",
          cacheGroups: {
            vendors: { 
                test: /node_modules/,
                name: 'vendors', 
                minSize: 0,
                minChunks: 1, 
                chunks: 'initial',
                priority: 2 // 该配置项是设置处理的优先级，数值越大越优先处理 
            },
            commons: {
              name: "comomns",
              test: resolve("src/components"), // 可自定义拓展规则
              minChunks: 2, // 最小共用次数
              minSize:0,   //代码最小多大，进行抽离
              priority: 1, //该配置项是设置处理的优先级，数值越大越优先处理 
            }
        }
    }
    

* * *

### 三、压缩文件`js\css`

*   使用`npm i -D webpack-parallel-uglify-plugin`启用多线程并行压缩JS

    optimization: {
        minimizer: [
            new ParallelUglifyPlugin({
                cacheDir: '.cache/', //缓存压缩，默认不缓存，设置存放位置开启
                test: /.js$/, //匹配需要压缩的文件，默认为/.js$/和Loader配置一样
                //include: [], 使用正则去选择需要被压缩的文件和Loader配置一样
                //exclude: [], 使用正则去去除不需要被压缩的文件和Loader配置一样
                //workerCount: 2, 开启几个子进程并发执行压缩
                // sourceMap: false, 是否输出source Map，开启会导致压缩变慢
                // uglifyJS: {}, 用于压缩ES6代码不可和uglifyJS同时使用
                uglifyJS:{//压缩ES5代码
                    output: {
                        // 是否输出可读性较强的代码，即会保留空格和制表符，默认为输出，为了达到更好的压缩效果，可以设置为false
                        beautify: false,
                        //是否保留代码中的注释，默认为保留，为了达到更好的压缩效果，可以设置为false
                        comments: false
                    },
                    compress: {
                        //是否在UglifyJS删除没有用到的代码时输出警告信息，默认为输出
                        warnings: false,
                        //是否删除代码中所有的console语句，默认为不删除，开启后，会删除所有的console语句
                        drop_console: true,
                        //是否内嵌虽然已经定义了，但是只用到一次的变量，比如将 var x = 1; y = x, 转换成 y = 1, 默认为否
                        collapse_vars: true,
                        // 提取出现多次但是没有定义成变量去引用的静态值
                        reduce_vars:true
                    }
                },
            }),
        ]
    },

*   **提取和压缩Css**
    
    1.  使用插件：`optimize-css-assets-webpack-plugin`、`mini-css-extract-plugin`
    2.  使用示例：

    // 提取css到单独的文件
    const MiniCssExtractPlugin = require("mini-css-extract-plugin");
    // optimizeCssPlugin CSS文件压缩插件
    const optimizeCssPlugin = require('optimize-css-assets-webpack-plugin');
    
    const extractSCSS = new MiniCssExtractPlugin({
        filename: 'css/[name].[contenthash:8].css',
        chunkFilename: 'css/[name]_[contenthash:8].css',
        fallback:'style-loader'
    });
    ...
    ...
    plugins: [
        new optimizeCssPlugin({
            assetNameRegExp: /\.css$/g,
            cssProcessor: require('cssnano'),
            cssProcessorOptions: { discardComments: { removeAll: true } },
            canPrint: true
        }),
    ]

* * *

### webpack配置接入CDN

*   **CDN**
*   网站接入CDN，需要将网页的静态资源上传到CDN服务器，使用CDN地址访问；

1.  使用CDN可以决解资源并行下载限制，处理静态资源Cookie同域名携带等问题；
2.  CDN缓存和回源需要合理的设置静态资源hash
3.  接入CDN会引入多个域名，增加域名解析时间，可进行预解析域名`<link rel="dns-prefetch" href="//js.dns.com" />`

*   **webpack实现接入**

1.  `output.publicPath`设置JavaScript地址
2.  `css-loader.publicPath`设置CSS导入的资源地址
3.  `WebPlugin.stylePublicPath`中设置Css文件地址

    // JavaScript
    output: {
        publicPath: '//js.cdn.com/js/',
        path: path.join(__dirname, '../docs/dist'), // 打包后的文件存放的地方
        // 为输出的JavaScript文件名加上Hash值使用`chunkhash`（chunkhash：根据模块内容变化；hash: 根据每次构建随机）
        filename: "js/[name].[chunkhash:8].js",
        chunkFilename: "js/[name]-[id].[chunkhash:8].js",
    },
    

* * *

### 开启gzip压缩

*   使用插件：`npm i -D compression-webpack-plugin`;
*   webpack配置

    const CompressionPlugin = require("compression-webpack-plugin");
    
    plugins: [
        new CompressionPlugin({
            filename: '[path].gz[query]', //目标资源名称。[file] 会被替换成原资源。[path] 会被替换成原资源路径，[query] 替换成原查询字符串
            algorithm: 'gzip',//算法
            test: /\.(js|css)$/,    //压缩 js 与 css
            threshold: 10240,//只处理比这个值大的资源。按字节计算
            minRatio: 0.8//只有压缩率比这个值小的资源才会被处理
        })
    ]

*   后台开启使用`koa`

    const staticCache = require('koa-static-cache');
    import config from './configs';
    
    const app = new Koa();
    
    app.use(staticCache(path.resolve(__dirname, "../dist"), {
        maxAge: 7 * 24 * 60 * 60,
        gzip: true, //开启
        dynamic: true,
    }))

* * *

### 接入treeShaking，剔除无用代码

*   `Tree Shaking`可以用来找出有用代码，去除JavaScript中用不上的死代码；但是它依赖于ES6静态花模块语法`import\export的导入和导出`
*   webpack接入

1.  修改`.babelrc`保留ES6模块话语句

*   注意新版本`babel-preset-env`已经预设`babel-preset-es2015`,babel推荐使用`babel-preset-env`取代`babel-preset-es2015`，并且继续使用`babel-preset-es2015`会发出警告信息。

    {
      "presets": [
        ["env", {
          "modules": false
        }]
      ],
      "plugins": ["syntax-dynamic-import"]
    }

![clipboard.png](https://segmentfault.com/img/bVbqoq4?w=2250&h=1214 "clipboard.png")

*   `webpack --display-used-exports`运行构建带上`--display-used-exports`可追踪到Tree Shaking的工作；
*   Webpack只能正确的分析出如何剔除死代码，需要接入UglifyJs处理剔除（配置见上）

* * *

### 开启Scope Hoistion

*   `scope hoisting`即作用域提升;
*   在构建过程中，webpack会借助ES6 模块化的静态特性，确定模块的依赖关系，将一个bundle中的静态依赖提升到顶部。（**所以需要和接入treeShaking一样配置Babel开启ES6模块化**）
*   **原理：**分析模块间的依赖关系，尽可能的将零散的模块合并到一个函数中去，前提不能造成代码冗余，因此只有被引用了一次的模块才能被合并。
*   接入好处：
    
    1.  代码体积减少
    2.  代码在运行时因为创建的函数作用域更少了，内存开销也随之变小
*   `webpack`接入`ModuleConcatenationPlugin`内置插件

    const ModuleConcatPlugin = require('webpack/lib/optimize/ModuleConcatenationPlugin');
    plugins: [
         new ModuleConcatPlugin(), //开启scope Hoisting
     ],

> **“积跬步、行千里”**—— 持续更新中~，喜欢的话留下个赞和关注哦！

*   往期经典好文：
    
    *   [你不知道的CORS跨域资源共享](https://segmentfault.com/a/1190000018464348)
    *   [性能优化篇---Webpack构建速度优化](https://segmentfault.com/a/1190000018493260)
    *   [React组件库封装初探--Modal](https://segmentfault.com/a/1190000018626140)
    *   [使用pm2部署node生产环境](https://segmentfault.com/a/1190000018439311)