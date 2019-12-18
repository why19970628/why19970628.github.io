---
title: Java序列化那些事儿
date: 2019-04-11 08:52:06
toc: true
tags: [Java]
categories: Java
thumbnail: https://tva1.sinaimg.cn/large/007rAy9hgy1g30kuf2vbkj31hc0u01kx.jpg
---

## 前言

对于一个Java程序员来说，Java序列化技术对于大家来说都不陌生，甚至你每天都在使用Java序列化技术，但是你真的了解它吗？本文我们将从以下几个方面来一探究竟
1. 什么是序列化
2. 如何实现序列化
3. 序列化中需要注意的几个问题

<!-- more -->




## 序列化是什么

Java序列化就是指将堆内存中的Java对象转化成一串二进制表示的字节数组，然后存储到磁盘文件，或者传递给其他网络节点（网络传输）的一个过程，也就是通过保存或转移这些字节数据来达到持久化的目的。那反序列化自然就是将二进制转化为对象的过程了。

## 如何实现序列化

想要完成对一个对象的序列化其实非常简单，你只需要实现`Serializable`接口即可,就像下面这样
```java
import java.io.Serializable;

public class User implements Serializable{

    private static final serialVersionUID = 1L;

    private String name;

    public static int age = 24;

    private transient String address = "LinFen";

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                '}';
    }
}

```

接下来我们做个试验，将User对象序列化，然后再反序列化，看看这两个对象有什么区别
```java
public class TestSerilizable {

    public static void main(String[] args) throws IOException, ClassNotFoundException {

        //原来的对象
        System.out.println("原来的User：");
        User user = new User();
        user.setName("Geek-Z");
        System.out.println(user);
        //创建序列化输出流
        ByteArrayOutputStream buff = new ByteArrayOutputStream();
        ObjectOutputStream out = new ObjectOutputStream(buff);
        //将序列化对象存入缓冲区
        out.writeObject(user);
        //修改相关值
        user.age = 25;
        user.setName("zft");
        //从缓冲区反序列化
        ObjectInputStream in = new ObjectInputStream(new ByteArrayInputStream(buff.toByteArray()));
        User newUser= (User) in.readObject();
        System.out.println("反序列化之后的newUser：");
        System.out.println(newUser);
    }

}
```

我们来看下输出的结果
```text
原来的User：
User{name='Geek-Z', age=24, address='LinFen'}
反序列化之后的newUser：
User{name='Geek-Z', age=25, address='null'}
```

到这里你可能有以下一些疑问
1. serialVersionUID有什么用
2. 为什么name的值没有发生变化，而age的值却发生了变化
3. address的值怎么变成了null

别着急，我们带着这些疑问来看看序列化中需要注意的一些事项

## 序列化ID的作用

我们将`User`类实现`Serializable`接口后，加了`serialVersionUID`字段，这就是所谓的序列化ID

```java
private static final serialVersionUID = 1L;
```  
序列化ID起着关键性的作用，它决定着反序列化成功与否。Java的序列化机制是通过判断运行时类的serialVersionUID来验证版本一致性的，在进行反序列化时，JVM会把传进来的字节流中的serialVersionUID与本地实体类中的serialVersionUID进行比较，如果相同则认为是一致的，便可以进行反序列化，否则就会报序列化版本不一致的异常。

那如果我不指定`serialVersionUID`字段呢？<br>
当你没有在实体类中显式的指定序列化ID时，Java序列化机制会根据编译时的class自动生成一个`serialVersionUID`作为序列化版本比较，这种情况下，只有同一次编译生成的class才会生成相同的`serialVersionUID`。譬如，当我们编写一个类时，随着时间s的推移，我们因为需求改动，需要在本地类中添加其他的字段，这个时候再反序列化时便会出现`serialVersionUID`不一致，导致反序列化失败。 所以习惯上我们会显式的指定序列化ID，当然你也可以使用IDE来生成一个序列化ID（目前主流IDE都有这个功能）。

## 静态变量

静态变量是属于类级别的，所以不能序列化，序列化是序列对象。

## transient关键字

上例中`address`被`transient`关键字修饰，也不能被序列化。故而反序列化以后没有对应的引用，所以为null

## 其他情况

* 当父类实现了`Serializable`接口，所有子类都可以被序列化
* 子类实现了`Serializable`接口，父类没有，父类中的属性不能序列化（不报错，数据会丢失），但是子类中属性仍能正确序列化
* 如果序列化的属性是对象，这个对象也必须实现`Serializable`接口，否则会报错
* 在反序列化时，如果对象的属性有修改或删减，修改的部分属性会丢失，但不会报错



