# Front-End-Interview & Personal blog share

- 分享一些前端知识和面试题，包括：[JavaScript](#1-JavaScript面试知识点总结)，[CSS](#2-CSS)，[Vue](#3-Vue相关)，[前端性能优化与webpack](#4-前端性能优化与webpack)，[算法](#5-算法)，[工具分享](#6-项目构建)相关的内容。
- 这个库会不定期地更新和分享。
- 收藏请点Star，订阅请点Watch。👋👋👋
- 如果大家在阅读的过程中发现有出错的地方，欢迎留言指正。


## 1. JavaScript面试知识点总结
### 目录

- [1. JS数据类型有哪些？如何进行类型判断？不同类型的内存图大致是怎样的？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%EF%BC%8C%E6%95%B0%E6%8D%AE%E5%86%85%E5%AD%98%E5%9B%BE%E7%AD%89%E7%AD%89.md)
- [2. 闭包是什么，原理是什么，怎么用？哪些场景下会用到？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E9%97%AD%E5%8C%85%E5%8E%9F%E7%90%86%E5%8F%8A%E4%BD%BF%E7%94%A8%E5%9C%BA%E6%99%AF.md)
- [3. 讲讲深拷贝与浅拷贝，如何实现，有哪些方式？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E6%B7%B1%E6%8B%B7%E8%B4%9D%2B%E6%B5%85%E6%8B%B7%E8%B4%9D.md)
- [4. 讲一讲 JavaScript 的垃圾回收机制](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/JavaScript%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E6%9C%BA%E5%88%B6.md)
- [5. 原型，原型链和继承的关系，如何实现继承？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E5%8E%9F%E5%9E%8B%2B%E5%8E%9F%E5%9E%8B%E9%93%BE%2B%E7%BB%A7%E6%89%BF.md)
- [6. 讲讲es6的新特性主要有哪些？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/ES6%E7%9A%84%E6%96%B0%E7%89%B9%E6%80%A7.md)
- [7. http协议，缓存协议（强缓存+协商缓存）](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/http%E5%8D%8F%E8%AE%AE%2B%E7%BC%93%E5%AD%98%E5%8D%8F%E8%AE%AE.md)
- [8. call, bind, apply,三者的关系和区别](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/call%2Cbind%2Capply%E4%B8%89%E8%80%85%E7%9A%84%E5%85%B3%E7%B3%BB%E5%92%8C%E5%8C%BA%E5%88%AB.md)
- [9. 说一说JavaScript的事件循环机制（Event Loop）](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF%E6%9C%BA%E5%88%B6.md)
- [10. 从输入URL到最终展示页面的过程中发生了什么？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E4%BB%8E%E8%BE%93%E5%85%A5URL%E5%88%B0%E6%9C%80%E7%BB%88%E5%B1%95%E7%A4%BA%E9%A1%B5%E9%9D%A2%E7%9A%84%E8%BF%87%E7%A8%8B%E4%B8%AD%E5%8F%91%E7%94%9F%E4%BA%86%E4%BB%80%E4%B9%88.md)
- [11. 你了解内部属性[[class]]吗？内部属性[[Class]]是什么？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E5%86%85%E9%83%A8%E5%B1%9E%E6%80%A7%5B%5BClass%5D%5D.md)
- [12. 你使用过node吗？在你做过的项目中有哪些node的应用？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/%E4%BD%BF%E7%94%A8%E8%BF%87node%E5%90%97%EF%BC%9F%E9%A1%B9%E7%9B%AE%E4%B8%AD%E6%9C%89%E5%93%AA%E4%BA%9Bnode%E7%9A%84%E5%BA%94%E7%94%A8.md)
- [13. 讲一讲JavaScript设计模式中的单例模式](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/JavaScript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%AD%E7%9A%84%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F.md)
- [14. ES6的箭头函数中this有什么特点？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/JavaScript%E9%9D%A2%E8%AF%95%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/ES6%E4%B8%AD%E7%AE%AD%E5%A4%B4%E5%87%BD%E6%95%B0this%E6%9C%89%E4%BB%80%E4%B9%88%E7%89%B9%E7%82%B9%EF%BC%9F.md)


## 2. CSS
### 目录

- [1. 垂直居中的几种实现方案 ](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/CSS%E7%9B%B8%E5%85%B3/%E5%9E%82%E7%9B%B4%E5%B1%85%E4%B8%AD%E7%9A%84%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%A1%88.md)
- [2. 你能用几种方案实现双栏布局？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/CSS%E7%9B%B8%E5%85%B3/%E5%8F%8C%E6%A0%8F%E5%B8%83%E5%B1%80.md)
- [3. CSS选择器的优先级顺序是怎样的呢？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/CSS%E7%9B%B8%E5%85%B3/%E9%80%89%E6%8B%A9%E5%99%A8%E7%9A%84%E4%BC%98%E5%85%88%E7%BA%A7%E9%A1%BA%E5%BA%8F.md)
- [4. display: none和visibility: hidden的区别](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/CSS%E7%9B%B8%E5%85%B3/display:%20none%E5%92%8Cvisibility:%20hidden%E7%9A%84%E5%8C%BA%E5%88%AB.md)


## 3. Vue相关
### 目录
- [1. Vue的生命周期有哪些？每个周期内完成的功能是什么？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/Vue%E7%9B%B8%E5%85%B3/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%8F%8A%E5%8A%9F%E8%83%BD.md)
- [2. 具体详细的讲一讲MVVM数据绑定的原理+实现](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/Vue%E7%9B%B8%E5%85%B3/MVVM%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0.md)
- [3. 介绍一下Vue与React之间有什么相同点与不同点](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/Vue%E7%9B%B8%E5%85%B3/%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8BVue%E4%B8%8EReact%E4%B9%8B%E9%97%B4%E6%9C%89%E4%BB%80%E4%B9%88%E7%9B%B8%E5%90%8C%E7%82%B9%E4%B8%8E%E4%B8%8D%E5%90%8C%E7%82%B9.md)
- [4. 路由模式有哪两种？它们的区别是什么？](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/Vue%E7%9B%B8%E5%85%B3/%E8%B7%AF%E7%94%B1%E6%A8%A1%E5%BC%8F%E6%9C%89%E5%93%AA%E4%B8%A4%E7%A7%8D%EF%BC%9F%E5%AE%83%E4%BB%AC%E7%9A%84%E5%8C%BA%E5%88%AB%E6%98%AF%E4%BB%80%E4%B9%88%EF%BC%9F.md)
- [6. 为什么Vue中不要用index作为key？实际在问diff算法](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/Vue%E7%9B%B8%E5%85%B3/%E4%B8%BA%E4%BB%80%E4%B9%88Vue%E4%B8%AD%E4%B8%8D%E8%A6%81%E7%94%A8index%E4%BD%9C%E4%B8%BAkey%EF%BC%9F%E5%AE%9E%E9%99%85%E5%9C%A8%E9%97%AEdiff%E7%AE%97%E6%B3%95%20.md)

## 4. 前端性能优化与webpack

### 目录

- [1. webpack中有哪些常用的plugin，分别是什么作用？]()
- [2. 你应该知道的几个webpack优化方法]()
- [3. 讲一讲你知道的前端性能优化方案]()
- [4. 讲一讲防抖与节流]()
- [5. 如何最大程度的优化webpack打包速度？]()
- [6. hash，contenthash，chunkhash的区别]()

## 5. 算法

几乎在所有大厂的面试中，算法是面试者（无论前端还是后端）永远无法逃避的一个重点内容。

笔者也写了一些LeetCode的算法（从易到难），参考[leetcode-algorithms](https://github.com/Jessica-Jiang-92/leetcode-algorithms)。欢迎大家留言讨论

### 目录

- [1. 写一个js函数，实现对一个数字每3位加一个逗号，如输入100000， 输出100,000（不考虑负数，小数)](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/%E7%AE%97%E6%B3%95/%E5%AE%9E%E7%8E%B0%E5%AF%B9%E6%95%B0%E5%AD%97%E6%AF%8F3%E4%BD%8D%E5%8A%A0%E4%B8%80%E4%B8%AA%E9%80%97%E5%8F%B7.md)
- [2. 统计一个字符串出现最多的字母：给出一段英文连续的英文字符串，找出重复出现次数最多的字母](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/%E7%AE%97%E6%B3%95/%E7%BB%9F%E8%AE%A1%E4%B8%80%E4%B8%AA%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%87%BA%E7%8E%B0%E6%9C%80%E5%A4%9A%E7%9A%84%E5%AD%97%E6%AF%8D.md)
- [3. 斐波那契数列：1、1、2、3、5、8、13、21。输入n，输出数列中第n位数的值。](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/%E7%AE%97%E6%B3%95/%E6%96%90%E6%B3%A2%E9%82%A3%E5%A5%91%E6%95%B0%E5%88%97.md)
- [4. 动态规划算法之背包问题](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/%E7%AE%97%E6%B3%95/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%AE%97%E6%B3%95.md)
- [5. 动态规划算法之完全背包问题](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/%E7%AE%97%E6%B3%95/%E5%AE%8C%E5%85%A8%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98.md)

## 6. 工具分享

前端知识确实庞大且繁杂，但实际工作中除了对基础知识的要求之外，项目整体自动化构建及自动化测试也成了不可缺少的技术栈。

### 目录

- [1. Continuous project building & Automatic Deployment-Jenkins+Docker](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/%E5%AE%9E%E7%94%A8%E5%B7%A5%E5%85%B7/Continuous%20project%20building%20%26%20Automatic%20Deployment-Jenkins%2BDocker.md)
- [2. Sharing of Automatic Testing Tools-Cypress](https://github.com/Jessica-Jiang-92/Front-End-Knowledge-Share/blob/master/%E5%AE%9E%E7%94%A8%E5%B7%A5%E5%85%B7/Sharing%20of%20Automatic%20Testing%20Tools-Cypress.md)







