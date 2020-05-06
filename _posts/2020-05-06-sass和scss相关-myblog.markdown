---

layout:     post
title:      "sass和scss相关"
date:       2020-05-06 13:46:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - css
---

> “Yeah It's on. ”
>





## 前序



### scss和sass的区别



**除了书写上面的区别，没有其他区别了**



*  Sass 是以严格的缩进式语法规则来书写，不带大括号({})和分号(;) 
*  Scss 的语法书写和我们的 CSS 语法书写方式非常类似 








# 正文







### ＠at-root



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







#### `@at-root`规范



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




#### `@at-root`和`&`的结合



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



-----



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





#### `@at-root`和`#{&}`结合





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





###  @content 

`@content`这个是不是个类似占位用的，代表里面是自定义的的



```scss
    @-webkit-keyframes #{$name} {
        @content;
    }
```





### @each

[ https://sass-lang.com/documentation/at-rules/control/each ]( https://sass-lang.com/documentation/at-rules/control/each )





```scss
@each $var in <list or map>
```



- **$var**: 它表示变量的名称。 @each规则将**$var**设置到列表中的每个项目，并使用值**$var**输出样式。
- ` <list or map>`: 这些是SassScript表达式，将返回列表或映射。





---



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







### !global

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









### !default

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





### deep



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











## 通用




### b 选择块

```scss
/* BEM
 -------------------------- */
@mixin b($block) {
  $B: $namespace+'-'+$block !global;

  .#{$B} {
    @content;
  }
}

```





### when 命中状态时

> $state-prefix: 'is-';

使用@at-root提至最外层

```scss
@mixin when($state) {
  @at-root {
    &.#{$state-prefix + $state} {
      @content;
    }
  }
}
```











