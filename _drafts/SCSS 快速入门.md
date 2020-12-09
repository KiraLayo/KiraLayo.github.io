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
$abc: mmm nnn;
```

### 作用域

可以定义于样式块之内，也可以之外。如果定义在样式块内，只能定义的样式块内使用。

`!global`可以提升变量作用域到全局

### 中划线 & 下划线

定义时完全取决于习惯，sass中大多数情况下两者可以互相引用`(暂时没有学到反例)`，都指向同一变量，也包括对混合器和Sass函数的命名。不过要注意，css本身的语法，中划线和下滑线时不相通的。

## SassScript

SassScript 可作用于任何属性，允许属性使用变量、算数运算等额外功能。变量就属于SassScript。

### 数据类型 

SassScript 支持 6 种主要的数据类型：

- 数字，`1, 2, 13, 10px`
- 字符串，有引号字符串与无引号字符串，`"foo", 'bar', baz`
- 颜色，`blue, #04a3f9, rgba(255,0,0,0.5)`
- 布尔型，`true, false`
- 空值，`null`
- 数组 (list)，用空格或逗号作分隔符，`1.5em 1em 0 2em, Helvetica, Arial, sans-serif`
- maps, 相当于 JavaScript 的 object，`(key1: value1, key2: value2)`

SassScript 也支持其他 CSS 属性值，比如 Unicode 字符集，或 `!important` 声明。然而Sass 不会特殊对待这些属性值，一律视为无引号字符串。

### 插值

`#{}`可以使有引号字符串将被编译为无引号字符串。

```scss
@mixin firefox-message($selector) {
  body.firefox #{$selector}:before {
    content: "Hi, Firefox users!";
  }
}

@include firefox-message(".header");

// 编译为
body.firefox .header:before {
  content: "Hi, Firefox users!"; 
}
```

### 运算

#### 数字运算 (Number Operations)

SassScript 支持数字的加减乘除、取整等运算 (`+, -, *, /, %`)，如果必要会在不同单位间转换值。

关系运算 `<, >, <=, >=` 也可用于数字运算，相等运算 `==, !=` 可用于所有数据类型。

```scss
p {
  width: 1in + 8pt;
}
```

##### 除法运算

`/` 在 CSS 中通常起到分隔数字的用途，SassScript 作为 CSS 语言的拓展当然也支持这个功能，同时也赋予了 `/` 除法运算的功能。

以下三种情况 `/` 将被视为除法运算符号：

- 如果值，或值的一部分，是变量或者函数的返回值
- 如果值被圆括号包裹
- 如果值是算数表达式的一部分

```scss
p {
  font: 10px/8px;             // Plain CSS, no division
  $width: 1000px;
  width: $width/2;            // Uses a variable, does division
  width: round(1.5)/2;        // Uses a function, does division
  height: (500px/2);          // Uses parentheses, does division
  margin-left: 5px + 8px/2px; // Uses +, does division
}
```

如果需要使用变量，同时又要确保 `/` 不做除法运算而是完整地编译到 CSS 文件中，只需要用 `#{}` 插值语句将变量包裹。

```scss
p {
  $font-size: 12px;
  $line-height: 30px;
  font: #{$font-size}/#{$line-height};
}
```

#### 颜色值运算

颜色值的运算是分段计算进行的，也就是分别计算红色，绿色，以及蓝色的值：

```scss
p {
  color: #010203 + #040506;
}

p {
  color: #010203 * 2;
}
```

如果颜色值包含 alpha channel（rgba 或 hsla 两种颜色值），必须拥有相等的 alpha 值才能进行运算，因为算术运算不会作用于 alpha 值。

颜色值的 alpha channel 可以通过 **opacify** 或 **transparentize** 两个函数进行调整。

```
$translucent-red: rgba(255, 0, 0, 0.5);
p {
  color: opacify($translucent-red, 0.3);
  background-color: transparentize($translucent-red, 0.25);
}
```

IE 滤镜要求所有的颜色值包含 alpha 层，而且格式必须固定 `#AABBCCDD`，使用 `ie_hex_str` 函数可以很容易地将颜色转化为 IE 滤镜要求的格式。

```scss
$translucent-red: rgba(255, 0, 0, 0.5);
$green: #00ff00;
div {
  filter: progid:DXImageTransform.Microsoft.gradient(enabled='false', startColorstr='#{ie-hex-str($green)}', endColorstr='#{ie-hex-str($translucent-red)}');
}
```

