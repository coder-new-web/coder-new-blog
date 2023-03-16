---
title: Less
date: 2023-03-01
category:
  - css
tag:
  - css预处理语言
---
:::info
💡基于vscode，需要安装插件Esay Less。

✨新建一个.less文件的后缀名，保存就会自动生成后缀名为.css的相同文件名。

👁️‍🗨️Less官网：[https://less.bootcss.com/](https://less.bootcss.com/)
:::

# 变量（Variables）

## 定义变量

```css
@width:10px;
@height:@width+10px;
```

## 使用变量

```css
.header{
    width: @width;
    height: @height;
}
```

# 混合(Mixins)

混合是一种将一组属性从一个规则集包含(或混入)到另一个规则集的方法

## 定义和使用混合器

```css
//ps：相比sass定义混合器的方法，这样定义看起来感觉像类一样
.bordered {
    border-top: dotted 1px black;
    border-bottom: solid 2px black;
}
```

### 使用混合器

```css
.header p {
    color: #111;
    .bordered();//使用混合器，将前面定义过的样式混合到此样式中去
}
```

## 定义可传参的混合器

```css
.border(@width,@style,@color){
  border: @width @style @color;//ps:注意是有空格的
}
```

### 使用可传参的混合器

```css
section{
  .border(5px,solid,red);//ps:必须要用逗号隔开
}
```

# 嵌套（Nesting）

less提供了使用嵌套代替层叠或与层叠结合使用的能力

## 使用嵌套

```css
.header{
    p{
        color: red;
    }
}
```

## 嵌套和伪类选择器

```css
.header{
   //这里的&指向的就是header
    &::after{
        content: '';
        display: block;
        clear: both;
    }
}
```

## @规则嵌套和冒泡

@ 规则（例如 @media 或 @supports）可以与选择器以相同的方式进行嵌套。@ 规则会被放在前面，同一规则集中的其它元素的相对顺序保持不变。这叫做冒泡（bubbling）。

```css
.component {
  width: 300px;
  @media (min-width: 768px) {
    width: 600px;
    @media  (min-resolution: 192dpi) {
      background-image: url(/img/retina2x.png);
    }
  }
  @media (min-width: 1280px) {
    width: 800px;
  }
}
```

编译为:

```css
.component {
  width: 300px;
}
@media (min-width: 768px) {
  .component {
    width: 600px;
  }
}
@media (min-width: 768px) and (min-resolution: 192dpi) {
  .component {
    background-image: url(/img/retina2x.png);
  }
}
@media (min-width: 1280px) {
  .component {
    width: 800px;
  }
}
```

# 运算(Operations)

算术运算符 ：+、-、*、/ 可以对任何数字、颜色或变量进行运算。
如果可能的话，算术运算符在加、减或比较之前会进行单位换算。
计算的结果以最左侧操作数的单位类型为准。
如果单位换算无效或失去意义，则忽略单位。无效的单位换算例如：px 到 cm 或 rad 到 % 的转换。

## 加减之前会对单位进行换算

1. 如果都有单位会以最左侧的单位进行换算
2. 如果没有单位，会以左侧的单位进行换算

```css
// 不同的单位，计算结果是以最左侧的单位为准。
@conversion-1: 5cm + 10mm; // 结果是 6cm
@incompatible-units: 2 + 5px - 3cm; // 结果是 4px

//如果没有单位，会以最左侧的单位作为基准单位
@conversion-2: 2 - 3cm - 5mm; // 结果是 -1.5cm
```

## 乘法和除法单位不做转换

- 如果遇到不同单位，会以左侧的单位进行换算。

```css
@base: 2cm * 3mm; // 结果是 6cm
```

## 对颜色进行算数运算

```css
@color: (#224488 / 2); // 结果是 #112244
background-color: #112244 + #111; // 结果是 #223355
```

## 色彩函数

## calc()特例

为了与 CSS 保持兼容，calc() 并不对数学表达式进行计算，但是在嵌套函数中会计算变量和数学公式的值。

```css
@var: 50vh/2;
width: calc(50% + (@var - 20px));  // 结果是 calc(50% + (25vh - 20px))
```

# 转义（Escaping）

### 转义+calc的使用

```css
.el-main{
  width:calc(~"100% - 200px");
}
```

# 函数（Functions）

# 命名空间和访问符

## 定义命名空间

```css
#bundle() {
  .button {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover {
      background-color: white;
    }
  }
  .tab { ... }
  .citation { ... }
}
```

## 访问带有命名空间的样式

现在，我们把.button 类混合到 #header a 中去：

```css
#header a {
  color: orange;
  #bundle.button();  // 还可以书写为 #bundle > .button 形式
}
```

## 与混合器的区别

- 在定义类名或id名后面加一个()
- 有了命名空间可以指定将某个样式混合进去，而混合器就是会把该类名或id名下的样式全部混合进去。

# 映射（Maps）

```css
#colors() {
  primary: blue;
  secondary: green;
}

.button {
  color: #colors[primary];
  border: 1px solid #colors[secondary];
}
```

输出结果：

```css
.button {
  color: blue;
  border: 1px solid green;
}
```

#

作用域（Scope）
通俗点讲就是花括号内的样式等级大于在外面定义的样式等级：

```css
//花括号内的样式等级大于花括号外面定义的样式等级
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}

#page {
  #header {
    color: @var; // white
  }
  @var: white;
}
```

# 注释（Comments）

块注释和行注释都可以使用：

```css
/* 一个块注释
 * style comment! */
@var: red;

// 这一行被注释掉了！
@var: white;
```

注意：
块级注释会输出在css文件中，而行注释不会输出在css文件中

# 导入（Importing）

```css
@import "library"; // 如果导入的文件是 .less 后缀名可以省略
@import "typo.css";//但是导入的是css就不可以省略
```
