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
- 基本作用：

  首先，这是一个webpack的插件，需要配合webpack和webpack-cli一起使用。这个插件的功能是生成代码分析报告，帮助提升代码质量和网站性能。

## 4. fork-ts-checker-webpack-plugin

从名字可以看出来，该插件主要用作处理ts相关的内容。

- npm官方解释：[https://www.npmjs.com/package/fork-ts-checker-webpack-plugin](https://www.npmjs.com/package/fork-ts-checker-webpack-plugin)
- 作用
  （1）TypeScript类型检查和EsLint连接（通过将每个进程移动到单独的进程）
  （2）支持最新的TypeScript功能，如项目参考和增量模式
  （3）支持Vue Single File Component
  （4）使用代码桢格式化器显示漂亮的错误信息

## 5. 一个完整的Webpack文件如何编写

```
const path = require('path');
const VueLoaderPlugin = require('vue-loader/lib/plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin');
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
const ForkTsCheckerWebpackPlugin = require('fork-ts-checker-webpack-plugin');

const isQuickMode = process.env.QUICK_MODE === 'true';
const isAnalysis = process.env.ANALYZE_BUNDLE === 'true';
const NODE_ENV = process.env.NODE_ENV || 'development';
const isProd = NODE_ENV === 'production';

function resolve(dir) {
  return path.join(__dirname, '..', dir);
}

const cssLoader = [
  {
    loader: 'css-loader',
    options: { minimize: isProd },
  },
  'postcss-loader',
  'less-loader',
];

const webpackCommonPlugins = [
  new VueLoaderPlugin(),
  new MiniCssExtractPlugin({
    filename: 'common.[chunkhash].css',
  }),
  ...(isProd ? [] : [new FriendlyErrorsPlugin()]),
];

if (isQuickMode) {
  webpackCommonPlugins.unshift(
    new ForkTsCheckerWebpackPlugin({
      memoryLimit: 1024 * 4,
      workers: ForkTsCheckerWebpackPlugin.ONE_CPU_FREE,
      ignoreDiagnostics: [2307],
    }),
  );
}

if (isAnalysis) {
  webpackCommonPlugins.push(
    new BundleAnalyzerPlugin({
      async: false,
    }),
  );
}

module.exports = {
  mode: NODE_ENV,
  devtool: isProd ? false : '#cheap-module-source-map',
  output: {
    path: resolve('dist'),
    publicPath: '/dist/',
    filename: '[name].[chunkhash].js',
  },
  externals: {
    jquery: 'jQuery',
  },
  resolve: {
    extensions: ['.webpack.js', '.web.js', '.ts', '.js', '.json', '.vue'],
    alias: {
      public: resolve('public'),
      jquery: resolve('public/js/jquery.min.js'),
      vue$: 'vue/dist/vue.esm.js',
      '@': resolve('src'),
    },
  },
  module: {
    noParse: /es6-promise\.js$/, // avoid webpack shimming process
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: {
          compilerOptions: {
            preserveWhitespace: false,
          },
        },
      },
      {
        enforce: 'pre',
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'eslint-loader',
      },
      {
        test: /\.js$/,
        loader: 'babel-loader',
        include: [resolve('src'), resolve('node_modules/vue-echarts'), resolve('node_modules/libphonenumber-js')],
      },
      {
        test: /\.ts$/,
        loader: 'ts-loader',
        exclude: /node_modules/,
        options: {
          appendTsSuffixTo: [/\.vue$/],
          happyPackMode: isQuickMode,
        },
      },
      {
        test: /\.(png|jpg|gif|svg)$/,
        loader: 'url-loader',
        options: {
          limit: 10000,
          name: '[name].[ext]?[hash]',
        },
      },
      {
        test: /\.(woff2?|eot|ttf|otf|svg)(\?.*)?$/,
        loader: 'file-loader',
        options: {
          limit: 10000,
          name: '[name].[hash:7].[ext]',
          esModule: false,
        },
      },
      {
        test: /\.(le|c)ss$/,
        oneOf: [
          {
            test: /App/,
            resourceQuery: /\?vue/,
            use: [MiniCssExtractPlugin.loader, ...cssLoader],
          },
          {
            use: ['vue-style-loader', ...cssLoader],
          },
        ],
      },
    ],
  },
  performance: {
    maxEntrypointSize: 300000,
    hints: isProd ? 'warning' : false,
  },
  plugins: webpackCommonPlugins,
};

```