#### 字符串运算 (String Operations)

`+` 可用于连接字符串，对于有无引号，依据`+`左侧值为准，空的值被视作插入了空字符串。

```
p {
  cursor: e + -resize;
}
```

#### 布尔运算 (Boolean Operations)

SassScript 支持布尔型的 `and` `or` 以及 `not` 运算。

#### 数组运算 (List Operations)

数组不支持任何运算方式，只能使用 **list functions** 控制。

### 函数指令

Sass 支持自定义函数，并能在任何属性值或 Sass script 中使用：

```scss
$grid-width: 40px;
$gutter-width: 10px;

@function grid-width($n) {
  @return $n * $grid-width + ($n - 1) * $gutter-width;
}

#sidebar { width: grid-width(5); }
```

### `&`在SassScript中应用

```scss
.foo.bar .baz.bang, .bip.qux {
  $selector: &;
  // $selector =  ((".foo.bar" ".baz.bang"), ".bip.qux")
}

@mixin does-parent-exist {
  // 是否存在父选择器
  @if & {
    &:hover {
      color: red;
    }
  } @else {
    a {
      color: red;
    }
  }
}
```

## SCSS嵌套样式

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

### @media

`@media`也支持嵌套，不过嵌套的`@media`命令会、自动包裹父级，可以多层嵌套，如果`@media`互相嵌套，一般会用`and`连接。

`@media`同时也支持SassScript

```scss
@media screen {
  .sidebar {
    @media (orientation: landscape) {
      width: 500px;
    }
  }
}

// 转化为
@media screen and (orientation: landscape) {
  .sidebar {
    width: 500px; 
  } 
}
```

### 父选择器的标识符`&`;

`SCSS` 在解开一个嵌套样式时就会把父选择器（`#content`）通过一个空格连接到子选择器的前边，使用后代选择器的形式，如果不希望使用后代选择器形式解析某条样式，则可以使用标识符`&`，比如在使用伪类的时候，`&`代表的是父选择器整体。

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

当包含父选择器标识符的嵌套规则被打开时，它不会像后代选择器那样进行拼接，而是`&`被父选择器直接替换。

```scss
#content aside {
  color: red;
  body.ie & { color: green }
}

/*编译后*/
#content aside {color: red};
body.ie #content aside { color: green }
```

如果有多层，`&`表示都是定义时，外层的父选择器

```scss
#main {
  color: black;
  a {
    font-weight: bold;
    &:hover { color: red; }
  }
}

#main {
  color: black; 
}

#main a {
	font-weight: bold; }
	#main a:hover {
		color: red; 
}
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

`sass`也有`@import`，`sass`的`@import`在生成`css`文件时就把相关文件导入进来。使用`sass`的`@import`样式并不需要指明被导入文件的全名。你可以省略`.sass`或`.scss`文件后缀，这种导入的文件可以成为局部文件。

```scss
@import "sidebar";
@import "rounded-corners", "text-shadow";
$family: unquote("Droid+Sans");
@import url("http://fonts.googleapis.com/css?family=\#{$family}");
```

不可以在混合指令 (mixin) 或控制指令 (control directives) 中嵌套 `@import`。

### 局部导入

`sass`文件最终会生成css文件，而一般一些通用的，只用来被引用的`sass`文件是不需要生成对应`css`文件的，如果需要不生成css，则在文件名前添加下划线，引入的时候可以省略下划线。

### 默认变量值

后声明的变量会覆盖之前的变量，包括引入`sass`的情况，如果不希望被引入时，覆盖原文件中的变量，则在对应变量后使用`!default`，如果值为null，则属于没有值，会被`!default`影响。

### 嵌套导入

`sass`允许`@import`命令写在`css`样式内，这种情况下，只会在样式范围内生效。

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

- 文件拓展名是 `.css`；
- 文件名以 `http://` 开头；
- 文件名是 `url()`；
- `@import` 包含 media queries。

有时候直接可以把css文件后缀改为scss进行导入。

## 静默注释

在原生的`css`中，注释对于其他人是直接可见的，除非在css不允许的地方注释，一般`css`中注释可以组织样式、简单的样式说明。但`sass`提供了一种方式可在生成的`css`文件中按需抹掉相应的注释，你并不希望每个浏览网站源码的人都能看到所有注释。

