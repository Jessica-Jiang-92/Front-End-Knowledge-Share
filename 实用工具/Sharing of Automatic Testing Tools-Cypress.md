## 官网
[Cypress.io](https://www.cypress.io/)，使用Cypress的好处是：所有的测试用例都采用Javascript编写，类似于Jquery的方式，对前端开发人员来说是非常友好的了。同时Cypress运行非常快，几乎只要当我们的页面渲染出来时就已经加载出来了，因为Cypress都是基于真实的DOM进行的测试；可视化的debug。

## 安装
注意：自己测试的时候最好不要以“Cypress/cypress”来命名，否则install的时候会报错。
- 方式1：因为我们在墙内，所以下载Cypress非常慢，经常会因为网络超时而报错。
```
npm install cypress --save-dev
yarn add cypress --save
```
- 方式2：安装tgz包
我们通过地址：[npm 仓库](https://registry.npmjs.org/cypress/6.6.0) 可以找到cypress对应的tgz包的下载位置（通过搜索dist找到）为：https://registry.npmjs.org/cypress/-/cypress-6.6.0.tgz。

然后再通过离线安装的方式<code> npm install cypress-6.6.0.tgz</code>，注意：此时的cypress-6.6.0.tgz必须在相应的文件夹中。

- 方式3：先下载Windows对应的ZIP包[cypress.zip](https://download.cypress.io/desktop)这个位置下载的包都是最新版本的，下载完成之后存放在本地，使用下面的命令进行安装:
```
CYPRESS_INSTALL_BINARY=D:\\Install-Packages\\Cypress\\cypress.zip npm install cypress
```
安装完成之后，我们就可以顺利使用Cypress了，但在使用之前，为了启动更方便需要在<code>packages.json</code>中配置
```
"scripts": {
  "cy:open": "cypress open"
}
```
然后再运行：<code>yarn run cy:open</code>或者<code>npm run cy:open</code>，成功之后展示如下：
![Cypress](https://user-images.githubusercontent.com/10249805/111119099-a2f49200-85a4-11eb-8a6a-6d7b886f2468.png)

如果我们的项目中配置了<code>.babelrc</code>，并且在运行之后报错<code>Webpack Compilation Error</code>则需要在启动Cypress之后的文件夹下创建一个空的<code>.babelrc</code>为<code>{}</code>，随后再次运行将不会在报错。该问题可以参考[Webpack Compilation Error: Cannot find module](https://github.com/cypress-io/cypress/issues/8364)

## 编写测试用例
接下来就可以编写自己的测试用例了。可以先参考这里[测试用例](https://docs.cypress.io/guides/getting-started/writing-your-first-test.html#Write-your-first-test)。这里举一个例子：
```
describe('First Cypress Test', function () {
  it('Does not do much!', function () {
    cy.visit('https://www.naver.com/');   // 访问Naver搜索引擎
    cy.get('#query').type('Cypress Test');   // 获取是否存在id="query"的元素，存在则输入“Cypress Test”
    cy.wait(120);
    cy.get('#search_btn').click();   // 获取是否存在id="search_btn"的元素，存在则点击
    cy.contains('JavaScript End to End Testing').click();   // 内容包含JavaScript End to End Testing的，则点击跳转
  });
});
```
什么是<code>describe</code>,<code>it</code>和<code>expect</code>?
- describe和it: 可以查看[Mocha](https://docs.cypress.io/guides/references/bundled-tools.html#Mocha);
- expect：可以查看[Chai](https://www.chaijs.com/).

## 如何编写真实的测试用例
1. 请参考：[Write a real test](https://docs.cypress.io/guides/getting-started/writing-your-first-test.html#Write-a-real-test)
2. 如果VScode中describe被加上了波浪线提示，请安装mocha避免```yarn add @types/mocha```;
3. 如果继续有"cy"，加红的波浪线提示，请在文件的第一行加上```/// <reference types="cypress" />```;
