---

layout:     post
title:      "css扩展语言less和sass和scss相关"
date:       2020-05-06 13:46:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Css
---

> “Yeah It's on. ”
>



# less

[https://less.bootcss.com/#%E6%A6%82%E8%A7%88](https://less.bootcss.com/#%E6%A6%82%E8%A7%88)

Less （Leaner Style Sheets 的缩写） 是一门向后兼容的 CSS 扩展语言。



## less to css online

[https://lesscss.org/less-preview/](https://lesscss.org/less-preview/)





## functions

[https://lesscss.org/functions/](https://lesscss.org/functions/)

### extract

```less
@list: apple, pear, coconut, orange;
value: extract(@list, 3);

// value: coconut;
```





## ~符号

[https://cloud.tencent.com/developer/article/1976170](https://cloud.tencent.com/developer/article/1976170)

在.less文件中写：

```less
.test_03{  
    width:calc(300px-30px);  
} 
```

将在相应的.css中得到如下效果：

```less
    .test_03 {  
      width: calc(270px);  
    }  
```

也就是说此时的300px-30px已由编译软件计算得出。



而在.less文件中写（加上~）：

```less
    .test_03{  
        width:~'calc(300px-30px)';  
    }  
```

在相应的.css中会得到：

```less
    .test_03 {  
      width: calc(300px-30px);  
    }  
```

也就是说让浏览器去计算300px-30px。

## modifyVars

使用modifyVars可以在运行时修改LESS变量。当用新的变量值调用了这个函数时，LESS文件将会被重新编译，但不会被重新加载。一个基本的用法示例：


```javascript
less.modifyVars({
    '@buttonFace': 'red',
    '@buttonText': '#fff'
});
```

### webpack的less-loader

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





## 问题

### 路径

Vue组件引入less文件，图片路径出现问题

```less
.bg-image(@url) {
  background-image: url("@{url}@2x.png");
  @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3) {
    background-image: url("@{url}@3x.png");
  }
}
```

出现问题
**.bg-image('brand')编译出来的图片的路径是less目录下**

最终解决

```less
.bg-image(@url){
 background-image:~"url(@{url}@2x.png)";
 @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3){
   background-image:~"url(@{url}@3x.png)";
 }
}
```

### 字符串拼接

[网页链接](https://blog.csdn.net/butterfly5211314/article/details/72667273)


```less
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

```less
// less官方文档给的循环实例，其实实际上这就是递归调用
.loop(@counter) when (@counter > 0) {
    width: (10px + @counter);
    .loop((@counter - 1));
}
```

# stylus



## 字符串拼接

在stylus中变量可以直接拼接成字符串

```stylus
bg-image($url)
  background-image: url($url + "@2x.png")
  @media (-webkit-min-device-pixel-ratio: 3),(min-device-pixel-ratio: 3)
    background-image: url($url + "@3x.png")
```







# sass



## sass-modules

[https://css-tricks.com/introducing-sass-modules/](https://css-tricks.com/introducing-sass-modules/)



## @use

[https://css-tricks.com/introducing-sass-modules/](https://css-tricks.com/introducing-sass-modules/)

```scss
@use 'buttons';
```

The file is only imported once, no matter how many times you @use it in a project.

该文件仅导入一次，无论您在项目中使用它多少次。









# scss和sass

**区别：除了书写上面的区别，没有其他区别了**

*  **Sass 是以严格的缩进式语法规则来书写，不带大括号({})和分号(;)** 
*  Scss 的语法书写和我们的 CSS 语法书写方式非常类似 



## :global

全局样式

有的时候我们在module.scss想使用全局样式，不希望className被module改变

```scss
.passwordInput {

  :global(.rv-password-input__item--focus) {
    box-sizing: border-box;
    border: 2px solid #2e6cf6;
  }
}
```





## 覆盖antd样式

```tsx
  <Select rootClassName="customSelect"></Select>
```

```scss
  :global(.customSelect) {
    background-color: rebeccapurple !important;

    :global(.ant-select-selector) {
      padding-left: calc(3rem - 8px) !important;
    }
  }
```





## 变量

举一个循环的例子：

```scss
  @for $i from 0 to 5 {
    // 这里一定要用 #{} 表达式  (虽然我也不知道为什么)  
    .wave#{$i}{
      background:lighten(red, $i * 10%);
      z-index:2 + $i;
      background-size:auto 100%;
      animation:w 1s $i * 0.2s forwards infinite;
    }
  }
```

### ` #{}`

 \#{}大括号中可以是任何有效的表达式，然后计算并返回这个表达式的值 

```scss
p {
  $font-size: 12px;
  $line-height: 30px;
  font: $font-size/$line-height;
}
```



上面是一段简单的SCSS代码，声明两个变量$font-size和$line-height，并对它们赋值。

代码的本意是通过font设置当前元素的字体大小与行高，然而编译成CSS却是如下结果：

```
p {
  font: 0.4; 
}
```

很明显代码将斜杠当做了除法运算符，并计算出两个变量相除的结果。

虽然没有语法错误，但是却没有达到我们的预期效果，通过#{} 可以轻松解决上述问题，代码修改如下：

```scss
p {
  $font-size: 12px;
  $line-height: 30px;
  font: #{$font-size}/#{$line-height};
}
```

将两个变量分别放入大括号中，编程CSS代码如下：

```css
p {
  font: 12px/30px; 
}
```



## ＠at-root

[https://sass-lang.com/documentation/at-rules/at-root/](https://sass-lang.com/documentation/at-rules/at-root/)

[ https://www.sass.hk/skill/sass40.html ]( https://www.sass.hk/skill/sass40.html )

在我们CSS中有一种命名方式是[BEM](http://www.w3cplus.com/blog/tags/325.html),如：

```css
.block {} 
.block__element{} 
.block--modifier{}
```

试想我们在Sass中是否可以通过下面的方式来实现上面样式代码

```
#{&}_element{}
```

不仿我们来验证一下：

```scss
.block {
    color: red;

    #{&}__element {
        color:blue;
    }

    #{&}--modifier {
        color: orange;
    }
}
```

悲催的是，编译出来的CSS并不是我们想要的代码：

```css
.block {
  color: red;
}
.block .block__element {
    color: blue;
}
.block .block--modifier {
    color: orange;
}
```

但在LESS和Stylus中，能很好的实现BEM类名的形式。此时在想，在Sass中有没有这样的功能呢？值得幸运的是，在Sass3.3中新增加了`@at-root`特性,能实现上面BEM的特性

```scss
.block {
  color: red;
  @at-root #{&}__element {
    color: blue;
  }
  @at-root #{&}--modifier {
    color: orange;
  }
}

```

前面的例子告诉我们`@at-root`是什么。通过他可以告诉Sass，你不想嵌套选择器。当使用`&`选择器时，就算你不想嵌套选择器，Sass也会自动嵌套。但往往很多时候，我们是不想要嵌套选择器，例如BEM。使用`@at-root`和`#{&}`可以引用父（在Sass中总是引用父选择器）和插值，可以嵌套，做一些其他的事情

`@at-root`规范

内联选择器模式

SCSS

```scss
.foo {
    @at-root .bar {
        color:gray;
    }
}
```

CSS

```css
.bar {
  color: gray; 
}
```

测试用例可以说明，`@at-root`的内联选择器模式，将不会让你的选择器发生任何的嵌套，直接移除了父选择。在来看一个嵌套深一点的用例：

SCSS

```scss
.foo {
    @at-root .bar {
        color: gray;

        @at-root button{
            color:red;

            @at-root span {
                color: orange;
            }
        }
    }
}
```

CSS

```css
.bar {
  color: gray; 
}
button {
  color: red; 
}
span {
  color: orange; 
}
```

在SCSS中嵌套，使用`@at-root`内联选择器模式，编译出来的CSS无任何嵌套，让代码更加的简单。回到SCSS中的嵌套中，如果不使用`@at-root`内联选择器模式，将会按代码的层级关系一层一层往下嵌套。

`@at-root`和`&`的结合

`&`在Sass中所起的作用，文章开头就简单的进行演示了。在`@at-root`中也同样可以配合`&`一起使用，下面我们同样来看几个用例：

SCSS

```scss
.foo {
    @at-root .bar & {
        color:gray;
    }
}
```

CSS

```css
.bar .foo {
  color: gray; 
}
```

大家很容易发现，这个示例和不加`@at-root`的SCSS代码一样，可以编译出完全相同的代码：

```scss
.foo {
    .bar & {
        color:gray;
    }
}
```



同样的，`&`符和`@at-root`按下面的方式一起工作：

SCSS

```scss
.foo {
    @at-root & .bar {
        color:gray;
    }
}

.foo {
    @at-root &.bar {
        color:gray;
    }
}
```

CSS

```css
.foo .bar {
  color: gray; 
}

.foo.bar {
  color: gray; 
}
```

同样如此，这种方式与不加`@at-root`方式，运行的效果是一样的：

```scss
.foo {
    & .bar {
        color:gray;
    }
}

.foo {
    &.bar {
        color:gray;
    }
}
```

如此说明，在Sass中同时使用`@at-root`和`&`起到的作用是一样的，换句话说，这样并没有带来新的特性，而且在整个开发中还带来了额外的工作量。

@at-root`和`#{&}结合

Sass有脚本模式`#{}`，他和`&`不同之处是，`&`只用作选择器，它只能出现在一个复合的开始选择器，类似于一个类型选择器，如`a`或者`h1`。但`#{}`他表示的是一个插值，它可以用在任何地方。同样的，当`@at-root`和`#{&}`一起使用时，可以给我们的开发带来极大的方便与优势。例如：



SCSS

```scss
.foo {
    @at-root .bar #{&} {
        color: gray;
    }
}

.foo {
    @at-root #{&} .bar {
        color:gray;
    }
}
.foo {
    @at-root #{&}.bar {
        color:gray;
    }
}
```

CSS

```css
.bar .foo {
  color: gray; 
}

.foo .bar {
  color: gray; 
}

.foo.bar {
  color: gray; 
}
```

 bem时使用`@at-root`就能尽显其英雄本色



## @content 

`@content`这个是不是个类似占位用的，代表里面是自定义的的

```scss
    @-webkit-keyframes #{$name} {
        @content;
    }
```



## @debug

The @debug directive prints the value of a SassScript expression to the standard error output stream. It’s useful for debugging Sass files that have complicated SassScript going on. 

 @debug指令将SassScript表达式的值打印到标准的错误输出流。这对于调试具有复杂SassScript的Sass文件非常有用 

```scss
@debug 10em + 12em;
```

编译为

```
Line 1 DEBUG: 22em
```

## @function

Sass 支持自定义函数，并能在任何属性值或 Sass script 中使用：

```scss
$grid-width: 40px;
$gutter-width: 10px;

@function grid-width($n) {
  @return $n * $grid-width + ($n - 1) * $gutter-width;
}

#sidebar { width: grid-width(5); }
```

编译为

```css
#sidebar {
  width: 240px; 
}
```

## @each

[ https://sass-lang.com/documentation/at-rules/control/each ]( https://sass-lang.com/documentation/at-rules/control/each )

```scss
@each $var in <list or map>
```

- **$var**: 它表示变量的名称。 @each规则将**$var**设置到列表中的每个项目，并使用值**$var**输出样式。
- ` <list or map>`: 这些是SassScript表达式，将返回列表或映射。



例子：

```scss
@each $color in red, green, yellow, blue {
  .p_#{$color} {
    background-color: #{$color};
  }
}
```

转义后：

```css
.p_red {
  background-color: red; }

.p_green {
  background-color: green; }

.p_yellow {
  background-color: yellow; }

.p_blue {
  background-color: blue; }
```



----



```scss
$icons: ("eye": "\f112", "start": "\f12e", "stop": "\f12f");

@each $name, $glyph in $icons {
  .icon-#{$name}:before {
    display: inline-block;
    font-family: "Icon Font";
    content: $glyph;
  }
}
```

转义后

```css
@charset "UTF-8";
.icon-eye:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "";
}

.icon-start:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "";
}

.icon-stop:before {
  display: inline-block;
  font-family: "Icon Font";
  content: "";
}
```

## !global

此标志可以改变一个局部变量的作用域范围。


```scss
@mixin button-style {
  $btn-bg-color: lightblue;
  color: $btn-bg-color;
}
button {
  @include button-style;
}
.wrap {
  background: $btn-bg-color;
}
```

上面的代码会报错如下:

```
Error: Undefined variable: "$btn-bg-color".
```

很明显，由于变量是声明在@mixin混合器之内，所以无法在.wrap选择器中使用。

代码修改如下:

```scss
@mixin button-style {
  $btn-bg-color: lightblue !global;
  color: $btn-bg-color;
}
button {
  @include button-style;
}
.wrap {
  background: $btn-bg-color;
}
```

上面的代码编译成css代码如下:

```css
button {
  color: lightblue; 
}
.wrap {
  background: lightblue; 
}
```

---

再来看一段代码实例:

```scss
@mixin button-style {
  $btn-bg-color: lightblue !global;
  color: $btn-bg-color;
}
.wrap {
  background: $btn-bg-color;
}
```

上面的代码尽管添加了!global，但是依然会报错。

这是因为@mixin混合器如果只声明没有引用的话，那么它内部的代码是没有生效的。

## !default

[ https://blog.csdn.net/weixin_44198965/article/details/101169272 ]( https://blog.csdn.net/weixin_44198965/article/details/101169272 )

如果分配给变量的值后面添加了 !default 标志 ，这意味着该变量如果已经赋值，那么它不会被重新赋值，但是，如果它尚未赋值，那么它会被赋予新的给定值。

**如果在此之前变量已经赋值，那就不使用默认值，如果没有赋值，则使用默认值。**

----

例子：

**如果变量之前没有赋值，则使用默认值：**

```scss
/* 如果之前没有赋值,则使用默认值 */
$const: "hello" !default;

div{
    const: $const;
}
```



编译为CSS：

```css
div {
  const: "hello"; 
}
```

可以看到，const 的值为 “hello”，正确。

----

**如果在此之前已经赋值，那就不再使用默认值：**

```scss
/* 如果之前已经赋值,则不再使用默认值 */
$const: "Hi";
$const: "hello" !default;

div{
    const: $const;
}
```

编译为CSS：

```
div {
  const: "Hi"; 
}
```

可以看到，const 的值为 “Hi” ，正确。

## deep

[scss中使用/deep/深度选择器报错的解决办法](https://juejin.cn/post/7202998949455921211)

[**/deep/ 是什麼**](https://medium.com/@debbyji/deep-%E6%98%AF%E4%BB%80%E9%BA%BC-%E8%81%8A%E8%81%8A-vue-%E8%A3%A1%E7%9A%84-scoped-css-d1877f902845)

**一般用于改第三方组件的样式**

Vue为了让scoped有效会在编译的时候为每个元素添加[data-v-xxx]，当你引入第三方组件的时候，这个[data-v-xxx]只会加在组件的第一层，内层元素不会添加

```scss
// 对于element的checkbox组件
.el-checkbox-group {
    margin: 10px;
    .el-checkbox.is-checked {
        .el-checkbox__label {
            color: #606266;
        }
    }
}
```

将编译成:

```css
.el-checkbox-group[data-v-6de9570b] {
    margin: 10px;
}

.el-checkbox-group[data-v-6de9570b] .el-checkbox.is-checked[data-v-xxx] .el-checkbox__label[data-v-xxx] {
    color: #606266;
}

```

-----



```scss
.el-checkbox-group {
    margin: 10px;
    /deep/ .el-checkbox.is-checked {
        .el-checkbox__label {
            color: #606266;
        }
    }
}
```

 将编译成 

```scss
.el-checkbox-group[data-v-6de9570b] {
    margin: 10px;
}
.el-checkbox-group[data-v-6de9570b] .el-checkbox.is-checked .el-checkbox__label {
    color: #606266;
}

```

## string 

[ https://www.runoob.com/sass/sass-string-func.html ]( https://www.runoob.com/sass/sass-string-func.html )

* quote(*string*)
* str-index(*string*, *substring*)
* str-insert(*string*, *insert*, *index*)
* str-length(*string*)
* str-slice(*string*, *start*, *end*)
* to-lower-case(*string*)
* to-upper-case(*string*)
* unique-id()
* unquote(*string*)

## Maps

Maps代表一个键和值对集合，其中键用于查找值。他们可以很容易地将值收集到命名组中，并且可以动态地访问这些组。在CSS中你找不到和他们类似的值，虽然他们的语法类似于媒体查询表达式：

```scss
$map: (key1: value1, key2: value2, key3: value3);
```

和列表（Lists）不同，Maps必须始终使用括号括起来，并且必须用逗号分隔。Maps中的键和值可以是任意的SassScript对象。一个Maps可能只有一个值与给定的键关联（尽管该值可以是一个列表）。一个给定的值可能与许多键关联。 

和列表（Lists）类似，Maps的主要操作使用的是 [SassScript 函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#map-functions)。[`map-get`函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_get-instance_method)用于查找map中的值，[`map-merge`函数](http://sass-lang.com/documentation/Sass/Script/Functions.html#map_merge-instance_method)用于添加值到map中的值， [`@each` 指令](https://www.html.cn/doc/sass/#each-multi-assign)可以用来为 map 中的每个键值对添加样式。map中键值对的顺序和map创建时始终相同。 

Maps不能转换为纯CSS。作为变量的值或参数传递给CSS函数将会导致错误。使用`inspect($value)` 函数以产生输出字符串，这对于调试 maps 非常有用。 

## inspect

Maps不能转换为纯CSS。作为变量的值或参数传递给CSS函数将会导致错误。使用`inspect($value)` 函数以产生输出字符串，这对于调试 maps 非常有用。 



## bem

b 选择块

添加前缀，包裹原来样式

```scss
/* BEM
$namespace: 'el';
 -------------------------- */
@mixin b($block) {
  $B: $namespace+'-'+$block !global;

  .#{$B} {
    @content;
  }
}

```

e 选择快

```scss
// $element-separator: '__';

@mixin e($element) {
  $E: $element !global;
  $selector: &;
  $currentSelector: "";
  @each $unit in $element {
    $currentSelector: #{$currentSelector + "." + $B + $element-separator + $unit + ","};
  }

  @if hitAllSpecialNestRule($selector) {
    @at-root {
      #{$selector} {
        #{$currentSelector} {
          @content;
        }
      }
    }
  } @else {
    @at-root {
      #{$currentSelector} {
        @content;
      }
    }
  }
}

// ---------------------------------------------




@function hitAllSpecialNestRule($selector) {
  @return containsModifier($selector) or containWhenFlag($selector) or containPseudoClass($selector);
}




@function containsModifier($selector) {
  $selector: selectorToString($selector);

  @if str-index($selector, $modifier-separator) {
    @return true;
  } @else {
    @return false;
  }
}

@function containWhenFlag($selector) {
  $selector: selectorToString($selector);

  @if str-index($selector, '.' + $state-prefix) {
    @return true
  } @else {
    @return false
  }
}

@function containPseudoClass($selector) {
  $selector: selectorToString($selector);

  @if str-index($selector, ':') {
    @return true
  } @else {
    @return false
  }
}

// BEM support Func
// ------------------------------------------


@function selectorToString($selector) {
  $selector: inspect($selector);
  $selector: str-slice($selector, 2, -2);
  @return $selector;
}
```

m 选择块 

```scss
//  $modifier-separator : --
@mixin m($modifier) {
  $selector: &;
  $currentSelector: "";
  @each $unit in $modifier {
    $currentSelector: #{$currentSelector + & + $modifier-separator + $unit + ","};
  }

  @at-root {
    #{$currentSelector} {
      @content;
    }
  }
}
```

## 状态命中

```scss
// $state-prefix: 'is-';

@mixin when($state) {
  // 使用@at-root提至最外层
  @at-root {
    &.#{$state-prefix + $state} {
      @content;
    }
  }
}
```

使用

```scss
.foo {
  color: red;

  @include when(active) {
    color: blue;
  }
}
```

转化成css

```css
.foo {
  color: red;
}

.foo.is-active {
  color: blue;
}
```



