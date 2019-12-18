---
title: Git之重新提交
date: 2019-05-21 14:24:23
tags: [Git]
categories: Git
thumbnail: https://tva1.sinaimg.cn/large/007rAy9hgy1g38x9b8d9mj31hc0u0b2a.jpg
---

## Git想要重新提交怎么办？

<!-- more -->

有时候，我们完成某次提交后，发现提交信息错误，或者是某些地方忘了修改。这个时候我们可以重新生成一次提交

## 命令介绍

```text
$ git commit --amend
```
我们可以使用该命令完成重新提交，有两点需要注意：

1. 如果上次提交完后马上执行此命令，则快照会保持不变，最终只是修改了提交信息而已
2. 执行此命令后，会弹出文本编辑器来编辑提交信息，我们可以看到之前的提交信息，编辑保存后可覆盖原先的提交信息

## 实战演示

有`test.txt`，其内容如下：

<div align="center">![test.txt](https://tva1.sinaimg.cn/large/007rAy9hgy1g38xstoq4dj30ow0hujsd.jpg)

第一次提交并查看提交历史：

<div align="center">![第一次提交](https://tva1.sinaimg.cn/large/007rAy9hgy1g38xys10b9j30ow0huwg1.jpg)

此时我发现我少做了些修改，于是我将`test.txt`修改：

<div align="center">![修改后的内容](https://tva1.sinaimg.cn/large/007rAy9hgy1g38y2iprtej30ow0huwfk.jpg)

然后我们用上面的命令进行重新提交：

<div align="center">![重新提交信息编辑](https://tva1.sinaimg.cn/large/007rAy9hgy1g38y4t8zn4j30ow0huq46.jpg)

执行完`git commit --amend`命令后，弹出上边的文本编辑器来编辑提交信息，可以看到我们之前的提交信息`first commit`，我们编辑重新提交的信息为`commit again`，然后查看提交历史：

<div align="center">![重新提交后查看](https://tva1.sinaimg.cn/large/007rAy9hgy1g38ybkwqhcj30ow0huabd.jpg)

可以看到我们重新生成了一次提交，覆盖掉了原先的那次提交