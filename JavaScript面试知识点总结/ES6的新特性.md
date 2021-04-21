## 参考文章
[1. ES6中常用的10个新特性讲解](https://juejin.cn/post/6844903618810757128)
[2. ES6新增特性](https://juejin.cn/post/6844903655099858957)

## 1. 变量声明：const和let
es6推荐使用let作为局部变量的声明，相比之前的var（因为有变量提升，所以无论声明在何处，都会被视为声明在函数的最顶部），let和var声明的区别：
```
var x = '全局变量';
{
  let x = '局部变量';
  console.log(x);   // 局部变量
}
console.log(x);   // 全局变量
```
let表示声明变量，const表示声明常量（意味着它的值在设置完成之后就不能再修改了），两者都是块级作用域；如果const是一个对象，对象所包含的值是可以被修改的。也就是说，对象所指向的地址没有改变。
```
const person = { name: 'Jessica' };

person.name = 'Nick'；   // 不会报错
person = { name: 'Nick' };    // 会报错，地址修改了
```
因此，需要我们注意：
- let关键字声明的变量不具备像变量提升（hosting）的特性。（为什么只是说像，因为实际上let声明的变量也具有百年来提升的特性，但是let声明之后，变量是存放在一个暂时性死区（dead zone）中，当用户在声明之前访问这个变量时，就会出现Reference Error这样的错误，看起来就像没有提升一样）;
- let和const只在最靠近的一个块中（花括号内）生效；
- 当使用常量const声明时，请使用大写变量，如：INIT_PAGE_SIZE；
- const在声明时必须被赋值。

## 2. 模板字符串
在es6之前我们是怎样处理模板字符串的呢？通过'\'和'+'来构建。
```
'I am a string, you are a number, so we are different, right? \
He is a boolean, he is different with us. His value is\
' + value_boolean + ', my value is ' + value_string + '.'; 
```
对于es6而言：
- 基本的字符串格式化，将表达式嵌入字符串中进行拼接，使用${}来界定；
- ES6反问号搞定。
```
`'I am a string, you are a number, so we are different, right?He is a boolean, he is different with us. His value is${value_boolean }, my value is ${value_string} .`
```

## 3. 箭头函数
箭头函数实际上就是函数的一种简写形式，使用括号包裹参数，再使用箭头（=>），再接上函数体。它最直观的三个特点是：
- 不需要function关键字来创建函数；
- 省略return关键字；
- 继承当前上下文的this关键字。
箭头函数与普通函数的区别主要是什么呢？可以参考：[ES6 - 箭头函数、箭头函数与普通函数的区别](https://juejin.cn/post/6844903805960585224)
```
// es5
var func = function(a, b) {
  return a + b;
} 
[1, 2, 3].map((function(item) {
  return item++;
}).bind(this));

// 箭头函数
var func = (a, b) => a + b;
[1, 2, 3].map(item => item++);
```
注意：
- 如果箭头函数没有参数，直接写一个空括号即可；
- 如果箭头函数的参数只有一个，可以省区包裹参数的括号；
- 如果箭头函数有多个参数，将参数一次用逗号分隔，包裹再括号中即可。
如下：
```
// 没有参数
let func = () => {
  console.log(123);
}

// 只有一个参数
let func = name => {
  console.log(name);
}

// 多个参数
let func = (name, age, height) => {
  return [name, age, height];
}
```
### 箭头函数与普通函数的区别
1. 语法简洁，清晰
2. 箭头函数不会创建自己的this（这个很重要！！！）
MDN的官方解释是：箭头函数没有自己的this，arguments，super或new.target。箭头函数表达式更适用于那些本来需要匿名函数的地方，并且它不能用作构造函数。因此它只会从自己作用域链的上一层继承this。
所以，箭头函数会捕获它在定义时（注意不是调用时）所处的外层执行环境的this，并且继承这个this，定义时确定了之后就永远不会改变了。请看下面的例子：
```
var id = 'outter';

function funA() {
  setTimeout(function() {
    console.log(this.id);
  }, 1000);
}

function funB() {
  setTimeout(() => {
    console.log(this.id);
  }, 1000);
}

funA.call({ id: 'inner' });   // outter
funB.call({ id: 'inner' });   // inner
```
分析：
- funA中的setTimeout中使用普通函数，当1秒后函数执行时，这个时候实在全局作用域中执行的，所以this指向Window对象，所以this.id就是全局变量id，输出“outter”;
-  funB中的setTimeout中使用箭头函数，这个箭头函数的this在定义的时候就确定了，他继承的是外层funB的执行环境中的this，而funB调用时this被call方法改变到了对象{ id: 'inner' }中，所以输出"inner"。

再来看另外一个例子：
```
var id = 'outter_obj';
var obj = {
  id: 'inner_obj',
  funA: function() {
    console.log(this.id);
 },
  funB: () => {
    console.log(this.id);
  }
};

obj.funA();    // inner_obj
obj.funB();    // outter_obj
```
分析：
- obj的方法funA中使用普通函数定义，普通函数作为对象的方法调用时，this指向它所属的对象。所以this.id就是obj.id，所以输出‘inner_obj’；
- 但是funB是使用箭头函数定义的，箭头函数的this实际上是继承的它定义时所处的全局执行环境中的this，所以指向Window对象，输出outter_obj。
- 需要注意的是：定义对象的花括号{}是无法形成一个单独的执行环境的，它依旧处于全局的执行环境中。
- 这个例子也说明了：箭头函数继承来的this指向永远不会改变（重要！！！）

### call() apply()和bind()无法改变箭头函数的this指向
call() apply()和bind()方法可以用来动态修改函数执行时this的指向，但由于箭头函数的this定义时就已经确定且永远不会改变。所以这些方法永远改变不了箭头函数的this指向，虽然即使这样做了代码也不会报错。
```
var id = 'outter';
// 箭头函数定义在全局作用域
let fun = () => {
    console.log(this.id)
};

fun();     // 'outter'
// this的指向不会改变，永远指向Window对象
fun.call({id: 'Obj'});     // 'outter'
fun.apply({id: 'Obj'});    // 'outter'
fun.bind({id: 'Obj'})();   // 'outter'
```

### 箭头函数不能作为构造函数使用
首先，先看看构造函数的new都做了什么？主要有如下几个步骤：
- JS内部首先会生成一个对象；
- 再把函数中的this指向该对象；
- 然后执行构造函数中的语句；
- 最终返回该对象实例。
但是，因为箭头函数没有自己的this，它的this其实继承了外层执行环境中的this，且this指向永远不会随在哪里调用、被谁调用而改变，所以箭头函数不能作为构造函数使用，否则当new调用时就会报错。
```
let fun = (name, age) => {
  this.name = name;
  this.age = age;
};

// 下面的代码会报错
let p = new fun('Jessica', 18);
```

### 箭头函数没有自己的arguments和prototype
1. 箭头函数没有自己的arguments对象，因为在箭头函数中访问arguments实际上获得的是外层局部执行环境中的值。
```
// 例1
let fun = (val) => {
  console.log(val);    // test
  // 但下面会报错：Uncaught ReferenceError: arguments is not defined，因为外层全局环境没有arguments对象
  console.log(arguments);
};

fun('test');

// 例2
function fun(name, age) {
  let args = arguments;
  console.log(args);
  let funIn = () => {
    let argsIn = arguments;
    console.log(argsIn);
    console.log(args === argsIn);
  };
  funIn();
}
fun('Jessica', 18);
```
![arguments](https://user-images.githubusercontent.com/10249805/109605411-15ef1900-7b60-11eb-86bb-4dcba4dce5cc.png)

从上图可以看出，fun函数内部的箭头函数funIn中的arguments对象，其实是沿作用域链向上访问的外层fun函数的arguments对象。但是，我们仍然可以使用rest参数代替arguments对象，来访问箭头函数的参数列表。

2. 箭头函数没有原型prototype
```
let sayName =() => {
  console.log('Hello Jessica');
};
console.log(sayName.prototype);    // undefined
```
## 4. 对象/数组的解构
ES5的写法：

![ES5写法](https://user-images.githubusercontent.com/10249805/109606863-36b86e00-7b62-11eb-9628-3fded1704e1d.png)

ES6写法：

![ES6写法](https://user-images.githubusercontent.com/10249805/109606881-3d46e580-7b62-11eb-9155-2f646aa6c5f5.png)

## 5. for...of和for...in
- for...of用于遍历一个迭代器，比如说数组：

（1）遍历数组
![1614668278](https://user-images.githubusercontent.com/10249805/109610440-bb59bb00-7b67-11eb-8a76-3b23ac39f462.png)

（2）遍历对象
这个时候会抛出异常。
![1614668399](https://user-images.githubusercontent.com/10249805/109610644-fbb93900-7b67-11eb-85a3-17cdc47fe9ae.png)


- for...in用来遍历对象中的属性
for...in如果应用于数组循环，返回的是数组下标、数组的属性和原型上的方法和属性，而for...in应用于对象循环返回的是对象的属性名和原型中的方法和属性。
（1）遍历对象
![1614666337](https://user-images.githubusercontent.com/10249805/109607446-2d7bd100-7b63-11eb-9010-7183bdd1895b.png)

（2）遍历数组
![1614668006](https://user-images.githubusercontent.com/10249805/109609979-163ee280-7b67-11eb-8b36-2af915e2e003.png)

总结：for...in更适合遍历对象，for...of更适合遍历数组。

## 7. ES6新增了class
ES6中支持class语法，不过，ES6的class不是新的对象继承模型，它只是原型链的语法糖表现形式。函数中使用static关键词定义构造函数的方法和属性。
```
class Student {
  constructor() {
    console.log("I'm a student.");
  }
 
  study() {
    console.log('study!');
  }
 
  static read() {
    console.log("Reading Now.");
  }
}
 
console.log(typeof Student); // function
let stu = new Student(); // "I'm a student."
stu.study(); // "study!"
stu.read(); // "Reading Now."
```
类中的继承和超集：
```
class Phone {
  constructor() {
    console.log("I'm a phone.");
  }
}
 
class MI extends Phone {
  constructor() {
    super();
    console.log("I'm a phone designed by xiaomi");
  }
}
 
let mi8 = new MI();
```
分析：
- extends允许一个子类继承父类，需要注意的是子类的constructor函数中需要执行super()函数。当然，也可以在子类方法中调用父类的方法，如：super.parentMethodName();
- 类的声明不会提升，如果你想使用某个class，那你必须在使用之前就定义它，否则会抛出一个ReferenceError的错误；
- 在类中定义函数不需要使用function关键字。

## 8. 默认参数
ES6可以在定义函数的时候设置参数的默认值。
```
function sayName(name='Jessica') {
  // 如果调用函数的时候没有传参，这个时候才会使用默认值。
  console.log(`My name is ${name}`);
}

sayName();    // =>My name is Jessica
sayName('Nick');    // =>My name is Nick
```



