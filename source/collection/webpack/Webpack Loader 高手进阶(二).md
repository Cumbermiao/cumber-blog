原文链接：[https://segmentfault.com/a/1190000018458388](https://segmentfault.com/a/1190000018458388)

Webpack Loader 高手进阶(二)
======================


Webpack 系列文章：

[Webpack Loader 高手进阶（一）](https://segmentfault.com/a/1190000018450503)  
[Webpack Loader 高手进阶（二）](https://segmentfault.com/a/1190000018458388)  
[Webpack Loader 高手进阶（三）](https://segmentfault.com/a/1190000018600714)

* * *

Webpack Loader 详解
-----------------

上篇文章主要讲了 loader 的配置，匹配相关的机制。这篇主要会讲当一个 module 被创建之后，使用 loader 去处理这个 module 内容的流程机制。首先我们来总体的看下整个的流程：

![图片描述](https://segmentfault.com/img/bVbpB1T?w=1910&h=1036 "图片描述")

在 module 一开始构建的过程中，首先会创建一个 loaderContext 对象，它和这个 module 是一一对应的关系，而这个 module 所使用的所有 loaders 都会共享这个 loaderContext 对象，每个 loader 执行的时候上下文就是这个 loaderContext 对象，所以可以在我们写的 loader 里面通过 this 来访问。

    // NormalModule.js
    
    const { runLoaders } = require('loader-runner')
    
    class NormalModule extends Module {
      ...
      createLoaderContext(resolver, options, compilation, fs) {
        const requestShortener = compilation.runtimeTemplate.requestShortener;
        // 初始化 loaderContext 对象，这些初始字段的具体内容解释在文档上有具体的解释(https://webpack.docschina.org/api/loaders/#this-data)
            const loaderContext = {
                version: 2,
                emitWarning: warning => {...},
                emitError: error => {...},
                exec: (code, filename) => {...},
                resolve(context, request, callback) {...},
                getResolve(options) {...},
                emitFile: (name, content, sourceMap) => {...},
                rootContext: options.context, // 项目的根路径
                webpack: true,
                sourceMap: !!this.useSourceMap,
                _module: this,
                _compilation: compilation,
                _compiler: compilation.compiler,
                fs: fs
            };
    
        // 触发 normalModuleLoader 的钩子函数，开发者可以利用这个钩子来对 loaderContext 进行拓展
            compilation.hooks.normalModuleLoader.call(loaderContext, this);
            if (options.loader) {
                Object.assign(loaderContext, options.loader);
            }
    
            return loaderContext;
      }
    
      doBuild(options, compilation, resolver, fs, callback) {
        // 创建 loaderContext 上下文
            const loaderContext = this.createLoaderContext(
                resolver,
                options,
                compilation,
                fs
        )
        
        runLoaders(
          {
            resource: this.resource, // 这个模块的路径
                    loaders: this.loaders, // 模块所使用的 loaders
                    context: loaderContext, // loaderContext 上下文
                    readResource: fs.readFile.bind(fs) // 读取文件的 node api
          },
          (err, result) => {
            // do something
          }
        )
      }
      ...
    }

当 loaderContext 初始化完成后，开始调用 runLoaders 方法，这个时候进入到了 loaders 的执行阶段。runLoaders 方法是由[loader-runner](https://github.com/webpack/loader-runner)这个独立的 npm 包提供的方法，那我们就一起来看下 runLoaders 方法内部是如何运行的。

首先根据传入的参数完成进一步的处理，同时对于 loaderContext 对象上的属性做进一步的拓展：

    exports.runLoaders = function runLoaders(options, callback) {
      // read options
        var resource = options.resource || ""; // 模块的路径
        var loaders = options.loaders || []; // 模块所需要使用的 loaders
        var loaderContext = options.context || {}; // 在 normalModule 里面创建的 loaderContext
        var readResource = options.readResource || readFile;
    
        var splittedResource = resource && splitQuery(resource);
        var resourcePath = splittedResource ? splittedResource[0] : undefined; // 模块实际路径
        var resourceQuery = splittedResource ? splittedResource[1] : undefined; // 模块路径 query 参数
        var contextDirectory = resourcePath ? dirname(resourcePath) : null; // 模块的父路径
    
        // execution state
        var requestCacheable = true;
        var fileDependencies = [];
        var contextDependencies = [];
    
        // prepare loader objects
        loaders = loaders.map(createLoaderObject); // 处理 loaders 
    
      // 拓展 loaderContext 的属性
        loaderContext.context = contextDirectory;
        loaderContext.loaderIndex = 0; // 当前正在执行的 loader 索引
        loaderContext.loaders = loaders;
        loaderContext.resourcePath = resourcePath;
        loaderContext.resourceQuery = resourceQuery;
        loaderContext.async = null; // 异步 loader
      loaderContext.callback = null;
    
      ...
    
      // 需要被构建的模块路径，将 loaderContext.resource -> getter/setter
      // 例如 /abc/resource.js?rrr
      Object.defineProperty(loaderContext, "resource", {
            enumerable: true,
            get: function() {
                if(loaderContext.resourcePath === undefined)
                    return undefined;
                return loaderContext.resourcePath + loaderContext.resourceQuery;
            },
            set: function(value) {
                var splittedResource = value && splitQuery(value);
                loaderContext.resourcePath = splittedResource ? splittedResource[0] : undefined;
                loaderContext.resourceQuery = splittedResource ? splittedResource[1] : undefined;
            }
      });
    
      // 构建这个 module 所有的 loader 及这个模块的 resouce 所组成的 request 字符串
      // 例如：/abc/loader1.js?xyz!/abc/node_modules/loader2/index.js!/abc/resource.js?rrr
        Object.defineProperty(loaderContext, "request", {
            enumerable: true,
            get: function() {
                return loaderContext.loaders.map(function(o) {
                    return o.request;
                }).concat(loaderContext.resource || "").join("!");
            }
      });
      // 在执行 loader 提供的 pitch 函数阶段传入的参数之一，剩下还未被调用的 loader.pitch 所组成的 request 字符串
        Object.defineProperty(loaderContext, "remainingRequest", {
            enumerable: true,
            get: function() {
                if(loaderContext.loaderIndex >= loaderContext.loaders.length - 1 && !loaderContext.resource)
                    return "";
                return loaderContext.loaders.slice(loaderContext.loaderIndex + 1).map(function(o) {
                    return o.request;
                }).concat(loaderContext.resource || "").join("!");
            }
      });
      // 在执行 loader 提供的 pitch 函数阶段传入的参数之一，包含当前 loader.pitch 所组成的 request 字符串
        Object.defineProperty(loaderContext, "currentRequest", {
            enumerable: true,
            get: function() {
                return loaderContext.loaders.slice(loaderContext.loaderIndex).map(function(o) {
                    return o.request;
                }).concat(loaderContext.resource || "").join("!");
            }
      });
      // 在执行 loader 提供的 pitch 函数阶段传入的参数之一，包含已经被执行的 loader.pitch 所组成的 request 字符串
        Object.defineProperty(loaderContext, "previousRequest", {
            enumerable: true,
            get: function() {
                return loaderContext.loaders.slice(0, loaderContext.loaderIndex).map(function(o) {
                    return o.request;
                }).join("!");
            }
      });
      // 获取当前正在执行的 loader 的query参数
      // 如果这个 loader 配置了 options 对象的话，this.query 就指向这个 option 对象
      // 如果 loader 中没有 options，而是以 query 字符串作为参数调用时，this.query 就是一个以 ? 开头的字符串
        Object.defineProperty(loaderContext, "query", {
            enumerable: true,
            get: function() {
                var entry = loaderContext.loaders[loaderContext.loaderIndex];
                return entry.options && typeof entry.options === "object" ? entry.options : entry.query;
            }
      });
      // 每个 loader 在 pitch 阶段和正常执行阶段都可以共享的 data 数据
        Object.defineProperty(loaderContext, "data", {
            enumerable: true,
            get: function() {
                return loaderContext.loaders[loaderContext.loaderIndex].data;
            }
      });
      
      var processOptions = {
            resourceBuffer: null, // module 的内容 buffer
            readResource: readResource
      };
      // 开始执行每个 loader 上的 pitch 函数
        iteratePitchingLoaders(processOptions, loaderContext, function(err, result) {
        // do something...
      });
    }

这里稍微总结下就是在 runLoaders 方法的初期会对相关参数进行初始化的操作，特别是将 loaderContext 上的部分属性改写为 getter/setter 函数，这样在不同的 loader 执行的阶段可以动态的获取一些参数。

接下来开始调用 iteratePitchingLoaders 方法执行每个 loader 上提供的 pitch 函数。大家写过 loader 的话应该都清楚，每个 loader 可以挂载一个 pitch 函数，每个 loader 提供的 pitch 方法和 loader 实际的执行顺序正好相反。这块的内容在 webpack 文档上也有详细的说明([请戳我](https://webpack.docschina.org/api/loaders/#%E8%B6%8A%E8%BF%87-loader-pitching-loader-))。

这些 pitch 函数并不是用来实际处理 module 的内容的，主要是可以利用 module 的 request，来做一些拦截处理的工作，从而达到在 loader 处理流程当中的一些定制化的处理需要，有关 pitch 函数具体的实战可以参见下一篇文档\[Webpack 高手进阶-loader 实战\] TODO: 链接

    function iteratePitchingLoaders() {
      // abort after last loader
        if(loaderContext.loaderIndex >= loaderContext.loaders.length)
            return processResource(options, loaderContext, callback);
    
      // 根据 loaderIndex 来获取当前需要执行的 loader
        var currentLoaderObject = loaderContext.loaders[loaderContext.loaderIndex];
    
      // iterate
      // 如果被执行过，那么直接跳过这个 loader 的 pitch 函数
        if(currentLoaderObject.pitchExecuted) {
            loaderContext.loaderIndex++;
            return iteratePitchingLoaders(options, loaderContext, callback);
        }
    
        // 加载 loader 模块
        // load loader module
        loadLoader(currentLoaderObject, function(err) {
            // do something ...
        });
    }

每次执行 pitch 函数前，首先根据 loaderIndex 来获取当前需要执行的 loader (currentLoaderObject)，调用 loadLoader 函数来加载这个 loader，loadLoader 内部兼容了 SystemJS，ES Module，CommonJs 这些模块定义，最终会将 loader 提供的 pitch 方法和普通方法赋值到 currentLoaderObject 上：

    // loadLoader.js
    module.exports = function (loader, callback) {
      ...
      var module = require(loader.path)
     
      ...
      loader.normal = module
    
      loader.pitch = module.pitch
    
      loader.raw = module.raw
    
      callback()
      ...
    }

当 loader 加载完后，开始执行 loadLoader 的回调：

    loadLoader(currentLoaderObject, function(err) {
      var fn = currentLoaderObject.pitch; // 获取 pitch 函数
      currentLoaderObject.pitchExecuted = true;
      if(!fn) return iteratePitchingLoaders(options, loaderContext, callback); // 如果这个 loader 没有提供 pitch 函数，那么直接跳过
    
      // 开始执行 pitch 函数
      runSyncOrAsync(
        fn,
        loaderContext, [loaderContext.remainingRequest, loaderContext.previousRequest, currentLoaderObject.data = {}],
        function(err) {
          if(err) return callback(err);
          var args = Array.prototype.slice.call(arguments, 1);
          // Determine whether to continue the pitching process based on
          // argument values (as opposed to argument presence) in order
          // to support synchronous and asynchronous usages.
          // 根据是否有参数返回来判断是否向下继续进行 pitch 函数的执行
          var hasArg = args.some(function(value) {
            return value !== undefined;
          });
          if(hasArg) {
            loaderContext.loaderIndex--;
            iterateNormalLoaders(options, loaderContext, args, callback);
          } else {
            iteratePitchingLoaders(options, loaderContext, callback);
          }
        }
      );
    })

这里出现了一个 runSyncOrAsync 方法，放到后文去讲，开始执行 pitch 函数，当 pitch 函数执行完后，执行传入的回调函数。我们看到回调函数里面会判断接收到的参数的个数，除了第一个 err 参数外，如果还有其他的参数(这些参数是 pitch 函数执行完后传入回调函数的)，那么会直接进入 loader 的 normal 方法执行阶段，并且会直接跳过后面的 loader 执行阶段。如果 pitch 函数没有返回值的话，那么进入到下一个 loader 的 pitch 函数的执行阶段。让我们再回到 iteratePitchingLoaders 方法内部，当所有 loader 上面的 pitch 函数都执行完后，即 loaderIndex 索引值 >= loader 数组长度的时候：

    function iteratePitchingLoaders () {
      ...
    
      if(loaderContext.loaderIndex >= loaderContext.loaders.length)
        return processResource(options, loaderContext, callback);
    
      ...
    }
    
    function processResource(options, loaderContext, callback) {
        // set loader index to last loader
        loaderContext.loaderIndex = loaderContext.loaders.length - 1;
    
        var resourcePath = loaderContext.resourcePath;
        if(resourcePath) {
            loaderContext.addDependency(resourcePath); // 添加依赖
            options.readResource(resourcePath, function(err, buffer) {
                if(err) return callback(err);
                options.resourceBuffer = buffer;
                iterateNormalLoaders(options, loaderContext, [buffer], callback);
            });
        } else {
            iterateNormalLoaders(options, loaderContext, [null], callback);
        }
    }

在 processResouce 方法内部调用 node API readResouce 读取 module 对应路径的文本内容，调用 iterateNormalLoaders 方法，开始进入 loader normal 方法的执行阶段。

    function iterateNormalLoaders () {
      if(loaderContext.loaderIndex < 0)
            return callback(null, args);
    
        var currentLoaderObject = loaderContext.loaders[loaderContext.loaderIndex];
    
        // iterate
        if(currentLoaderObject.normalExecuted) {
            loaderContext.loaderIndex--;
            return iterateNormalLoaders(options, loaderContext, args, callback);
        }
    
        var fn = currentLoaderObject.normal;
        currentLoaderObject.normalExecuted = true;
        if(!fn) {
            return iterateNormalLoaders(options, loaderContext, args, callback);
        }
    
      // buffer 和 utf8 string 之间的转化
        convertArgs(args, currentLoaderObject.raw);
    
        runSyncOrAsync(fn, loaderContext, args, function(err) {
            if(err) return callback(err);
    
            var args = Array.prototype.slice.call(arguments, 1);
            iterateNormalLoaders(options, loaderContext, args, callback);
        });
    }

在 iterateNormalLoaders 方法内部就是依照从右到左的顺序(正好与 pitch 方法执行顺序相反)依次执行每个 loader 上的 normal 方法。loader 不管是 pitch 方法还是 normal 方法的执行可为同步的，也可设为异步的(这里说下 normal 方法的)。一般如果你写的 loader 里面可能涉及到计算量较大的情况时，可将你的 loader 异步化，在你 loader 方法里面调用`this.async`方法，返回异步的回调函数，当你 loader 内部实际的内容执行完后，可调用这个异步的回调来进入下一个 loader 的执行。

    module.exports = function (content) {
      const callback = this.async()
      someAsyncOperation(content, function(err, result) {
        if (err) return callback(err);
        callback(null, result);
      });
    }

除了调用 this.async 来异步化 loader 之外，还有一种方式就是在你的 loader 里面去返回一个 promise，只有当这个 promise 被 resolve 之后，才会调用下一个 loader(具体实现机制见下文)：

    module.exports = function (content) {
      return new Promise(resolve => {
        someAsyncOpertion(content, function(err, result) {
          if (err) resolve(err)
          resolve(null, result)
        })
      })
    }

这里还有一个地方需要注意的就是，上下游 loader 之间的数据传递过程中，如果下游的 loader 接收到的参数为一个，那么可以在上一个 loader 执行结束后，如果是同步就直接 return 出去：

    module.exports = function (content) {
      // do something
      return content
    }

如果是异步就直接调用异步回调传递下去(参见上面 loader 异步化)。如果下游 loader 接收的参数多于一个，那么上一个 loader 执行结束后，如果是同步那么就需要调用 loaderContext 提供的 callback 函数:

    module.exports = function (content) {
      // do something
      this.callback(null, content, argA, argB)
    }

如果是异步的还是继续调用异步回调函数传递下去(参见上面 loader 异步化)。具体的执行机制涉及到上文还没讲到的 runSyncOrAsync 方法，它提供了上下游 loader 调用的接口：

    function runSyncOrAsync(fn, context, args, callback) {
        var isSync = true; // 是否为同步
        var isDone = false;
        var isError = false; // internal error
        var reportedError = false;
        // 给 loaderContext 上下文赋值 async 函数，用以将 loader 异步化，并返回异步回调
        context.async = function async() {
            if(isDone) {
                if(reportedError) return; // ignore
                throw new Error("async(): The callback was already called.");
            }
            isSync = false; // 同步标志位置为 false
            return innerCallback;
      };
      // callback 的形式可以向下一个 loader 多个参数
        var innerCallback = context.callback = function() {
            if(isDone) {
                if(reportedError) return; // ignore
                throw new Error("callback(): The callback was already called.");
            }
            isDone = true;
            isSync = false;
            try {
                callback.apply(null, arguments);
            } catch(e) {
                isError = true;
                throw e;
            }
        };
        try {
            // 开始执行 loader
            var result = (function LOADER_EXECUTION() {
                return fn.apply(context, args);
        }());
        // 如果为同步的执行
            if(isSync) {
          isDone = true;
          // 如果 loader 执行后没有返回值，执行 callback 开始下一个 loader 执行
                if(result === undefined)
            return callback();
          // loader 返回值为一个 promise 实例，待这个实例被resolve或者reject后执行下一个 loader。这也是 loader 异步化的一种方式
                if(result && typeof result === "object" && typeof result.then === "function") {
                    return result.catch(callback).then(function(r) {
                        callback(null, r);
                    });
          }
          // 如果 loader 执行后有返回值，执行 callback 开始下一个 loader 执行
                return callback(null, result);
            }
        } catch(e) {
            // do something
        }
    }

以上就是对于 module 在构建过程中 loader 执行流程的源码分析。可能平时在使用 webpack 过程了解相关的 loader 执行规则和策略，再配合这篇对于内部机制的分析，应该会对 webpack loader 的使用有更加深刻的印象。

**文章首发于个人github blog: [Biu-blog](https://github.com/CommanderXL/Biu-blog)，欢迎大家关注~**