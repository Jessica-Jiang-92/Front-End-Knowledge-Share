## 参考文章
[1. 从ES6重新认识JavaScript设计模式(一): 单例模式](https://zhuanlan.zhihu.com/p/34754447)

## 单例模式

### 1. 什么是单例模式，特点是什么
单例模式是一种非常常用但却相对而言比较简单的设计模式。它是指在一个类中只能有一个实例，即使多次实例化该类，也只能返回第一次实例化后的对象。单例模式不仅能减少不必要的内存开销，并且在减少全局的函数和变量冲突也具有重要的意义。

单例模式能保证一个类只有一个实例，并且提供一个访问它的全局访问点。也就是说，在整个生命周期中，该对象的生产都始终是一个，不曾变化。

它的特点是：
- 公有方法获取实例；
- 私有的构造函数；
- 私有的成员变量。

### 2. 作用
1. 在要求线程安全的情况下，保证了类实例的唯一性，线程安全；
2. 在不需要多实例存在时，保证了类实例的单一性，不浪费内存。

### 3. 最简单的单例模式
即使目前我们对单例模式还比较模糊，但在我们日常开发中也早就已经使用过单例模式了。看下面的例子：
```
let timeTool = {
  name: '时间处理工具',
  getISODate: function() {},
  getUTCDate: function() {},
};
```
以对象字面量创建对象的方式在js开发中很常见。上面的例子就是以[对象字面量](https://zhuanlan.zhihu.com/p/38995653)的方式来封装了一些方法处理时间格式。全局只暴露了一个<code>timeTool</code>对象，在需要使用时，只需要采用<code>timeTool.getISODate()</code>调用即可。<code>timeTool</code>对象就是单例模式的体现。在JavaScript创建对象的方式十分灵活，可以直接通过对象字面量的方式实例化一个对象，而其他面向对象的语言必须使用类进行实例化。所以这里的 <code>timeTool</code>已经是一个实例，且ES6中 <code>let</code>和 <code>const</code>不允许重复声明的特性，确保了<code>timeTool</code>不能被重新覆盖。

### 4. 惰性单例
采用对象字面量创建单例只能适用于简单的应用场景，一旦该对象十分复杂，那么创建对象本身就需要一定的耗时，且该对象可能需要一些私有变量和私有方法。此时使用对象字面量创建单例就不再行得通了，我们还是需要采用构造函数的方式实例化对象。下面就是使用立即执行函数和构造函数的方式改造上面的<code>timeTool</code>工具。
```
let timeTool = (function() {
  let _instance = null;
  
  function init() {
    let now = new Date();    // 私有变量
    // 公用属性和方法
    this.name = '时间处理工具';
    this.getISODate = function() {
      return now.toISOString();
    }
    this.getUTCDate = function() {
      return now.toUTCString();
    }
  }

  return function() {
    if (!_instance) {
      _instance = new init();
    }
    return _instance;
  }
})()
```
这时的<code>timeTool</code>实际上是一个函数，_instance作为实例对象最开始赋值为<code>null</code>,<code>init</code>函数是其构造函数，用于实例化对象，立即执行函数返回的是匿名函数，用于判断实例是否创建，只有当调用<code>timeTool()</code>时进行实例化，这就是惰性单例的应用，不在js加载时就进行实例化创建，而是在需要的时候再进行单例的创建。如果再次调用，那么返回的永远是第一次实例化后的实例对象。
```
let ins1 = timeTool();
let ins2 = timeTool();

console.log(ins1 === ins2);     // true
```

## 单例模式的应用场景

### 1. 命名空间
一个项目往往都不止一个程序员来开发和维护，一个程序员很难去弄清楚另外一个程序员暴露在项目中的全局变量和方法。如果将变量和方法都暴露在全局中，变量冲突是难以避免的。就像下面的事故一样：
```
// 开发者A写了一大段js代码
function addNum() {}

// 另一个开发者B开始写js代码
var addNum = '';

// A重新维护该js代码
addNum();    // 这个时候抛出错误：Uncaught TypeError: addNum is not a function
```
命名空间就是用来解决全局变量冲突的问题，我们完全可以只暴露一个对象名，将变量作为该对象的属性，将方法作为该对象的方法，这样就能大大减少全局变量的个数。
```
// 开发者A写了一大段js代码
let devA = {
  addNum() {};
};

// 另一个开发者B开始写js代码
let devB = {
  addNum: '';
}

// A重新维护该js代码
devA.addNum();
```
上面的代码中，<code>devA</code>和<code>devB</code>就是两个命名空间，采用命名空间可以有效减少全局变量的数量，以此解决变量冲突的发生。

### 2. 管理模块
上面讲到的<code>timeTool</code>对象时一个只用来处理时间的工具库，但是实际开发过程中的库可能会有多种多样的功能，例如处理ajax请求，操作dom或者处理事件。这个时候单例模式还可以用来管理代码库中的各个模块，如下所示：
```
var devA = (function() {
  // ajax模块
  var ajax = {
    get: function(api, obj) { console.log('ajax get调用'); },
    post: function(api, obj) {}
  };

  // dom模块
  var dom = {
    get: function() {},
    create: function() {},
  };

  // event模块
  var event = {
    add: function() {},
    remove: function() {},
  };

  return {
    ajax: ajax,
    dom: dom,
    event: event,
  };
})()
```
上面的代码库中有<code>ajax</code>, <code>dom</code>和<code>event</code>三个模块，用同一个命名空间<code>devA</code>来管理。在进行相应的操作的时候，只需要<code>devA.ajax.get()</code>进行调用即可。这样可以让库的功能更加清晰。

##  ES6中的单例模式

### 1. ES6创建对象
ES6创建对象时引入了<code>class</code>和<code>constructor</code>来创建。下面我们来看看如何使用ES6语法实例化苹果公司。
```
class Apple {
  constructor(name, creator, products) {
    this.name = name;
    this.creator = creator;
    this.products = products;
  }
}

let appleCompany = new Apple('苹果公司'， ‘乔布斯’, ['iPhone', 'iMac', 'iPad', 'iPod']);
let copyApple = new Apple('苹果公司'， ‘李四’, ['iPhone', 'iMac', 'iPad', 'iPod']);
```

### 2. ES6中创建的单例模式
显然，在全世界范围内，苹果公司有且只有一个。所以<code>appleCompany</code>应该是一个单例，现在我们使用ES6语法将<code>constructor</code>改写为单例模式的构造器。
```
class SingletonApple {
  constructor(name, creator, products) {
    if(!SingletonApple .instance) {
      this.name = name;
      this.creator = creator;
      this.products = products;
      // 将this挂载到SingletonApple这个类的instance属性上
      SingletonApple.instance = this;
    }
    return SingletonApple.instance;
  }
}

let appleCompany = new SingletonApple('苹果公司'， ‘乔布斯’, ['iPhone', 'iMac', 'iPad', 'iPod']);
let copyApple = new SingletonApple('苹果公司'， ‘李四’, ['iPhone', 'iMac', 'iPad', 'iPod']);

console.log(appleCompany === copyApple);    // true
```

### 3. ES6的静态方法优化代码
ES6为<code>class</code>提供了<code>static</code>关键字定义的静态方法，我们可以将<code>constructor</code>中判断是否实例化的逻辑放入一个静态方法<code>getInstance</code>中，调用该静态方法获取实例，<code>constructor</code>中只包含需要实例化的代码，这样可以增强代码的可读性、结构更加优化。
```
class SingletonApple {
  constructor(name, creator, products) {
      this.name = name;
      this.creator = creator;
      this.products = products;
  }

  // 静态方法
  static getInstance(name, creator, products) {
      if(!this.instance) {
        this.instance = new SingletonApple(name, creator, products);
      }
      return this.instance;
    }
}

let appleCompany = new SingletonApple('苹果公司'， ‘乔布斯’, ['iPhone', 'iMac', 'iPad', 'iPod']);
let copyApple = new SingletonApple('苹果公司'， ‘李四’, ['iPhone', 'iMac', 'iPad', 'iPod']);

console.log(appleCompany === copyApple);    // true
```

## 传统OO语言的单例模式
1. 使用闭包创建，且符合惰性单例的特征。
```
var Singleton = function(name) {
  this.name = name;
}

Singleton.prototype.getName = function() {
  alert(this.name);
}
// 利用闭包创建符合惰性的单例
Singleton.getInstance = (function(name)) {
  var instance;
  return function(name) {
    if (!instance) {
      instance = new Singleton(name);
    }
  }
})();

var a = Singleton.getInstance('test1');
var b = Singleton.getInstance('test2');

console.log(a === b);    // true
```
2. 透明的单例模式
```
// 反面的单例模式例子
var CreateDiv = (function() {
  var instance;
  var CreateDiv = function(html) {
    if (instance) {
      return instance;
    }
    this.html = html;
    this.init();
    return instance = this;
  };

  CreateDiv.prototype.init = function() {
    var div = document.createElement('div');
    div.innerHTML = this.html;
    document.body.appendChild(div);
  }

  return CreateDiv;
})();

var a = new CreateDiv('test1');
var b = new CreateDiv('test2');
```
这种形式的单例模式特点：
为了把instance封装起来，我们使用了自执行的匿名函数和闭包，并且让这个匿名函数返回真正的Singleton构造方法，当然这增加了程序的复杂性。

CreateDiv构造函数做了两件事。
- 创建对象和执行初始化init方法；
- 保证只有一个对象。
但是，这样创建出来的实例，不符合设计模式中的单一职责的概念。

## 项目实战应用

### 1.实现登陆弹框
登陆弹框在项目中是一个比较经典的单例模式，因为对于大部分网站不需要用户必须登陆才能浏览，所以登陆操作的弹框可以在用户点击登陆按钮后再进行创建。而且登陆框永远只有一个，不会出现多个登陆框的情况，也就意味着再次点击登录按钮后返回的永远是一个登录框的实例。

我们梳理一下登录框的流程，然后再实现。
1. 给顶部导航模块的登录按钮注册点击事件；
2. 登录按钮点击后JS动态创建遮罩层和登录框；
3. 遮罩层和登录框插入到页面中；
4. 给登录框中的关闭按钮注册事件，用于关闭遮罩层和弹框；
5. 给登录框中的输入框添加校验；
6. 给登录框中的确定按钮添加事件，用于ajax请求；
7. 给登录框中的清空按钮添加事件，用于清空输入框。

因为5和6是登录框的实际项目逻辑，与单例模式关系不大，所以这里主要实现1-4步。

### 1. 给页面添加顶部导航栏的HTML代码
```
<nav class="top-bar">
  <div class="top-bar_left">
    TEST
  </div>
  <div class="top-bar_right">
    <div class="login-btn">登录</div>
    <div class="signin-btn">注册</div>
  </div>
</nav>
```
### 2. 使用ES6语法创建Login类
```
class Login {

  //构造器
  constructor() {
    this.init();
  }

  //初始化方法
  init() {
    //新建div
    let mask = document.createElement('div');
    //添加样式
    mask.classList.add('mask-layer');
    //添加模板字符串
    mask.innerHTML = 
    `
    <div class="login-wrapper">
      <div class="login-title">
        <div class="title-text">登录框</div>
        <div class="close-btn">×</div>
      </div>
      <div class="username-input user-input">
        <span class="login-text">用户名:</span>
        <input type="text">
      </div>
      <div class="pwd-input user-input">
        <span class="login-text">密码:</span>
        <input type="password">
      </div>
      <div class="btn-wrapper">
        <button class="confrim-btn">确定</button>
        <button class="clear-btn">清空</button>
      </div>
    </div>
    `;
    //插入元素
    document.body.insertBefore(mask, document.body.childNodes[0]);

    //添加关闭登录框事件
    Login.addCloseLoginEvent();
    
    //静态方法: 获取元素
    static getLoginDom(cls) {
      return  document.querySelector(cls);
    }

    //静态方法: 注册关闭登录框事件
    static addCloseLoginEvent() {
      this.getLoginDom('.close-btn').addEventListener('click', () => {
        //给遮罩层添加style, 用于隐藏遮罩层
        this.getLoginDom('.mask-layer').style = "display: none";
      })
    }

  //静态方法: 获取实例(单例)
  static getInstance() {
    if(!this.instance) {
      this.instance = new Login();
    } else {
      //移除遮罩层style, 用于显示遮罩层
      this.getLoginDom('.mask-layer').removeAttribute('style');
    }
    return this.instance;
    }
  }
```

### 3. 给登录框添加注册点击事件
```
// 注册点击事件
Login.getLoginDom('.login-btn').addEventListener('click', () => {
  Login.getInstance();
})
```
分析：
在上面的登录框中，实现了只创建一个Login类，但是却实现了一个并不简单的登录功能。在第一次点击登录按钮的时候，我们调用<code>Login.getInstance()</code>实例化了一个登录框，且在之后的点击中并没有重新创建新的登录框，只是移除掉了<code>display:none</code>这个样式来显示登录框，节省了内存开销。

## 总结
单例模式虽然简单，但是在项目中的应用场景却是相当多的，单例模式的核心是<b>确保只有一个实例，并提供全局访问。</b>就像我们只需要一个浏览器的<code>window</code>对象，jQuery的<code>$</code>对象也不再需要第二个。由于JavaScript代码书写方式十分灵活，这也导致了如果没有严格的规范的情况下，大型的项目中JavaScript不利于多人协同开发，使用单例模式进行命名空间来管理模块是一个很好的开发习惯，能够有效的解决协同开发变量冲突的问题，灵活使用单例模式，也能够减少不必要的内存开销，提高使用体验。

