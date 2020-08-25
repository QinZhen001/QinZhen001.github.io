---
layout:     post
title:      "node调试工具--nodemon"
date:       2018-02-05 13:58:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Node
---

> “Yeah It's on. ”


## 正文
[网页链接](http://blog.csdn.net/sinat_36871349/article/details/53433324)


### Installation
```
npm install -g nodemon
```
And nodemon will be installed globally to your system path.

You can also install nodemon as a developement dependency:
```
npm install --save-dev nodemon
```

With a local installation, nodemon will not be available in your system path. Instead, the local installation of nodemon can be run by calling it from within an npm script (such as npm start) or using npx nodemon.



### Usage

nodemon wraps your application, so you can pass all the arguments you would normally pass to your app:
```
nodemon [your node app]
```

Using nodemon is simple, if my application accepted a host and port as the arguments, I would start it as so:
```
nodemon ./server.js localhost 8080
```


#### Running non-node scripts

nodemon can also be used to execute and monitor other programs. nodemon will read the file extension of the script being run and monitor that extension instead of .js if there's no nodemon.json:

```
nodemon --exec "python -v" ./app.py
```






>npm地址
https://www.npmjs.com/package/nodemon