`sass`另外提供了一种不同于`css`标准注释格式`/* ... */`的注释语法，它们以`//`开头，注释内容直到行末，即**静默注释**。

将 `!` 作为多行注释的第一个字符表示在压缩输出模式下保留这条注释并输出到 CSS 文件中，通常用于添加版权信息。

```scss
body {
  color: #333; // 这种注释内容不会出现在生成的css文件中
  padding: 0; /* 这种注释内容会出现在生成的css文件中 */
}

body {
  color /* 这块注释内容不会出现在生成的css中 */: #333;
  padding: 1; /* 这块注释内容也不会出现在生成的css中 */ 0;
}
```

## 混合器

复用部分样式，可以使用混合器实现，混合器使用@mixin标识符定义，比较像`CSS @`标识符，`@media、@font-face`。

```scss
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}

// 最终生成
.notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

混合器过度使用，可能导致生成的样式表过大，导致加载缓慢，所以避免滥用。

### 何时使用混合器

混合器与`css`类似，区别就是类名是在`html`文件中应用的，而混合器是在样式表中应用的。类名具有语义化含义，混合器是展示性的描述。

### 混合器中的CSS样式

```scss
@mixin no-bullets {
  list-style: none;
  li {
    list-style-image: none;
    list-style-type: none;
    margin-left: 0px;
  }
}

ul.plain {
  color: #444;
  @include no-bullets;
}

// 最终css
ul.plain {
  color: #444;
  list-style: none;
}
ul.plain li {
  list-style-image: none;
  list-style-type: none;
  margin-left: 0px;
}
```

混合器中的样式甚至可以使用`sass`的父选择器标识符`&`。使用起来跟不用混合器时一样，`sass`解开嵌套样式时，用父样式中的选择器替代`&`。

如果一个混合器只包含`css`样式，不包含属性，那这个混合器就可以在文档的顶部调用，写在所有的`css`样式之外。

### 给混合器传参

混合器并不一定总得生成相同的样式。可以通过在`@include`混合器时给混合器传参，来定制混合器生成的精确样式。

```scss
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}

// 按照命名赋参
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}

// 按照顺序赋参
a {
    @include link-colors(
      blue,
      green,
      red
  );
}
// 参数变量
@mixin box-shadow($shadows...) {
  -moz-box-shadow: $shadows;
  -webkit-box-shadow: $shadows;
  box-shadow: $shadows;
}

.shadows1 {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}

@mixin colors($text, $background, $border) {
  color: $text;
  background-color: $background;
  border-color: $border;
}

$values: #ff0000, #00ff00, #0000ff;

.primary {
  @include colors($values...);
}
```

### 默认参数值

可以给参数指定一个默认值。参数默认值使用`$name: default-value`的声明形式，默认值可以是任何有效的`css`属性值，甚至是其他参数的引用。

```scss
@mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```

### 向混合样式中导入内容

在引用混合样式的时候，可以先将一段代码导入到混合指令中，然后再输出混合样式，额外导入的部分将出现在 `@content` 标志的地方

```scss
@mixin apply-to-ie6-only {
  * html {
    @content;
  }
}
@include apply-to-ie6-only {
  #logo {
    background-image: url(/logo.gif);
  }
}

* html #logo {
  background-image: url(/logo.gif);
}
```

**为便于书写，`@mixin` 可以用 `=` 表示，而 `@include` 可以用 `+` 表示**

## 使用选择器继承来精简CSS

使用`sass`的时候，最后一个减少重复的主要特性就是选择器继承。基于`Nicole Sullivan`面向对象的`css`的理念，选择器继承是说一个选择器可以继承为另一个选择器定义的所有样式。通过`@extend`语法实现。

```scss
//通过选择器继承继承样式
.error {
  border: 1px solid red;
  background-color: #fdd;
}
//将会继承.error选择器的所有样式
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

如果不存在用于继承的选择器，`@extend`会报错，生成的css不可控，在`@extend`后增加`!optional`可以避免这种情况

```scss
a.important {
  @extend .notice !optional;
}
```

`.seriousError`不仅会继承`.error`自身的所有样式，任何跟`.error`有关的组合选择器样式也会被`.seriousError`以组合选择器的形式继承

