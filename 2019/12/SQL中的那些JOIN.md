---
title: SQL中的那些JOIN
date: 2019-05-17 09:42:10
tags: 数据库
categories: 数据库
thumbnail: https://tva1.sinaimg.cn/large/007rAy9hgy1g37ju6fvxej31hc0u04oa.jpg
---

## 前言

之前被SQL中的各种JOIN绕晕了头，所以特此用一篇博文来搞清楚SQL JOINs.

<!-- more -->

## 概述

我们将讨论从两个关系表中返回数据的七种不同方法，来看看我们将要讨论的七种JOIN：

1. INNER JOIN
2. LEFT JOIN
3. RIGHT JOIN
4. OUTER JOIN
5. LEFT JOIN EXCLUDING INNER JOIN
6. RIGHT JOIN EXCLUDING INNER JOIN
7. OUTER JOIN EXCLUDING INNER JOIN

## INNER JOIN

<div align=center>![INNER JOIN](https://tva1.sinaimg.cn/large/007rAy9hgy1g37juj7mqij307604u3yb.jpg)

这是最简单、最容易理解也是最常见的一种JOIN。该查询将返回左表（表A）和右表（表B）匹配到的记录，语法如下：
```mysql
SELECT <select_list> 
FROM Table_A A
INNER JOIN Table_B B
ON A.Key = B.Key
```

## LEFT JOIN

<div align=center>![LEFT JOIN](https://tva1.sinaimg.cn/large/007rAy9hgy1g37juwlujqj307604u0sj.jpg)

无论这些记录中的任何记录是否与右表（表B）匹配，此查询都将返回左表（表A）中的所有记录。它还将返回右表中的所有匹配到的记录。语法如下：
```mysql
SELECT <select_list>
FROM Table_A A
LEFT JOIN Table_B B
ON A.Key = B.Key
```

## RIGHT JOIN

<div align=center>![RIGHT JOIN](https://tva1.sinaimg.cn/large/007rAy9hgy1g37jv5v1xoj307604u0sj.jpg)

无论这些记录中的任何记录是否与左表（表A）匹配，此查询将返回右表（表B）中的所有记录。它还将返回左表中所有匹配到的记录。语法如下：
```mysql
SELECT <select_list>
FROM Table_A A
RIGHT JOIN Table_B B
ON A.Key = B.Key
```

## OUTER JOIN

<div align="center">![FULL_OUTER_JOIN.png](https://tva1.sinaimg.cn/large/007rAy9hgy1g37jvop9mlj307604u0sj.jpg)

也可以称为`FULL OUTER JOIN`或者`FULL JOIN`，该查询将会返回两张表中所有的记录。语法如下：
```mysql
SELECT <select_list>
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.Key = B.Key
```

## Left Excluding JOIN

<div align="center">![LEFT_EXCLUDING_JOIN.png](https://tva1.sinaimg.cn/large/007rAy9hgy1g37jw0u83vj307604u3yb.jpg)

该查询将返回左表（表A）中排除与右表（表B）匹配到的记录以外的所有记录。语法如下：
```mysql
SELECT <select_list> 
FROM Table_A A
LEFT JOIN Table_B B
ON A.Key = B.Key
WHERE B.Key IS NULL
```

## Right Excluding JOIN

<div align="center">![RIGHT_EXCLUDING_JOIN.png](https://tva1.sinaimg.cn/large/007rAy9hgy1g37jwbupcrj307604u0sj.jpg)

该查询将返回右表（表B）中排除与左表（表A）匹配到的记录以外的所有记录。语法如下：
```mysql
SELECT <select_list>
FROM Table_A A
RIGHT JOIN Table_B B
ON A.Key = B.Key
WHERE A.Key IS NULL
```

## Outer Excluding JOIN

<div align="center">![OUTER_EXCLUDING_JOIN.png](https://tva1.sinaimg.cn/large/007rAy9hgy1g37jwl1ebaj307604uwea.jpg)

该查询将取并集，然后去交集。语法如下：
```mysql
SELECT <select_list>
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.Key = B.Key
WHERE A.Key IS NULL OR B.Key IS NULL
```

## 示例

接下来，我们将用两张表`Table_A`，`Table_B`来进行演示以上七种JOIN，它们的数据如下：

`Table_A`：

| PK   | Value      |
| ---- | ---------- |
| 1    | FOX        |
| 2    | COP        |
| 3    | TAXI       |
| 6    | WASHINGTON |
| 7    | DELL       |
| 5    | ARIZONA    |
| 4    | LINCOLN    |
| 10   | LUCENT     |

`Table_B`：

| PK   | Value     |
| ---- | --------- |
| 1    | TROT      |
| 2    | CAR       |
| 3    | CAB       |
| 6    | MONUMENT  |
| 7    | PC        |
| 8    | MICROSOFT |
| 9    | APPLE     |
| 11   | SCOTCH    |

### INNER JOIN

```mysql
SELECT
	A.PK AS A_PK,
	A.Value AS A_Value,
    B.Value AS B_Value,
	B.PK AS B_PK
FROM Table_A A
INNER JOIN Table_B B
ON A.PK = B.PK
```

| A_PK | A_Value    | B_Value  | B_PK |
| ---- | ---------- | -------- | ---- |
| 1    | FOX        | TROT     | 1    |
| 2    | COP        | CAR      | 2    |
| 3    | TAXI       | CAB      | 3    |
| 6    | WASHINGTON | MONUMENT | 6    |
| 7    | DELL       | PC       | 7    |

### LEFT JOIN

```mysql
SELECT 
	A.PK AS A_PK, 
	A.Value AS A_Value,
	B.Value AS B_Value, 
	B.PK AS B_PK
FROM Table_A A
LEFT JOIN Table_B B
ON A.PK = B.PK
```

| A_PK | A_Value    | B_Value  | B_PK |
| ---- | ---------- | -------- | ---- |
| 1    | FOX        | TROT     | 1    |
| 2    | COP        | CAR      | 2    |
| 3    | TAXI       | CAB      | 3    |
| 4    | LINCOLN    | NULL     | NULL |
| 5    | ARIZONA    | NULL     | NULL |
| 6    | WASHINGTON | MONUMENT | 6    |
| 7    | DELL       | PC       | 7    |
| 10   | LUCENT     | NULL     | NULL |

### RIGHT JOIN

```mysql
SELECT 
	A.PK AS A_PK, 
	A.Value AS A_Value,
	B.Value AS B_Value, 
	B.PK AS B_PK
FROM Table_A A
RIGHT JOIN Table_B B
ON A.PK = B.PK
```

| A_PK | A_Value    | B_Value   | B_PK |
| ---- | ---------- | --------- | ---- |
| 1    | FOX        | TROT      | 1    |
| 2    | COP        | CAR       | 2    |
| 3    | TAXI       | CAB       | 3    |
| 6    | WASHINGTON | MONUMENT  | 6    |
| 7    | DELL       | PC        | 7    |
| NULL | NULL       | MICROSOFT | 8    |
| NULL | NULL       | APPLE     | 9    |
| NULL | NULL       | SCOTCH    | 11   |

### OUTER JOIN

```mysql
SELECT 
	A.PK AS A_PK, 
	A.Value AS A_Value,
	B.Value AS B_Value, 
	B.PK AS B_PK
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.PK = B.PK
```

| A_PK | A_Value    | B_Value   | B_PK |
| ---- | ---------- | --------- | ---- |
| 1    | FOX        | TROT      | 1    |
| 2    | COP        | CAR       | 2    |
| 3    | TAXI       | CAB       | 3    |
| 6    | WASHINGTON | MONUMENT  | 6    |
| 7    | DELL       | PC        | 7    |
| NULL | NULL       | MICROSOFT | 8    |
| NULL | NULL       | APPLE     | 9    |
| NULL | NULL       | SCOTCH    | 11   |
| 5    | ARIZONA    | NULL      | NULL |
| 4    | LINCOLN    | NULL      | NULL |
| 10   | LUCENT     | NULL      | NULL |

### LEFT EXCLUDING JOIN

```mysql
SELECT 
	A.PK AS A_PK, 
	A.Value AS A_Value,
	B.Value AS B_Value, 
	B.PK AS B_PK
FROM Table_A A
LEFT JOIN Table_B B
ON A.PK = B.PK
WHERE B.PK IS NULL
```

| A_PK | A_Value | B_Value | B_PK |
| ---- | ------- | ------- | ---- |
| 4    | LINCOLN | NULL    | NULL |
| 5    | ARIZONA | NULL    | NULL |
| 10   | LUCENT  | NULL    | NULL |

### RIGHT EXCLUDING JOIN

```mysql
SELECT 
	A.PK AS A_PK, 
	A.Value AS A_Value,
	B.Value AS B_Value, 
	B.PK AS B_PK
FROM Table_A A
RIGHT JOIN Table_B B
ON A.PK = B.PK
WHERE A.PK IS NULL
```

| A_PK | A_Value | B_Value   | B_PK |
| ---- | ------- | --------- | ---- |
| NULL | NULL    | MICROSOFT | 8    |
| NULL | NULL    | APPLE     | 9    |
| NULL | NULL    | SCOTCH    | 11   |

### OUTER EXCLUDING JOIN

```mysql
SELECT 
	A.PK AS A_PK, 
	A.Value AS A_Value,
	B.Value AS B_Value, 
	B.PK AS B_PK
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.PK = B.PK
WHERE A.PK IS NULL
OR B.PK IS NULL
```

| A_PK | A_Value | B_Value   | B_PK |
| ---- | ------- | --------- | ---- |
| NULL | NULL    | MICROSOFT | 8    |
| NULL | NULL    | APPLE     | 9    |
| NULL | NULL    | SCOTCH    | 11   |
| 5    | ARIZONA | NULL      | NULL |
| 4    | LINCOLN | NULL      | NULL |
| 10   | LUCENT  | NULL      | NULL |

## 总览

整体看下这七种JOIN：

<div align="center">![Visual_SQL_JOINS_V2.png](https://tva1.sinaimg.cn/large/007rAy9hgy1g37jwxmhczj30go0d4tcu.jpg)

## 参考

* [Visual Representation of SQL Joins](https://www.codeproject.com/Articles/33052/Visual-Representation-of-SQL-Joins)