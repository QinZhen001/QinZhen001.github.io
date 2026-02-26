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



### ESLint 9 vs 8

ESLint 9 是一次重大的升级，它引入了全新的“扁平配置”系统，旨在让配置更现代、更清晰。下面这个表格能帮你快速了解核心变化。

| 特性对比           | ESLint 8                                    | ESLint 9                                                     |
| :----------------- | :------------------------------------------ | :----------------------------------------------------------- |
| **配置系统**       | 传统配置（`.eslintrc.js`等）                | **扁平配置**（`eslint.config.js`）                           |
| **插件与配置继承** | 通过字符串（如 `plugin:react/recommended`） | 通过**直接导入**（`import react from 'eslint-plugin-react'`） |
| **忽略文件**       | 使用 `.eslintignore`                        | 在配置的 `ignores`字段中定义                                 |
| **TypeScript配置** | 不支持                                      | **支持** `eslint.config.ts`                                  |
| **性能**           | -                                           | 针对大型代码库优化                                           |

- **焕然一新的配置体验**

  ESLint 9 的**扁平配置**是最大亮点。它放弃了以往多种格式的配置文件，统一使用一个名`eslint.config.js`的入口点，并且必须使用 ES 模块语法（`import/export`）。这种设计让配置结构更加扁平化和显式，你可以清晰地看到一个数组如何组合所有规则，避免了旧版可能出现的复杂继承和合并问题。

- **插件与配置的使用方式**

  在 ESLint 9 中，你不再是通过字符串名来引用插件和配置。相反，你需要先使用 `import`语句将它们导入，然后将导入的对象直接用在配置中。这种方式更符合现代的 JavaScript 开发习惯，也让配置的依赖关系一目了然。

- **忽略文件与配置方式**

  ESLint 9 不再读取项目根目录下的 `.eslintignore`文件。你需要将希望忽略的文件或目录模式直接写在配置文件顶层的 `ignores`字段中

- **新功能与优化**

  ESLint 9 增加了一些新能力，例如支持使用 TypeScript 来编写配置文件（`eslint.config.ts`）此外，它在处理大型代码库时性能有所提升，并且从 v9.26.0 开始，引入了对 MCP 的支持，这使得 AI 编程助手可以更便捷地集成 ESLint 的功能



### ESLint 配置中 extends和 plugins

- **`plugins`** 好比是**工具箱**，它为你提供了新的工具（规则），但盒子里的工具默认是未启用的。
- **`extends`** 则像是**一份现成的工具使用说明书或配方**，它基于已有的工具箱（`plugins`），告诉你应该启用哪些工具（规则）以及如何设置它们。

下面这个表格能帮你快速把握它们的核心区别。

| 特性                 | `plugins`                                                    | `extends`                                                    |
| :------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **核心作用**         | **引入能力**：扩展 ESLint 本身没有的新规则，用于支持特定技术栈（如 React、Vue、TypeScript）。 | **应用配置**：直接启用并配置好一套成熟的规则集合（包括来自插件的规则和原生规则），定义规则的错误级别和选项。 |
| **好比**             | 为你提供了一个新的、装满各种螺丝刀和扳手的工具箱。           | 一份详细的装配说明书，明确告诉你该用工具箱里的哪把螺丝刀、哪个扳手，以及拧多紧。 |
| **是否自动启用规则** | **否**。仅仅引入插件，其内部的规则默认不会被激活。           | **是**。继承一个配置后，其包含的所有规则会立即生效。         |
| **配置内容**         | 通常只包含插件名称，如 `plugins: ['vue', '@typescript-eslint']`。 | 指定配置来源，格式通常为 `extends: ['eslint:recommended', 'plugin:vue/recommended']`。 |

- **`plugins`的角色：扩展规则库**

  当你的项目使用了 ESLint 默认不支持的语法或框架（例如 Vue 的单文件组件、TypeScript 的类型注解）时，就需要对应的插件。插件（如 `eslint-plugin-vue`）定义了专门用于检查这些新语法的规则。但仅仅引入插件，就像把工具箱放在旁边，并不会自动使用里面的工具。

- **`extends`的角色：批量应用最佳实践**手动从插件的大量规则中挑选并配置是非常繁琐的。`extends`解决了这个问题。它允许你直接继承一个预先定义好的规则集

#### 实践建议与选择

- **优先使用 `extends`**：对于大多数项目，尤其是刚开始时，**优先考虑使用一个成熟的、符合你技术栈的 `extends`配置**。这能让你快速获得一套经过验证的最佳实践，避免从零开始配置数百条规则的麻烦。
- **按需引入 `plugins`**：当你使用的 `extends`配置已经包含了某个插件（如 `eslint-config-airbnb`包含了 React 相关插件），你通常不需要再单独在 `plugins`中声明它。只有在你的 `extends`没有覆盖到某个特定需求时，才需要手动添加 `plugins`并进行规则配置。
- **理解继承顺序**：`extends`数组中的配置是**从后往前覆盖**的。即后面的配置可以覆盖前面的同名规则。利用这个特性，你可以先用一个基础配置，再用一个更严格的配置来覆盖它。







