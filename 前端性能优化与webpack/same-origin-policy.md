## 概述

浏览器安全的基石是"同源政策"（same-origin policy）。很多开发者都知道这一点，但了解得不全面。

## 1. 同源策略的定义

如果两个 URL 的 protocol、port (en-US) (如果有指定的话)和 host 都相同的话，则这两个 URL 是同源。这个方案也被称为“协议/主机/端口元组”，
或者直接是 “元组”。（“元组” 是指一组项目构成的整体，双重/三重/四重/五重/等的通用形式）。

也就是下面三者相同：
- 协议相同
- 域名相同
- 端口相同

举例来说，`http://www.example.com/dir/page.html`这个网址，协议是`http://`，域名是`www.example.com`，端口是`80`（默认端口可以省略）。它的同源情况如下。

- `http://www.example.com/dir2/other.html`：同源
- `http://example.com/dir/other.html`：不同源（域名不同）
- `http://v2.www.example.com/dir/other.html`：不同源（域名不同）
- `http://www.example.com:81/dir/other.html`：不同源（端口不同）

再比如与 URL `http://store.company.com/dir/page.html` 的源进行对比的示例：

|URL|结果|原因|
|-|-|-|
|http://store.company.com/dir2/other.html|同源|只有路径不同|
|http://store.company.com/dir/inner/another.html|同源|只有路径不同|
|https://store.company.com/secure.html|非同源|协议不同|
|http://store.company.com:81/dir/etc.html|非同源|端口不同（http://默认端口是80）|
|http://news.company.com/dir/other.html|非同源|主机不同|

## 2. 同源策略的目的

其目的是为了保证用户信息的安全，防止恶意的网站窃取数据。

设想一种场景：A网站时一家银行，用户登录以后，又去浏览其他网站。如果其他网站可以读取A网站的Cookie，会发生什么？
如果Cookie包含隐私（比如存款总额），这些信息就会泄漏。更加严重的是Cookie往往用来保存用户的登录状态，如果用户没有推出登录，其他
网站就可以冒出用户，为所欲为。因为浏览器还规定，提交表单不受同源策略的限制。由此可见，“同源策略”是必需的，否则Cookie可以共享，互
联网就毫无安全可言了。

## 3. 同源限制的范围

随着互联网的发展，“同源策略”越来越严格。目前，如果非同源，共有三种行为受到限制：
- （1）Cookie，LocalStorage和IndexDB无法读取；
- （2）DOM无法获得；
- （3）AJAX请求不能发送。

虽然这些限制是必要的，但是有时候很不方便，合理的用途也受到影响。那么就来看看该如何规避这3种限制呢？

## 4. 规避同源策略的限制

### 4.1 Cookie

Cookie是写入浏览器的一小段信息，只有同源的网页才能共享。但是，两个网页一级域名相同，只是耳机域名不同，浏览器允许通过设置`document.domain`共享Cookie。

举个例子：A网页是`http://test.example.com/a`，B网页是`http://test.example.com/b`，那么只要设置相同的`document.domain`，两个网页就可以共享Cookie。
```
document.domain = 'example.com';
```
现在，A网页通过脚本设置一个Cookie，如`document.cookie = 'hello, I am cookie';`

B网页在此时就可以读到这个Cookie，`var cookie = document.cookie;`

但需要注意的是：这种方法只适用于Cookie和iframe窗口，LocalStorage和IndexDB无法通过这种方法，规避同源策略，而要使用PostMessage API来实现。另外，服务器也可以在设置Cookie的时候，指定Cookie的所属域名为一级域名，比如：`.example.com`。
```
Set-Cookie: key=value; domain=.example.com; path=/
```
这样一来，二级和三级域名不用做任何设置，都可以读取这个Cookie。

### 4.2 iframe

如果两个网页不同源，就无法拿到对方的DOM。典型的例子就是`iframe`和`window.open`方法打开的窗口，它们与父窗口无法通信。

