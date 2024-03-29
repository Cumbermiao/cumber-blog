原文链接：[https://segmentfault.com/a/1190000018600714](https://segmentfault.com/a/1190000018600714)

Webpack Loader 高手进阶(三)
======================


Webpack 系列文章：

[Webpack Loader 高手进阶（一）](https://segmentfault.com/a/1190000018450503)  
[Webpack Loader 高手进阶（二）](https://segmentfault.com/a/1190000018458388)  
[Webpack Loader 高手进阶（三）](https://segmentfault.com/a/1190000018600714)

* * *

Webpack Loader 详解
-----------------

前2篇文章主要通过源码分析了 loader 的配置，匹配和加载，执行等内容，这篇文章会通过具体的实例来学习下如何去实现一个 loader。

这里我们来看下 [vue-loader(v15)](https://vue-loader.vuejs.org/zh/#vue-loader-%E6%98%AF%E4%BB%80%E4%B9%88%EF%BC%9F) 内部的相关内容，这里会讲解下有关 vue-loader 的大致处理流程，不会深入特别细节的地方。

    git clone git@github.com:vuejs/vue-loader.git

我们使用 vue-loader 官方仓库当中的 example 目录的内容作为整篇文章的示例。

首先我们都知道 vue-loader 配合 webpack 给我们开发 Vue 应用提供了非常大的便利性，允许我们在 SFC(single file component) 中去写我们的 template/script/style，同时 v15 版本的 vue-loader 还允许开发在 SFC 当中写 custom block。最终一个 Vue SFC 通过 vue-loader 的处理，会将 template/script/style/custom block 拆解为独立的 block，每个 block 还可以再交给对应的 loader 去做进一步的处理，例如你的 template 是使用 pug 来书写的，那么首先使用 vue-loader 获取一个 SFC 内部 pug 模板的内容，然后再交给 pug 相关的 loader 处理，可以说 vue-loader 对于 Vue SFC 来说是一个入口处理器。

在实际运用过程中，我们先来看下有关 Vue 的 webpack 配置：

    const VueloaderPlugin = require('vue-loader/lib/plugin')
    
    module.exports = {
      ...
      module: {
        rules: [
          ...
          {
            test: /\.vue$/,
            loader: 'vue-loader'
          }
        ]
      }
    
      plugins: [
        new VueloaderPlugin()
      ]
      ...
    }

一个就是 module.rules 有关的配置，如果处理的 module 路径是以`.vue`形式结尾的，那么会交给 vue-loader 来处理，同时在 v15 版本**必须**要使用 vue-loader 内部提供的一个 plugin，它的职责是将你定义过的其它规则复制并应用到 `.vue` 文件里相应语言的块。例如，如果你有一条匹配 `/\.js$/` 的规则，那么它会应用到 `.vue` 文件里的 `<script>` 块，说到这里我们就一起先来看看这个 plugin 里面到底做了哪些工作。

### VueLoaderPlugin

我们都清楚 webpack plugin 的装载过程是在整个 webpack 编译周期中初始阶段，我们先来看下 VueLoaderPlugin 内部源码的实现：

    // vue-loader/lib/plugin.js
    
    class VueLoaderPlugin {
      apply() {
        ...
        // use webpack's RuleSet utility to normalize user rules
        const rawRules = compiler.options.module.rules
        const { rules } = new RuleSet(rawRules)
    
        // find the rule that applies to vue files
        // 判断是否有给`.vue`或`.vue.html`进行 module.rule 的配置
        let vueRuleIndex = rawRules.findIndex(createMatcher(`foo.vue`))
        if (vueRuleIndex < 0) {
          vueRuleIndex = rawRules.findIndex(createMatcher(`foo.vue.html`))
        }
        const vueRule = rules[vueRuleIndex]
    
        ...
    
        // 判断对于`.vue`或`.vue.html`配置的 module.rule 是否有 vue-loader
        // get the normlized "use" for vue files
        const vueUse = vueRule.use
        // get vue-loader options
        const vueLoaderUseIndex = vueUse.findIndex(u => {
          return /^vue-loader|(\/|\\|@)vue-loader/.test(u.loader)
        })
        ...
    
        // 创建 pitcher loader 的配置
        const pitcher = {
          loader: require.resolve('./loaders/pitcher'),
          resourceQuery: query => {
            const parsed = qs.parse(query.slice(1))
            return parsed.vue != null
          },
          options: {
            cacheDirectory: vueLoaderUse.options.cacheDirectory,
            cacheIdentifier: vueLoaderUse.options.cacheIdentifier
          }
        }
    
        // 拓展开发者的 module.rule 配置，加入 vue-loader 内部提供的 pitcher loader
        // replace original rules
        compiler.options.module.rules = [
          pitcher,
          ...clonedRules,
          ...rules
        ]
      }
    }

这个 plugin 主要完成了以下三部分的工作：

1.  判断是否有给`.vue`或`.vue.html`进行 module.rule 的配置；
2.  判断对于`.vue`或`.vue.html`配置的 module.rule 是否有 vue-loader；
3.  拓展开发者的 module.rule 配置，加入 vue-loader 内部提供的 pitcher loader

我们看到有关 pitcher loader 的 rule 匹配条件是通过`resourceQuery`方法来进行判断的，即判断 module path 上的 query 参数是否存在 vue，例如：

    // 这种类型的 module path 就会匹配上
    './source.vue?vue&type=template&id=27e4e96e&scoped=true&lang=pug&'

如果存在的话，那么就需要将这个 loader 加入到构建这个 module 的 loaders 数组当中。以上就是 VueLoaderPlugin 所做的工作，其中涉及到拓展后的 module rule 里面加入的 pitcher loader 具体做的工作后文会分析。

### Step 1

接下来我们看下 vue-loader 的内部实现。首先来看下入口文件的相关内容：

    // vue-loader/lib/index.js
    ...
    const { parse } = require('@vue/component-compiler-utils')
    
    function loadTemplateCompiler () {
      try {
        return require('vue-template-compiler')
      } catch (e) {
        throw new Error(
          `[vue-loader] vue-template-compiler must be installed as a peer dependency, ` +
          `or a compatible compiler implementation must be passed via options.`
        )
      }
    }
    
    module.exports = function(source) {
      const loaderContext = this // 获取 loaderContext 对象
    
      // 从 loaderContext 获取相关参数
      const {
        target, // webpack 构建目标，默认为 web
        request, // module request 路径(由 path 和 query 组成)
        minimize, // 构建模式
        sourceMap, // 是否开启 sourceMap
        rootContext, // 项目的根路径
        resourcePath, // module 的 path 路径
        resourceQuery // module 的 query 参数
      } = loaderContext
    
      // 接下来就是一系列对于参数和路径的处理
      const rawQuery = resourceQuery.slice(1)
      const inheritQuery = `&${rawQuery}`
      const incomingQuery = qs.parse(rawQuery)
      const options = loaderUtils.getOptions(loaderContext) || {}
    
      ...
      
    
      // 开始解析 sfc，根据不同的 block 来拆解对应的内容
      const descriptor = parse({
        source,
        compiler: options.compiler || loadTemplateCompiler(),
        filename,
        sourceRoot,
        needMap: sourceMap
      })
    
      // 如果 query 参数上带了 block 的 type 类型，那么会直接返回对应 block 的内容
      // 例如： foo.vue?vue&type=template，那么会直接返回 template 的文本内容
      if (incomingQuery.type) {
        return selectBlock(
          descriptor,
          loaderContext,
          incomingQuery,
          !!options.appendExtension
        )
      }
    
      ...
    
      // template
      let templateImport = `var render, staticRenderFns`
      let templateRequest
      if (descriptor.template) {
        const src = descriptor.template.src || resourcePath
        const idQuery = `&id=${id}`
        const scopedQuery = hasScoped ? `&scoped=true` : ``
        const attrsQuery = attrsToQuery(descriptor.template.attrs)
        const query = `?vue&type=template${idQuery}${scopedQuery}${attrsQuery}${inheritQuery}`
        const request = templateRequest = stringifyRequest(src + query)
        templateImport = `import { render, staticRenderFns } from ${request}`
      }
    
      // script
      let scriptImport = `var script = {}`
      if (descriptor.script) {
        const src = descriptor.script.src || resourcePath
        const attrsQuery = attrsToQuery(descriptor.script.attrs, 'js')
        const query = `?vue&type=script${attrsQuery}${inheritQuery}`
        const request = stringifyRequest(src + query)
        scriptImport = (
          `import script from ${request}\n` +
          `export * from ${request}` // support named exports
        )
      }
    
      // styles
      let stylesCode = ``
      if (descriptor.styles.length) {
        stylesCode = genStylesCode(
          loaderContext,
          descriptor.styles,
          id,
          resourcePath,
          stringifyRequest,
          needsHotReload,
          isServer || isShadow // needs explicit injection?
        )
      }
    
      let code = `
    ${templateImport}
    ${scriptImport}
    ${stylesCode}
    
    /* normalize component */
    import normalizer from ${stringifyRequest(`!${componentNormalizerPath}`)}
    var component = normalizer(
      script,
      render,
      staticRenderFns,
      ${hasFunctional ? `true` : `false`},
      ${/injectStyles/.test(stylesCode) ? `injectStyles` : `null`},
      ${hasScoped ? JSON.stringify(id) : `null`},
      ${isServer ? JSON.stringify(hash(request)) : `null`}
      ${isShadow ? `,true` : ``}
    )
      `.trim() + `\n`
    
      if (descriptor.customBlocks && descriptor.customBlocks.length) {
        code += genCustomBlocksCode(
          descriptor.customBlocks,
          resourcePath,
          resourceQuery,
          stringifyRequest
        )
      }
    
      ...
    
      // Expose filename. This is used by the devtools and Vue runtime warnings.
      code += `\ncomponent.options.__file = ${
        isProduction
          // For security reasons, only expose the file's basename in production.
          ? JSON.stringify(filename)
          // Expose the file's full path in development, so that it can be opened
          // from the devtools.
          : JSON.stringify(rawShortFilePath.replace(/\\/g, '/'))
      }`
    
      code += `\nexport default component.exports`
      return code
    }

以上就是 vue-loader 的入口文件(index.js)主要做的工作：对于 request 上不带 type 类型的 Vue SFC 进行 parse，获取每个 block 的相关内容，将不同类型的 block 组件的 Vue SFC 转化成 js module 字符串，具体的内容如下：

    import { render, staticRenderFns } from "./source.vue?vue&type=template&id=27e4e96e&scoped=true&lang=pug&"
    import script from "./source.vue?vue&type=script&lang=js&"
    export * from "./source.vue?vue&type=script&lang=js&"
    import style0 from "./source.vue?vue&type=style&index=0&id=27e4e96e&scoped=true&lang=css&"
    
    /* normalize component */
    import normalizer from "!../lib/runtime/componentNormalizer.js"
    var component = normalizer(
      script,
      render,
      staticRenderFns,
      false,
      null,
      "27e4e96e",
      null
    )
    
    /* custom blocks */
    import block0 from "./source.vue?vue&type=custom&index=0&blockType=foo"
    if (typeof block0 === 'function') block0(component)
    
    // 省略了有关 hotReload 的代码
    
    component.options.__file = "example/source.vue"
    export default component.exports

从生成的 js module 字符串来看：将由 source.vue 提供 render函数/staticRenderFns，js script，style样式，并交由 normalizer 进行统一的格式化，最终导出 component.exports。

### Step 2

这样 vue-loader 处理的第一个阶段结束了，vue-loader 在这一阶段将 Vue SFC 转化为 js module 后，接下来进入到第二阶段，将新生成的 js module 加入到 webpack 的编译环节，即对这个 js module 进行 AST 的解析以及相关依赖的收集过程，这里我用每个 request 去标记每个被收集的 module(这里只说明和 Vue SFC 相关的模块内容)：

    [
     './source.vue?vue&type=template&id=27e4e96e&scoped=true&lang=pug&',
     './source.vue?vue&type=script&lang=js&',
     './source.vue?vue&type=style&index=0&id=27e4e96e&scoped=true&lang=css&',
     './source.vue?vue&type=custom&index=0&blockType=foo'
    ]

我们看到通过 vue-loader 处理到得到的 module path 上的 query 参数都带有 vue 字段。这里便涉及到了我们在文章开篇提到的 VueLoaderPlugin 加入的 pitcher loader。如果遇到了 query 参数上带有 vue 字段的 module path，那么就会把 pitcher loader 加入到处理这个 module 的 loaders 数组当中。因此这个 module 最终也会经过 pitcher loader 的处理。此外在 loader 的配置顺序上，pitcher loader 为第一个，因此在处理 Vue SFC 模块的时候，最先也是交由 pitcher loader 来处理。

事实上对一个 Vue SFC 处理的第二阶段就是刚才提到的，Vue SFC 会经由 pitcher loader 来做进一步的处理。那么我们就来看下 vue-loader 内部提供的 pitcher loader 主要是做了哪些工作呢：

1.  剔除 eslint loader；
2.  剔除 pitcher loader 自身；
3.  根据不同 type query 参数进行拦截处理，返回对应的内容，跳过后面的 loader 执行的阶段，进入到 module parse 阶段

    // vue-loader/lib/loaders/pitcher.js
    
    module.export = code => code
    
    module.pitch = function () {
      ...
      const query = qs.parse(this.resourceQuery.slice(1))
      let loaders = this.loaders
    
      // 剔除 eslint loader
      // if this is a language block request, eslint-loader may get matched
      // multiple times
      if (query.type) {
        // if this is an inline block, since the whole file itself is being linted,
        // remove eslint-loader to avoid duplicate linting.
        if (/\.vue$/.test(this.resourcePath)) {
          loaders = loaders.filter(l => !isESLintLoader(l))
        } else {
          // This is a src import. Just make sure there's not more than 1 instance
          // of eslint present.
          loaders = dedupeESLintLoader(loaders)
        }
      }
    
      // 剔除 pitcher loader 自身
      // remove self
      loaders = loaders.filter(isPitcher)
    
      if (query.type === 'style') {
        const cssLoaderIndex = loaders.findIndex(isCSSLoader)
        if (cssLoaderIndex > -1) {
          const afterLoaders = loaders.slice(0, cssLoaderIndex + 1)
          const beforeLoaders = loaders.slice(cssLoaderIndex + 1)
          const request = genRequest([
            ...afterLoaders,
            stylePostLoaderPath,
            ...beforeLoaders
          ])
          return `import mod from ${request}; export default mod; export * from ${request}`
        }
      }
    
      if (query.type === 'template') {
        const path = require('path')
        const cacheLoader = cacheDirectory && cacheIdentifier
          ? [`cache-loader?${JSON.stringify({
            // For some reason, webpack fails to generate consistent hash if we
            // use absolute paths here, even though the path is only used in a
            // comment. For now we have to ensure cacheDirectory is a relative path.
            cacheDirectory: path.isAbsolute(cacheDirectory)
              ? path.relative(process.cwd(), cacheDirectory)
              : cacheDirectory,
            cacheIdentifier: hash(cacheIdentifier) + '-vue-loader-template'
          })}`]
          : []
        const request = genRequest([
          ...cacheLoader,
          templateLoaderPath + `??vue-loader-options`,
          ...loaders
        ])
        // the template compiler uses esm exports
        return `export * from ${request}`
      }
    
      // if a custom block has no other matching loader other than vue-loader itself,
      // we should ignore it
      if (query.type === `custom` &&
          loaders.length === 1 &&
          loaders[0].path === selfPath) {
        return ``
      }
    
      // When the user defines a rule that has only resourceQuery but no test,
      // both that rule and the cloned rule will match, resulting in duplicated
      // loaders. Therefore it is necessary to perform a dedupe here.
      const request = genRequest(loaders)
      return `import mod from ${request}; export default mod; export * from ${request}`
    }

对于 style block 的处理，首先判断是否有 css-loader，如果有的话就重新生成一个新的 request，这个 request 包含了 vue-loader 内部提供的 stylePostLoader，并返回一个 js module，根据 pitch 函数的规则，pitcher loader 后面的 loader 都会被跳过，这个时候开始编译这个返回的 js module。相关的内容为：

    import mod from "-!../node_modules/vue-style-loader/index.js!../node_modules/css-loader/index.js!../lib/loaders/stylePostLoader.js!../lib/index.js??vue-loader-options!./source.vue?vue&type=style&index=0&id=27e4e96e&scoped=true&lang=css&"
    export default mod
    export * from "-!../node_modules/vue-style-loader/index.js!../node_modules/css-loader/index.js!../lib/loaders/stylePostLoader.js!../lib/index.js??vue-loader-options!./source.vue?vue&type=style&index=0&id=27e4e96e&scoped=true&lang=css&"  

对于 template block 的处理流程类似，生成一个新的 request，这个 request 包含了 vue-loader 内部提供的 templateLoader，并返回一个 js module，并跳过后面的 loader，然后开始编译返回的 js module。相关的内容为：

    export * from "-!../lib/loaders/templateLoader.js??vue-loader-options!../node_modules/pug-plain-loader/index.js!../lib/index.js??vue-loader-options!./source.vue?vue&type=template&id=27e4e96e&scoped=true&lang=pug&"

这样对于一个 Vue SFC 处理的第二阶段也就结束了，通过 pitcher loader 去拦截不同类型的 block，并返回新的 js module，跳过后面的 loader 的执行，同时在内部会剔除掉 pitcher loader，这样在进入到下一个处理阶段的时候，pitcher loader 不在使用的 loader 范围之内，因此下一阶段 Vue SFC 便不会经由 pitcher loader 来处理。

### Step 3

接下来进入到第三个阶段，编译返回的新的 js module，完成 AST 的解析和依赖收集工作，并开始处理不同类型的 block 的编译转换工作。就拿 Vue SFC 当中的 style / template block 来举例，

style block 会经过以下的流程处理：

source.vue?vue&type=style -> vue-loader(抽离 style block) -> stylePostLoader(处理作用域 scoped css) -> css-loader(处理相关资源引入路径) -> vue-style-loader(动态创建 style 标签插入 css)

![图片描述](https://segmentfault.com/img/bVbqc2T?w=2034&h=946 "图片描述")

template block 会经过以下的流程处理：

source.vue?vue&type=template -> vue-loader(抽离 template block ) -> pug-plain-loader(将 pug 模块转化为 html 字符串) -> templateLoader(编译 html 模板字符串，生成 render/staticRenderFns 函数并暴露出去)

![图片描述](https://segmentfault.com/img/bVbqc20?w=2074&h=922 "图片描述")

我们看到经过 vue-loader 处理时，会根据不同 module path 的类型(query 参数上的 type 字段)来抽离 SFC 当中不同类型的 block。这也是 vue-loader 内部定义的相关规则：

    // vue-loader/lib/index.js
    
    const qs = require('querystring')
    const selectBlock = require('./select')
    ...
    
    module.exports = function (source) {
      ...
      const rawQuery = resourceQuery.slice(1)
      const inheritQuery = `&${rawQuery}`
      const incomingQuery = qs.parse(rawQuery)
    
      ...
      const descriptor = parse({
        source,
        compiler: options.compiler || loadTemplateCompiler(),
        filename,
        sourceRoot,
        needMap: sourceMap
      })
    
      // if the query has a type field, this is a language block request
      // e.g. foo.vue?type=template&id=xxxxx
      // and we will return early
      if (incomingQuery.type) {
        return selectBlock(
          descriptor,
          loaderContext,
          incomingQuery,
          !!options.appendExtension
        )
      }
      ...
    }

当 module path 上的 query 参数带有 type 字段，那么会直接调用 selectBlock 方法去获取 type 对应类型的 block 内容，跳过 vue-loader 后面的处理流程(这也是与 vue-loader 第一次处理这个 module时流程不一样的地方)，并进入到下一个 loader 的处理流程中，selectBlock 方法内部主要就是根据不同的 type 类型(template/script/style/custom)，来获取 descriptor 上对应类型的 content 内容并传入到下一个 loader 处理：

    module.exports = function selectBlock (
      descriptor,
      loaderContext,
      query,
      appendExtension
    ) {
      // template
      if (query.type === `template`) {
        if (appendExtension) {
          loaderContext.resourcePath += '.' + (descriptor.template.lang || 'html')
        }
        loaderContext.callback(
          null,
          descriptor.template.content,
          descriptor.template.map
        )
        return
      }
    
      // script
      if (query.type === `script`) {
        if (appendExtension) {
          loaderContext.resourcePath += '.' + (descriptor.script.lang || 'js')
        }
        loaderContext.callback(
          null,
          descriptor.script.content,
          descriptor.script.map
        )
        return
      }
    
      // styles
      if (query.type === `style` && query.index != null) {
        const style = descriptor.styles[query.index]
        if (appendExtension) {
          loaderContext.resourcePath += '.' + (style.lang || 'css')
        }
        loaderContext.callback(
          null,
          style.content,
          style.map
        )
        return
      }
    
      // custom
      if (query.type === 'custom' && query.index != null) {
        const block = descriptor.customBlocks[query.index]
        loaderContext.callback(
          null,
          block.content,
          block.map
        )
        return
      }
    }

### 总结

通过 vue-loader 的源码我们看到一个 Vue SFC 在整个编译构建环节是怎么样一步一步处理的，这也是得益于 webpack 给开发这提供了这样一种 loader 的机制，使得开发者通过这样一种方式去对项目源码做对应的转换工作以满足相关的开发需求。结合之前的2篇有关 webpack loader 源码的分析，大家应该对 loader 有了更加深入的理解，也希望大家活学活用，利用 loader 机制去完成更多贴合实际需求的开发工作。

**文章首发于个人github blog: [Biu-blog](https://github.com/CommanderXL/Biu-blog)，欢迎大家关注~**