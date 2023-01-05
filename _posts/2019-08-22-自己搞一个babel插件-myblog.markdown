---
layout:     post
title:      "自己搞一个babel插件"
date:       2019-08-22 15:03:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Babel
---

> “Yeah It's on. ”


## 正文


[https://juejin.im/post/5b9dbd7a5188255c6a042b71](https://juejin.im/post/5b9dbd7a5188255c6a042b71)


[babel-handbook](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/zh-Hans/plugin-handbook.md#toc-replacing-a-node)



### babel工作的原理



![](https://s1.ax1x.com/2020/06/02/tNiKDf.png)



**Babel对代码进行转换，会将JS代码转换为AST抽象语法树(解析)，对树进行静态分析(转换)，然后再将语法树转换为JS代码(生成)。每一层树被称为节点。每一层节点都会有type属性，用来描述节点的类型。其他属性用来进一步描述节点的类型。**




```javascript
// 将代码生成对应的抽象语法树

// 代码
const result = 1 + 1

// 代码生成的AST
{
  "type": "Program",
  "start": 0,
  "end": 20,
  "body": [
    {
      "type": "VariableDeclaration",
      "start": 0,
      "end": 20,
      "declarations": [
        {
          "type": "VariableDeclarator",
          "start": 6,
          "end": 20,
          "id": {
            "type": "Identifier",
            "start": 6,
            "end": 12,
            "name": "result"
          },
          "init": {
            "type": "BinaryExpression",
            "start": 15,
            "end": 20,
            "left": {
              "type": "Literal",
              "start": 15,
              "end": 16,
              "value": 1,
              "raw": "1"
            },
            "operator": "+",
            "right": {
              "type": "Literal",
              "start": 19,
              "end": 20,
              "value": 1,
              "raw": "1"
            }
          }
        }
      ],
      "kind": "const"
    }
  ],
  "sourceType": "module"
}
```



### astexplorer

[https://astexplorer.net/](https://astexplorer.net/)



一个简单plugin例子：

https://astexplorer.net/#/gist/c103ecf502c10b6d9e5ab69df5580a55/fc6e168a7f32738fb6bb2e0b55c5ea1ad17830fd



### 基础知识

[ https://juejin.im/post/5d94bfbf5188256db95589be ]( https://juejin.im/post/5d94bfbf5188256db95589be )



####  @babel/core 



 ‘微内核’架构中的‘内核’。对于Babel来说，这个内核主要干这些事情： 



- 加载和处理配置(config)
- 加载插件
- 调用 `Parser` 进行语法解析，生成 `AST`
- 调用 `Traverser` 遍历AST，并使用`访问者模式`应用'插件'对 AST 进行转换
- 生成代码，包括SourceMap转换和源代码生成





####  预设（Presets）

[https://www.babeljs.cn/docs/presets](https://www.babeljs.cn/docs/presets)


不想自己动手组合插件？没问题！preset 可以作为 Babel 插件的组合，甚至可以作为可以共享的 options 配置。

我们已经针对常用环境编写了一些 preset：

* @babel/preset-env
* @babel/preset-flow
* @babel/preset-react
* @babel/preset-typescript




#### 遍历


AST是树形的结构, AST的转换的步骤就是通过访问者对AST的遍历实现的。访问者会定义处理不同的节点类型的方法。遍历树形结构的同时,, 遇到对应的节点类型会执行相对应的方法。


#### **访问者**


Visitors访问者本身就是一个对象，对象上不同的属性, 对应着不同的AST节点类型。例如，AST拥有BinaryExpression(二元表达式)类型的节点, 如果在访问者上定义BinaryExpression属性名的方法, 则这个方法在遇到BinaryExpression类型的节点, 就会执行, BinaryExpression方法的参数则是该节点的路径。**注意对每一个节点的遍历会执行两次, 进入节点一次, 退出节点一次**



```javascript
const visitors = {
  enter (path) {
    // 进入该节点
  },
  exit (path) {
    // 退出该节点
  }
}
```



#### 路径


每一个节点都拥有自身的路径对象(访问者的参数, 就是该节点的路径对象), 路径对象上定义了不同的属性和方法。例如: path.node代表了该节点的子节点, path.parent则代表了该节点的父节点。path.replaceWithMultiple方法则定义的是替换该节点的方法。



**节点的路径信息, 存在于访问者的参数中, 访问者的默认的参数就是节点的路径对象**


#### 替换节点

**替换一个节点**


```javascript
BinaryExpression(path) {
  path.replaceWith(
    t.binaryExpression("**", path.node.left, t.numberLiteral(2))
  );
}
```




**用多节点替换单节点**




```javascript
ReturnStatement(path) {
  path.replaceWithMultiple([
    t.expressionStatement(t.stringLiteral("Is this the real life?")),
    t.expressionStatement(t.stringLiteral("Is this just fantasy?")),
    t.expressionStatement(t.stringLiteral("(Enjoy singing the rest of the song in your head)")),
  ]);
}
```



#### 停止遍历



如果你的插件需要在某种情况下不运行，最简单的做法是尽早写回,但是这里还是会进行下一个的BinaryExpression的遍历。

```javascript
BinaryExpression(path) {
  if (path.node.operator !== '**') return;
}
```


如何做到直接停止遍历？



**利用path.stop()**


```
const visitor = {
  FunctionDeclaration(path, state) {
    console.log("1111")
    path.stop()
  },
}
```

>这里只会输出一次 1111




## 插件


### 插件选项


如果您想让您的用户自定义您的Babel插件的行为您可以接受用户可以指定的插件特定选项，如下所示：



```javascript
{
  plugins: [
    ["my-plugin", {
      "option1": true,
      "option2": false
    }]
  ]
}
```


这些选项会通过状态对象传递给插件访问者：


```javascript
export default function({ types: t }) {
  return {
    visitor: {
      FunctionDeclaration(path, state) {
        console.log(state.opts);
        // { option1: true, option2: false }
      }
    }
  }
}
```





这些选项是特定于插件的，您不能访问其他插件中的选项。



### 插件之前或之后函数


插件可以具有在插件之前或之后运行的函数。它们可以用于设置或清理/分析目的。


```javascript
export default function({ types: t }) {
  return {
    pre(state) {
      this.cache = new Map();
    },
    visitor: {
      StringLiteral(path) {
        this.cache.set(path.node.value, 1);
      }
    },
    post(state) {
      console.log(this.cache);
    }
  };
}
```



### 插件顺序



插件的排列顺序很重要。


这意味着如果两个转换插件都将处理“程序（Program）”的某个代码片段，则将根据转换插件或 preset 的排列顺序依次执行。



* 插件在 Presets 前运行。
* 插件顺序从前往后排列。
* preset 顺序是颠倒的（从后往前）。


例子：

```javascript
{
  "plugins": ["transform-decorators-legacy", "transform-class-properties"]
}
```

先执行 transform-decorators-legacy ，在执行 transform-class-properties。



重要的时，preset 的顺序是 颠倒的。如下设置：



```javascript
{
  "presets": ["es2015", "react", "stage-2"]
}
```

将按如下顺序执行：stage-2、react 然后是 es2015。

这主要的是为了确保向后兼容，因为大多数用户将 "es2015" 排在 "stage-0" 之前。有关详细信息，请参阅 notes on potential traversal API changes。



## 遇到的问题

### 获取当前文件的文件名

[http://cn.voidcc.com/question/p-aqzjwhiv-gx.html](http://cn.voidcc.com/question/p-aqzjwhiv-gx.html)



我正在尝试为babel编写一个插件，如何可以拿到当前文件的文件名？

```javascript
export default function({ types: t }) { 
    return { 
    visitor: { 
     Identifier(path) { 
     // something here?? 
     } 
    } 
    }; 
} 
```

方法一:

```javascript
 console.log("this", this.filename)
```


方法二：


```javascript
Identifier(path, state) { 
    console.log(state.file.opts.filename); 
} 
```



### Maximum call stack size exceeded while using path.replaceWith


[https://stackoverflow.com/questions/37539432/babel-maximum-call-stack-size-exceeded-while-using-path-replacewith](https://stackoverflow.com/questions/37539432/babel-maximum-call-stack-size-exceeded-while-using-path-replacewith)

当使用path.replaceWith的时候一定要注意，是否用了相同节点替换了节点



例子：





```javascript
  FunctionDeclaration(path, state) {
    // .....
    // 这种情况会导致不断重复FunctionDeclaration
    
    
    path.replaceWith(t.functionDeclaration(t.identifier("aaa"), [], ...))
  }
```

报错：
the program crashed with Maximum call stack size exceeded





### 函数

[如何给所有的async函数添加try/catch？](https://juejin.cn/post/7155434131831128094)

**函数分为4种情况：函数声明 、箭头函数 、函数表达式 、函数为对象的方法**

```
FunctionDeclaration	函数声明	声明一个函数，例如 function
FunctionExpression	函数表达式	例如const func = function () {}
ObjectMethod	对象中定义的方法	例如 let obj = { fn () {} }
ArrowFunctionExpression	箭头函数表达式	例如const func = ()=> {}
```