```scss
//.seriousError从.error继承样式
.error a{  //应用到.seriousError a
  color: red;
  font-weight: 100;
}
h1.error { //应用到hl.seriousError
  font-size: 1.2rem;
}
```

### 何时使用继承

当一个元素拥有的类（比如说`.seriousError`）表明它属于另一个类（比如说`.error`），这时使用继承再合适不过了。

### 继承的高级用法

任何`css`规则都可以继承其他规则，几乎任何`css`规则也都可以被继承。大多数情况你可能只想对类使用继承，但是有些场合你可能想做得更多。最常用的一种高级用法是继承一个`html`元素的样式。尽管默认的浏览器样式不会被继承，因为它们不属于样式表中的样式，但是你对`html`元素添加的所有样式都会被继承。

```scss
.disabled {
  color: gray;
  @extend a;
}
```

一条样式规则继承了一个复杂的选择器，那么它只会继承这个复杂选择器命中的元素所应用的样式。

`.seriousError``@extend``.important.error` ， 那么`.important.error` 和`h1.important.error` 的样式都会被`.seriousError`继承，但是`.important`或者`.error下`的样式则不会被继承。

`#main .error`这种选择器序列是不能被继承的。这是因为从`#main .error`中继承的样式一般情况下会跟直接从`.error`中继承的样式基本一致

### 继承的工作细节

`@extend` 的作用是将重复使用的样式 (`.error`) 延伸 (extend) 给需要包含这个样式的特殊样式（`.seriousError`）。所谓延伸就是被继承样式的选择器中加入继承者的群组选择器分类，多层嵌套同理。

关于`@extend`有两个要点你应该知道。

- 跟混合器相比，继承生成的`css`代码相对更少。因为继承仅仅是重复选择器，而不会重复属性，所以使用继承往往比混合器生成的`css`体积更小。如果你非常关心你站点的速度，请牢记这一点。
- 继承遵从`css`层叠的规则。当两个不同的`css`规则应用到同一个`html`元素上时，并且这两个不同的`css`规则对同一属性的修饰存在不同的值，`css`层叠规则会决定应用哪个样式。相当直观：通常权重更高的选择器胜出，如果权重相同，定义在后边的规则胜出。

混合器本身不会引起`css`层叠的问题，因为混合器把样式直接放到了`css`规则中，而继承存在样式层叠的问题。被继承的样式会保持原有定义位置和选择器权重不变。

总结：`SelectorA``@extend`` SelectorB`, `SelectorB`的样式会应用于`SelectorA`，但前提也是`SelectorA`匹配到的元素。

当合并选择器时，`@extend` 会很聪明地避免无谓的重复，`.seriousError.seriousError` 将编译为 `.seriousError`，不能匹配任何元素的选择器（比如 `#main#footer` ）也会删除。

```scss
.hoverlink {
  @extend a:hover;
}
.comment a.user:hover {
  font-weight: bold;
}

// 编译为
.comment a.user:hover, .comment .user.hoverlink {
  font-weight: bold; 
}

.error {
  border: 1px #f00;
  background-color: #fdd;
}
.attention {
  font-size: 3em;
  background-color: #ff0;
}
.seriousError {
  @extend .error;
  @extend .attention;
  border-width: 3px;
}

// 编译为
.error, .seriousError {
  border: 1px #f00;
  background-color: #fdd; 
}

.attention, .seriousError {
  font-size: 3em;
  background-color: #ff0; 
}

.seriousError {
  border-width: 3px; 
}
```

### 使用继承的最佳实践

尽量避免用后代选择器去继承

```
.foo .bar { @extend .baz; }
.bip .baz { a: b; }
```

如果两个列 (sequence) 包含了相同的选择器，相同部分将会合并在一起，其他部分交替输出。

```scss
#admin .tabbar a {
  font-weight: bold;
}
#demo .overview .fakelink {
  @extend a;
}
// 编译为
#admin .tabbar a,
#admin .tabbar #demo .overview .fakelink,
#demo .overview #admin .tabbar .fakelink {
  font-weight: bold; 
}

#admin .tabbar a {
  font-weight: bold;
}
#admin .overview .fakelink {
  @extend a;
}

#admin .tabbar a,
#admin .tabbar .overview .fakelink,
#admin .overview .tabbar .fakelink {
  font-weight: bold; 
}
```

**参考**

[SCSS快速入门](https://www.sass.hk/guide/)

