---
layout: java
title: java-面向对象
date: 2022-03-30 15:39:45
tags:
---

## 面向对象

> Java 是一种面向对象的编程语言。面向对象编程，英文是 Object-Oriented Programming，简称 OOP。

面向对象的基本概念，包括：类、实例、方法；

面向对象的实现方式，包括：继承、多态；

### class

Java 中使用 `class`定义类，使用 `new` 创建实例。

#### public private protected static

使用 `public` 声明的属性、方法可以在`实例`中被访问到， 使用 `private`声明的属性方法只能在当前 `class` 中被访问，使用 `protected` 的属性、方法可以在子类中访问。

使用 `static` 定义的属性或者方法属于类，所有实例访问的都是同一个，修改静态属性所有实例访问的静态属性都会改变。静态方法中无法使用 `this`，无法访问实例属性，只能访问静态属性。

如果一个类内部还定义了类，定义的类叫做`嵌套类`，嵌套类能访问类的 `private` 属性及方法。

#### this

在 `class` 中可以通过 `this` 访问当前实例。

#### 可变参数

在声明方法时，如果设置了形参，那么调用该方法必须严格按照参数的定义传入，如果不确定参数的个数可以使用 `...`表示，例如：

```java
public void setNames(String... names) {
  this.names = names;
}

```

#### 构造函数

在声明 class 时，如果没有构造函数，编译器会自动生成构造函数，如果有则不再生成。
构造方法没有返回值（也没有 void）。
class 的构造方法可以有多个，一个构造方法可以使用 `this()` 调用其他构造方法。
初始化时， class 中的属性如果没有赋值，会使用默认值，引用类型使用 null，基础类型根据类型使用默认值。

```java
public class Main {
    public static void main(String[] args) {
        Person p1 = new Person("Xiao Ming", 15); // 既可以调用带参数的构造方法
        Person p2 = new Person(); // 也可以调用无参数构造方法
    }
}

class Person {
    private String name;
    private int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return this.name;
    }

    public int getAge() {
        return this.age;
    }
}

```

#### 方法重载

和构造方法类似，类方法也可以同名，可以通过参数区分调用哪个方法。
注意：方法重载的返回值类型通常都是相同的。
方法重载的目的是，功能类似的方法使用同一名字，更容易记住，因此，调用起来更简单。

#### 继承

子类使用 `extends` 继承父类。
Java 中严禁子类定义与父类重名的字段。
一个类只能继承于一个类，不能从多个类继承。

继承链： Object -> Persion -> Student Java 中所有类继承自 Object， Object 没有父类。

子类继承父类时，子类的构造方法如果没有手动调用 `super`，编译器会自动调用父类的默认构造方法`super()`，如果父类没有默认构造方法（不接受参数的构造方法）则会编译失败。

##### 阻止继承

正常情况下，只要某个 class 没有`final`修饰符，那么任何类都可以从该 class 继承。

从 Java 15 开始，允许使用 sealed 修饰 class，并通过`permits`明确写出能够从该 class 继承的子类名称，例如：

```java
public sealed class Shape permits Rect, Circle, Triangle {
    ...
}
```

除 Rect,Circle,Triangle 之外的类无法继承 Shape 类。

在 Java 中子类可以转成父类称之为向上转型，而父类转成子类即向下转型时有可能会报错，转型前可以使用 `instanceof`判断是否是该类的实例。

```java
Student s = new Student();
Person p = s; // upcasting, ok
Object o1 = p; // upcasting, ok
```

#### 多态

> 多态是指，针对某个类型的方法调用，其真正执行的方法取决于运行时期实际类型的方法。

多态的特性就是，运行期才能动态决定调用的子类方法。对某个类型调用某个方法，执行的实际方法可能是某个子类的覆写方法。

多态体现为重载和重写。如果子类定义了一个与父类方法签名完全相同的方法，那么这就是重写，如果签名不一样就是重载。
==注意：方法名相同，方法参数相同，但方法返回值不同，也是不同的方法。在 Java 程序中，出现这种情况，编译器会报错。==
加上@Override 可以让编译器帮助检查是否进行了正确的覆写。

#### final

final 修饰符有多种作用：

1. final 修饰的方法可以阻止被覆写（重写）；

2. final 修饰的 class 可以阻止被继承；

3. final 修饰的 field 必须在创建对象时初始化，随后不可修改。

### 抽象类