比如，父窗口下运行命令，如果`iframe`窗口不是同源，就会报错。
```
document.getElementById("myIFrame").contentWindow.document
// Uncaught DOMException: Blocked a frame from accessing a cross-origin frame.
```
上面的命令中，父窗口想获取子窗口的DOM，因为跨源导致报错。反之亦然，子窗口获取主窗口的DOM也会报错。
```
window.parent.document.body
// 报错
```
如果两个窗口一级域名相同，只是二级域名不同，那么设置上一节介绍的`document.domain`属性，就可以规避同源政策，拿到DOM。
对于完全不同源的网站，目前有三种方法，可以解决跨域窗口的通信问题。
- 片段识别符（fragment identifier）
- window.name
- 跨文档通信API（Cross-document messaging）

#### (1) 片段识别符

片段标识符（fragment identifier）指的是，URL的#号后面的部分，比如`http://example.com/x.html#fragment`的#fragment。如果只是改变片段标识符，页面不会重新刷新。
父窗口可以把信息，写入子窗口的片段标识符。
```
var src = originURL + '#' + data;
document.getElementById('myIFrame').src = src;
```
子窗口通过监听`hashchange`事件得到通知。
```
window.onhashchange = checkMessage;

function checkMessage() {
  var message = window.location.hash;
  // ...
}
```
同样的，子窗口也可以改变父窗口的片段标识符。
```
parent.location.href= target + "#" + hash;
```
#### (2) window.name
浏览器窗口有`window.name`属性。这个属性的最大特点是，无论是否同源，只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。
父窗口先打开一个子窗口，载入一个不同源的网页，该网页将信息写入`window.name`属性。
```
window.name = data;
```
接着，子窗口跳回一个与主窗口同域的网址。
```
location = 'http://www.baidu.com/parent';
```
然后，主窗口就可以读取子窗口的`window.name`了。
```
var data = document.getElementById('myFrame').contentWindow.name;
```
这种方法的优点是，`window.name`容量很大，可以放置非常长的字符串；缺点是必须监听子窗口`window.nam`e属性的变化，影响网页性能。

#### (3) window.postMessage

上面两种方法都属于破解，HTML5为了解决这个问题，引入了一个全新的API：跨文档通信 API（Cross-document messaging）。
这个API为`window`对象新增了一个`window.postMessage`方法，允许跨窗口通信，不论这两个窗口是否同源。
举例来说，父窗口`http://parent.com`向子窗口`http://children.com`发消息，调用postMessage方法就可以了。
```
var popup = window.open('http://children.com', 'title');
popup.postMessage('Hello World!', 'http://children.com');
```
`postMessage`方法的第一个参数是具体的信息内容，第二个参数是接收消息的窗口的源（origin），即"协议 + 域名 + 端口"。也可以设为*，表示不限制域名，向所有窗口发送。
子窗口向父窗口发送消息的写法类似。
```
window.opener.postMessage('Nice to see you', 'http://parent.com');
```
父窗口和子窗口都可以通过`message事件`，监听对方的消息。
```
window.addEventListener('message', function(e) {
  console.log(e.data);
},false);
```
`message`事件的事件对象`event`，提供以下三个属性:
- event.source：发送消息的窗口
- event.origin: 消息发向的网址
- event.data: 消息内容

下面的例子是，子窗口通过`event.source`属性引用父窗口，然后发送消息。
```
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  event.source.postMessage('Nice to see you!', '*');
}
```
`event.origin`属性可以过滤不是发给本窗口的消息。
```
window.addEventListener('message', receiveMessage);
function receiveMessage(event) {
  if (event.origin !== 'http://parent.com') return;
  if (event.data === 'Hello World') {
      event.source.postMessage('Hello', event.origin);
  } else {
    console.log(event.data);
  }
}
```
#### (4) LocalStorage

