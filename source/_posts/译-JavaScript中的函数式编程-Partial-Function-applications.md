title: '[译] JavaScript中的函数式编程--Partial Function applications'
date: 2015-09-24 23:53:40
tags:
---

#### 序  
最近在研究函数式编程，对函数式编程的概念还是有些模糊，毕竟自己只玩过`Impure FPL`中得一丢丢FP Feature  
  在使用google的同时，发觉了一些不错的资料，这里选择了我熟悉的JavaScript style -- FP文章做翻译

#### 原文链接:  
[https://lostechies.com/derickbailey/2012/07/20/partially-applied-functions-in-javascript/](https://lostechies.com/derickbailey/2012/07/20/partially-applied-functions-in-javascript/)  

#### 译
近期，我发现自己用JavaScript做了越来越多的函数式编程，尤其是最近的几天里我正在做一个基于WinJS的项目，并且大量的使用到了Promise，它对我来说是一个挑战。
这让我感到有点奇特，考虑到当时我正对使用`Backbone.js`构建面向对象的JavaScript应用有热情。但在此同时，JavaScript中有一些非常自然的函数式风格，你可以用
函数式风格来做许多非常有趣的东西，我鼓励大家去研究它。

Partial Function Application（部分函数应用）
在我研究函数式风格的过程中，我学习到了一个工具 -- 部分函数应用，它源自于Wikipedia(维基百科)
，允许我们给一个函数提供一个参数，从而产生出一个参数较少的函数。注意部分函数应用不同于柯里化。
这两个概念有点类似，但可以确定地是，它们是两个种东西，不能互相颠倒。  

部分函数应用的真正意思是，我们可以把一个预期有2个参数的函数，通过固定第一个参数来创建一个部分应用的函数，
这样结果它本身是一个预期只有一个参数的函数。当再一次调用的时候，讲产生与上一次计算的结果。
例：
```JavaScript
  function add(a, b) {
    return a + b;
  }

  // partially apply this function with the 'a' parameter set to 1  
  // 部分得应用将这个函数的参数'a'设置成了1
  var partial = add.bind(null, 1);  

  // complete the function application by providing the "b" parameter  
  // with a value of "2". 1 + 2 = 3, so this results in 3.  
  // 通过给参数b赋值2，完成函数的应用，会和上一次的执行计算。因此则结果是3。
  partial(2); //=> 3  

```
Here, we’re using the ECMAScript 5 standard of the “bind” function to do the partial application. The “bind” function serves two purposes: 1) to bind a function’s context (“this” argument) and 2) to create a partially applied function.

这里，我们使用到了ES5标准中得`bind`函数来实现部分应用。`bind`功能的是：
1）绑定一个函数的上下文（`this`参数）2）创建一个部分应用的函数
