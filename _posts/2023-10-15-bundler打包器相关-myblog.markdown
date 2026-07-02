---
layout:     post
title:      "esbuild相关"
date:       2023-10-15 16:06:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”





# rollup

[https://rollupjs.org/guide/en/](https://rollupjs.org/guide/en/)

[中文文档](https://www.rollupjs.com/)







## 基础 



### -- configPlugin

**Allows specifying Rollup plugins to transpile or otherwise control the parsing of your configuration file.** The main benefit is that it allows you to use non-JavaScript configuration files. For instance the following will allow you to write your configuration in TypeScript, provided you have `@rollup/plugin-typescript` installed:

使用Rollup plugin 处理 配置文件

例子：

```bash
rollup --config rollup.config.ts --configPlugin @rollup/plugin-typescript
```





 

## repl

[https://rollupjs.org/repl](https://rollupjs.org/repl)

> REPL 是一种编程环境，全称为"Read-Eval-Print Loop"，意为"读取-求值-输出循环"。它是一种交互式的编程环境，可以逐行读取代码，对代码进行求值或执行，并将结果输出到屏幕上。



## multiple input

多入口打包

```tsx
// rollup.config.ts
import { defineConfig } from 'rollup'

const config1 = defineConfig({})
// ...

export default (commandLineArgs: any): RollupOptions[] => {
  const isDev = commandLineArgs.watch

  return defineConfig([
    config1,
    config2,
    config3
  ])
}
```





## support *CommonJS*

Rollup supports *ES modules* out of the box. However, to support *CommonJS*, the following plugins are required:

- [@rollup/plugin-commonjs](https://github.com/rollup/plugins/tree/master/packages/commonjs)
- [@rollup/plugin-node-resolve](https://github.com/rollup/plugins/tree/master/packages/node-resolve)

`@rollup/plugin-commonjs` 应该在其他插件 *之前* 使用——这是为了防止其他插件进行的更改破坏了 CommonJS 检测。





## 集成三方库

[https://www.rollupjs.com/guide/tools](https://www.rollupjs.com/guide/tools)

有时候，您的项目可能需要依赖于一些 NPM 包。与 webpack 和 Browserify 等其他打包器不同，Rollup 并不能“开箱即用”地处理 NPM 包的依赖关系——我们需要添加一些配置。

当出现 Unresolved dependencies

因为 `import` 声明被转换成为 CommonJS 规范的 `require` 语句，但是三方库**并未** 被打包在 bundle 中。为此，我们需要一个插件@rollup/plugin-node-resolve,可以让 Rollup 查找到外部模块



## plugin 



### rollup-plugin-esbuild

[https://www.npmjs.com/package/rollup-plugin-esbuild](https://www.npmjs.com/package/rollup-plugin-esbuild)

[esbuild](https://github.com/evanw/esbuild) is by far one of the fastest TS/ESNext to ES6 compilers and minifier, this plugin replaces `rollup-plugin-typescript2`, `@rollup/plugin-typescript` and `rollup-plugin-terser` for you.



### rollup-plugin-visualizer

[https://www.npmjs.com/package/rollup-plugin-visualizer](https://www.npmjs.com/package/rollup-plugin-visualizer)

分析打包体积



### @rollup/plugin-commonjs

[https://www.npmjs.com/package/@rollup/plugin-commonjs](https://www.npmjs.com/package/@rollup/plugin-commonjs)

🍣 A Rollup plugin to convert CommonJS modules to ES6, so they can be included in a Rollup bundle



### @rollup/plugin-node-resolve

🍣 A Rollup plugin which locates modules using the [Node resolution algorithm](https://nodejs.org/api/modules.html#modules_all_together), for using third party modules in `node_modules`

可以让 Rollup 找到外部模块

加载node_modules里面的三方库

```ts
import { nodeResolve } from "@rollup/plugin-node-resolve";

 nodeResolve({
    browser: true,  // browser module resolutions 解析浏览器的包
    mainFields: ['browser', 'module', 'main'],
    preferBuiltins: false,
    extensions: [".mjs", ".js", ".json", ".ts"]
 }),
```

### @rollup/pluginutils

[https://www.npmjs.com/package/@rollup/pluginutils](https://www.npmjs.com/package/@rollup/pluginutils)

A set of utility functions commonly used by 🍣 Rollup plugins.

为写 rollup plugin 提供的 utils 工具函数

```tsx
export {
  addExtension,   // 增加后缀
  attachScopes,   // 依附作用域 （ast处理）
  createFilter, // 处理 include 和 exclude  (用于 transform 钩子)
  dataToEsm,  // data =>  esmodule
  extractAssignedNames,  // 获取ast的node的names
  makeLegalIdentifier, // 变成下滑线命名
  normalizePath  // 规范化路径
};
```

### Inter-plugin communication

[https://rollupjs.org/guide/en/#inter-plugin-communication](https://rollupjs.org/guide/en/#inter-plugin-communication)

```tsx
function parentPlugin() {
  return {
    name: 'parent',
    api: {
      //...methods and properties exposed for other plugins
      doSomething(...args) {
        // do something interesting
      }
    }
    // ...plugin hooks
  };
}

function dependentPlugin() {
  let parentApi;
  return {
    name: 'dependent',
    buildStart({ plugins }) {
      const parentName = 'parent';
      const parentPlugin = plugins.find(plugin => plugin.name === parentName);
      if (!parentPlugin) {
        // or handle this silently if it is optional
        throw new Error(`This plugin depends on the "${parentName}" plugin.`);
      }
      // now you can access the API methods in subsequent hooks
      parentApi = parentPlugin.api;
    },
    transform(code, id) {
      if (thereIsAReasonToDoSomething(id)) {
        parentApi.doSomething(id);
      }
    }
  };
}
```

通过提供api 一个插件可以调用另外一个插件提供的方法。

## 问题

### embed all dependencies into one fat target bundle

[embed all dependencies into one fat target bundle](https://stackoverflow.com/questions/52125190/how-to-embed-all-dependencies-into-one-fat-target-bundle-with-rollup-js)

Use [rollup-plugin-node-resolve](https://github.com/rollup/plugins/tree/master/packages/node-resolve) (and [rollup-plugin-commonjs](https://github.com/rollup/plugins/tree/master/packages/commonjs) if you have CommonJS dependencies).





# unbuild

[https://github.com/unjs/unbuild](https://github.com/unjs/unbuild)

> A unified javascript build system

Robust [rollup](https://rollupjs.org/) based bundler that supports typescript and generates commonjs and module formats + type declarations.

```tsx
// build.config.ts
import { defineBuildConfig } from 'unbuild'

export default defineBuildConfig({
  entries: [
    './src/index'
  ],
  clean: true,
  declaration: true, // generate .d.ts files
  rollup: {
    emitCJS: true,  // generate .cjs files
    commonjs: true,
    resolve: true,
    inlineDependencies: true,
  },
})
```



[ERROR] Could not resolve

哪怕配置好了 resolve 相关配置 还是会有找不到相关模块的error ，在这种情况下放弃 unbuild



# turbopack

[https://turbo.build/pack/docs/why-turbopack](https://turbo.build/pack/docs/why-turbopack)

Turbopack 是一个新兴的 JavaScript 构建工具，旨在为现代web开发提供高效的打包和编译解决方案。它是 Webpack 的后继者，采用 Rust 语言进行开发，以优化速度和性能。

1. **速度**: 由于采用了 Rust 语言和增量构建技术，Turbopack 在处理大型项目时可以显著减少构建时间。
2. **现代化**: 它专门为现代 JavaScript 框架（如 React、Vue 和 Svelte）而设计，优化了这些框架的使用体验。
3. **易用性**: Turbopack 的配置比 Webpack 更加简洁，帮助开发者更容易上手和使用。
4. **兼容性**: Turbopack 旨在与现有的工具和生态系统兼容，允许开发者逐步迁移现有项目。





# father

[https://github.com/umijs/father](https://github.com/umijs/father)

father 是一款 NPM 包研发工具，能够帮助开发者更高效、高质量地研发 NPM 包、生成构建产物、再完成发布。





# esbuild

**esbuild** 是一个现代的 JavaScript 打包工具和构建工具，专门设计用于快速构建项目。其主要特点包括：

1. **极快的构建速度**：esbuild 用 Go 语言编写，利用并发处理和其他优化技术，使其在构建速度上远超传统的 JavaScript 工具，例如 Webpack 或 Parcel。
2. **支持现代 JavaScript 功能**：esbuild 支持 ES6、TypeScript 和 JSX 等现代 JavaScript 特性，使其非常适合与现代前端开发框架（如 React 和 Vue）结合使用。
3. **简单的配置**：虽然 esbuild 支持复杂的构建需求，其配置相对简单，易于上手。许多基本用例只需要少量配置即可运行。
4. **代码拆分和树摇（Tree Shaking）**：esbuild 支持代码拆分，从而提高应用的加载性能，并能去除未使用的代码，减少最终输出的包体积。
5. **用于开发和生产**：esbuild 既可以用于快速开发（支持热重载），也能生成适合生产环境的优化代码。



## 基础

[https://esbuild.github.io/](https://esbuild.github.io/)

```tsx
esbuild src/main.js --minify --format=esm --target=es6  --bundle --outfile=build/agora-miniapp-sdk.js
```

minify:  代码压缩

format：模块化 （iife cjs esm）

target： 目标  

bundle：打包

outfile： 输出地址

### serve

[https://esbuild.github.io/api/#serve](https://esbuild.github.io/api/#serve)



### Inject

[https://esbuild.github.io/api/#inject](https://esbuild.github.io/api/#inject)

注入变量 （效果相当于webpack插件之ProvidePlugin）



### tree-shaking

[https://esbuild.github.io/api/#tree-shaking](https://esbuild.github.io/api/#tree-shaking)

默认会带tree-shaking功能



### analyze

[https://esbuild.github.io/api/#analyze](https://esbuild.github.io/api/#analyze)



### target

[https://esbuild.github.io/api/#target](https://esbuild.github.io/api/#target)





## 缺点



### tree shaking 

[esbuild doesn't remove unused imports from external modules](esbuild doesn't remove unused imports from external modules)





### target es5

[Lowering for ES5?](https://github.com/evanw/esbuild/issues/297)

**结论：esbuild 还不支持将代码转为es5**

If you use a syntax feature that esbuild doesn't yet have support for transforming to your current language target, esbuild will generate an error where the unsupported syntax is used. This is often the case when targeting the es5 language version, for example, since esbuild only supports transforming most newer JavaScript syntax features to es6.

如果您使用的语法特性esbuild还不支持转换到当前语言目标，则esbuild将在使用不支持的语法时生成一个错误。例如，当目标是es5语言版本时，通常会出现这种情况，因为esbuild只支持将大多数较新的JavaScript语法特性转换为es6。

---

举个例子：

当存在 async 函数时 使用esbuild build target 设为 es5

会存在报错：

**Transforming async functions to the configured target environment ("es5") is not supported yet**

如果要支持es5 可以使用 tsup



# swc

[https://swc.rs/](https://swc.rs/)

[https://swc.nodejs.cn/](https://swc.nodejs.cn/)

SWC 是一个基于 Rust 的可扩展平台，适用于下一代快速开发工具。比传统的 JavaScript 编译器（如 Babel）速度更快。它能够显著缩短编译时间，特别是在大型项目中。

 它被 Next.js、Parcel 和 Deno 等工具以及 Vercel、字节跳动、腾讯、Shopify 等公司使用。

缺点：

* 开发一个 SWC 的插件，首先要学习 Rust 和 WebAssembly，上手门槛明显很高



## @swc/wasm-web

[https://swc.rs/docs/usage/wasm](https://swc.rs/docs/usage/wasm)

@swc/wasm-web的作用是提供一个将JavaScript代码转换为WebAssembly二进制格式的工具。通过将JavaScript代码转换为WebAssembly，可以在Web浏览器中更高效地执行代码，提高性能和加载速度。这个工具还可以帮助开发人员利用WebAssembly的并行执行能力来加速应用程序。









# tsup

[https://tsup.egoist.dev/](https://tsup.egoist.dev/)

[必知必会tsup打包库原理分析](https://juejin.cn/post/7148442413420265485)

Bundle your TypeScript library with no config, powered by [esbuild](https://github.com/evanw/esbuild).

可以快速打包 typescript 库，无需任何配置，并且基于`esbuild`进行打包，同时也可以快速生成`ts`类型(rollup进行打包，rollup-plugin-dts这个插件进行打包)，它还支持`Cli`脚手架运行，方便又高效



## es5-support

[https://tsup.egoist.dev/#es5-support](https://tsup.egoist.dev/#es5-support)

your code will be transpiled by esbuild to es2020 first, and then transpiled to es5 by [SWC](https://swc.rs/).





## tree-shaking

[https://tsup.egoist.dev/#tree-shaking](https://tsup.egoist.dev/#tree-shaking)

esbuild has [tree shaking](https://esbuild.github.io/api/#tree-shaking) enabled by default, but sometimes it's not working very well, see [#1794](https://github.com/evanw/esbuild/issues/1794) [#1435](https://github.com/evanw/esbuild/issues/1435), so tsup offers an additional option to let you use Rollup for tree shaking instead:

esbuild 默认启用 Tree Shaking，但有时效果不太好，请参阅 #1794 #1435，因此 tsup 提供了一个附加选项，让您可以使用 Rollup 进行 Tree Shaking







# gulp





## through2

[https://www.npmjs.com/package/through2](https://www.npmjs.com/package/through2)


[https://github.com/yangblink/gulp-aliyun-oss/blob/master/index.js](https://github.com/yangblink/gulp-aliyun-oss/blob/master/index.js)

最近一直写gulp相关的东西，会用到大量gulp相关的插件，偶尔会去看下这些插件的源码，发现基本上都用到了一个库through2

```javascript
    var stream = through2.obj(function (file, enc, cb) {
        var filename = file.relative;
        var self = this;
        var getFileKey = function(){
            return option.prefix
                + ((!option.prefix || (option.prefix[option.prefix.length - 1]) === '/') ? '' : '/')
                + path.posix.relative(file.base, file.path);
        };
        
        if(file.isBuffer()){
          // console.log(filename);
          co(function* () {
            var result = yield client.put(getFileKey(), file.contents, ossOpt);
			log('OK:', colors.green(filename));
            cb(null, file);
          })
          .catch(function (err) {
            log('ERR:', colors.red(filename + "\t" + err.code));
            cb(err, null);
          })  
        }
        else {
          cb();
        }
    });
```


所以，在此深入了解下through2



through2经常被用于处理node的stream

[https://segmentfault.com/a/1190000011740894](https://segmentfault.com/a/1190000011740894)



```javascript
gulp.task('rewrite', () => {
  return gulp.src('./through/enter.txt')
    .pipe(through2.obj(function(chunk, enc, callback) {
      const { contents } = chunk;
      for (var i = 0; i < contents.length; i++) {
        if (contents[i] === 97) {
          contents[i] = 122;
        }
      }

      chunk.contents = contents;
      this.push(chunk);

      callback();
    }))
    .pipe(gulp.dest('./dist'));
});
```



这里将文件中所有的字符a转换为字符z，在写gulp插件时一定会应用到这个包，下面就来窥探一下这个使用率非常高的包。



### Transform stream


through2的源码仅仅就100多行，本质上就是对于node原生的transform流进行的封装，先来看下Transform stream。Transform是一个双工流，既可读，也可写，但是与Duplex还是有着一些区别，Duplex的写和读可以说是没有任何的关联，是两个缓冲区和管道互补干扰，而Transform将其输入和输出是存在相互关联的，中间做了处理。具体差别可以参考下面图片对比：


### through2源码

在了解Transform stream之后，through2的源码非常的简单，就是对于其的一层封装，暴露出三个api(through2，through2.obj，through2.ctor)而且三者接收的参数一致，因为都是由一个工厂方法创造出的：


```javascript
function through2 (construct) {
  return function (options, transform, flush) {
    // 做了一些参数整理
    if (typeof options == 'function') {
      flush     = transform
      transform = options
      options   = {}
    }

    if (typeof transform != 'function')
      transform = noop

    if (typeof flush != 'function')
      flush = null

    return construct(options, transform, flush)
  }
}
```

来看一下through2对于Transform stream的再加工，也就是源码中的DestroyableTransform，与其名字一样，就是一个替我们实现好了destory方法的Transform stream：


```javascript
DestroyableTransform.prototype.destroy = function(err) {
  if (this._destroyed) return
  this._destroyed = true

  var self = this
  // 触发destory后，close掉流
  process.nextTick(function() {
    if (err)
      self.emit('error', err)
    self.emit('close')
  })
}
```



through2与through2.obj全部是创造出一个再加工后的Transform，区别如下：



* 后者开启了对象模式（objectMode属性为true），写入的参数不仅仅限制在string or uint8Array
* 后者降低了阈值（highWaterMark为16，而不是默认的16kb），这样做的原因，是为了和node的默认保持一致，具体可以参见这里


through2.ctor可以用来再次定制，其返回的是一个构造函数，用法可以参考下面：




```javascript
const Tran = through.ctor(function(chunk, enc, callback) {
  console.log('transform', chunk.toString());
  callback(null, chunk);
});
const transform = new Tran();
```







# rolldown

https://rolldown.rs/

**Fast Rust-based bundler for JavaScript**
