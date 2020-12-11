## 核心概念

### 模块(modules)

在模块化编程中，开发者将程序分解成离散功能块(discrete **chunks** of functionality)，并称之为模块。

模块具有比完整程序更小的接触面，使得校验、调试、测试轻而易举。

### webpack 模块

相较Node模块，webpack *模块*能够以各种方式表达它们的依赖关系。

- ES2015 import 语句
- CommonJS require() 语句
- AMD define 和 require 语句
- css/sass/less 文件中的 @import 语句。
- 样式(url(...))或 HTML 文件(<img src=...>)中的图片链接(image url)

### 支持的模块类型

webpack 通过 *loader* 可以支持各种语言和预处理器编写模块。*loader* 描述了 webpack **如何**处理 非 JavaScript(non-JavaScript) **模块**，并且在 *bundle* 中引入这些*依赖*。 

总的来说，webpack 提供了可定制的、强大和丰富的 API，允许**任何技术栈**使用 webpack，保持了在你的开发、测试和生成流程中**无侵入性(non-opinionated)**。

### 入口(entry points)

webpack 应该使用哪个模块，来作为构建其内部*依赖图*的开始。进入入口起点后，默认值为 `./src`。

webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。每个依赖项随即被处理，最后输出到称之为 *bundles* 的文件中，

通过在 webpack 配置中配置 entry 属性，来指定一个入口起点（或多个入口起点）

```javascript
// webpack.config.js
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

#### 单入口

用法：`entry: string|Array<string>`

缺点不够灵活

```javascript
const config = {
  entry: {
    main: './path/to/my/entry/file.js'
  }
};
// 简写
const config = {
  entry: './path/to/my/entry/file.js'
};

module.exports = config;
```

#### 对象语法

用法：`entry: {[entryChunkName: string]: string|Array<string>}`

是**可扩展的 webpack 配置**

```javascript
const config = {
  entry: {
    app: './src/app.js',
    vendors: './src/vendors.js'
  }
};
```

**“可扩展的 webpack 配置”**是指，可重用并且可以与其他配置组合使用。这是一种流行的技术，用于将关注点(concern)从环境(environment)、构建目标(build target)、运行时(runtime)中分离。然后使用专门的工具（如* webpack-merge）将它们合并。

每个入口都会产生一个与其他入口独立的依赖图，使用`CommonsChunkPlugin`可以为共享的代码创建bundle

### 输出

webpack 在哪里输出它所创建的 *bundles*，以及如何命名这些文件,默认值为`./dist`。

```javascript
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```

控制 webpack 如何向硬盘写入编译文件，即使有多个入口，但只指定一个输出配置。(ps：指的是配置，而不是输出文件只有一个)

`output` 属性的最低要求是，将它的值设置为一个对象。

- `filename` 用于输出文件的文件名。
- 目标输出目录 `path` 的绝对路径。

```javascript
const config = {
  output: {
    filename: 'bundle.js',
    path: '/home/proj/public/assets'
  }
};

module.exports = config;
```

#### 多个入口起点

```javascript
{
  entry: {
    app: './src/app.js',
    search: './src/search.js'
  },
  output: {
    filename: '[name].js',
    path: __dirname + '/dist'
  }
}
```

如果配置创建了多个单独的 "chunk"（例如，使用多个入口起点或使用像 CommonsChunkPlugin 这样的插件），则应该使用[占位符(substitutions)](https://www.webpackjs.com/configuration/output#output-filename)来确保每个文件具有唯一的名称。

### loader

loader 用于对模块的源代码进行转换。loader 可以使你在 `import` 或"加载"模块时预处理文件。

loader 可以将所有类型的文件转换为 webpack 能够处理的有效模块，然后你就可以利用 webpack 的打包能力，对它们进行处理。

本质上，webpack loader 将所有类型的文件，转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块。

loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 `import` CSS文件。

在更高层面，在 webpack 的配置中 **loader** 有两个目标：

1. `test` 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。
2. `use` 属性，表示进行转换时，应该使用哪个 loader。

```javascript
const path = require('path');

const config = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};

