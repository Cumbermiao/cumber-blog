---
layout: java
title: java-概念
date: 2022-03-29 15:55:31
tags:
---

参考文章[廖雪峰Java简介](https://www.liaoxuefeng.com/wiki/1252599548343744/1255876875896416)

### Java介于编译型语言和解释型语言之间。

- 编译型语言如C、C++，代码是直接编译成机器码执行，但是不同的平台（x86、ARM等）CPU的指令集不同，因此，需要编译出每一种平台的对应机器码。
- 解释型语言如Python、Ruby可以由解释器直接加载源码然后运行，代价是运行效率太低。

Java是将代码编译成一种“字节码”，它类似于抽象的CPU指令，然后，针对不同平台编写虚拟机，不同平台的虚拟机负责加载字节码并执行，这样就实现了“一次编写，到处运行”的效果。当然，这是针对Java开发者而言。对于虚拟机，需要为每个平台分别开发。

### Java 版本

- Java SE就是标准版，包含标准的JVM和标准库.
- Java EE是企业版，它只是在Java SE的基础上加上了大量的API和库，以便方便开发Web应用、数据库、消息服务等，Java EE的应用使用的虚拟机和Java SE完全相同。
- Java ME就和Java SE不同，它是一个针对嵌入式设备的“瘦身版”，Java SE的标准库无法在Java ME上使用，Java ME的虚拟机也是“瘦身版”。

### JDK JRE

- JDK：Java Development Kit 如果只有Java源码，要编译成Java字节码，就需要JDK，因为JDK除了包含JRE，还提供了编译器、调试器等开发工具。
- JRE：Java Runtime Environment 是运行Java字节码的虚拟机。
