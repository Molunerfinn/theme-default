# element-theme-default 语法解析

[element-theme-default](https://github.com/ElementUI/theme-default)提供的工具和文档只能通过修改`element-variables.css`这个文件进行一些局部样式调整，比如整体的颜色风格，一些长宽、边距、圆角尺寸等。如果需要进行定制、二次开发的话，单纯修改`element-variables.css`是不够的。还需要修改`element-theme-default`的源码。在查看`element-theme-default`源码的时候发现了一些有趣的东西。记录如下，方便二次开发。

首先是`element-theme-default`采用的是下一代CSS风格的开发方式，用了基于PostCss的[Post-salad](https://github.com/ElemeFE/postcss-salad)来编译，而不是我之前认为的SCSS\LESS等预处理。不过总体而言如果有预处理的经验，还是能够很快上手。

几个显著特征：

- 采用var进行全局样式变量定义
- 支持@import引入外部css
- 支持层级嵌套书写
- 支持一些独特的语法

一些独特的语法列举如下：

1.`@component-namespace` 后面跟着的通常是`el`，会通知整个组件的class前缀渲染为`el`

以下的部分是跟在`@component-namespace`层级之后,也就是都在`@component-namespace el {...}`花括号内。

2.`@b` 后面跟着的class会渲染为：`el-class`。例如：
```css
@b alert{...}
```
会被渲染为
```css
el-alert{...}
```

3.`@modifier`或者`@m`后面跟着的class会被渲染为：`--class`。例如：

```css
@b alert{
  @modifier info{...}
  @m warning{...}
}
```
会被渲染为
```
el-alert--info{...}
el-alert--warning{...}
```

4.`@e`后面跟着的class会被渲染为：`__class`。例如：
```css
@b alert{
  @e content{...}
}
```
会被渲染为
```css
el-alert__content{...}
```

5.`@when`后面跟着的class会被渲染为：`.is-class`。例如：
```css
@b alert{
  @e title{
    @when bold {...}
  }
}
```
会被渲染为
```css
el-alert__title.is-bold{...}
```

6.`@mixin button-size` 后面跟着四个值，分别代表是padding上下、padding左右，font-size，border-radius。

例如：
```css
@b button{
  @mixin button-size 10px 20px 30px 40px;
}
```
会被渲染为
```css
.el-button{
  padding: 10px 20px;
  font-size: 30px;
  border-radius: 40px;
}
```
7.`@mixin button-variant`后面跟着3个值，分别代表color、background-color、border-color。同时还会渲染一系列的hover\active\focus等颜色的深浅变化。
例如：
```css
@b button {
  @mixin button-variant white blue black;
}
```
会被渲染为：
```css
.el-button{
  color: white;
  background-color: blue;
  border-color: black;
}

.el-button:hover,
.el-button:focus{...}

/* 一系列颜色变化 */
......
```

8.`tint()`和`shade()`函数，分别用来使颜色提升亮度、颜色降低亮度用的。接受两个参数，第一个是颜色值，第二个是明暗百分比。

例如：
```css
.foo {
  color: tint(#BADA55, 42%);
}

.bar {
  background-color: shade(#663399, 42%);
}
```
会被渲染为：
```css
.foo {
  color: #e2efb7;
}

.bar {
  background-color: #2a1540;
}
```

# element-theme-default 二次开发指南

官方给出的[例子](http://element.eleme.io/#/zh-CN/component/custom-theme)目前还有一些问题，由于缺少了`vue-popup`组件，在`et --watch`的时候会报错。

我fork的仓库里加入了`vue-popup`的组件，在官方解决这个问题之前暂时可以采用如下方式（目前跟官方仓库保持同步）：

## 首先是安装工具：

```
npm i element-theme -g
```

## 然后安装默认主题（本仓库）：

```
npm i https://github.com/Molunerfinn/theme-default.git -D
```

## 初始化变量文件

```
et -i
```
如果使用默认配置，执行后当前目录会有一个`element-variables.css`文件。内部包含了主题所用到的所有变量，它们使用 CSS4 的风格定义。大致结构如下：
```css
:root {

  /* Colors
  -------------------------- */
  --color-primary: #20a0ff;
  --color-success: #13ce66;
  --color-warning: #f7ba2a;
  --color-danger: #ff4949;
  --color-info: #50BFFF;
  --color-blue: #2e90fe;
  --color-blue-light: #5da9ff;
  --color-blue-lighter: rgba(var(--color-blue), 0.12);
  --color-white: #fff;
  --color-black: #000;
  --color-grey: #C0CCDA;
```

## 修改变量

可以通过修改`element-variables.css`文件里的变量，即可改变整体风格。

## 修改源码

进入`node_modules/element-theme-default/src`目录下修改相应文件的代码即可。

## 编译主题

保存文件后，到命令行里执行 et 编译主题，如果你想启用 watch 模式，实时编译主题，增加 -w 参数。

```
et -w
```

**注意：**修改源码的时候不会触发编译的watch模式，需要手动再保存一遍`element-variables.css`这个文件才可以触发编译效果。

开发愉快~

-------


# element-theme-default
> element component defualt theme.


## Installation
```shell
npm i element-theme-default -S
```

## Usage

Use Sass Or postcss-import
```css
@import 'element-theme-default';
```

Or Use webpack
```javascript
import 'element-theme-default';
```

Or
```html
<link rel="stylesheet" href="path/to/node_modules/element-theme-default/lib/index.css">
```

##  Import your need
```javascript
import 'element-theme-default/lib/input.css';
import 'element-theme-default/lib/select.css';

// ...
```
