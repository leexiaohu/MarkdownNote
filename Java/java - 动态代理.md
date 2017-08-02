# Java动态代理机制

[TOC]

## 一、动态代理的概念

动态代理技术就是用来产生一个对象的代理对象的。

    1、代理对象存在的价值主要用于拦截对真实业务对象的访问。
    2、代理对象应该具有和目标对象(真实业务对象)相同的方法。

## 二、动态代理实现

> 在java的动态代理机制中，有两个重要的类或接口，一个是 InvocationHandler(Interface)、另一个则是 Proxy(Class).

> 每一个动态代理类都必须要实现InvocationHandler这个接口，并且每个代理类的实例都关联到了一个handler，当我们通过代理对象调用一个方法的时候，这个方法的调用就会被转发为由InvocationHandler这个接口的 invoke 方法来进行调用。

###### 动态代理的实现步骤

- 创建一个日志处理接口`ILogPrintService`和接口的实现类`LogPrintServiceImpl`
- 创建一个`InvocationHandler` 接口的实现（处理器），复写`invoke`方法，对实际方法的调用转到对处理器`invoke`方法的调用上实现了代理
- 通过"Proxy"类提供的一个newProxyInstance方法用来创建一个对象的代理对象。
- `static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) `

`newProxyInstance`方法用来返回一个代理对象，这个方法总共有3个参数，`ClassLoader loader`用来指明生成代理对象使用哪个类装载器，`Class<?>[] interfaces`用来指明生成哪个对象的代理对象，通过接口指定，`InvocationHandler h`用来指明产生的这个代理对象要做什么事情。所以我们只需要调用`newProxyInstance`方法就可以得到某一个对象的代理对象了。

## 三、动态类加载
##### 类加载器
所有 Java 应用中的类都是被 java.lang.ClassLoader 类的一系列子类加载的。因此要想动态加载类的话也必须使用 java.lang.ClassLoader 的子类。

一个类一旦被加载时，这个类引用的所有类也同时会被加载。类加载过程是一个递归的模式，所有相关的类都会被加载。但并不一定是一个应用里面所有类都会被加载，与这个被加载类的引用链无关的类是不会被加载的，直到有引用关系的时候它们才会被加载。

##### 类加载体系
在 Java 中类加载是一个有序的体系。当你新创建一个标准的 Java 类加载器时你必须提供它的父加载器。当一个类加载器被调用来加载一个类的时候，首先会调用这个加载器的父加载器来加载。如果父加载器无法找到这个类，这时候这个加载器才会尝试去加载这个类。

##### 类加载
类加载器加载类的顺序如下： 
1、检查这个类是否已经被加载。 
2、如果没有被加载，则首先调用父加载器加载。 
3、如果父加载器不能加载这个类，则尝试加载这个类。
##### 动态类加载
动态加载一个类十分简单。你要做的就是获取一个类加载器然后调用它的 loadClass()方法。下面是个例子：
```java
public class MainClass {

  public static void main(String[] args){

    ClassLoader classLoader = MainClass.class.getClassLoader();

    try {
        Class aClass = classLoader.loadClass("com.jenkov.MyClass");
        System.out.println("aClass.getName() = " + aClass.getName());
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    }

}
```