通过`window.postMessage`，读写其他窗口的 LocalStorage 也成为了可能。下面是一个例子，主窗口写入`iframe`子窗口的`localStorage`。
```
window.onmessage = function(e) {
  if (e.origin !== 'http://children.com') {
    return;
  }
  var payload = JSON.parse(e.data);
  localStorage.setItem(payload.key, JSON.stringify(payload.data));
};
```
上面代码中，子窗口将父窗口发来的消息，写入自己的`LocalStorage`。父窗口发送消息的代码如下
```
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
win.postMessage(JSON.stringify({key: 'storage', data: obj}), 'http://children.com');
```
加强版的子窗口接收消息的代码如下。
```
window.onmessage = function(e) {
  if (e.origin !== 'http://children.com') return;
  var payload = JSON.parse(e.data);
  switch (payload.method) {
    case 'set':
      localStorage.setItem(payload.key, JSON.stringify(payload.data));
      break;
    case 'get':
      var parent = window.parent;
      var data = localStorage.getItem(payload.key);
      parent.postMessage(data, 'http://parent.com');
      break;
    case 'remove':
      localStorage.removeItem(payload.key);
      break;
  }
};
```
加强版的父窗口发送消息代码如下。
```
var win = document.getElementsByTagName('iframe')[0].contentWindow;
var obj = { name: 'Jack' };
// 存入对象
win.postMessage(JSON.stringify({key: 'storage', method: 'set', data: obj}), 'http://children.com');
// 读取对象
win.postMessage(JSON.stringify({key: 'storage', method: "get"}), "*");
window.onmessage = function(e) {
  if (e.origin != 'http://parent.com') return;
  // "Jack"
  console.log(JSON.parse(e.data).name);
};
```
### 4.3 AJAX

同源政策规定，AJAX请求只能发给同源的网址，否则就报错。除了架设服务器代理（浏览器请求同源服务器，再由后者请求外部服务），有三种方法规避这个限制。

1. JSONP
2. WebSocket
3. CORS

#### (1) JSONP
JSONP是服务器与客户端跨源通信的常用方法。

最大特点就是简单适用，老式浏览器全部支持，服务器改造非常小。
它的基本思想是，网页通过添加一个`<script>`元素，向服务器请求JSON数据，这种做法不受同源政策限制；服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。
首先，网页动态插入`<script>`元素，由它向跨源网址发出请求。

```
function addScriptTag(src) {
  var script = document.createElement('script');
  script.setAttribute("type","text/javascript");
  script.src = src;
  document.body.appendChild(script);
}

window.onload = function () {
  addScriptTag('http://example.com/ip?callback=foo');
}

function foo(data) {
  console.log('Your public IP address is: ' + data.ip);
};
```
上面代码通过动态添加`<script>`元素，向服务器`example.com`发出请求。注意，该请求的查询字符串有一个callback参数，用来指定回调函数的名字，这对于JSONP是必需的。

服务器收到这个请求以后，会将数据放在回调函数的参数位置返回。

```
foo({
  "ip": "8.8.8.8"
});
```
由于`<script>`元素请求的脚本，直接作为代码运行。这时，只要浏览器定义了foo函数，该函数就会立即调用。作为参数的JSON数据被视为JavaScript对象，而不是字符串，因此避免了使用`JSON.parse`的步骤。

#### (2) WebSocket
WebSocket是一种通信协议，使用`ws://（非加密）和wss://（加密）`作为协议前缀。该协议不实行同源政策，只要服务器支持，就可以通过它进行跨源通信。下面的例子，展示的是WebSocket请求头信息：
```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```
上面代码中，有一个字段是Origin，表示该请求的请求源（origin），即发自哪个域名。
正是因为有了Origin这个字段，所以WebSocket才没有实行同源政策。因为服务器可以根据这个字段，判断是否许可本次通信。如果该域名在白名单内，服务器就会做出如下回应。

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```
#### (3) CORS

CORS是跨源资源分享（Cross-Origin Resource Sharing）的缩写。它是W3C标准，是跨源AJAX请求的根本解决方法。相比JSONP只能发GET请求，CORS允许任何类型的请求。

CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。
整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。




