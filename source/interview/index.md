---
title: 面试经
date: 2019-02-22 14:24:58
---

## 实践

- [手写 Promise](http://cumbermiao.github.io/interview/01.html)
- [手写 Call、apply、bind](http://cumbermiao.github.io/interview/02.html)
- [new 实现过程 & instanceof 实现](http://cumbermiao.github.io/interview/03.html)

# 面试经

## 手写 eventEmitter

## dom diff

## ast

## 异步队列：微队列、宏队列

## node 中的 Event Loop 与 浏览器的区别

timer => i/o => idle,prepare => poll => check => close callbacks

## GC 新生代算法与老生代算法

## console 控制台 \$0

## Vue 系列

- vue 观察者模式
- 观察者与发布订阅的区别

## React 系列

- react 如何实现数据绑定

## 常见数组问题

- 排序
- 去重

## js 中的观察者

- Mutation Observer
- Intersection Observer
- Resize Observer
- Performance Observer

## js 中使用的 api

- getBoundingClientRect
- requestAnimationFrame

## shadow dom

### 下列的 a标签嵌套失败， 最外层的a标签会变成 <a href="/" style="display: block"></a> 包含的内容显示下后面。
````html
<a href="/" style="display: block">
  <p class="li-product">
    <a class="li-product-img clearfix">
      <div class="wrap">
        <img
          src="http://git.vemic.com/uploads/-/system/project/avatar/10/2111.jpg?width=40"
          alt=""
        />
      </div>
    </a>
    <a class="li-product-title" href="/"
      >High frame rate USB Webcam MJPEG 640X360 260fps,</a
    >
  </p>
</a>
``` ## 深拷贝与浅拷贝 #### 浅拷贝 - Object.assign - ... #### 深拷贝 -
JSON.parse(JSON.stringify(object)) , 存在缺点：JSON.stringfy 会忽略
undefined、symbol 类型、函数， 且无法解决循环引用的对象。 - 简易版深拷贝 ```js
function deepClone(obj) { function isObject(o) { return (typeof o === 'object'
|| typeof o === 'function') && o !== null } if (!isObject(obj)) { throw new
Error('非对象') } let isArray = Array.isArray(obj) let newObj = isArray ?
[...obj] : { ...obj } Reflect.ownKeys(newObj).forEach(key => { newObj[key] =
isObject(obj[key]) ? deepClone(obj[key]) : obj[key] }) return newObj }
````

## TODO

#### Symbol.hasInstance 自定义类的 instancesof

#### toPrimitive 转换对象逻辑

- 如果已经是原始类型了，那就不需要转换了；
- 调用 x.valueOf()，如果转换为基础类型，就返回转换的值；
- 调用 x.toString()，如果转换为基础类型，就返回转换的值；
- 如果都没有返回原始类型，就会报错；

#### MessageChannel ， 如果你所需拷贝的对象含有内置类型并且不包含函数，可以使用 MessageChannel 进行深拷贝

#### CommonJS 中 require 实现原理
