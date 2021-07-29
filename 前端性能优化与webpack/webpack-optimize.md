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
```
loader: 'babel-loader?cacheDirectory=true'
```
### 1-2 cache-loader缓存loader处理结果

在一些性能开销较大的 `loader` 之前添加 `cache-loader`，以将处理结果缓存到磁盘里，这样下次打包可以直接使用缓存结果而不需要重新打包。
```
module.exports = {
  module: {
    rules: [
      {
        // js 文件才使用 babel
        test: /\.js$/,
        use: [
          'cache-loader',
          ...loaders
        ],
      }
    ]
  }
}
```
那这么说的话，我给每个`loder`前面都加上`cache-loader`，然而凡事物极必反，保存和读取这些缓存文件会有一些时间开销，所以请只对性能开销较大的 `loader` 使用 `cache-loader`。关于这个`cache-loader`更详细的使用方法请参照这里[cache-loader使用注意](https://www.webpackjs.com/loaders/cache-loader/)。

### 1-3 使用多线程处理打包

受限于`Node`是单线程运行的，所以 `Webpack` 在打包的过程中也是单线程的，特别是在执行 `Loader` 的时候，长时间编译的任务很多，这样就会导致等待的情况。那么我们可以使用一些方法将 `Loader` 的同步执行转换为并行，这样就能充分利用系统资源来提高打包速度了。

#### (1) HappyPack

[HappyPack](https://github.com/amireh/happypack)，快乐的打包。人如其名，就是能够让`Webpack`把打包任务分解给多个子线程去并发的执行，子线程处理完后再把结果发送给主线程。
```
module: {
  rules: [
    {
        test: /\.js$/,
        // 把对 .js 文件的处理转交给 id 为 babel 的 HappyPack 实例
        use: ['happypack/loader?id=babel'],
        exclude: path.resolve(__dirname, 'node_modules'),
    },
    {
        test: /\.css$/,
        // 把对 .css 文件的处理转交给 id 为 css 的 HappyPack 实例
        use: ['happypack/loader?id=css']
    }
  ]
},
plugins: [
  	new HappyPack({
        id: 'js', //ID是标识符的意思，ID用来代理当前的happypack是用来处理一类特定的文件的
        threads: 4, //你要开启多少个子进程去处理这一类型的文件
        loaders: [ 'babel-loader' ]
    }),
    new HappyPack({
        id: 'css',
        threads: 2,
        loaders: [ 'style-loader', 'css-loader' ]
    })
]
```
#### (2) thread-loader

[thread-loader](https://www.webpackjs.com/loaders/thread-loader/) ，在`worker` 池`(worker pool)`中运行加载器`loader`。把`thread-loader` 放置在其他 `loader` 之前， 放置在这个 `thread-loader` 之后的 `loader` 就会在一个单独的 `worker` 池(worker pool)中运行。
```
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        include: path.resolve('src'),
        use: [
          {
              loader: "thread-loader",
              // 有同样配置的 loader 会共享一个 worker 池(worker pool)
              options: {
                  // 产生的 worker 的数量，默认是 cpu 的核心数
                  workers: 2,

                  // 一个 worker 进程中并行执行工作的数量
                  // 默认为 20
                  workerParallelJobs: 50,

                  // 额外的 node.js 参数
                  workerNodeArgs: ['--max-old-space-size', '1024'],

                  // 闲置时定时删除 worker 进程
                  // 默认为 500ms
                  // 可以设置为无穷大， 这样在监视模式(--watch)下可以保持 worker 持续存在
                  poolTimeout: 2000,

                  // 池(pool)分配给 worker 的工作数量
                  // 默认为 200
                  // 降低这个数值会降低总体的效率，但是会提升工作分布更均一
                  poolParallelJobs: 50,

                  // 池(pool)的名称
                  // 可以修改名称来创建其余选项都一样的池(pool)
                  name: "my-pool"
              }
          }, 
          {
              loader:'babel-loader'
          }
        ]
      }
    ]
  }
}
```
同样，`thread-loader`也不是越多越好，也请只在耗时的`loader`上使用。




