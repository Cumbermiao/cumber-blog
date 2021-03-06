---
title: 性能优化
date: 2020-03-26 16:40:51
updated: 2021-01-24 10:33:10
categories: Computer
tags: 性能优化
---

# 性能优化

参考文章
- [网站性能优化—CRP](https://segmentfault.com/a/1190000008550336)
- [2018 前端性能优化清单](https://juejin.im/post/5a966bd16fb9a0635172a50a)
- [掘进翻译系列前端性能优化](https://github.com/xitu/gold-miner)

参考了以上几篇文章内容，将性能优化过程分为了三个阶段：评估指标、设置目标、优化方法；其中前两个阶段在实际的操作过程中总是被忽略或者一概而过，大部分开发者可能更注重于优化所使用的优化手段。

的确性能优化的过程中大部分的工作是在第三阶段，但如果只执行第三阶段我觉得所作的优化最起码是不全面的，或者是片面的，更严重的可能是无用的。这三个阶段在我看来是一个简单的分析框架，第一步筛选出你所需要关注的性能指标，不同类型的指标可以看出产品所关注的重点， 第二步为这些指标设置合理的目标，第三步有了全面的指标及目标就可以具体的使用对应的优化手段来完成优化。

## 1. 评估性能指标
> 性能优化是一个很大的话题， 其影响因素也很多， 设备、浏览器、协议、网络、延迟都会造成性能明显的变化。所以考虑的因素也很多， 这就需要我们设计一个完善的性能指标， 需要考虑到 CDN，缓存，代理，负载均衡，服务器等。

1. 根据用户建立性能评估指标， 根据竞争对手的性能，自己用户的代表设备的性能， 制定初步的指标。

2. 根据业务员场景制定合适的指标， 通常来说指标可以分为以下几类：
  - 基于数量的指标： 衡量请求数量、权重和性能评分。对于长期监控和告警非常有用，对理解用户体验没什么帮助。
  - 里程碑式指标： 使用加载过程中的各个状态来标记， 如 首位字节时间(Time To First Byte) , 首次可交互时间(Time To Interactive)。 这对于用户体验很有用。
  - 渲染指标： 估计内容渲染的时间。 例如渲染开始时间(Start Render)和速度指数(Speed Index) 。 这对检测和调整渲染性能很有用， 但对检测重要内容何时出现、何时可交互帮助不大。
  - 自定义指标： 衡量某个特定的用户实践， 例如 Twitter 的首次发推时间(Time To First Tweet)

3. 为了使性能画像更加完整， 我们可以在所有类型中都选择一些指标， 正常来说比较重要的指标如下：
  - 首次有效绘制(First Meaningful Paint, FMP): 主要内容绘制的时间。
  - 首次可交互时间(Time To Interactive, TTI)： 页面布局已经稳定， 主线程可以响应用户的输入。
  - 首次输入延迟(First Input Delay, FID): 用户首次与页面交互，到网页能够响应该交互的时间。
  - CPU 耗时： 描述主线程处理有效负载时繁忙程度的指标， 显示在绘制、渲染、运行脚本和加载时， 主线程被阻塞的频次和时长。利用 WebPageTest 可以获取主线程处理故障的详细视图。
  - 速度指数(Speed Index)：衡量视觉上页面内容充满的速度。
  - 自定义指标： 自定义指标由业务和用户体验专门设置， 需要对重要像素、关键脚本、必要的 CSS 样式和相关静态资源有个清晰的概念， 并能够测算用户需要多长时间来下载他们。

## 2. 设置合理的目标
1. 100ms 响应时间， 60fps 动画。
2. 速度指数 <1250， TTI(首次可交互时间) < 5s， 关键文件大小 < 170kb(压缩后，压缩前 0.7m)。
  关键文件里包含关键路径 HTML/CSS/JS、路由、状态管理、使用程序、框架和应用程序逻辑。
3. http 请求资源数 <=6 ， http/2 请求资源数 <=20。
4. 首屏代码覆盖率 >=90%

## 3. 优化方法

### 1. 考虑使用 [PRPL 模式](https://web.dev/apply-instant-loading-with-prpl/) 以及 [Application Shell 架构](https://developers.google.com/web/updates/2015/11/app-shell)

#### PRPL模式
- P: Push(or preload) the most important resources.
使用 preload 预加载关键资源。

- R: render the initial route as soon as possible.
提高 FP 速度的方法之一可以使用内联的方式嵌入关键 js/css 资源， 使用 async 异步加载 js 资源。
或者使用服务端渲染。 但是这两种方式都有缺点， 内联会导致代码维护困难， 服务端渲染会影响 TTI 指标。

- P: Pre-cache remaining assets.
对静态资源进行预缓存， 可以使用 service-worker。

- L: Lazy load other routes and non-critical assets.
使用合理的拆包策略，将所有类型资源拆分， 拆分后可以 preload 重要的资源。
对非关键图片进行懒加载。

#### Application Shell 架构
> 一个 application shell 是支持用户界面的最小HTML、CSS和JavaScript。 其特点在于加载较快、可以被缓存、动态显示内容。
Application Shell 的核心在于使用 service-worker 对 shell（外壳） 进行缓存，对于内容使用请求等动态展示。
在首次访问时为了尽快的展示内容（首次渲染），对首次渲染需要的 css 使用内联的方式， 对于外联的 css 进行异步加载可以使用 js 或者 RFA ， js 全部进行异步加载。  

### 2. 考虑框架的选择
并非每个项目都需要框架， 对于简单的业务使用原生 js 或者 jquery 开发能够大大减少 react，vue，angular 所带来的的 js 文件的大小。 js 大小的减少， 可以提升首次可交互的时间。
另外， 有测量表明针对首次内容渲染时间（从导航到浏览器从 DOM 渲染第一部分内容的时间）， vue 和 preact 是最快的， 其次是 react。

### 3. 接口请求的优化
使用 rest 规范定义接口。
由于 rest 接口返回的数据量常常大于渲染所需要的数据量，如果很多资源需要来自接口的数据， 就会造成浪费。 此时可以考虑 GraphQL 进行接口查询。

### 4. 选择合适的 CDN
根据你拥有的动态数据量， 将内容的某些部分推送到 CDN 并从中提供静态版本， 从而避免数据库请求。


### 5. 资源优化
#### 5.1 使用 Brotli 或 Zopfli 来对纯文本进行压缩
- 动态压缩是即时进行的。用户发出请求，内容被压缩（当用户等待时），压缩后的内容被提供。
- 静态压缩是指在用户请求之前将资产压缩到磁盘上。当用户请求资产时，不会发生压缩。预压缩资产仅通过磁盘提供。

Brotli 一种开源的无损数据格式，现已被所有现代浏览器所支持。因为它比较依赖配置，所以这种压缩可能会（非常）慢，但较慢的压缩意味着更高的压缩率。不过它解压速度很快。
对于动态压缩， 需要考虑其压缩资源所耗费的时间选择合适的压缩级别。 但是对于静态压缩， 首选最高级别的压缩。

或者，你可以考虑使用将数据编码为 Deflate、Gzip 和 Zlib 格式的 Zopfli 的压缩算法。任何普通的 Gzip 压缩资源都可以通过 Zopfli 改进的 Deflate 编码达到比 Zlib 的最大压缩率小 3% 到 8%的文件大小。

#### 5.2 响应式图片和 webp/AVIF 和图片优化
尽量使用带有 img标签的 srcset、sizes 属性的响应式图片属性和 <picture> 元素。
WebP 图像文件大小等价于 Guetzli 和 Zopfli，但它并不支持像 JPEG 这样的渐进式渲染。所以尽管 WebP 图像在网络中的传输速度更快， 但是其兼容不够好， 而且渲染速度较慢， 需要结合场景来使用。
[AVIF](https://juejin.cn/post/6899496770280095752) 是比 WebP 还要新的一种格式，它支持全分辨率的10位和12位颜色，所生成的图像比其他已知格式小10倍，同样他的缺点也是兼容性支持。

确保 JPGE 是渐进加载且经过压缩的。

#### 5.3 Web 字体
Web 字体首选系统自带的字体。
对于自定义的 web font 进行压缩， 因为 unicode 编码里面肯定会有很多空的编码。
根据场景使用 preload。

### 6. 构建优化

#### 6.1  降低 js 的解析成本

js 有一个解析成本， 其相关因素有 js 的大小、硬件的差异。 而仅由 js 大小引起性能问题的情况相对少见， 反而是由于硬件的原因会造成解析和执行的时间有较大的差异。

为了减少 js 的解析成本，在构建项目时我们可以借助一些工具来减少 js 的体积。如： webpack-bundle-analyzer, webpack size-plugin 等。
在开发时我们也可以借助一些工具来分析引入的依赖的大小，如： import cost for vscode。

更有效的方法是避免解析成本， 如 Ember 推出的二进制模板，或者考虑 wasm。

我们需要了解， 虽然 js 的大小很重要， 但是随着 js 体积的增大， 解析和编译时间不一定线性增加。

#### 6.2 tree shaking， scope hoisting， code splitting
使用 tree shaking 清理无用的代码。
使用 scope hoisting 减少 js 体积。
使用 code splitting 分隔 bundle ， 按需加载。
考虑使用 preload-webpack-plugin 预加载 js 。

#### 6.3 web worker
为了减少 TTI ， 可以考虑将高耗时的 js 放到 web worker 或者使用 service worker 缓存。

#### 6.4 AoT(ahead of time)
考虑使用 AoT(angular) 编译器将一些客户端渲染放到服务器上， 从而快速输出可用结果。

其他的编译器还有 JIT (just-in-time) 即时编译器。

#### 6.5 仅将遗留代码提供给旧版浏览器
设置两个构建， 一个使用 es6， 一个使用 es5。
由于现代浏览器对于 es6 的支持较好， 我们可以使用 babel 转义目标浏览器所不支持的特性。使用 `script type='module'` 让支持 es 模块的浏览器加载 es6 的构建。可以结合 `link rel="modulepreload"` 使用。
对于老浏览器使用 `script nomodule` 加载 es5 构建。

[modulepreload](https://developers.google.com/web/updates/2017/12/modulepreload)可以预加载模块及其依赖，提高带宽使用率。

#### 6.6 增量解耦
对于一些老项目， 可以使用增量解耦的方式淘汰依赖，如 jquery 等。
可以参考 [github jquery的淘汰](https://github.blog/2018-09-06-removing-jquery-from-github-frontend/)。
主要方式：
- 检测每行代码 jquery 的比例，确保该比例是下降的。
- 使用 eslint 的方式警告开发者不要使用 jquery 相关的 api。
- 当检测到新增代码中使用了 jquery 特性时， 指定构建失败。

#### 6.7 持续监控性能
防止业务变更，代码变动导致性能波动，我们可以对项目进行持续监控。
我们可以借助一些可持续监控工具来描述性能画像， 甚至可以添加自定义指标。
例如 travis ci 结合 lighthhouse 可以很方便的获取到当前网页的性能。



### 7. 其他优化
#### 7.1 升级成 http 2.0
#### 7.2 资源提示
##### 7.2.1 preload
> 使用 <link rel="preload" href="" as=""> 可以用来预加载指定资源。
preload 用来预加载当前页面中重要的资源， 通过 as 属性标记当前资源的类型， 浏览器识别可以对加载的资源定义优先级。
当前页面跳转后， preload 资源会中断加载。

##### 7.2.2 prefetch
> prefetch 是一个低优先级的资源提示，允许浏览器在后台（空闲时）获取将来可能用得到的资源，并且将他们存储在浏览器的缓存中。

pretetch 针对于下一个页面需要预加载的资源。

##### 7.2.3 prerender
> 与 prefetch 类似， 都是针对下一个页面的内容， 但是 prerender 会在后台把页面渲染出来， 使用不当容易造成资源浪费。
对于有 post、put等ajax 请求， video，audio标签等页面不会预渲染。

##### 7.2.4 preconnect
> preconnect 预先完成 DNS 解析 + TLS 协商 + CP 握手。
preconnect 是个有效而且克制的资源优化方法，它不仅可以优化页面并且可以防止资源利用的浪费。
常用于对 cdn 与预连接。

##### 7.2.5 dns-prefetch
> dns 查找是一个耗时的过程， 使用 <link rel="dns-prefetch" href=""> 可以用来预解析当前页面可能跳转的域名。
