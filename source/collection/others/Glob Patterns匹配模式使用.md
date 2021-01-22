原文链接：[https://juejin.im/post/5c2797f8e51d45176760e2cf](https://juejin.im/post/5c2797f8e51d45176760e2cf)

[

](/user/57dfea705bbb50005e7005e8)

[快乐平方](/user/57dfea705bbb50005e7005e8)[![lv-2](https://b-gold-cdn.xitu.io/v3/static/img/lv-2.f597b88.svg)](/book/5c90640c5188252d7941f5bb/section/5c9065385188252da6320022)

2018年12月29日阅读 1008

关注

Glob Patterns匹配模式使用
===================

前段时间在用workbox时，在做precache时，匹配模式基于的是Glob Pattern模式，于是就看了下相关文档。

下面翻译一下node-glob的使用，原文：[github.com/isaacs/node…](https://link.juejin.im?target=https%3A%2F%2Fgithub.com%2Fisaacs%2Fnode-glob%23glob-primer)

* * *

Glob
----

像在shell里面，用`*`等匹配模式来匹配文件。

Glob基于Javascript实现，使用`minimatch`库进行匹配。

![](https://user-gold-cdn.xitu.io/2018/12/29/167faa9b9ea0aced?imageslim)

### 使用

使用`npm`安装

    npm i glob
    复制代码

    var glob = require("glob")
    
    // options可选
    glob("**/*.js", options, function (er, files) {
      // files是匹配到的文件数组
      // 如果`options`中的`nonull`为true, 当未发现时， files返回["**/*.js"]
      // `er`是一个错误对象或者null
    })
    复制代码

### Glob 入门

"Glob"是一种模式，类似于在命令行中输入`ls *.js`，或是在`.gitignore`文件中写`build/*`。

在解析路径模式时，大括号内使用逗号进行分隔，分隔部分可以包含`/`，所以`a{/b/c,bcd}`会被展开为`a/b/c`和`abcd`。

在匹配路径使用时，以下字符有一些特殊的作用：

*   `*`：匹配单路径下的 0 个或 多个 字符串。
*   `?`：匹配一个字符串。
*   `[...]`：匹配指定范围内的字符串，类似于正则表达式中的`[]`。如果`[]`中的第一个字符串是`!`或者`^`，则匹配不在范围内的任意字符串。
*   `!(pattern|pattern|pattern)`：匹配与提供模式中不匹配的内容。
*   `?(pattern|pattern|pattern)`：匹配提供模式中的 0次 或 1次。
*   `+(pattern|pattern|pattern)`：匹配提供模式中的 1次 或 多次。
*   `*(a|b|c)`：匹配提供模式中的 0次 或 多次。
*   `@(pattern|pat*|pat?erN)`：匹配与提供模式中完全匹配的。
*   `**`：和`*`一样，可以匹配路径中的 0个 或 多个，而且`**`可以匹配当前目录和子目录。但无法抓去符号链接的目录。

#### `.`点

如果匹配的文件或目录部分以`.`开头，除非匹配的路径也以`.`开头，否则不会匹配任何模式。

例如 `a/.*/c`模式会匹配`a/.b/c`。然而`a/*/c`模式却不会进行匹配，因为`*`并不是以`.`开始的。

你可以通过设置option `dot:true`，让Glob把`.`做为正常字符串。

#### Basename的匹配

如果你在 option中设置`matchBase:true`，当模式中没有`/`，它会在任意目录下去匹配。例如`*.js`会匹配到`test/simple/basic.js`。

#### 空集

如果没有匹配到任何文件，它会返回空数组`[]`。它与shell不一样，shell会在未匹配的情况下返回本身。例如：

    $ echo a*s*d*f
    a*s*d*f
    复制代码

可以通过设置option`nonull:true`，来获得和bash shell一样的效果。

### glob.hasMagic(pattern, \[options\])

如果`pattern`中有任何特殊字符串，则返回为`true`，否则返回为`false`。

注意：`options`会影响结果。 如果options中设置`noext:true`，那么`+(a|b)`将不会视为魔术模式。 如果模式中支持`{}`表达式，那么也属于Magic，例如`a/{b/c,x/y}`。除非option设置`nobrace:true`。

### glob(pattern, \[options\], cb)

*   `pattern {String}`： 匹配模式。
*   `options {Object}`
*   `cb {Function}`
    *   `err {Error | null}`
    *   `matches {Array<String>}`： 匹配模式后的文件名数组。

执行异步全局搜索。

### glob.sync(pattern, \[options\])

*   `pattern {String}`：匹配模式。
*   `options {Object}`
*   `return: {Array<String>}`：匹配模式下的文件名。

执行异步全局搜索。

### Class: glob.Glob

通过实例化`glob.Glob`来创建`Glob`对象。

    var Glob = require("glob").Glob
    var mg = new Glob(pattern, options, cb)
    复制代码

它是一个`EventEmitter`。它会立即执行遍历目录进行匹配。

#### new glob.Glob(pattern, \[options\], \[cb\])

*   `pattern {String}`： 匹配模式。
*   `options {Object}`
*   `cb {Function}`：成功或失败的回掉。
    *   `err {Error | null}`
    *   `matches {Array<String>}`： 匹配后的文件名数组。

注意 如果option设置`sync`标志, 那么匹配结果可以通过`g.found`获取。

#### 属性

*   `minimatch` ：glob使用的`minimatch`对象。
*   `options` ：传入的option对象。
*   `aborted` ：布尔值，在调用`abort()`时会设置为`true`。 在`abort()`后无法再进行全局搜索，但是可以通过重新设置`statCache`去避免重复系统调用。
*   `cache` ：Object，可能有以下值：
    *   `false` - 路径不存在。
    *   `true` - 路径存在。
    *   `'FILE'` - 路径存在，但不是目录。
    *   `'DIR'` - 路径存在，是目录。
    *   `[file, entries, ...]` - 路径存在，结果是数组，类似于`fs.readdir`的结果。
*   `statCache`：`fs.stat`结果缓存，避免多次计算相同路径。
*   `symlinks` ：记录哪些路径是符号链接，与`**`模式相关。
*   `realpathCache` ：传递给`fs.realpath`的可选参数，避免不必要的系统调用。存储在Glob的实例对象上，可重复使用。

#### 事件

*   `end`：匹配完成后，收到匹配的结果。当`nonull`设置时，在没有匹配结果的情况下会在返回的数组中包含原匹配模式字符串，默认情况下匹配结果是被排序的, 除非设置`nosort`。
*   `match`： 每次发现匹配结果就会触发，结果中包含匹配的信息。它不会删除重复数据和解析的真实路径。
*   `error`：在发生意外错误时触发。 如果设置`options.strict`，那么任何`fs`的错误都会触发。
*   `abort`：`abort()`调用时触发。

#### 方法

*   `pause`：暂时停止搜索。
*   `resume`：恢复搜索。
*   `abort`：终止搜索。

#### 选项

所有`Minimatch`的选项都可以传递给Glob去改变匹配模式，此外还添加了一些特有的`glob-specific`。

除非另有说明，否则所有选项默认都为`false`。

所有选项也会被添加到Glob对象上。

如果正在运行多个glob操作，可以通过Glob对象的传递options给后面的操作使用，方便某些`stat`和`readdir`调用。可以通过共享`symlinks` `statCache` `realpathCache` `cache` option加快并行glob操作。

*   `cwd`：要搜索的当前目录。默认`process.cwd()`。
*   `root`：以`/`开始的挂载位置。默认是`path.resolve(options.cwd, "/")`(Unix系统是`/`，其他的一些Windows系统是`C:\`)。
*   `dot`：在正常模式和`**`模式下包含`.`。注意，在模式字符串中定义的点将始终与`.`文件匹配。
*   `nomount`：默认匹配模式中以`/`开始的会转到根目录的挂载点，以便返回有效的文件路径。设置`debug`标志关闭此行为。
*   `mark`：在目录匹配中添加`/`。注意，这需要额外的`stat`调用。
*   `nosort`：结果不排序。
*   `stat`：设置`true`统计stat所有结果。它可能会降低性能，并且完全没必要, 除非`readdir`认为文件存在的不可靠标志。
*   `silent`：读取目录遇到异常错误，并向`stderr`打印报警信息。设置option`silent`，关闭警告信息。
*   `strict`：尝试读取目录遇到异常错误时，进行会继续搜索其他匹配项。设置`strict`后，当出现这些情况时会引发错误。
*   `cache` 可以看上同的`cache`属性。传入之前生成的缓存对象保存一些`fs`调用。
*   `statCache`：文件系统信息结果的缓存，防止不必要的`stat`调用。 通常不需要设置它，如果你知道文件系统不会在调用中改变，你可以将`statCache`从一个`glob()`中调用传递给另一个的options对象中。
*   `symlinks` 已知符号链接的缓存。在解析`**`匹配时，你可以传入上一次生成的符号链接对象用来保存`stat`调用。
*   `sync` 弃用：使用`glob.sync(pattern, opts)`替代。
*   `nounique`：在一些情况中，`{}`模式会导致同一个文件在结果中出现多次。默认情况下，它会防止结果中出现重复值。设置这个属性会关闭这个行为。
*   `nonull`：设置不返回空数组，而是返回一个包含模式本身的数组。这是`glob(3)`中的默认值。
*   `debug`：开启后会在`minimatch`和`glob`开启日志记录。
*   `nobrace`：不展开大括号，如`{a,b}`和`{1..3}`。
*   `noglobstar`：针对多个文件名不匹配`**`（即 会替换为正常的`*`）。
*   `noext`：不去匹配`+(a|b)`“extglob”模式。
*   `nocase`：不区分大小写的匹配。注意：在一些不区分大小写的系统中`no-magic`模式默认会被匹配，`stat`和`readir`将不会引发错误。
*   `matchBase`：如果模式中不包含`/`字符串，则执行仅基于basename匹配。也就是说`*.js`会被视为`**/*.js`，会匹配所有目录下的所有js。
*   `nodir`：只匹配文件，不匹配目录。 (注意：只匹配目录，只需要在模式的最后加上`/`)
*   `ignore`：添加模式或glob模式的数组去排除匹配。注意，`ignore`模式下总是`dot:true`，其他设置无效。
*   `follow`：`**`模式下，访问符号链接目录。注意 它可能会导致在出现循环链接时出现大量重复引用。
*   `realpath`：设置为`true`会在所有结果中调用`fs.realpath`。在无法解析的符号链接下，返回匹配结果的完整绝对路径。(虽然它通常是一个失效的符号链接)
*   `absolute`：设置为真，将会在匹配的结果中接收到绝对路径。与`realpath`不同，它也会影响`match`事件中的返回值。

* * *

> 博客名称：[王乐平博客](https://link.juejin.im?target=http%3A%2F%2Fblog.lepingde.com%2F)
> 
> CSDN博客地址：[blog.csdn.net/lecepin](https://link.juejin.im?target=http%3A%2F%2Fblog.csdn.net%2Flecepin)

[![知识共享许可协议](https://user-gold-cdn.xitu.io/2018/12/29/167faa9c0c91ec8b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)](https://link.juejin.im?target=http%3A%2F%2Fcreativecommons.org%2Flicenses%2Fby-nc-nd%2F4.0%2F)

本作品采用[知识共享署名-非商业性使用-禁止演绎 4.0 国际许可协议](https://link.juejin.im?target=http%3A%2F%2Fcreativecommons.org%2Flicenses%2Fby-nc-nd%2F4.0%2F)进行许可。