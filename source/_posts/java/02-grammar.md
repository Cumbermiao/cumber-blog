---
layout: java
title: java-语法1
date: 2022-03-29 16:57:27
tags:
---

### Hello World

```java
// Hello.java
public class Hello {
  public static void main(String[] args) {
    System.out.println("Hello world");
  }
}
```

创建一个文件名为 `Hello.java` 的文件， 里面输入以上代码。==Java 中一个文件只能有一个 public 类， public 类名和文件名要完全一致。==
`class` 定义了一个类 Hello，类名首字母需要大写，==class 类名大小写敏感==； Hello 类里定义了一个 main 方法.

> Java 规定，某个类定义的 public static void main(String[] args)是 Java 程序的固定入口方法.

#### 编译与执行

`javac`是编译器，使用 `javac Hello.java` 可以将 Java 源码编译为`字节码文件`。
`java`是虚拟机，使用 `java Hello`可以执行`Hello.class`文件。

### 命名规范

#### 类名规范

1. 类名必须以英文字母开头，后接字母，数字和下划线的组合
2. 习惯以大写字母开头

#### 方法名规范

1. 类名必须以英文字母开头，后接字母，数字和下划线的组合
2. 习惯以小写字母开头

### 变量

Java 中变量分为两种类型：基本类型和引用类型。

#### 基本类型

- 整数类型： byte， short， int， long；
- 浮点数类型： float， double；
- 字符类型： char；
- 布尔类型： boolean；

> 计算机内存的最小存储单元是字节（byte），一个字节就是一个 8 位二进制数，即 8 个 bit。它的二进制表示范围从 00000000 - 11111111 ，换算成十进制是 0-255，换算成十六进制是 00~ff。
> 一个字节是 1byte，1024 字节是 1K，1024K 是 1M，1024M 是 1G，1024G 是 1T。

### final、var

使用 `final` 修饰符来声明常量，常量声明之后不可修改。例如 `final double PI = 3.14;`。

使用 `var` 关键字可以省略变量类型，例如 `StringBuilder sb = new StringBuilder();` 可以简写为 `var sb = new StringBuilder();`。

### 作用域

在 Java 中以`{}`为作用域范围。

### 字符及字符串类型

`char` 保存一个 Unicode 字符； `char`类型可以被转换为数字类型，值为 Unicode 编码的十进制。 ==String 类型无法转换成数字类型。==

`String` 类型使用 `""`表示，使用 `\`进行转义，常见转义字符有`"`,`'`,`\`,`换行 \n`,`回车 \r`,`tab \t`,`Unicode字符 \u####`。
使用`""" """`表示多行字符串。

### 数组类型

定义数组使用`类型[]`。Java 数组有以下特点：

1. 数组初始化有默认值，整型为 0，浮点型为 0.0，布尔型为 false;
2. 数字一旦创建，大小就无法改变；

数组可以通过 length 属性获取长度，初始化时可以不定义数组长度，索引超过数组范围时运行会报错。

### ==

`==`可以判断基础类型变量的值是否相等，如果用于引用类型则判断两个变量的引用是否相等（类似 js 中的 `===`），判断两个引用类型的值是否相等可以使用 `equal`方法。

### switch

switch 基本用法与 js 一致，Java 12 后新增了一种写法用于保证只有一种路径会执行且不需要写 break ，写法如下：

```java
public class Main {
  public static void main(String[] args) {
    String fruit = "apple";
    switch (fruit) {
    case "apple" -> System.out.println("Selected apple");
    case "pear" -> System.out.println("Selected pear");
    case "mango" -> {
      System.out.println("Selected mango");
      System.out.println("Good choice!");
    }
    default -> System.out.println("No fruit selected");
    }
  }
}

```

改写法可以让 swtich 返回值：

```java
int opt = switch (fruit) {
  case "apple" -> 1;
  case "pear", "mango" -> 2;
  default -> 0;
};
```

使用 `yield` 也可以在 swtich 中返回值。

### for 循环

for 循环基本用法与 js 一致。
`for each` 循环用来变量数组中的每个元素，而不是索引，它能遍历所有“可迭代”的数据类型。用法如下：

```java
public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 4, 9, 16, 25 };
        for (int n : ns) {
            System.out.println(n);
        }
    }
}
```

### 数组

`Arrays.sort(ns)` 可以对数组进行排序，数组排序使用的是原地排序，即 ns 的引用指向不变。

使用 `Arrays.deepToString()` 方法可以打印多为数组。