---
layout:     post
title:      "less相关"
date:       2018-05-07 13:14:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”



[网页链接](https://www.imooc.com/wenda/detail/348038?block_id=tuijian_yw)



## 路径问题

Vue组件引入less文件，图片路径出现问题


less文件的mixin.less文件
```
.bg-image(@url) {
  background-image: url("@{url}@2x.png");
  @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3) {
    background-image: url("@{url}@3x.png");
  }
}
```


header组件的vue文件导入mixin.less
header.vue使用.bg-images(@url)

出现问题
**.bg-image('brand')编译出来的图片的路径是less目录下**

最终解决

```
.bg-image(@url){
 background-image:~"url(@{url}@2x.png)";
 @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3){
   background-image:~"url(@{url}@3x.png)";
 }
}
```

## LESS中关于‘~’符号的小问题

在.less文件中写：
```
.test_03{  
    width:calc(300px-30px);  
} 
```
将在相应的.css中得到如下效果：
```
    .test_03 {  
      width: calc(270px);  
    }  
```
也就是说此时的300px-30px已由编译软件计算得出。



而在.less文件中写（加上~）：
```
    .test_03{  
        width:~'calc(300px-30px)';  
    }  
```
在相应的.css中会得到：
```
    .test_03 {  
      width: calc(300px-30px);  
    }  
```
也就是说让浏览器去计算300px-30px。

## less字符串拼接

[网页链接](https://blog.csdn.net/butterfly5211314/article/details/72667273)


```
@iconUrl: "/img";

// mixin
.c-icon(@bgImg) {
    background-image: url(@bgImg);
}


.bg {
    @someImgUrl: "/icon.png";

    // call mixin .c-icon();
    .c-icon("@{iconUrl}@{someImgUrl}");

    // or below
    // .c-icon("@{iconUrl}/icon.png");
}
```

**字符串拼接中取字符串以@{varName}这种形式即可**


---



>在stylus中变量可以直接拼接成字符串
```
bg-image($url)
  background-image: url($url + "@2x.png")
  @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3)
    background-image: url($url + "@3x.png")
```

## modifyVars

使用modifyVars可以在运行时修改LESS变量。当用新的变量值调用了这个函数时，LESS文件将会被重新编译，但不会被重新加载。一个基本的用法示例：


```javascript
less.modifyVars({
    '@buttonFace': 'red',
    '@buttonText': '#fff'
});
```

### webpack less-loader 的modifyVars

在module rules 中的less-loader 下配置options modifyVars。 
实现 更改less 中的变量
```javascript
{
                test: /\.less/, 
                use: [
                    {
                        loader: "style-loader"
                    },
                    {
                        loader: "css-loader"
                    },
                    {
                        loader: "less-loader",
                        options: {
                            "modifyVars":{ "@test": "#ffb200",  }
                        }
                    }
                ]
            },
```


在组件中

[https://juejin.im/post/5ca41617f265da3092006155](https://juejin.im/post/5ca41617f265da3092006155)

```javascript
import React from 'react';
import { loadScript } from '../../shared/utils';
import './index.less';
const colorCluters = ['red', 'blue', 'green'];

export default class ColorPicker extends React.Component {
    handleColorChange = color => {
        const changeColor = () => {
            window.less
                .modifyVars({  // 调用 `less.modifyVars` 方法来改变变量值
                    '@primary-color': color,
                    '@bg-color': '#2f54eb',
                })
                .then(() => {
                    console.log('修改成功');
                });
        };
        const lessUrl =
            'https://cdnjs.cloudflare.com/ajax/libs/less.js/2.7.2/less.min.js';

        if (this.lessLoaded) {
            changeColor();
        } else {
            window.less = {
                async: true,
            };

            loadScript(lessUrl).then(() => {
                this.lessLoaded = true;
                changeColor();
            });
        }
    };

    render() {
        return (
            <ul className="color-picker">
                {colorCluters.map(color => (
                    <li
                        style={{ color }}
                        onClick={() => {
                            this.handleColorChange(color);
                        }}>
                        color
                    </li>
                ))}
            </ul>
        );
    }
}

```

>这是在antd ui多颜色主题中实现的



## Functions

[https://lesscss.org/functions/](https://lesscss.org/functions/)

### extract

```less
@list: apple, pear, coconut, orange;
value: extract(@list, 3);

// value: coconut;
```





## 补充


### @import引入路径中使用波浪号~
```
@import "~common/stylus/variable"
```

这个应该是webpack的路径解析相关的用法。
~这里应该是**指定的模块解析目录下进行匹配对应文件**，
webpack中可以通过resolve.modules字段修改模块目录。


在webpack.base.config.js下
```
  resolve: {
    extensions: ['.js', '.vue', '.json'],
    alias: {
      '@': resolve('src'),
      'common': resolve('src/common'),
      'components': resolve('src/components'),
      'api': resolve('src/api'),
      'base': resolve('src/base')
    }
  }
```


[https://www.npmjs.com/package/less-loader](https://www.npmjs.com/package/less-loader)


>@import引入路径中使用波浪号~，stylus和less效果一致




### 循环
```
// less官方文档给的循环实例，其实实际上这就是递归调用
.loop(@counter) when (@counter > 0) {
    width: (10px + @counter);
    .loop((@counter - 1));
}
```



### less to css online

[https://lesscss.org/less-preview/](https://lesscss.org/less-preview/)





