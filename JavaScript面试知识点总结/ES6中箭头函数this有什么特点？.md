## 前言

箭头函数应该是ES6中新增的最受欢迎的一个特性。这里我主要从三个方面来介绍箭头函数：this的指向；自执行函数；面试相关

## 1. 什么是箭头函数？

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
下面举几个例子：
```
let obj = {
    fn:function(){
        console.log('我是普通函数',this === obj)
        return ()=>{
            console.log('我是箭头函数',this === obj)
        }
    }
}

console.log(obj.fn()())

// 我是普通函数 true
// 我是箭头函数 true

从这个例子，我们可以看出，箭头函数的 this 与外层函数的 this 是相等的。
```
继续看一个多层箭头函数嵌套的例子：
```
let obj = {
    fn:function(){
        console.log('我是普通函数',this === obj)
        return ()=>{
            console.log('第一个箭头函数',this === obj)
            return ()=>{
                console.log('第二个箭头函数',this === obj)
                return ()=>{
                    console.log('第三个箭头函数',this === obj)
                }
            }
        }
    }
}

console.log(obj.fn()()()())
// 我是普通函数 true
// 第一个箭头函数 true
// 第二个箭头函数 true
// 第三个箭头函数 true

在这个例子中，我们知道，对于箭头函数来说，箭头函数的 this 与外层的第一个普通函数的 this 相等，与嵌套了几层箭头函数无关。
```
再来看一个没有外层函数的例子：
```
let obj = {
    fn:()=>{
        console.log(this === window);
    }
}
console.log(obj.fn())

// true

这个例子证明了：在箭头函数外层没有普通函数时，箭头函数的 this 与全局对象相等。

需要注意的是，浏览器环境下全局对象为 window，node 环境下全局对象为 global，验证的时候需要区分一下。
```

## 2. 箭头函数碰上call, apply, bind

箭头函数中根本没有自己的 this ,那么当箭头函数碰到 call、apply、bind 时，会发生什么呢？
我们知道，call 和 apply 的作用是改变函数 this 的指向，传递参数，并将函数执行， 而 bind 的作用是生成一个绑定 this 并预设函数参数的新函数。
所以：
- 对箭头函数使用call/apply方法时，只会传入参数并调用函数，并不会改变箭头函数中this的指向。
- 当对箭头函数使用bind方法时，只会返回一个预设参数的新函数，并不会绑定新函数的this指向。
我们看下面的代码：
```
window.name = 'window_name';

let f1 = function(){return this.name}
let f2 = ()=> this.name

let obj = {name:'obj_name'}

f1.call(obj) // obj_name
f2.call(obj) // window_name

f1.apply(obj) // obj_name
f2.apply(obj) // window_name

f1.bind(obj)() // obj_name
f2.bind(obj)() // window_name
```
这里，我们声明了普通函数 f1，箭头函数 f2。
普通函数的 this 指向是动态可变的，所以在对 f1 使用 call、apply、bind 时，f1 内部的 this 指向会发生改变。
箭头函数的 this 指向在其定义时就已确定，永远不会发生改变，所以在对 f2 使用 call、apply、bind 时，会忽略传入的上下文参数。

## 3. 自执行函数

在 ES6 的箭头函数出现之前，自执行函数一般会写成这样：
```
(function(){
    console.log(1)
})()
```
或者
```
(function(){
    console.log(1)
}())
```
箭头函数当然也可以被用作自执行函数，可以这样写：
```
(() => {
    console.log(1)
})()
```
但是，下面这种写法会报错：
```
(() => {
    console.log(1)
}())
```
那么，为什么会报错呢？

我们知道箭头函数是属于 AssignmentExpression 的一种，而函数调用属于 CallExpression，规范中要求当 CallExpression 时，
左边的表达式必须是 MemberExpression 或其他的 CallExpression，而箭头函数不属于这两种表达式，所以在编译时就会报错。

## 4. 面试题目

在面试中关于箭头函数的考察，主要集中在 arguments 关键字的指向和箭头函数的this指向上。

- 题目1
```
function foo(n) {
  var f = () => arguments[0] + n;
  return f();
}

let res = foo(2);

console.log(res); // 输出结果是什么？
```
![1](https://user-images.githubusercontent.com/82437559/117522024-83497b00-afe3-11eb-879a-9ec7ca337500.png)

箭头函数没有自己的 arguments ，所以题中的 arguments 指代的是 foo 函数的arguments对象。所以arguments[0]等于2，n 等于2，结果为4。

- 题目2
```
function A() {
  this.foo = 1
}

A.prototype.bar = () => console.log(this.foo)

let a = new A()
a.bar() // 输出结果是多少
```
![2](https://user-images.githubusercontent.com/82437559/117522090-fb17a580-afe3-11eb-81ea-7dc583468644.png)

箭头函数没有自己的 this，所以箭头函数的 this 等价于外层非箭头函数作用域的this。 由于箭头函数的外层没有普通函数，
所以箭头函数中的 this 等价于全局对象，所以输出为 undefined。



