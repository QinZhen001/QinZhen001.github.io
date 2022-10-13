---
layout:     post
title:      "through2"
date:       2019-07-04 16:41:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Bundler
---

> “Yeah It's on. ”



## 正文

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







