---
title: spring boot之项目结构
date: 2019-05-13 19:20:04
toc: true
tags: 
	- Java
	- Springboot
categories: 
	- Springboot
thumbnail: https://tva1.sinaimg.cn/large/007rAy9hgy1g30kv9bongj31hc0u0gwz.jpg
---

## Structuring Your Code

spring boot不会要求开发人员必须按某种项目结构来构建自己的应用，但是仍对我们提出了建议

<!-- more -->

## Using the “default” Package

当我们在声明一个类的时候，spring boot官方建议为这个类声明一个`package`。因为如果你的类没有声明`package`，那么spring boot应用在使用`@ComponentScan`，`@EntityScan`和`@SpringBootApplication`注解时将有可能出错

> 使用Java建议的`package`命名规范（使用反向域名），例如`com.example.project`

## Locating the Main Application Class

spring boot官方建议我们将主应用程序类放在相对于其他类的根包中。`@SpingBootApplication`注解通常放在我们的主程序类上，它隐含的为某些元素定义了一个基础的搜索包。例如，如果你使用`JPA`,`@SpringBootApplication`注解的主类所在的包将被用来搜索`@Entity`所注解的类。使用根包还可以保证组件扫描只应用于你的项目

## A typical layout

```java
com
 +- example
     +- myapplication
         +- Application.java
         |
         +- customer
         |   +- Customer.java
         |   +- CustomerController.java
         |   +- CustomerService.java
         |   +- CustomerRepository.java
         |
         +- order
             +- Order.java
             +- OrderController.java
             +- OrderService.java
             +- OrderRepository.java
```

`Application.java`将声明一个`main`方法，并且加上`@SpringBootApplication`注解，就像下面这样

```java
package com.example.myapplication;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

## 总结

使用spring boot进行开发的项目，尽量遵循以上规则，这样会使项目结构看起来很清晰，而且会避免一些不必要的错误