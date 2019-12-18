---
title: TCP的连接释放（四次挥手）
date: 2019-05-22 19:00:24
tags: [计算机网络]
categories: 计算机网络
thumbnail: https://tva1.sinaimg.cn/large/007rAy9hgy1g3aapgmzk8j30w80eijrc.jpg
---

建立一个TCP连接需要三次握手，而终止一个连接则需要四次挥手

<!-- more -->

## 前言

* 需要四次挥手的原意是因为TCP连接是全双工的（数据在两个方向上能同时传递），因此每个方向必须单独的关闭。
* 原则就是，当一方完成它的数据发送后就可以发送一个FIN来终止这个方向连接
* 当一端收到一个FIN，它必须通知应用层另一端已经终止了那个方向的数据传输
* 发送FIN通常是应用层关闭的结果
* 收到一个FIN只意味着这一方向上没有数据流动，一个TCP连接在接收到一个FIN后仍可以发送数据 

## 四次挥手图示

<div align="center">![四次挥手](https://tva1.sinaimg.cn/large/007rAy9hgy1g3ab62pfu9j30mn0cr0vt.jpg)

## 四次挥手详解

数据传输完成后，通信双方均可释放连接。现在A和B都处于`ESTABLISHED`

