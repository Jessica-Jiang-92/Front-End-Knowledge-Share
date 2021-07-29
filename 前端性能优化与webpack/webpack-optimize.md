# 前言

webpack 是一个模块打包器。它的主要目标是将 JavaScript 文件打包在一起，打包后的文件用于在浏览器中使用，但它也能够胜任转换(transform)、打包(bundle)或包裹(package)任何资源(resource or asset)。在日常开发工作中，我们除了会使用webpack以及会编写它的配置文件之外，我们还需要了解一些关于webpack性能优化的方法，这样在实际工作就能够如虎添翼，增强自身的竞争力。

关于webpack优化的方法我将其分为两大类，如下：

- 可以提高webpack打包速度，减少打包时间的优化方法
- 可以让 Webpack 打出来的包体积更小的优化方法

## 1. 提高webpack打包速度

### 1-1 优化loader的搜索范围

对于Loader来说，影响打包效率首当其冲必属 `Babel` 了。因为 `Babel` 会将代码转为字符串生成 `AST`，然后对 `AST` 继续进行转变最后再生成新的代码，项目越大，转换代码越多，效率就越低。当然了，我们是有办法优化的。

首先我们可以优化 Loader 的文件搜索范围，在使用loader时,我们可以指定哪些文件不通过loader处理,或者指定哪些文件通过loader处理。
```
module.exports = {
  module: {
    rules: [
      {
        // js 文件才使用 babel
        test: /\.js$/,
        use: ['babel-loader'],
        // 只处理src文件夹下面的文件
        include: path.resolve('src'),
        // 不处理node_modules下面的文件
        exclude: /node_modules/
      }
    ]
  }
}
```
对于 `Babel` 来说，我们肯定是希望只作用在 `JS` 代码上的，然后 `node_modules` 中使用的代码都是编译过的，所以我们也完全没有必要再去处理一遍。

另外，对于`babel-loader`，我们还可以将 `Babel` 编译过的文件缓存起来，下次只需要编译更改过的代码文件即可，这样可以大幅度加快打包时间。




