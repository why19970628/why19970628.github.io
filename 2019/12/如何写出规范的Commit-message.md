---
title: 如何写出规范的Commit message
date: 2019-04-02 19:24:21
toc: true
tags: [Git]
categories: Git 
thumbnail: https://tva1.sinaimg.cn/large/007rAy9hgy1g30knq1y03j31hc0u04eb.jpg
---

众所周知，Git对每一次的提交都非常的严格，如果不写 commit message就不允许提交，你可以执行以下命令来完成一次commit

```shell
$git commit -m 'first commit'
```
如果一行不够用，你可以执行以下命令

```shell
$git commit
```
此时会调用文本编辑器，比如`Vi`，让你可以输入多行的commit message

commit message具有很重要的作用，接下来我们将学习如何写出规范的commit message.

<!-- more -->

## commit message的作用

* 记录每一次的commit的详细信息，方便以后查看
* `git log`查看历史时，可过滤掉某些commit,快速定位到自己想要查阅的commit
* 可以从commit生成change log

## Angular commit message规范

目前应用最广的规范就是[Angular规范](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit#heading=h.greljkmo14y0)了，搞张图看一下
![](http://static.oschina.net/uploads/img/201601/08081241_2tdU.png)

## commit message的格式

```text
	<type>(<scope>): <subject>
	<BLANK LINE>
	<body>
	<BLANK LINE>
	<footer>
```
任何一行都不可超过100个字符，这是为了让message在github或者其他git工具上看起来更容易

一个标准的commit message包含`header`，`body`，`footer`，中间用空行分开

## Revert

如果当前commit是对之前commit A的一次revert，那本次commit的header必须以`revert`开头，紧跟着A的header，然后body内容写上`This reverts commit <hash>`，hash是commit A的SHA值

## Message header

header包含`type`，`scope`，`subject`

1. type

type用来说明本次commit类型，使用来修复bug还是用来增加新功能

* feat(feature) 新功能
* fix (fix bug)
* docs (documentation)
* style (formatting,missing semi colons, ...) 格式
* refactor 重构
* test (when adding missing tests) 
* chore (maintain) 构建过程或辅助工具的变动

2. scope

scope可以是commit更改的任何内容，代表commit影响的范围,例如数据层、控制层、视图层等。如果没有更合适的范围，你可以使用`*`

3. subject

subject是对本次commit改变的一个简短的说明

* 使用动词，第一人称现在时："change" not "changed" nor "changes"
* 首字母不要大写
* 结尾处不加句号(.)

## Message body

body内容是对本次commit的详细描述，看下以下示例

```text
	More detailed explanatory text, if necessary.  Wrap it to 
	about 72 characters or so. 

	Further paragraphs come after blank lines.

	- Bullet points are okay, too
	- Use a hanging indent
```

* 使用动词，第一人称现在时："change" not "changed" nor "changes"
* 包含代码变动的动机，以及与之前的行为形成对比

## Message footer

footer一般有两个作用

1. 不兼容的变动

所有不兼容的变动都必须在footer中提及，用`BREAKING CHANGE`开头，message的其余部分是对更改，理由和迁移说明的描述

```text
BREAKING CHANGE: isolate scope bindings definition has changed and
    the inject option for the directive controller injection was removed.
    
    To migrate the code follow the example below:
    
    Before:
    
    scope: {
      myAttr: 'attribute',
      myBind: 'bind',
      myExpression: 'expression',
      myEval: 'evaluate',
      myAccessor: 'accessor'
    }
    
    After:
    
    scope: {
      myAttr: '@',
      myBind: '@',
      myExpression: '&',
      // myEval - usually not useful, but in cases where the expression is assignable, you can use '='
      myAccessor: '=' // in directive's template change myAccessor() to myAccessor
    }
    
    The removed `inject` wasn't generaly useful for directives so there should be no code using it.

```
2. 解决issues

被修复的issues可以放在footer中，前边加上Closes关键字，像下面这样：

```text
Closes #234
```
或者像下面这样，关闭多个issues:

```text
Closes #123, #245, #992
```

## Examples

```text
feat($browser): onUrlChange event (popstate/hashchange/polling)

Added new event to $browser:
- forward popstate event if available
- forward hashchange event if popstate not available
- do polling when neither popstate nor hashchange available

Breaks $browser.onHashChange, which was removed (use onUrlChange instead)

```

```text
fix($compile): couple of unit tests for IE9

Older IEs serialize html uppercased, but IE9 does not...
Would be better to expect case insensitive, unfortunately jasmine does
not allow to user regexps for throw expectations.

Closes #392
Breaks foo.bar api, foo.baz should be used instead

```

```text
feat(directive): ng:disabled, ng:checked, ng:multiple, ng:readonly, ng:selected

New directives for proper binding these attributes in older browsers (IE).
Added coresponding description, live examples and e2e tests.

Closes #351

```

```text
style($location): add couple of missing semi colons
```

```text
docs(guide): updated fixed docs from Google Docs

Couple of typos fixed:
- indentation
- batchLogbatchLog -> batchLog
- start periodic checking
- missing brace

```

```text
feat($compile): simplify isolate scope bindings

Changed the isolate scope binding options to:
  - @attr - attribute binding (including interpolation)
  - =model - by-directional model binding
  - &expr - expression execution binding

This change simplifies the terminology as well as
number of choices available to the developer. It
also supports local name aliasing from the parent.

BREAKING CHANGE: isolate scope bindings definition has changed and
the inject option for the directive controller injection was removed.

To migrate the code follow the example below:

Before:

scope: {
  myAttr: 'attribute',
  myBind: 'bind',
  myExpression: 'expression',
  myEval: 'evaluate',
  myAccessor: 'accessor'
}

After:

scope: {
  myAttr: '@',
  myBind: '@',
  myExpression: '&',
  // myEval - usually not useful, but in cases where the expression is assignable, you can use '='
  myAccessor: '=' // in directive's template change myAccessor() to myAccessor
}

The removed `inject` wasn't generaly useful for directives so there should be no code using it.

```


