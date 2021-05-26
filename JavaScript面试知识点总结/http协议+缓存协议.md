## 参考文章
[1. http协议缓存机制](https://juejin.cn/post/6844903491396190215)

## 前言
http相关知识毫无疑问肯定是每个前端开发必备的知识之一，下面我们来看看http缓存的流程图：

![http缓存机制](https://user-images.githubusercontent.com/82437559/119597219-2cc9a280-be13-11eb-909e-a584d44f789c.png)

## 1. http报文的组成

我们先来看看http报文的组成部分有哪些。

### 1. 包含属性的首部（header）:

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

### 2.包含数据的主题部分(body)

HTTP请求真正想要传输的部分。

## 2. http缓存规则

HTTP缓存有多种规则，根据是否需要重新向服务器发起请求来分类，可以将其分为两大类(强制缓存，协商缓存)，强制缓存如果生效，不需要再和服务器发生交互，
而协商缓存不管是否生效，都需要与服务端发生交互。
两类缓存规则可以同时存在，强制缓存优先级高于对比缓存，也就是说，当执行强制缓存的规则时，如果缓存生效，直接使用缓存，不再执行协商缓存规则。









