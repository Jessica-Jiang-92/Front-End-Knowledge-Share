## 前言

箭头函数应该是ES6中新增的最受欢迎的一个特性。这里我主要从三个方面来介绍箭头函数：this的指向；自执行函数；面试相关

## 什么是箭头函数

形式上就是类似于(=>)所定义的一种新语法，对比一下ES5和ES6就能够知道。

ES5：
```
function addNum(n) {
  return n + 5;
}

addNum(3);  // 8
```

ES6: 
```
var addNum = n => n + 5;

addNum(3); // 8
```
箭头函数的写法简洁得多，由于隐式返回，我们还可以省略花括号和 return 语句。它的特点总结起来就是：
1. 更短的语法（没有参数时，使用圆括号代替；只有一个参数时，可以省略圆括号；有多个参数时，使用逗号进行分割）
2. 不能通过new关键字调用：箭头函数没有[[construct]]方法，所以不能被用作构造函数。
```
let F = () => {};

let f = new F(); // 报错
```
3. 没有原型：由于不可用通过new关键字调用，因而没有构建原型的需求，所以箭头函数不存在prototype这个属性
4. 不能用作Generator函数：在箭头函数中，不可用使用yield命令，因此箭头函数不能用作Generator函数
5. 没有 arguments、super、new.target
6. 没有 this 绑定
在理解箭头函数中的this指向问题之前，我们先来回看在 ES5 中的一个例子：
```
var obj = {
  value:0,
  fn:function(){
    this.value ++
  }
}
obj.fn();
console.log(obj.value); // 1

在每次调用 obj.fn() 时，期望的是将 obj.value 加 1。
```
现在我们将代码修改一下：
```
var obj = {
  value:0,
  fn:function(){
    var f = function(){
      this.value ++
    }
    f();
  }
}
obj.fn();
console.log(obj.value); // 0

我们将代码修改了一下，在 obj.fn 方法内增加了一个函数 f ，并将 obj.value 加 1 的动作放到了函数 f 中。
但是由于 javascript 语言设计上的一个错误，函数 f 中的 this 并不是 方法 obj.fn 中的 this，导致我们没
法获取到 obj.value 。
```
为了解决此类问题，在 ES5 中，我们通常会将外部函数中的`this`赋值给一个临时变量（通常命名为 that、_this、self），
在内层函数中若希望使用外层函数的this 时，通过这个临时变量来获取。修改代码如下：
```
var obj = {
  value:0,
  fn:function(){
    var _this = this;
    var f = function(){
      _this.value ++
    }
    f();
  }
}
obj.fn();
console.log(obj.value); // 1

从这个例子中，我们知道了在 ES5 中如何解决内部函数获取外部函数 this 的办法。
然后我们来看看箭头函数相对于 ES5 中的函数来说，它的 this 指向有和不同。
```
箭头函数this的定义：箭头函数体内的 this 对象就是定义时所在的对象，而不是使用时所在的对象。

怎么理解这个定义呢？
```
function fn(){
  let f = ()=>{
    console.log(this)
  }
}
```
我们将上述代码通过babel转换成ES5格式的代码，如下：
```
function fn(){
var _this=this;
var f = function f() {
  console.log(_this);
  };
}
```
我们发现居然和之前在 ES5 中解决内层函数获取外层函数 this 的方法一样，定义一个临时变量_this。
那么箭头函数的this哪里去了，答案是：箭头函数根本没有自己的this。
总结一下就是：
- 箭头函数的外层如果有普通函数，那么箭头函数的 this 就是外层普通函数的this
- 箭头函数的外层如果没有普通函数，那么箭头函数的 this 就是全局变量












