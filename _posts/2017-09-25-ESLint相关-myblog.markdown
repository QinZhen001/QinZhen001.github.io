---
layout:     post
title:      "EsLint相关"
date:       2017-09-25 11:00:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - EsLint
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.jianshu.com/p/2bcdce1dc8d4)

[自己配置的eslint规则](https://github.com/QinZhen001/eslint-config-qzconfig)


**ESLint是一个用来识别 ECMAScript 并且按照规则给出报告的代码检测工具，使用它可以避免低级错误和统一代码的风格。**

ESLint被设计为完全可配置的，主要有两种方式来配置ESLint：

* 在注释中配置：使用JavaScript注释直接把配置嵌入到JS文件中。
* 配置文件：使用下面任一的文件来为全部的目录和它的子目录指定配置信息。


1. javascript：使用.eslintrc.js文件并导出一个包含配置的对象。
2. YAML：.eslintrc.yaml或者.eslintrc.yml
3. JSON：.eslintrc.json，并且此文件允许使用JS形式的注释
4. 废弃的用法：.eslintrc，此文件可以是JSON或者YAML
5. package.json：在package.json文件中创建eslintConfig属性，所有的配置包含在此属性中。


这些文件的优先级则是按照以上出现的顺序（.eslintrc.js > .eslintrc.yaml > .eslintrc.yml > .eslintrc.json > .eslintrc > package.json）。

----------

可以被配置的信息主要分为3类：

* Environments：你的javascript脚步将要运行在什么环境（如：nodejs，browser，commonjs等）中。
* Globals：执行代码时脚步需要访问的额外全局变量。
* Rules：开启某些规则，也可以设置规则的等级。

### 指定全局变量
可以在配置文件或注释中指定额外的全局变量，false表明变量只读：
#### 注释
```
/* global var1, var2 */
/* global var1:false, var2:false */
```

#### js 文件
```
// .eslintrc.js
module.exports = {
  globals: {
    var1: true,
    var2: true,
  },
};
```



### parserOptions

 ```js
 parserOptions: {
    parser: 'babel-eslint',
    ecmaVersion: 8,
    sourceType: 'module',
    ecmaFeatures: {
      "jsx": true
}
 ```



#### parser：babel-eslint

[ https://www.npmjs.com/package/babel-eslint ]( https://www.npmjs.com/package/babel-eslint )



**babel-eslint** allows you to lint **ALL** valid Babel code with the fantastic [ESLint](https://github.com/eslint/eslint).

Babel - ESLint允许你将所有有效的Babel代码与神奇的ESLint连接起来。


You only need to use babel-eslint if you are using types (Flow) or experimental features not supported in ESLint itself yet. Otherwise try the default parser (you don't have to use it just because you are using Babel). 



---



发现这个的原因是：

eslint报错  `Parsing error: Unexpected token =`



原因：开发环境与ESLint当前的解析功能不兼容







### 规则 Configuration

运行 eslint --init 之后，.eslintrc 文件会在你的文件夹中自动创建。你可以在 .eslintrc 文件中看到许多像这样的规则：

```json
{
    "rules": {
        "semi": ["error", "always"],
        "quotes": ["error", "double"]
    }
}
```



"semi" 和 "quotes" 是 ESLint 中 规则 的名称。第一个值是错误级别，可以使下面的值之一：



* "off" or 0 - 关闭规则
* "warn" or 1 - 将规则视为一个警告（不会影响退出码）
* "error" or 2 - 将规则视为一个错误 (退出码为1)



### Specifying Environments

```javascript
module.exports = {
  root: true,
  env: {
    node: true
  },
  'extends': [
    'plugin:vue/essential',
    '@vue/standard'
  ],
  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off'
  },
  parserOptions: {
    parser: 'babel-eslint'
  }
}
```


一个环境定义了一组预定义的全局变量。可用的环境包括：







* browser - 浏览器环境中的全局变量。
* node - Node.js 全局变量和 Node.js 作用域。
* commonjs - CommonJS 全局变量和 CommonJS 作用域 (用于 Browserify/WebPack 打包的只在浏览器中运行的代码)。
* shared-node-browser - Node.js 和 Browser 通用全局变量。
* es6 - 启用除了 modules 以外的所有 ECMAScript 6 特性（该选项会自动设置 * * ecmaVersion 解析器选项为 6）。
* worker - Web Workers 全局变量。
* amd - 将 require() 和 define() 定义为像 amd 一样的全局变量。
* mocha - 添加所有的 Mocha 测试全局变量。
* jasmine - 添加所有的 Jasmine 版本 1.3 和 2.0 的测试全局变量。
* jest - Jest 全局变量。
* phantomjs - PhantomJS 全局变量。
* protractor - Protractor 全局变量。
* qunit - QUnit 全局变量。
* jquery - jQuery 全局变量。
* prototypejs - Prototype.js 全局变量。
* shelljs - ShellJS 全局变量。
* meteor - Meteor 全局变量。
* mongo - MongoDB 全局变量。
* applescript - AppleScript 全局变量。
* nashorn - Java 8 Nashorn 全局变量。
* serviceworker - Service Worker 全局变量。
* atomtest - Atom 测试全局变量。
* embertest - Ember 测试全局变量。
* webextensions - WebExtensions 全局变量。
* greasemonkey - GreaseMonkey 全局变量。





### Configuration


运行 eslint --init 之后，.eslintrc 文件会在你的文件夹中自动创建。你可以在 .eslintrc 文件中看到许多像这样的规则：










### no-new

[网页链接](http://eslint.org/docs/rules/no-new)

The goal of using new with a constructor is typically to create an object of a particular type and store that object in a variable, such as:

var person = new Person();

It’s less common to use new and not store the result, such as:

new Person();

In this case, the created object is thrown away because its reference isn’t stored anywhere, and in many cases, this means that the constructor should be replaced with a function that doesn’t require new to be used.
Rule Details

This rule is aimed at maintaining consistency and convention by disallowing constructor calls using the new keyword that do not assign the resulting object to a variable.

Examples of incorrect code for this rule:
```
/*eslint no-new: "error"*/
new Thing();
```
Examples of correct code for this rule:
```
/*eslint no-new: "error"*/
var thing = new Thing();
Thing();
```


存在ESLint
.js中new后一定要赋值给某个变量
避免这种情况，单独加一条规则：
/* eslint-disable no-new */

```
/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App }
})
```








