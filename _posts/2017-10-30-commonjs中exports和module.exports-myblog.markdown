---
layout:     post
title:      "commonjs中exports和module.exports"
date:       2017-10-30 20:31:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Node
---

> “Yeah It's on. ”


## 正文
 
[网页链接](https://www.ycjcl.cc/2017/02/10/module-exportshe-exportsde-qu-bie/)

### Module.exports
module.exports对象是由模块系统创建的。
有时这是难以接受的；许多人希望他们的模块成为某个类的实例。 为了实现这个，需要将期望导出的对象赋值给module.exports。 注意，将期望的对象赋值给exports会简单地重新绑定到本地exports变量上，这可能不是你想要的。

### exports
exports变量是在模块的文件级别作用域内有效的，它在模块被执行前被赋于 module.exports 的值。它有一个快捷方式，以便 module.exports.f = ... 可以被更简洁地写成exports.f = ...。 注意，就像任何变量，如果一个新的值被赋值给exports，它就不再绑定到module.exports(其实是exports.属性会自动挂载到没有命名冲突的module.exports.属性)

### require

从require导入方式去理解，关键有两个变量(全局变量module.exports，局部变量exports)、一个返回值(module.exports)


```
console.log(exports); // {}  
console.log(module.exports);  // {}  
console.log(exports === module.exports);    // true  
console.log(exports == module.exports);        // true  
/**
 Module {
  id: '.',
  exports: {},
  parent: null,
  filename: '/1.js',
  loaded: false,
  children: [],
  paths:
   [ 
     '/node_modules' ] 
 }
 */
console.log(module);
```

1. 每个js文件一创建，都有一个var exports = module.exports = {};，使exports和module.exports都指向一个空对象。
2. module是全局内置对象，exports是被var创建的局部对象。
3. module.exports和exports所指向的内存地址相同
 **(exports 是指向的 module.exports 的引用)**


----------


```
// 2.js
exports.id = 'exports的id';  
exports.id2 = 'exports的id2';  
exports.func = function(){  
    console.log('exports的函数');
};
exports.func2 = function() {  
    console.log('exports的函数2');
};
module.exports = {  
    id: 'module.exports的id',
    func:function(){
        console.log('module.exports的函数');
    }

};
```

```
// 3.js
var a = require('./2.js');  
// 当属性和函数在module.exports都有定义时：
console.log(a.id);  // module.exports的id  
console.log(a.func()); // module.exports的函数

// 当属性在module.exports没有定义，函数在module.exports有定义
console.log(a.id2);  // undefined  
console.log(a.func());  // module.exports的函数

// 当函数在module.exports没有定义，属性在module.exports有定义
console.log(a.id);        // module.exports的id  
console.log(a.func2());    // 报错了 TypeError: a.func2 is not a function  
```

1. module.exports像是exports的大哥，当module.exports以{}整体导出时会覆盖exports的属性和方法，
2. 注意，若只是将属性/方法挂载在module.exports./exports.上时，exports.id=1和module.exports.id=100，module.exports.id=function(){}和exports.id=function(){}，最后id的值取决于exports.id和module.exports.id的顺序，谁在后，就是最后的值
3. 若exports和module.exports同时赋值时，exports所使用的属性和方法必须出现在module.exports，若属性没有在module.exports中定义的话，出现undefined，若方法没有在module.exports中定义，会抛出TypeError错误。


----------
```
// 4.js
var a = require('./5.js');  
// 若传的是类，new一个对象
var person = new a('Kylin',20);  
console.log(person.speak()); // my name is Kylin ,my age is 20

// 若不需要在构造函数时初始化参数，直接调用方法/属性
// a.speak();  // my name is kylin ,my age is 20
```

```
// 5.js
// Person类
function Person(name,age){  
    this.name = name;
    this.age = age;
}
// 为类添加方法
Person.prototype.speak = function(){  
    console.log('my name is '+this.name+' ,my age is '+this.age);
};

// 返回类
module.exports = Person;

// 若构造函数没有传入参数(name,age)，直接传入对象
// module.exports = new Person('kylin',20);
```

**说了这么多，其实建议就是，如果只是单一属性或方法的话，就使用exports.属性/方法。要是导出多个属性或方法或使用对象构造方法，结合prototype等，就建议使用module.exports = {}**



## 补充


### require源码粗读

[https://github.com/CommanderXL/biu-blog/issues/24](https://github.com/CommanderXL/biu-blog/issues/24)


在我们的node.js程序当中，我们使用require这个看起来是全局(后面会解释为什么看起来是全局的)的方法去加载其他模块。


```
const util = require('./util')
```

首先我们来看下关于这个方法，node.js内部是如何定义的：

```javascript
Module.prototype.require = function () {
  assert(path, 'missing path');
  assert(typeof path === 'string', 'path must be a string');
  // 实际上是调用Module._load方法
  return Module._load(path, this, /* isMain */ false);
}

Module._load = function (request, parent, isMain) {
  .....

  // 获取文件名
  var filename = Module._resolveFilename(request, parent, isMain);

  // _cache缓存的模块
  var cachedModule = Module._cache[filename];
  if (cachedModule) {
    updateChildren(parent, cachedModule, true);
    return cachedModule.exports;
  }

  // 如果是nativeModule模块
  if (NativeModule.nonInternalExists(filename)) {
    debug('load native module %s', request);
    return NativeModule.require(filename);
  }

  // Don't call updateChildren(), Module constructor already does.
  // 初始化一个新的module
  var module = new Module(filename, parent);

  if (isMain) {
    process.mainModule = module;
    module.id = '.';
  }

  // 加载模块前，就将这个模块缓存起来。注意node.js的模块加载系统是如何避免循环依赖的
  Module._cache[filename] = module;

  // 加载module
  tryModuleLoad(module, filename);

  // 将module.exports导出的内容返回
  return module.exports;
}
```


Module._load方法是一个内部的方法，主要是:



* 根据你传入的代表模块路径的字符串来查找相应的模块路径;
* 根据找到的模块路径来做缓存;
* 进而去加载对应的模块。


接下来我们来看下node.js是如何根据传入的模块路径字符串来查找对应的模块的：



```javascript
Module._resolveFilename = function (request, parent, isMain, options) {
  if (NativeModule.nonInternalExists(request)) {
    return request;
  }

  var paths;

  if (typeof options === 'object' && options !== null &&
      Array.isArray(options.paths)) {
    ...
  } else {
    // 获取模块的大致路径 [parentDir]  | [id, [parentDir]]
    paths = Module._resolveLookupPaths(request, parent, true);
  }

  // look up the filename first, since that's the cache key.
  // node index.js
  // request = index.js
  // paths = ['/root/foo/bar/index.js', '/root/foo/bar']
  var filename = Module._findPath(request, paths, isMain);
  if (!filename) {
    var err = new Error(`Cannot find module '${request}'`);
    err.code = 'MODULE_NOT_FOUND';
    throw err;
  }
  return filename;
}
```



在这个方法内部，需要调用一个内部的方法：Module._resolveLookupPaths，这个方法会依据父模块的路径获取所有这个模块可能的路径：


```javascript
Module._resolveLookupPaths = function (request, parent, newReturn) {
  ...
}
```

这个方法内部有以下几种情况的处理：


是启动模块，即通过node xxx启动的模块,这个时候node.js会直接获取到你这个程序执行路径，并在这个方法当中返回



require(xxx) require一个存在于node_modules中的模块,这个时候会对执行路径上所有可能存在node_modules的路径进行遍历一遍

require(./) require一个相对路径或者绝对路径的模块 直接返回父路径


----------


当拿到需要找寻的路径后，调用Module._findPath方法去查找对应的文件路径。



```javascript
Module._findPath = function (request, paths, isMain) {
  if (path.isAbsolute(request)) {
    paths = [''];
  } else if (!paths || paths.length === 0) {
    return false;
  }

  // \x00 -> null，相当于空字符串
  var cacheKey = request + '\x00' +
                (paths.length === 1 ? paths[0] : paths.join('\x00'));
  // 路径的缓存
  var entry = Module._pathCache[cacheKey];
  if (entry)
    return entry;

  var exts;
  // 尾部是否带有/
  var trailingSlash = request.length > 0 &&
                      request.charCodeAt(request.length - 1) === 47/*/*/;

  // For each path
  for (var i = 0; i < paths.length; i++) {
    // Don't search further if path doesn't exist
    const curPath = paths[i];   // 当前路径
    if (curPath && stat(curPath) < 1) continue;
    var basePath = path.resolve(curPath, request);
    var filename;

    // 调用internalModuleStat方法来判断文件类型
    var rc = stat(basePath);
    // 如果路径不以/结尾，那么可能是文件，也可能是文件夹
    if (!trailingSlash) {
      if (rc === 0) {  // File.  文件
        if (preserveSymlinks && !isMain) {
          filename = path.resolve(basePath);
        } else {
          filename = toRealPath(basePath);
        }
      } else if (rc === 1) {  // Directory. 当提供的路径是文件夹的情况下会去这个路径下找package.json中的main字段对应的模块的入口文件
        if (exts === undefined)
          // '.js' '.json' '.node' '.ms'
          exts = Object.keys(Module._extensions);
        // 获取pkg内部的main字段对应的值
        filename = tryPackage(basePath, exts, isMain);
      }

      if (!filename) {
        // try it with each of the extensions
        if (exts === undefined)
          exts = Object.keys(Module._extensions);
        filename = tryExtensions(basePath, exts, isMain); // ${basePath}.(js|json|node)等文件后缀，看是否文件存在
      }
    }

    // 如果路径以/结尾，那么就是文件夹
    if (!filename && rc === 1) {  // Directory.
      if (exts === undefined)
        exts = Object.keys(Module._extensions);
      filename = tryPackage(basePath, exts, isMain) ||
        // try it with each of the extensions at "index"
        tryExtensions(path.resolve(basePath, 'index'), exts, isMain);
    }

    if (filename) {
      // Warn once if '.' resolved outside the module dir
      if (request === '.' && i > 0) {
        if (!warned) {
          warned = true;
          process.emitWarning(
            'warning: require(\'.\') resolved outside the package ' +
            'directory. This functionality is deprecated and will be removed ' +
            'soon.',
            'DeprecationWarning', 'DEP0019');
        }
      }

      // 缓存路径
      Module._pathCache[cacheKey] = filename;
      return filename;
    }
  }
  return false;
}

function tryPackage(requestPath, exts, isMain) {
  var pkg = readPackage(requestPath); // 获取package.json当中的main字段

  if (!pkg) return false;

  var filename = path.resolve(requestPath, pkg);  // 解析路径
  return tryFile(filename, isMain) ||             // 直接判断这个文件是否存在
         tryExtensions(filename, exts, isMain) || // 判断这个分别以js,json,node等后缀结尾的文件是否存在
         tryExtensions(path.resolve(filename, 'index'), exts, isMain);  // 判断这个分别以 ${filename}/index.(js|json|node)等后缀结尾的文件是否存在
}
```


梳理下上面查询模块时的一个策略：

**require模块的时候，传入的字符串最后一个字符不是/时：**


* 如果是个文件，那么直接返回这个文件的路径
* 如果是个文件夹，那么会找个这个文件夹下是否有package.json文件，以及这个文件当中的main字段对应的路径(对应源码当中的方法为tryPackage)：
  * 如果main字段对应的路径是一个文件且存在，那么就返回这个路径
  * main字段对应的路径对应没有带后缀，那么尝试使用.js，.json，.node，.ms后缀去加载对应文件
  * 如果以上2个条件都不满足，那么尝试对应路径下的index.js，index.json，index.node文件
* 如果以上2个方法都没有找到对应文件路径，那么就对文件路径后添加分别添加.js，.json，.node，.ms后缀去加载对应的文件(对应源码当中的方法为tryExtensions)



**require模块的时候，传入的字符串最后一个字符是/时，即require的是一个文件夹时：**


* 首先查询这个文件夹下的package.json文件中的main字段对应的路径，具体的流程方法和上面说的查找package.json文件的一致
* 查询当前文件下的index.js，index.json，index.node等文件

当找到文件的路径后就调用tryModuleLoad开始加载模块了，这个方法内部实际上是调用了模块实例的load方法：

```javascript
Module.prototype.load = function () {

  ...
  this.filename = filename;
  // 定义module的paths。获取这个module路径上所有可能的node_modules路径
  this.paths = Module._nodeModulePaths(path.dirname(filename));

  var extension = path.extname(filename) || '.js';
  if (!Module._extensions[extension]) extension = '.js';
  // 开始load这个文件
  Module._extensions[extension](this, filename);
  this.loaded = true;

  ...
}
```


调用Module._extension方法去加载不同格式的文件，就拿js文件来说：


```javascript
Module._extensions['.js'] = function(module, filename) {
  // 首先读取文件的文本内容
  var content = fs.readFileSync(filename, 'utf8');
  module._compile(internalModule.stripBOM(content), filename);
};
```



内部调用了Module.prototype._compile这个方法：

```javascript
Module.prototype._compile = function (content, filename)) {
  content = internalModule.stripShebang(content);

  // create wrapper function
  // 将源码的文本包裹一层
  var wrapper = Module.wrap(content);

  // vm.runInThisContext在一个v8的虚拟机内部执行wrapper后的代码
  var compiledWrapper = vm.runInThisContext(wrapper, {
    filename: filename,
    lineOffset: 0,
    displayErrors: true
  });

  var inspectorWrapper = null;
  if (process._breakFirstLine && process._eval == null) {
    if (!resolvedArgv) {
      // we enter the repl if we're not given a filename argument.
      if (process.argv[1]) {
        resolvedArgv = Module._resolveFilename(process.argv[1], null, false);
      } else {
        resolvedArgv = 'repl';
      }
    }

    // Set breakpoint on module start
    if (filename === resolvedArgv) {
      delete process._breakFirstLine;
      inspectorWrapper = process.binding('inspector').callAndPauseOnStart;
    }
  }
  var dirname = path.dirname(filename);
  // 构造require函数
  var require = internalModule.makeRequireFunction(this);
  var depth = internalModule.requireDepth;
  if (depth === 0) stat.cache = new Map();
  var result;
  if (inspectorWrapper) {
    result = inspectorWrapper(compiledWrapper, this.exports, this.exports,
                              require, this, filename, dirname);
  } else {
    // 开始执行这个函数
    // 传入的参数依次是 module.exports / require / module / filename / dirname
    result = compiledWrapper.call(this.exports, this.exports, require, this,
                                  filename, dirname);
  }
  if (depth === 0) stat.cache = null;
  return result;
}

Module.wrap = function(script) {
  return Module.wrapper[0] + script + Module.wrapper[1];
};

Module.wrapper = [
  '(function (exports, require, module, __filename, __dirname) { ',
  '\n});'
];
```



* 通过Module.wrap将源码包裹一层(遵循commonJS规范)
* 通过调用vmv8虚拟机暴露出来的方法来构造一个新的函数
* 完成函数的调用



通过源码发现，Module.wrapper在对源码文本进行包裹的时候，传入了5个参数:


exports

是对于第三个参数module的exports属性的引用

require

这个require并非是Module.prototype.require方法，而是通过internalModule.makeRequireFunction重新构造出来的，这个方法内部还是依赖Module.prototype.require方法去加载模块的，同时还对这个require方法做了一些拓展。

module

module对象，如果需要向外暴露API供其他模块来使用，需要在module.exports属性上定义

__filename

当前文件的绝对路径


__dirname


当前文件的父文件夹的绝对路径

---



**exports 和 module.exports的关系**


特别注意第一个参数和第三参数的联系：第一参数是对于第三个参数的exports属性的引用。一旦将某个模块exports赋值给另外一个新的对象，那么就断开了exports属性和module.exports之间的引用关系，同时在其他模块当中也无法引用在当前模块中通过exports暴露出去的API，对于模块的引用始终是获取module.exports属性







### ESmodule和CommonJS互转


[https://www.zhihu.com/question/288322186](https://www.zhihu.com/question/288322186)


道理上,ES module转成CommonJS,其default导出只能映射到module.exports.default上不能映射到CommonJS传统的module.exports上,因为ES module可以同时有命名导出,你让module.exports既是一个导出值,又是一个namespace object。(什么?你说在默认导数上加个其他属性又不会死?嗯,那你考虑下默认导出为primitive值甚至为null的情况)




