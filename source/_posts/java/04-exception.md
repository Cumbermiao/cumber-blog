---
layout: java
title: java-异常
date: 2022-03-31 19:37:50
tags:
---

## 异常

> Java 中内置了一套异常处理机制，总使用异常来表示错误。
异常是一种`class`，因此它本身带有类型信息。

继承关系如下：
<pre>
<code class="language-ascii" style="font-family: &quot;Courier New&quot;, Consolas, monospace;">                     
                     ┌───────────┐
                     │  Object   │
                     └───────────┘
                           ▲
                           │
                     ┌───────────┐
                     │ Throwable │
                     └───────────┘
                           ▲
                 ┌─────────┴─────────┐
                 │                   │
           ┌───────────┐       ┌───────────┐
           │   Error   │       │ Exception │
           └───────────┘       └───────────┘
                 ▲                   ▲
         ┌───────┘              ┌────┴──────────┐
         │                      │               │
┌─────────────────┐    ┌─────────────────┐┌───────────┐
│OutOfMemoryError │... │RuntimeException ││IOException│...
└─────────────────┘    └─────────────────┘└───────────┘
                                ▲
                    ┌───────────┴─────────────┐
                    │                         │
         ┌─────────────────────┐ ┌─────────────────────────┐
         │NullPointerException │ │IllegalArgumentException │...
         └─────────────────────┘ └─────────────────────────┘
</code>
</pre>

`Throwable`有两个体系： `Error`，`Exception`； Error 表示严重错误，程序对此一般无能为力；而 Exception 是运行时的错误，可以被捕获处理。

Java 规定：
- 必须捕获的异常，包括Exception及其子类，但不包括RuntimeException及其子类，这种类型的异常称为Checked Exception。
- 不需要捕获的异常，包括Error及其子类，RuntimeException及其子类。

```java
public class Main {
    public static void main(String[] args) {
        try {
            byte[] bs = toGBK("中文");
            System.out.println(Arrays.toString(bs));
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace(); //打印异常
        }
    }

    static byte[] toGBK(String s) throws UnsupportedEncodingException {
        // 用指定编码转换String为byte[]:
        return s.getBytes("GBK");
    }
}
```
由于 `toGBK` 方法声明了可能会抛出 `UnsupportedEncodingException`异常，在调用时需要使用 `try catch finally`捕获该异常否则编译器会报错；如果是测试代码可以在 main 方法中声明异常，就不需要 catch 异常了，但是如果有异常程序会立即退出。

## 多 catch 语句

Java 中可以使用多个 `catch`语句，每个`catch`捕获一个异常，JVM捕获到异常后会按照顺序匹配异常，当某个`catch`被匹配到后，后面的`catch`不会再执行，即多个`catch`只会有一个语句被执行。

所以写 catch 时需要注意顺序，子类需要写在前面。