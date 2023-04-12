---
layout:     post
title:      "Eslint相关"
date:       2017-09-25 11:00:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Eslint
---

> “Yeah It's on. ”


## 正文
[网页链接](http://www.jianshu.com/p/2bcdce1dc8d4)

[自己配置的eslint规则](https://github.com/QinZhen001/eslint-config-qzconfig)

[ESLint - 守住优雅的护城河](https://juejin.im/post/6886265504378388487?utm_source=gold_browser_extension)

> 是一个开源的 JavaScript 的 linting 工具，使用 [espree](https://link.zhihu.com/?target=https%3A//github.com/eslint/espree) 将 JavaScript 代码解析成抽象语法树 (AST)，然后通过AST 来分析我们代码，从而给予我们两种提示：

 

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

[https://eslint.org/docs/latest/user-guide/getting-started](https://eslint.org/docs/latest/user-guide/getting-started)

运行 eslint --init 之后，.eslintrc 文件会在你的文件夹中自动创建。你可以在 .eslintrc 文件中看到许多像这样的规则：

```json
{
    "rules": {
        "semi": ["error", "always"],
        "quotes": ["error", "double"]
    }
}
```

- `"off"` or `0` - turn the rule off
- `"warn"` or `1` - turn the rule on as a warning (doesn’t affect exit code)
- `"error"` or `2` - turn the rule on as an error (exit code will be 1)







#### no-new

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

#### no-case-declarations

[https://eslint.org/docs/rules/no-case-declarations](https://eslint.org/docs/rules/no-case-declarations)

[https://segmentfault.com/q/1010000012751143](https://segmentfault.com/q/1010000012751143)

该规则禁止词法声明 (`let`、`const`、`function` 和 `class`) 出现在 `case`或`default` 子句中。原因是，词法声明在整个 `switch` 语句块中是可见的，但是它只有在运行到它定义的 `case` 语句时，才会进行初始化操作。

为了保证词法声明语句只在当前 `case` 语句中有效，将你子句包裹在块中。

> 该规则旨在避免访问未经初始化的词法绑定以及跨 `case` 语句访问被提升的函数。

```js
switch (foo) {
    case 1:
        let x = 1;
        break;
    case 2:
        const y = 2;
        break;
    case 3:
        function f() {}
        break;
    default:
        class C {}
}
```

大概是指上面`case 1`里的`x`在`case 2`里也会影响，所以要用`{}`包起来，防止`x`提升到整个`switch`语句。





### Command Line Interface

[https://eslint.org/docs/latest/user-guide/command-line-interface](https://eslint.org/docs/latest/user-guide/command-line-interface)



### --init

[https://eslint.org/docs/latest/user-guide/command-line-interface#--init](https://eslint.org/docs/latest/user-guide/command-line-interface#--init)

当我们弄好eslint配置文件后

执行：

```
eslint --init
```

This option will run `npm init @eslint/config` to start config initialization wizard. It’s designed to help new users quickly create .eslintrc file by answering a few questions, choosing a popular style guide.

The resulting configuration file will be created in the current directory.



[How would you like to use ESLint?](https://dev.to/devdammak/setting-up-eslint-in-your-javascript-project-with-vs-code-2amf)

- **To check syntax only** => it helps you correct your syntax and make sure it conform to standard.
- **To check syntax and find problems** => to help you check for syntax correctness and also help to find any problems in your code base
- **To check syntax, find problems, and enforce code style**_ => to help you check for syntax, find problem and enforce style, enforcing style means to conforms to a particular coding standard such as Airbnb, Google and other Standard coding style. But I always go for the last option the one with syntax, find problems and enforce code style



### --quiet

This option allows you to disable reporting on warnings. If you enable this option, only errors are reported by ESLint.

不展示warnings只展示errors

```bash
npx eslint --quiet file.js
```





### --cache

Store the info about processed files in order to only operate on the changed ones. The cache is stored in `.eslintcache` by default. Enabling this option can dramatically improve ESLint’s running time by ensuring that only changed files are linted.

**Note:** If you run ESLint with `--cache` and then run ESLint without `--cache`, the `.eslintcache` file will be deleted. This is necessary because the results of the lint might change and make `.eslintcache` invalid. If you want to control when the cache file is deleted, then use `--cache-location` to specify an alternate location for the cache file.



## 补充



### vs code 插件

[https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)





### eslint 和 stylelint 的区别

[https://juejin.cn/post/6844904151793532936](https://juejin.cn/post/6844904151793532936)

eslint 是什么前文已经讲述过了 

那么 stylelint  是什么呢？



- 解析器
- 插件入口
- 命名规则

与 eslint 最核心的区别，无疑就是解析器。**stylelint 所使用的解析器，是大名鼎鼎的[postcss](https://api.postcss.org/)。**如果开发过 postcss 插件就会发现，stylelint 的处理逻辑就类似于 postcss 插件。



具体实现上来说，stylelint 通过`stylelint.createPlugin`方法，接收一个 rule 回调函数，并返回一个函数。函数中可以取到所检测 css 代码的 postcss 对象，该对象可以调用 postcss 的 api 对代码进行解析、遍历、修改等操作：

```js
function rule(actual) {
  return (root, result) => {
    // root即为postcss对象
   };
}
```

相比 eslint，css 的节点类型少很多，主要有`rule`，比如`#main { border: 1px solid black; }`、`decl`，比如`color: red`、`atrule`，比如`@media`、`comment`等。



对于我们检测 css 属性值是否含有色值的需求，可以调用`root.walkDecls`对所有 css 规则做遍历：

```js
root.walkDecls((decl) => {
  if (decl) { ... }
});
```

随后，再利用[postcss-value-parser](https://github.com/TrySound/postcss-value-parser)解析出规则中的值部分，通过枚举或正则，判断是否为色值：

```js
const parsed = valueParser(decl.value);
parsed.walk((node) => {
  const { type, value, sourceIndex } = node;

  if (type === "word") {
    if (
      /^#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})$/.test(value) ||
      colorKeywords.includes(value)
    ) {
      ...
    }
  }

  if (type === "function" && /^(rgba?|hsla?)$/.test(value)) {
    ...
  }
});

```



### 自动修复

```
eslint src/** --fix
```





那对于老的项目，可能已经存在很多遗留的风格问题，导致 ESLint 检查通不过，此时又不可能把所有问题都一一修复掉，全盘阻止提交势必会造成影响。**另外对于单次提交而言，如果每次都检查 src 下的所有文件，也是没有必要的。所以我们需要使用[lint-staged](https://github.com/okonet/lint-staged)工具只针对当前修改的部分进行检测。**

```
// package.json
{
    "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,vue}": [
      "eslint --fix",
      "git add"
    ]
  }
```



### ESLint 结合 Prettier

[搞懂 ESLint 和 Prettier](https://zhuanlan.zhihu.com/p/80574300)

[https://juejin.cn/post/6924568874700505102](https://juejin.cn/post/6924568874700505102)

[为什么我不使用 Prettier](https://antfu.me/posts/why-not-prettier-zh)





ESLint 主要解决了两类问题

- 代码质量规则 (code-quality rules)

- 代码风格规则 (code-formatting rules)

**Prettier 接管了两个问题其中的代码格式的问题，而使用 Prettier + ESLint 就完完全全解决了两个问题**。

```
npm install --save-dev eslint prettier eslint-config-prettier eslint-plugin-prettier
```









[官方的推荐配置](https://www.npmjs.com/package/eslint-plugin-prettier)

项目根目录添加 .eslintrc.js 和 .prettierrc

```
// .eslintrc.js
{
  "extends": ["plugin:prettier/recommended"]
}
// .prettierrc
{
  "singleQuote": true
}
```





### **eslint-config-** 比较

[https://zenn.dev/tapioca/articles/5685d794f6452b](https://zenn.dev/tapioca/articles/5685d794f6452b)

