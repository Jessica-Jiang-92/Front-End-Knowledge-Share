## 项目中常用的Webpack Plugins

### 1. mini-css-extract-plugin

- 官方文档：[MiniCssExtractPlugin-Webpack](https://webpack.docschina.org/plugins/mini-css-extract-plugin/)
- 插件作用

  它会将CSS提取到单独的文件中，为每个包含CSS的JS文件创建一个CSS文件，并且支持CSS和SourceMap的按需加载。
  
  该插件基于Webpack v5新特性构建，并且需要webpack 5才能正常工作。
- 与 extract-text-webpack-plugin 相比

（1）异步加载
（2）没有重复的编译（性能）
（3）更容易