module.exports = config;
```

webpack 编译器，当碰到「在 `require()`/`import` 语句中被解析为 '.txt' 的路径」时，在打包之前，**使用** `raw-loader` 转换一下。

#### 使用 loader

##### 配置

在 webpack.config.js 文件中指定 loader。

 **webpack 的配置文件，是导出一个对象的 JavaScript 文件。**此对象，由 webpack 根据对象定义的属性进行解析。

因为 webpack 配置是标准的 Node.js CommonJS 模块，你**可以做到以下事情**：

- 通过 `require(...)` 导入其他文件
- 通过 `require(...)` 使用 npm 的工具函数
- 使用 JavaScript 控制流表达式，例如 `?:` 操作符
- 对常用值使用常量或变量
- 编写并执行函数来生成部分配置

虽然技术上可行，**但应避免以下做法**：

- 在使用 webpack 命令行接口(CLI)（应该编写自己的命令行接口(CLI)，或使用 --env）时，访问命令行接口(CLI)参数
- 导出不确定的值（调用 webpack 两次应该产生同样的输出文件）
- 编写很长的配置（应该将配置拆分为多个文件）

除了导出单个配置对象，还有一些方式满足其他需求。

```js
// 同步函数 会有固定的两个参数，
// 环境对象(environment)
// 选项 map 对象
module.exports = function(env, argv) {
  return {
    mode: env.production ? 'production' : 'development',
    devtool: env.production ? 'source-maps' : 'eval',
    plugins: [
       new webpack.optimize.UglifyJsPlugin({
        compress: argv['optimize-minimize'] // 只有传入 -p 或 --optimize-minimize
       })
    ]    
  }
};

// 异步
module.exports = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({
        entry: './app.js',
        /* ... */
      })
    }, 5000)
  })
}

// 多个配置对象
module.exports = [
  {
    output: {
      filename: './dist-amd.js',
      libraryTarget: 'amd'
    },
    entry: './app.js',
    mode: 'production',
	}, 
  {
    output: {
      filename: './dist-commonjs.js',
      libraryTarget: 'commonjs'
    },
    entry: './app.js',
    mode: 'production',
	}
]
```

##### 内联

在每个 import 语句中显式指定 loader。

```js
import Styles from 'style-loader!css-loader?modules!./styles.css';
```

##### CLI

在 shell 命令中指定它们

```sh
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```

#### loader 特性

- loader 支持链式传递。能够对资源使用流水线(pipeline)。一组链式的 loader 将按照相反的顺序执行。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。
- loader 可以是同步的，也可以是异步的。
- loader 运行在 Node.js 中，并且能够执行任何可能的操作。
- loader 接收查询参数。用于对 loader 传递配置。
- loader 也能够使用 `options` 对象进行配置。
- 除了使用 `package.json` 常见的 `main` 属性，还可以将普通的 npm 模块导出为 loader，做法是在 `package.json` 里定义一个 `loader` 字段。
- 插件(plugin)可以为 loader 带来更多特性。
- loader 能够产生额外的任意文件。

#### 解析 loader

loader 遵循标准的模块解析。多数情况下，loader 将从模块路径（通常将模块路径认为是 npm install, node_modules）解析。

### 插件

loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务。

插件目的在于解决 **loader** 无法实现的**其他事**。

想要使用一个插件，你只需要 `require()` 它，然后把它添加到 `plugins` 数组中。

多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 `new` 操作符来创建它的一个实例。

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过 npm 安装
const webpack = require('webpack'); // 用于访问内置插件

const config = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};

module.exports = config;
```

### 模式

提供 `mode` 配置选项，告知 webpack 使用相应模式的内置优化。

用法：

```javascript
// 在配置中提供 mode 选项：
module.exports = {
  mode: 'production'
};
```

```bash
# 在 CLI 参数中传递
webpack --mode=production
```

- development
  - 会将 `process.env.NODE_ENV` 的值设为 `development`。启用 `NamedChunksPlugin` 和 `NamedModulesPlugin`。
- production
  - 会将 `process.env.NODE_ENV` 的值设为 `production`。启用 `FlagDependencyUsagePlugin`, `FlagIncludedChunksPlugin`, `ModuleConcatenationPlugin`, `NoEmitOnErrorsPlugin`, `OccurrenceOrderPlugin`, `SideEffectsFlagPlugin` 和 `UglifyJsPlugin`.

## 模块解析(module resolution)

resolver 是一个库(library)，用于帮助找到模块的绝对路径。一个模块可以作为另一个模块的依赖模块，然后被后者引用。

```js
import foo from 'path/to/module'
// 或者
require('path/to/module')
```

