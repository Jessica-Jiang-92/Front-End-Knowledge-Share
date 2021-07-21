## 参考文章
[1. http协议缓存机制](https://juejin.cn/post/6844903491396190215)

## 前言
http相关知识毫无疑问肯定是每个前端开发必备的知识之一，下面我们来看看http缓存的流程图：

![http缓存机制](https://user-images.githubusercontent.com/82437559/119609012-77eeb000-be29-11eb-8e24-be9f9c8d7cc8.png)


## 1. http报文的组成

我们先来看看http报文的组成部分有哪些。

### 1.1 包含属性的首部（header）:

附加信息（cookie，缓存信息等）与缓存相关的规则信息，均包含在header中。
常见的请求头如下：
```
Accept: text/html,image/*                                      #浏览器可以接收的类型
Accept-Charset: ISO-8859-1                                     #浏览器可以接收的编码类型
Accept-Encoding: gzip,compress                                 #浏览器可以接收压缩编码类型
Accept-Language: en-us,zh-cn                                   #浏览器可以接收的语言和国家类型
Host: www.lks.cn:80                                            #浏览器请求的主机和端口
If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT               #某个页面缓存时间
Referer: http://www.lks.cn/index.html                          #请求来自于哪个页面
User-Agent: Mozilla/4.0 compatible; MSIE 5.5; Windows NT 5.0   #浏览器相关信息
Cookie:                                                        #浏览器暂存服务器发送的信息
Connection: close1.0/Keep-Alive1.1                             #HTTP请求的版本的特点
Date: Tue, 11 Jul 2000 18:23:51GMT                             #请求网站的时间
Allow:GET                                                      #请求的方法 GET 常见的还有POST
Keep-Alive:5                                                   #连接的时间；5
Connection:keep-alive                                          #是否是长连接
Cache-Control:max-age=300                                      #缓存的最长时间 300s
```

### 1.2 包含数据的主题部分(body)

HTTP请求真正想要传输的部分。

## 2. http缓存规则

HTTP缓存有多种规则，根据是否需要重新向服务器发起请求来分类，可以将其分为两大类(强制缓存，协商缓存)，强制缓存如果生效，不需要再和服务器发生交互，
而协商缓存不管是否生效，都需要与服务端发生交互。
两类缓存规则可以同时存在，强制缓存优先级高于对比缓存，也就是说，当执行强制缓存的规则时，如果缓存生效，直接使用缓存，不再执行协商缓存规则。

第一次请求数据时可以用下面的图描述：
![第一次请求数据](https://user-images.githubusercontent.com/82437559/126440077-bbdd2fcd-1a58-4815-a8c1-0e55d5b3c0c3.png)

- 强制缓存：如果该种缓存规则生效，不需要再和服务器发生交互；优先级高于协商缓存。
- 协商缓存：不管是否生效，都需要与服务端发生交互。

### 2.1 强制缓存

客户端再请求数据的时候，再缓存数据未失效的情况下，可以直接使用缓存数据，那么浏览器是如何判断缓存数据是否失效呢？
浏览器向服务器请求数据时，服务器会将数据和缓存规则一并返回，缓存规则信息包含在响应header中。对于强制缓存来说，响应header中会有两个字段来标明失效规则
`Expires/Cache-Control`使用chrome的开发者工具，可以很明显的看到对于强制缓存生效时，网络请求的情况。（类似于"from disk cache"这种标识）

- Expires
Expires的值为服务端返回的到期时间，即下一次请求时，请求时间小于服务端返回的到期时间，直接使用缓存数据。不过Expires 是HTTP 1.0的东西，现在默认浏览器均默认
使用HTTP 1.1，所以它的作用基本忽略。另一个问题是，到期时间是由服务端生成的，但是客户端时间可能跟服务端时间有误差，这就会导致缓存命中的误差。
所以HTTP 1.1 的版本，使用Cache-Control替代。

- Cache-Control
Cache-Control 是最重要的规则。常见的取值有private、public、no-cache、max-age，no-store，默认为private。
private: 客户端可以缓存
public: 客户端和代理服务器都可缓存（前端的同学，可以认为public和private是一样的）
max-age=xxx: 缓存的内容将在 xxx 秒后失效
no-cache: 需要使用对比缓存来验证缓存数据（后面介绍）
no-store: 所有内容都不会缓存，强制缓存，对比缓存都不会触发（对于前端开发来说，缓存越多越好，所以基本不用）

### 2.2 协商缓存






