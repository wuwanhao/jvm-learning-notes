# JVM学习笔记01-JVM体系结构和双亲委派机制



## JVM生命周期

### 创建

Java虚拟机的启动是由引导类加载器创建的一个初始类来完成的

一个运行中的JVM的任务就是执行java程序

### 执行

程序开始时它执行，程序结束时他结束

`JPS`命令输出当前正在执行的Java进程

执行一个所谓的Java程序的时候，真正在执行的是一个叫做Java虚拟机的进程



### 退出

- 正常执行结束
- 出现异常而终止
- OS出现错误导致JVM终止
- 代码中主动结束JVM的执行
- JNI规范中描述的使用JNI的API加载或卸载JVM





## 体系结构

> JVM的位置

JVM 处于操作系统和 Java 程序之间（处于JRE中，JRE中包含了JVM）

> JVM的体系结构

![image-20210402195728211](C:\Users\14612\AppData\Roaming\Typora\typora-user-images\image-20210402195728211.png)

**方法区和堆中会存在垃圾回收（GC）**，而虚拟机栈、本地方法栈和程序计数器中不会存在垃圾回收（GC），因为它们不会产生垃圾



## 类加载器

![image-20210402205341879](C:\Users\14612\AppData\Roaming\Typora\typora-user-images\image-20210402205341879.png)

> 作用

加载class文件

> 分类

#### BootstrapClassLoader（启动类加载器）

`c++`编写，加载`java`核心库 `java.*`,构造`ExtClassLoader`和`AppClassLoader`。由于引导类加载器涉及到虚拟机本地实现细节，开发者无法直接获取到启动类加载器的引用，所以不允许直接通过引用进行操作

#### ExtClassLoader （标准扩展类加载器）

`java`编写，加载扩展库，如`classpath`中的`jre` ，`javax.*`或者
 `java.ext.dir` 指定位置中的类，开发者可以直接使用标准扩展类加载器。

#### AppClassLoader（系统类加载器）

```
java`编写，加载程序所在的目录，如`user.dir`所在的位置的`class
```

#### CustomClassLoader（用户自定义类加载器）

`java`编写,用户自定义的类加载器,可加载指定路径的`class`文件



## 双亲委派机制

双亲委派机制 https://blog.csdn.net/codeyanbao/article/details/82875064 

1. 类加载器收到类加载请求，
2. 将这个请求向上委托给父类加载器去完成，一直向上委托，知道委托给启动类加载器。
3. 启动类加载器检查是否能够加载当前这个类，能加载就结束，使用当前的加载器，否则，抛出异常，通知子加载器进行加载
4. 重复步骤3

## 双亲委派机制的作用

1. 防止重复加载同一个`.class`。通过委托去向上面问一问，加载过了，就不用再加载一遍。保证数据安全。
2. 保证核心`.class`不能被篡改。通过委托方式，不会去篡改核心`.class`，即使篡改也不会去加载，即使加载也不会是同一个`.class`对象了。不同的加载器加载同一个`.class`也不是同一个`Class`对象。这样保证了`Class`执行安全。