所依赖的模块可以是来自应用程序代码或第三方的库(library)。resolver 帮助 webpack 找到 bundle 中需要引入的模块代码，这些代码在包含在每个 `require`/`import` 语句中。 当打包模块时，`webpack` 使用 [enhanced-resolve](https://github.com/webpack/enhanced-resolve) 来解析文件路径

### webpack 中的解析规则

#### 绝对路径

```js
import "/home/me/file";

import "C:\\Users\\me\\file";
```

#### 相对路径

使用 `import` 或 `require` 的资源文件(resource file)所在的目录被认为是上下文目录(context directory)。

```js
import "../src/file1";
import "./file2";
```

#### 模块路径

```js
import "module";
import "module/lib/file";
```

模块将在 resolve.modules 中指定的所有目录内搜索。 你可以替换初始模块路径，此替换路径通过使用 resolve.alias 配置选项来创建一个别名。

都是根据**webpack.config.js**中的配置来解析

如果路径指向一个文件：

- 如果路径具有文件扩展名，则被直接将文件打包。
- 否则，将使用 [`resolve.extensions`] 选项作为文件扩展名来解析，此选项告诉解析器在解析中能够接受哪些扩展名（例如 `.js`, `.jsx`）。

如果路径指向一个文件夹，则采取以下步骤找到具有正确扩展名的正确文件：

- 如果文件夹中包含 **package.json** 文件，则按照顺序查找 **resolve.mainFields** 配置选项中指定的字段。并且 **package.json** 中的第一个这样的字段确定文件路径。比如**resolve.mainFields**中有`main`，则在对应文件夹中的**package.json**中的**main**字段查找路径。
- 如果 **package.json** 文件不存在或者 **package.json** 文件中的 **main** 字段没有返回一个有效路径，则按照顺序查找 resolve.mainFiles 配置选项中指定的文件名，看是否能在 **import/require** 目录下匹配到一个存在的文件名。
- 文件扩展名通过 `resolve.extensions` 选项采用类似的方法进行解析。

### 解析 Loader(Resolving Loaders)

Loader 解析遵循与文件解析器指定的规则相同的规则。但是 [`resolveLoader`](https://www.webpackjs.com/configuration/resolve/#resolveloader) 配置选项可以用来为 Loader 提供独立的解析规则。

### 缓存

每个文件系统访问都被缓存，以便更快触发对同一文件的多个并行或串行请求。在[观察模式](https://www.webpackjs.com/configuration/watch/#watch)下，只有修改过的文件会从缓存中摘出。如果关闭观察模式，在每次编译前清理缓存。

## 依赖图(dependency graph)

一个文件依赖于另一个文件， *依赖关系* 。webpack 接收非代码资源(non-code asset)（例如图像或 web 字体），可以把它们作为 _依赖_ 提供给你的应用程序。

从 *入口起点* 开始，webpack 递归地构建一个 *依赖图* ，这个依赖图包含着应用程序所需的每个模块，然后将所有这些模块打包为少量的 *bundle* - 通常只有一个 - 可由浏览器加载。

## Manifest

在使用 webpack 构建的典型应用程序或站点中，有三种主要的代码类型：

1. 你或你的团队编写的源码。
2. 你的源码会依赖的任何第三方的 library 或 "vendor" 代码。
3. webpack 的 runtime 和 *manifest*，管理所有模块的交互。

### Runtime

在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。

### Manifest

当编译器(compiler)开始执行、解析和映射应用程序时，它会保留所有模块的详细要点。这个数据集合称为 "Manifest"，当完成打包并发送到浏览器时，会在运行时通过 Manifest 来解析和加载模块。无论你选择哪种[模块语法](https://www.webpackjs.com/api/module-methods)，那些 `import` 或 `require` 语句现在都已经转换为 `__webpack_require__` 方法，此方法指向模块标识符(module identifier)。通过使用 manifest 中的数据，runtime 将能够查询模块标识符，检索出背后对应的模块。

## 构建目标

服务器和浏览器代码都可以用 JavaScript 编写，所以 webpack 提供了多种*构建目标(target)*，默认是`web`

```javascript
module.exports = {
  target: 'node'
};
```

### 多个 Target

webpack 不支持向 `target` 传入多个字符串，你可以通过打包两份分离的配置来创建同构的库

```javascript
var serverConfig = {
  target: 'node',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.node.js'
  }
  //…
};

var clientConfig = {
  target: 'web', // <=== 默认是 'web'，可省略
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'lib.js'
  }
  //…
};

module.exports = [ serverConfig, clientConfig ];
```