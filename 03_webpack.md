# webpack

- webpack 将源代码和依赖包打包成浏览器可执行的 html, js, css 文件；
- webpack 本身是 JS 写的，需要运行环境，也就是 Node

## 项目的工程化开发过程

1. 划分项目目录
2. 确定包管理工具 npm/yarn/pnpm
3. 确定打包工具 webpack
4. 确定使用框架 Vue/React/Angular

脚手架工具已自动配置好这些工具。

## webpack使用的核心 node 模块: path

`path`用于处理文件路径。

[path.resolve()](https://nodejs.org/dist/latest-v16.x/docs/api/path.html#pathresolvepaths)：从右往左字符串直到形成一个绝对路径。

注意，在路径中，`/`表示根目录，是一个绝对路径，`./`表示当前目录，是相对路径。

webpack 取别名时要用到绝对路径。

## webpack打包过程

webpack 进行打包时，会从入口开始，递归地构建一个依赖关系图 (dependency graph)，其中包含应用程序所需的每个模块，然后遍历 graph，将这些模块打包成一个或多个。

webpack 默认只打包.js文件，其他文件需要用对应的 loader 先进行处理。

## loader

loader 作用是对模块进行转换。webpack 默认只对 js 文件打包，如果 import css文件，webpack 处理不了，会报错，要用 css-loader 先对 css 文件进行转换。

module.rules对象用于配置所有的 loader, 解析 loader 时会从右往左进行。

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
}
```

`pocss-loader`: 一个用JS处理css的工具，安装相应插件发挥对应功能，如`postcss-preset-env`:

```js
use: [
  {
    loader: 'postcss-loader',
    options: {
      postcssOptions: {
        plugins: ['postcss-preset-env']
      }
    }
  }
]
```

### 打包图片

处理图片等资源的配置，webpack 已内置处理图片功能，但仍需要配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif|svg))$/,
        type: "asset"
      }
    ]
  }
}
```

打包图片时，小图片可用base64嵌入文档，大图片单独打包。

### 打包 js 文件

`babel-loader`: 用于对 js 文件进行转译，如高级 ES 语法转成 ES5 语法，用法和 postcss 一样，安装特定插件发挥相应功能。

### 解析路径

webpack 用 resolve 中的配置解析路径：

```js
module.exports = {
  resolve: {
    extensions: ['.js', '.vue', '.jsx', '.ts', 'json'], // import文件时可省略后缀名
    alias: {
      '@': path.resolve(__dirname, './src')
    }
  }

}
```

## Plugins

与 loader 的区别：
  - loader 用来转化特定的模块，如 css-loader 处理 css 文件；
  - plugin 用于执行更广泛的任务，如注入环境变量，打包优化等。

`clean-webpack-plugin`：用于打包时，自动删除 dist 文件夹。

`html-webpack-plugin`：自动生成打包后的 html，可指定模板。

`DefinePlugin`: 自带插件，用于配置打包过程中的全局变量。

## 配置本地开发服务器

`npm install webpack-dev-server -D`

package.json的script字段中添加 `"dev": "webpack serve"`，本地服务器自带HMR，hot module replacement，修改模块只会更新该模块内容，不会全局刷新浏览器，默认开启，但要进行设置，如`module.host.accept="@/App.vue"`