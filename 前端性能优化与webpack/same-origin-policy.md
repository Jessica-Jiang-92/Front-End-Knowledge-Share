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

```










