---
title: 简单学习CSS选择器
categorys:
  - 前端
tags:
  - CSS
  - HTML
---

## 元素选择器/类型选择器

匹配文档语言元素类型的名称，匹配文档树中该元素类型的每一个实例。

```css
html {
  /*...*/
}
```

## 选择器分组

多个选择器使用同一规则，规则左边，用逗号分隔。

```css
h2, p {
  /*...*/
}
```

### 通配符选择器

CSS2 引入

显示为一个星号（*），该选择器可以与任何元素匹配。

```css
* {
  /*...*/
}
```

### 声明分组

```css
h1 {font: 28px Verdana;}
h1 {color: blue;}
h1 {background: red;}
// 同等
h1 {font: 28px Verdana; color: white; background: black;}
```

## 类选择器

类选择器允许以一种独立于文档元素的方式来指定样式。

```css
*.important {
  /*...*/
}
// 等同
.important {
  /*...*/
}
```

### 结合元素选择器

类选择器可以结合元素选择器来使用。

```css
/*匹配class值为important的p元素*/
p.important {/*...*/}
```

### 多类选择器

选择**同时**包含这些类名的元素（类名的顺序不限）。

```css
.important.urgent {/*...*/}
```

> **注意**
>
> - 区分大小写

## ID 选择器

ID 选择器允许以一种独立于文档元素的方式来指定样式。

```css
*#intro {
  /*...*/
}
/*等同*/
#intro {
  /*...*/
}
```

**注意**
 - 不能使用 ID 词列表
 - ID 可能匹配多个规则
 - ID 区分大小写


## 属性选择器

CSS 2 引入了属性选择器。

属性选择器可以根据元素的属性及属性值来选择元素。

### 简单属性选择

选择有某个属性的元素，而不论属性值是什么，可以使用简单属性选择器。

```css
*[title] {
  /*...*/
}

[title] {
  /*...*/
}

a[title] {
  /*元素选择器结合属性选择器*/
}

a[title][href] {
  /*元素选择器结合多属性选择器*/
}
```

### 根据具体属性值选择

选择有特定属性值的元素。

```css
a[href="..."] {
  /*...*/
}
```

##### 注意

属性与属性值必须完全匹配，如果属性值包含值列表，匹配就可能出问题。

```html
<p class="important warning">This paragraph is a very important warning.</p>
```

```css
p[class="important"]{
  /*无法匹配*/
}
```

```css
p[class="important warning"] {
  /*匹配*/
}
```

#### 根据部分属性值选择

如果需要根据属性值中的词列表的某个词进行选择，则需要使用波浪号（~）。

```css
p[class~="important"] {
  /*...*/
}
/*等价*/
p.important {
  /*...*/
}
```

#### 子串匹配属性选择器

引用W3

| 选择器               | 描述                                                         |
| :------------------- | :----------------------------------------------------------- |
| [attribute]          | 用于选取带有指定属性的元素。                                 |
| [attribute=value]    | 用于选取带有指定属性和值的元素。                             |
| [attribute~=value]   | 用于选取属性值中包含指定词汇的元素。                         |
| [attribute\|=value]  | 用于选取带有以指定值开头的属性值的元素，该值必须是整个单词。 |
| [attribute^=value]   | 匹配属性值以指定值开头的每个元素。                           |
| [attribute$=value]   | 匹配属性值以指定值结尾的每个元素。                           |
| [attribute**=value*] | 匹配属性值中包含指定值的每个元素。                           |

**注意**

[attribute\|=value]

该值必须是整个单词，比如 lang="en"，或者后面跟着连字符，比如 lang="en-us"。

[attribute~=value]

包含单词value的元素，注释必须是单词。

这里的单词可以指的是值列表或者字符串中的值或单词。

## 后代选择器

**descendant selector**

又称为包含选择器，后代选择器可以选择作为某元素后代的元素。

根据上下文选择元素

```css
li strong {
	/*...*/
}
```

元素之间的层次间隔可以是**无限的**

会寻找元素继承中**所有**派生元素，无论嵌套层次有多深。

## 子元素选择器

选择作为某元素子元素的元素，而不是嵌套多层。

```css
h1 > strong {
	/*...*/
}
```

### 结合其他选择器

```css
table.company td > p
```

## 相邻兄弟选择器

**Adjacent sibling selector**

选择**紧接**在另一元素后的元素，且二者有相同父元素。

```css
h1 + p {
	/*...*/
}
```
### 结合其他选择器

```css
html > body table + ul {/*...*/}
```

## 伪类

伪类用于向某些选择器添加特殊的效果。

### 语法

```css
selector : pseudo-class {property: value}
```

```css
selector.class : pseudo-class {property: value}
```

### 锚伪类

链接的不同状态都可以不同的方式显示，活动状态，已被访问状态，未被访问状态，和鼠标悬停状态。

```css
a:link {color: #FF0000}		/* 未访问的链接 */
a:visited {color: #00FF00}	/* 已访问的链接 */
a:hover {color: #FF00FF}	/* 鼠标移动到链接上 */
a:active {color: #0000FF}	/* 选定的链接 */
```

**注意**

- a:hover 必须被置于 a:link 和 a:visited 之后，才是有效的。
- a:active 必须被置于 a:hover 之后，才是有效的。
- 伪类名称对大小写不敏感。

### 伪类与 CSS 类

```css
a.red : visited {color: #FF0000}
```

```html
<a class="red" href="css_syntax.asp">CSS Syntax</a>
```

### :first-child 伪类

CSS引入

是子元素，且是第一

```css
p:first-child {/*第一个为p的元素*/}
li:first-child {/*第一个为li的元素*/}
```

