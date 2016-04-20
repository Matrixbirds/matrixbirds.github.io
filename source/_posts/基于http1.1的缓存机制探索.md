title: 基于http1.1的缓存机制探索
date: 2016-04-20 22:20:23
tags:
  - http
  - cache
---

项目里用了rails的一些基于http 1.1协议的cache探索，然而对http cache远离还是不够理解，于是查询了一些资料。打算写一篇博客来总结一下。


### 首先HTTP是什么？为何要强调`semantics`

HTTP(Hypertext Transfer Protocol =: 超`文本`传输协议)。
既然是文本自然离不开文字。文字要表达的意思可以用`semantics`（语义）这个词来代替。
HTTP由`request`(请求)和`response`(响应)两种消息构成。
HTTP协议工作机制则是根据`request`和`response`的消息里的字段（这些字段包括：HTTP的方法，HTTP的状态码，HTTP的头）
解读这些消息里有价值的语义信息实现`request`关联`response`。


### 理解什么是`semantically transparent`

rfc2616里的原文描述是这样的：
```English
  A cache behaves in a "semantically transparent" manner, with
  respect to a particular response, when its use affects neither the
  requesting client nor the origin server, except to improve
  performance. When a cache is semantically transparent, the client
  receives exactly the same response (except for hop-by-hop headers)
  that it would have received had its request been handled directly
  by the origin server.
```

大意是这样的:
缓存表现在语义透明的情况。语义透明就是说，当特定的响应，既不影响接受它的客户端，也不影响发起它的服务端时。
当缓存表现为语义透明的时候，客户端得到的响应是服务器以前就处理过（除了`hop-by-hop`头不一样以外，其他部分完全一样）


### HTTP 1.1协议中是如何通过cache提升性能的呢？

rfc2616里描述，http1.1协议里`caching`的目的是在特定的业务场景下减少冗余的http请求or冗余的http响应。

在必要的时候http1.1允许[源服务器](https://tools.ietf.org/html/rfc2616#page-10)、缓存、客户端显示的减少透明度。

#### Cache-control
`Cache-control`是一个用于覆盖默认缓存算法的http header字段。当默认缓存和`Cache-control`缓存冲突时

* http1.1中通过`Expiration Model`（过期模型）实现减少多余的http请求  
  *


* http1.1中通过`Validation Model`（验证模型）实现减少多余的http响应  


### HTTP 1.1中提供的缓存response的机制
 *

###


#### 参考资料

[rfc2616#section-13](http://tools.ietf.org/html/rfc2616#section-13)
[总结 Web 应用中常用的各种 Cache](https://ruby-china.org/topics/19389)
