---
layout:     post
title:      "AST相关"
date:       2019-04-06 23:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - AST
---

> “Yeah It's on. ”




[Parser API](https://developer.mozilla.org/zh-CN/docs/Mozilla/Projects/SpiderMonkey/Parser_API#Builder_objects)


## 正文

[在线AST转换器](https://astexplorer.net/)

[https://segmentfault.com/a/1190000016231512](https://segmentfault.com/a/1190000016231512)


通过抽象语法树解析，我们可以像童年时拆解玩具一样，透视Javascript这台机器的运转，并且重新按着你的意愿来组装。


```javascript
function add(a, b) {
    return a + b
}
```

首先，我们拿到的这个语法块，是一个FunctionDeclaration(函数定义)对象。



用力拆开，它成了三块：

* 一个id，就是它的名字，即add
* 两个params，就是它的参数，即[a, b]
* 一块body，也就是大括号内的一堆东西

add没办法继续拆下去了，它是一个最基础Identifier（标志）对象，用来作为函数的唯一标志，就像人的姓名一样。

```
{
    name: 'add'
    type: 'identifier'
    ...
}
```


params继续拆下去，其实是两个Identifier组成的数组。之后也没办法拆下去了。

```javascript
[
    {
        name: 'a'
        type: 'identifier'
        ...
    },
    {
        name: 'b'
        type: 'identifier'
        ...
    }
]
```




### estree


js社区有一种非官法的语法表达标准：estree，是一种json风格的AST，现在流行的bable，eslint的实现也是基于estree。



estree是一个相对简单的静态语法描述，除了在源代码分析，转换方面有很大用处外，也可以用于语言的学习。把estree作为一个规范的快速索引，如果遇到有疑惑的地方，通过这个索引快速定位到规范的官方说明。规范里面，包含语法的静态和动态描述。


estree(es5)简要的结构如下

```javascript
Node objects
Identifier
Literal
RegExpLiteral
Programs
Functions
Statements
   Expression/Block/Empty/Debugger/With/Control/Choice/Exceptions/Loops
Declarations
    Function/Variable
Expressions
    This/Array/Object/Property/Function/Unary/Binary/AssignmentLogical/
    Logical/Member/Conditional/Call/New/Sequence
Patterns
```

JS整体语法整体分为三级别：programe：stament：expresstion;




#### **Node objects**

ESTree AST nodes are represented as Node objects, which may have any prototype inheritance but which implement the following interface:

```javascript
interface Node {
    type: string;
    loc: SourceLocation | null;
}
```


The type field is a string representing the AST variant type. Each subtype of Node is documented below with the specific string of its type field. You can use this field to determine which interface a node implements.

The loc field represents the source location information of the node. If the node contains no information about the source location, the field is null; otherwise it is an object consisting of a start position (the position of the first character of the parsed source region) and an end position (the position of the first character after the parsed source region):

```javascript
interface SourceLocation {
    source: string | null;
    start: Position;
    end: Position;
}
```



#### Literal 和 identifier


literals指那些值就是它本身的符号。而identifier或者叫标示符，是指它们的值是通过literal来表示的。




```javascript
interface Literal <: Expression {
    type: "Literal";
    value: string | boolean | null | number | RegExp;
}


A literal token. Note that a literal can be an expression.
```




```javascript
interface Identifier <: Expression, Pattern {
    type: "Identifier";
    name: string;
}

An identifier. Note that an identifier may be an expression or a destructuring pattern.
```




#### Expression

```javascript
interface Expression <: Node { }

Any expression node. Since the left-hand side of an assignment may be any expression in general, an expression can also be a pattern.
```



#### Function
```javascript
interface Function <: Node {
    id: Identifier | null;
    params: [ Pattern ];
    body: FunctionBody;
}
```


A function declaration or expression.



#### Pattern 
```javascript
interface Pattern <: Node { }
```

Destructuring binding and assignment are not part of ES5, but all binding positions accept Pattern to allow for destructuring in ES6. **Nevertheless, for ES5, the only Pattern subtype is Identifier.**

## 补充


### acorn


acorn是一个符合estree规范的高性能的的js解析器，输出的ast符合estree规范。acron也被大量我们熟悉的工具采用。




## 自己写一个babel插件

[https://juejin.im/post/5b9dbd7a5188255c6a042b71](https://juejin.im/post/5b9dbd7a5188255c6a042b71)















