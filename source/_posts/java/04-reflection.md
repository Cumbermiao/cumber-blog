---
layout: java
title: java-反射
date: 2022-04-01 11:10:38
tags:
---

## 反射

> 反射就是Reflection，Java的反射是指程序在运行期可以拿到一个对象的所有信息。

反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。

Java 中除了基础类型外，其他类型都是 `class`（包括`interface`）。

JVM中可以创建 `Class` 实例，我们自己的 java 程序无法创建。

由于JVM为每个加载的class创建了对应的Class实例，并在实例中保存了该class的所有信息，包括类名、包名、父类、实现的接口、所有方法、字段等，因此，如果获取了某个Class实例，我们就可以通过这个Class实例获取到该实例对应的class的所有信息。

这种通过Class实例获取class信息的方法称为反射（Reflection）。

## 获取 Class 实例

1. 通过 `class` 静态变量获取
```java
Class cls = String.class;
```

2. 实例可以通过 `getClass` 方法获取
```java
String s = "hello";
Class cls = s.getClass();
```

3. 通过 `class` 完整类名获取
```java
Class cls = Class.forName("java.lang.String");
```


## 获取字段

获取`class`实例字段信息的方法：
- Field getField(name) 获取 public 字段 name 的字段信息（包含父类）；
- Field getDeclaredField(name) 获取当前类的 name 的字段信息；
- Field[] getFields() 获取所有 public 字段的字段信息（包含父类）
- Field[] getDeclaredFields() 获取当前类的所有字段信息

### Field

Field 对象包含字段的所有信息：

- getName() 返回字段的名称；
- getType() 返回字段所属类型的 Class；
- getModifiers()：返回字段的修饰符，它是一个int，不同的bit表示不同的含义。

### 获取、设置字段值

使用 `Field.get(Object)` 方法可以获取到指定字段的值，但是要注意该字段是否为`public`，如果是在子类里面访问父类的`private`字段，需要获取前设置 `f.setAccessible(true)`，不然会报`IllegalAccessException` 异常。

使用`Field.set(Object, Object)`方法可以设置字段值，同样也要注意修改`private`字段值需要设置`f.setAccessible(true)`。

## 获取方法

- Method getMethod(name, Class...) 获取某个 public 方法（包含父类）
- Method getDeclaredMethod(name, Class...) 获取当前类的方法
- Method[] getMethods()：获取所有public的Method（包括父类）
- Method[] getDeclaredMethods()：获取当前类的所有Method（不包括父类）

### Method

Method 对象包含一个方法的所有信息：
- getName() 返回方法名称；
- getReturnType() 返回方法的返回值类型，也是一个 Class 实例；
- getParameterTypes() 返回方法的参数类型，是一个 Class 数组；
- getModifiers() 返回方法的修饰符，它是一个int，不同的bit表示不同的含义；

### 调用方法

`Method.invoke(instance, param)` Method 提供了 invoke 方法来调用对应方法，类似于 js 中的 apply，第一个参数是对象实例，后面的参数要与方法参数一致，否则报错。
如果 Method 是一个静态方法， 第一个参数传入 null 。
如果 Method 是一个 `private` 方法，在其他地方调用时需要设置 `Method.setAccessible(true)`，否则会得到 `IllegalAccessException` 异常。

## 获取构造方法

通过 Class 实例可以获取构造函数

- getConstructor(Class...)：获取某个public的Constructor；
- getDeclaredConstructor(Class...)：获取某个Constructor；
- getConstructors()：获取所有public的Constructor；
- getDeclaredConstructors()：获取所有Constructor。

### newInstance

`Class.newInstance` 方法可以创建对应的 class 实例，==但是它只能调用`public`的无参数构造方法==
```java
public class Main {
    public static void main(String[] args) throws Exception {
        // 获取构造方法Integer(int):
        Constructor cons1 = Integer.class.getConstructor(int.class);
        // 调用构造方法:
        Integer n1 = (Integer) cons1.newInstance(123);
        System.out.println(n1);

        // 获取构造方法Integer(String)
        Constructor cons2 = Integer.class.getConstructor(String.class);
        Integer n2 = (Integer) cons2.newInstance("456");
        System.out.println(n2);
    }
}
```

## 获取继承关系

getSuperclass() 可以获取到当前类的父类；

getInterfaces() 可以获取到当前类的所有接口；
```java
public class Main {
    public static void main(String[] args) throws Exception {
        Class s = Integer.class;
        Class[] is = s.getInterfaces();
        for (Class i : is) {
            System.out.println(i);
        }
    }
}
```

通过Class对象的isAssignableFrom()方法可以判断一个向上转型是否可以实现。