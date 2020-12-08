---
categorys:
  - 前端
tags:
  - CSS
  - SCSS
  - SASS
---

简单的SASS/SCSS入门

## SASS & SCSS

SCSS和SASS指的是一种CSS预加载器

- SASS 是以'.sass'后缀为扩展名，而 SCSS 是以'.scss'后缀为扩展名。
- SASS 是以严格的缩进式语法规则来书写，不带大括号({})和分号(;)。
- SCSS 的语法书写和CSS 语法书写方式非常类似，使用大括号({})和分号(;)，并且对空白符不敏感。

## 变量

类似CSS属性声明，使用`$`开头，表示多值属性时可以使用空格或者逗号分隔，变量中可以引用其他定义的变量。

```scss
$abc: mmm nn;
```

### 作用域

可以定义于规则块之内，也可以之外。如果定义在规则块内，只能定义的规则块内使用。

### 中划线 & 下划线

定义时完全取决于习惯，sass中大多数情况下两者可以互相引用`(暂时没有学到反例)`，都指向同一变量，也包括对混合器和Sass函数的命名。不过要注意，css本身的语法，中划线和下滑线时不相通的。

## SCSS嵌套规则

```css
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

```scss
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}	

 /* 编译后 */
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

### 父选择器的标识符`&`;

`SCSS` 在解开一个嵌套规则时就会把父选择器（`#content`）通过一个空格连接到子选择器的前边，使用后代选择器的形式，如果不希望使用后代选择器形式解析某条规则，则可以使用标识符`&`，比如在使用伪类的时候。

```
article a {
  color: blue;
  &:hover { color: red }
}
```

### 群组选择器的嵌套

```scss
/*css*/
.container h1, .container h2, .container h3 { 
  margin-bottom: .8em 
}

/*scss写法*/
.container {
  h1, h2, h3 {
    margin-bottom: .8em
  }
}
```

```scss
nav, aside {
  a {
    color: blue
  }
}

/*渲染css*/
nav a, aside a {
  color: blue
}
```

### 子组合选择器和同层组合选择器：>、+和~

```scss
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  nav + & { margin-top: 0 }
}

/*渲染css*/
article ~ article { border-top: 1px dashed #ccc }
article > footer { background: #eee }
article dl > dt { color: #333 }
article dl > dd { color: #555 }
nav + article { margin-top: 0 }
```

### 嵌套属性

部分属性有很多共同前缀，一般可能要反复写。

```scss
nav {
  border: {
    style: solid;
    width: 1px;
    color: #ccc;
  }
}

// 
nav {
  border: 1px solid #ccc {
		left: 0px;
		right: 0px;
  }
}

// 渲染css结果
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```

## 导入SASS文件

`css`有一个不常用特性`@import`，它允许在一个`css`文件中导入其他`css`文件，只有执行到`@import`时，浏览器才会去下载其他`css`文件。

`sass`也有`@import`规则，`sass`的`@import`规则在生成`css`文件时就把相关文件导入进来。使用`sass`的`@import`规则并不需要指明被导入文件的全名。你可以省略`.sass`或`.scss`文件后缀，这种导入的文件可以成为局部文件。

```scss
@import "sidebar";
```

### 局部导入

`sass`文件最终会生成css文件，而一般一些通用的，只用来被引用的`sass`文件是不需要生成对应`css`文件的，如果需要不生成css，则在文件名前添加下划线，引入的时候可以省略下划线。

### 默认变量值

后声明的变量会覆盖之前的变量，包括引入`sass`的情况，如果不希望被引入时，覆盖原文件中的变量，则在对应变量后使用`!default`。

### 嵌套导入

`sass`允许`@import`命令写在`css`规则内，这种情况下，只会在规则范围内生效。

```scss
.blue-theme {@import "blue-theme"}

//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。

.blue-theme {
  aside {
    background: blue;
    color: #fff;
  }
}
```

### 原生的CSS导入

`sass`中导入有时候会有例外，在下列三种情况下会生成原生的`CSS@import`：

- 被导入文件的名字以`.css`结尾；
- 被导入文件的名字是一个URL地址（比如http://www.sass.hk/css/css.css);
- 被导入文件的名字是`CSS`的url()值。

有时候直接可以把css文件后缀改为scss进行导入。

### 静默注释

在原生的`css`中，注释对于其他人是直接可见的，但`sass`提供了一种方式可在生成的`css`文件中按需抹掉相应的注释，你并不希望每个浏览网站源码的人都能看到所有注释。

`sass`另外提供了一种不同于`css`标准注释格式`/* ... */`的注释语法，它们以`//`开头，注释内容直到行末，即**静默注释**。