如果一个 class 定义了方法，但没有具体执行代码，这个方法就是抽象方法，抽象方法用 abstract 修饰。

因为无法执行抽象方法，因此这个类也必须申明为抽象类（abstract class）。

使用 abstract 修饰的类就是抽象类。

抽象类无法被实例化。

### 接口

接口 `interface` 只能定义实例方法，不能定义实例字段，实例方法可以省略 `public abstract`. 接口可以定义静态字段（class 的字段），字段必须为常量。
一个类可以实现多个接口，使用 `implements` 关键字.
接口也可以继承，使用 `extends` 关键字，接口中方法可以使用 `default` 定义默认方法。

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Student("Xiao Ming");
        p.run();
    }
}

interface Person {
    String getName();
    default void run() {
        System.out.println(getName() + " run");
    }
}

class Student implements Person {
    private String name;

    public Student(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
}
```

### 包

Java 定义了一种名字空间，称之为包：package。一个类总是属于某个包，类名（比如 Person）只是一个简写，真正的完整类名是包名.类名。

在定义 class 的时候，需要在第一行声明这个 class 所属的包。

包可以是多层结构，用.隔开。例如：java.util。==包没有父子关系。java.util 和 java.util.zip 是不同的包，两者没有任何继承关系。==

Java 中所有 Java 文件对应的目录层次要和包的层次一致。

#### 调用

在 class 中引用其他 class，一种方法是使用完整的类名 `包名.类名`，一种方法是使用 `import`。使用 `import` 在编译时实际上也是转换成第一种写法。
例如调用 ming.Person 类：

```java
// 第一种写法
public class Student {
    public void main(String[] args){
        Person p = new ming.Person();
    }
}


// 第二种写法
import ming.Person;

public class Student {
    public void main(String[] args){
        Person p = new Person();
    }
}
```

`import 包名.*` 可以导入包里面所有的 class，但是不推荐使用，如果导入的包过多，不知道类所对应的包名。

Java 编译器最终编译出的.class 文件只使用完整类名，因此，在代码中，当编译器遇到一个 class 名称时：

1. 如果是完整类名，就直接根据完整类名查找这个 class；

2. 如果是简单类名，按下面的顺序依次查找：

   - 查找当前 package 是否存在这个 class；

   - 查找 import 的包是否包含这个 class；

   - 查找 java.lang 包是否包含这个 class。

#### 包作用域

位于同一个包的类，可以访问包作用域的字段和方法。不用 public、protected、private 修饰的字段和方法就是包作用域。

为了避免名字冲突，我们需要确定唯一的包名。推荐的做法是使用倒置的域名来确保唯一性。例如：

org.apache
org.apache.commons.log
com.liaoxuefeng.sample

### 内部类

如果一个类被定义在另一个类的内部，那么这个类叫做内部类。内部类分为 `Inner Class`, `Anonymous Class`, `Static Nested Class`。

#### Inner Class

Inner Class 的实例依附于一个 Outer Class 的实例， Inner Class 中可以使用 `Outer.this` 获取到 Outer Class 的实例。而且 Inner Class 可以访问 Outer Class 中 private 定义的属性及方法。

示例如下：

```java
public class Main {
    public static void main(String[] args) {
        Outer outer = new Outer("Nested"); // 实例化一个Outer
        Outer.Inner inner = outer.new Inner(); // 实例化一个Inner
        inner.hello();
    }
}

class Outer {
    private String name;

    Outer(String name) {
        this.name = name;
    }

    class Inner {
        void hello() {
            System.out.println("Hello, " + Outer.this.name);
        }
    }
}

```

#### Anonymous Class

匿名类可以在方法内部中创建，定义匿名类的时候必须要**实例化**它，写法如下：

```java
Runnable r = new Runnable() {
    // 实现代码
}
```

匿名类和 Inner Class 一样，可以**访问 Outer Class 的 private 字段和方法**。之所以我们要定义匿名类，是因为在这里我们通常不关心类名，比直接定义 Inner Class 可以少写很多代码。

#### Static Nested Class

静态内部类（Static Nested Class）和 Inner Class 类似，但是使用 static 修饰，它无法引用 Outer.this，但它可以访问 Outer 的 private 静态字段和静态方法。

### 模块化 classpath jar

参考文章:

- [classpath,jar](https://www.liaoxuefeng.com/wiki/1252599548343744/1260466914339296)
- [模块化](https://www.liaoxuefeng.com/wiki/1252599548343744/1281795926523938)
