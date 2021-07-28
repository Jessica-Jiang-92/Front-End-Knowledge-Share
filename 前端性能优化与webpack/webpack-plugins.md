# 项目中常用的Webpack Plugins

## 1. mini-css-extract-plugin

- 官方文档：[MiniCssExtractPlugin-Webpack](https://webpack.docschina.org/plugins/mini-css-extract-plugin/)
- 插件作用

  它会将CSS提取到单独的文件中，为每个包含CSS的JS文件创建一个CSS文件，并且支持CSS和SourceMap的按需加载。
  
  该插件基于Webpack v5新特性构建，并且需要webpack 5才能正常工作。
- 与 extract-text-webpack-plugin 相比

1. 异步加载
2. 没有重复的编译（性能）
3. 更容易使用
4. 特别针对CSS开发

- 建议 `mini-css-extract-plugin` 与 `css-loader` 一起使用。之后将 loader 与 plugin 添加到你的 webpack 配置文件中。

## 2. friendly-errors-webpack-plugin

- 介绍：[Friendly-errors-webpack-plugin 介绍](https://www.cnblogs.com/angelasp/p/10622283.html)
- 插件作用：Friendly-errors-webpack-plugin识别某些类别的webpack错误，并清理，聚合和优先级，以提供更好的开发人员体验。

如：我们运行nodejs时，能够看到控制台上面打印出来的红色标识的`Error`信息，就是该插件友好性的一个体现

## 3. webpack-bundle-analyzer

- 插件介绍：[webpack-bundle-analyzer插件快速入门](https://juejin.cn/post/6844903825216651271)
- 项目中使用：`const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;`



