title: nodejs陪我过国庆
date: 2016-10-03 23:42:39
tags:
 - nodejs
 - 国庆节
---

### 为什么突然用node(为了装逼啊...)
  由于之前用ruby基于多线程做了一个服务（等有空专门为这个服务新写一篇博客），遇到了种种的race condition带来的坑。虽说rails本身是线程安全，  
我用的又是MRI（有GIL，不会因为native extension造成线程安全的影响），理论上是说只要项目里的ruby代码（包括gem）都是线程安全的就不会出  
现各种race condition的问题。早就听说nodejs的牛逼，只让使用者知道它只有一个主线程，通过`协作式多任务`来避开多线程的`race condition`,  
并弥补了自己只有一个主线程造成的一些人生遗憾，然而一直没有机会去用，于是决定国庆假日抽时间，去写个`demo`练练手，如果体验好的话，打算最近的一个项目  
就用node写了。

### 痛苦的开始
  很久以前对nodejs也有所了解，还有对nodejs api已经生疏，加上从来没用它写过完整的服务端应用，在与公司js大神们的交流之后，  
我打算尝试用koa写个[cms](https://github.com/Matrixbirds/okaeri)的demo，因为已经习惯rails的`convention`，cms的架子也是仿rails搭了一套，  
在这个痛苦的过程中了解了`koa`深深的让我知道了它的牛逼之处。

### Koa 
  `koa`是一个基于wsgi协议实现的一个轻量级的nodejs web框架，它要求使用者实现generator函数，并且把它作为一个中间件，use到koa里。
来接受以及响应请求。
  * generator:
```javascript
  function *middleware() {
    const n = yield 1;
    return n;
  }
  gen = middleware();
  val = gen.next(); // { value: 1, done: false }
  end = gen.next(val.value); // { value: 1, done: true }
```
  koa会根据use的顺序以及generator的特性把几个中间件，串连在一期实现中间件之间的切换，koa通过引入了`co`，来实现异步控制流。
generator的yield只是表明它会等待右边的表达式执行完之后才会继续往下执行。
`co`会把yield后面的异步函数的执行结果保存并且带入到直到被再次暂停或者执行结束。
```javascript
function *middleware() {
  const n = yield PromisifyAsyncCallback();
  return n;
}
gen = middleware();
val = gen.next();
val.then((data) => {
  console.log(gen.next(data)), // { value: data, done true}
});
```
由于generator的yield特性，如果yield后面是一个generator函数，当前的yield会等待这个generator全部执行完之后才会返回执行权限给yield，`koa`就是通过这一特性实现，middlware之间的切换。
然而这些都是经过我耗时很久的理解、读源码、debug以及看博客之后的小总结，在不知道`co`和`generator`的情况下用`koa`有一种被狗日的感觉。。

### debug
  在node debug模式下的repl里手动调用异步函数，只能看到repl返回了当前对象的值，并没有去执行异步的代码，我以为是代码写的不对，结果经过好长时间的排查，发现原来是node debug模式repl的运行机制有点独特。
正常开node的repl里写同样地代码来调用异步函数是可以看到异步函数的执行过程。（问题还在调查中..未完待续..)