### vs code 插件

[https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)



### vscode setting

```json
   // 明确指定 ESLint 要验证的语言
  "eslint.validate": [
    "javascript",
    "typescript",
    "javascriptreact",
    "typescriptreact",
    "vue", // 如果你的项目使用 Vue
    "html"
  ],
  // !!! 核心配置：保存时执行代码操作 !!!
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  // (可选) 设置 ESLint 在保存时运行，确保问题能被及时检测到
  "eslint.run": "onSave",  解释一下在vscode的setting的作用
```





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



### ESLint 和 Prettier 区别

[搞懂 ESLint 和 Prettier](https://zhuanlan.zhihu.com/p/80574300)

[https://juejin.cn/post/6924568874700505102](https://juejin.cn/post/6924568874700505102)

[为什么我不使用 Prettier](https://antfu.me/posts/why-not-prettier-zh)

ESLint 主要解决了两类问题

- 代码质量规则 (code-quality rules)

- 代码风格规则 (code-formatting rules)

ESLint是一个JavaScript代码静态分析工具，用于检查代码中的错误、潜在问题和编码规范等，可以通过配置文件对代码进行自定义规则和扩展。

Prettier是一个代码格式化工具，旨在统一、美化代码的风格和格式，能够自动调整代码的缩进、换行和其他格式选项，让代码保持一致易读。

**Prettier 接管了两个问题其中的代码格式的问题，而使用 Prettier + ESLint 就完完全全解决了两个问题**。

#### eslint-config-prettier 和 eslint-plugin-prettier



```
npm install --save-dev eslint prettier eslint-config-prettier eslint-plugin-prettier
```

- **`eslint-config-prettier`**：用于**关闭** ESLint 中所有与代码格式（格式化）相关的、可能与 Prettier 冲突的规则。
- **`eslint-plugin-prettier`**：将 Prettier 作为 ESLint 的一条规则来运行。这样，当你运行 `eslint --fix`时，它会用 Prettier 来处理代码格式问题

[官方的推荐配置](https://www.npmjs.com/package/eslint-plugin-prettier)

项目根目录添加 .eslintrc.js 和 .prettierrc

```
// .eslintrc.js  用于eslint8  
{
  "extends": ["plugin:prettier/recommended"]  //  确保这行在最后，以覆盖之前的格式规则
}
// .prettierrc
{
  "singleQuote": true
}
```

这个 `plugin:prettier/recommended`实际上做了三件事：

- 启用了 `eslint-plugin-prettier`插件。
- 在规则中设置了 `"prettier/prettier": "error"`，让 Prettier 的格式化问题在 ESLint 中报错。
- 通过 `eslint-config-prettier`关闭了 ESLint 中冲突的格式规则

eslint 9 用法参考：[https://www.npmjs.com/package/eslint-plugin-prettier](https://www.npmjs.com/package/eslint-plugin-prettier)

| 特性               | `eslint-config-prettier`                                     | `eslint-plugin-prettier`                                     |
| :----------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **核心角色**       | **冲突规则关闭器**                                           | **Prettier格式化执行器**                                     |
| **主要作用**       | 关闭ESLint中所有与代码**格式化**相关、且可能与Prettier冲突的规则。 | 将Prettier作为一条ESLint规则来运行，使代码格式问题以ESLint错误的形式报告。 |
| **好比**           | 交通协调员，让两条可能冲突的道路规则中的一条停止使用。       | 一位严格的交警，直接在新的统一规则下执法。                   |
| **工作方式**       | 一个ESLint**共享配置**，内部包含大量规则设置为 `"off"`。     | 一个ESLint**插件**，提供一条名为 `prettier/prettier`的新规则。 |
| **是否主动格式化** | **否**                                                       | **是**（当运行 `eslint --fix`时）                            |

eslint-plugin-prettier  内部会去使用 eslint-config-prettier

eslint-config-prettier 不需要主动调用

https://github.com/prettier/eslint-plugin-prettier/blob/main/recommended.js

```json
{
  "name": "eslint-plugin-prettier",
  "version": "5.5.4",
  "engines": {
    "node": "^14.18.0 || >=16.0.0"
  },
  "main": "eslint-plugin-prettier.js",
  "types": "eslint-plugin-prettier.d.ts",
  "files": [
    "eslint-plugin-prettier.d.ts",
    "eslint-plugin-prettier.js",
    "recommended.d.ts",
    "recommended.js",
    "worker.mjs"
  ],
  "peerDependencies": {
    "@types/eslint": ">=8.0.0",
    "eslint": ">=8.0.0",
    "eslint-config-prettier": ">= 7.0.0 <10.0.0 || >=10.1.0",
    "prettier": ">=3.0.0"
  },
  "peerDependenciesMeta": {
    "@types/eslint": {
      "optional": true
    },
    "eslint-config-prettier": {
      "optional": true
    }
  }, 
    "overrides": {
      "prettier": "^3.6.1"
    }
  }
}
```





### **eslint-config-** 比较

[https://zenn.dev/tapioca/articles/5685d794f6452b](https://zenn.dev/tapioca/articles/5685d794f6452b)

