# 前言

webpack 是一个模块打包器。它的主要目标是将 JavaScript 文件打包在一起，打包后的文件用于在浏览器中使用，但它也能够胜任转换(transform)、打包(bundle)或包裹(package)任何资源(resource or asset)。在日常开发工作中，我们除了会使用webpack以及会编写它的配置文件之外，我们还需要了解一些关于webpack性能优化的方法，这样在实际工作就能够如虎添翼，增强自身的竞争力。

关于webpack优化的方法我将其分为两大类，如下：

- 可以提高webpack打包速度，减少打包时间的优化方法
- 可以让 Webpack 打出来的包体积更小的优化方法

## 1. 提高webpack打包速度

### 1-1 优化loader的搜索范围





