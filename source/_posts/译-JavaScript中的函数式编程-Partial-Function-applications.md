title: '[译] JavaScript中的函数式编程--Partial Function applications'
date: 2015-09-24 23:53:40
tags:
  - 函数式编程
  - javascript
  - 翻译
---

#### 序  
最近在研究函数式编程，对函数式编程的概念还是有些模糊，毕竟自己只玩过`Impure FPL`中得一丢丢FP Feature  
在使用google的同时，发觉了一些不错的资料，这里选择了我熟悉的JavaScript style -- FP文章做翻译
翻译的不好欢迎大家评论并且指出:D。Thank ur for reading.

#### 原文链接:  
[https://lostechies.com/derickbailey/2012/07/20/partially-applied-functions-in-javascript/](https://lostechies.com/derickbailey/2012/07/20/partially-applied-functions-in-javascript/)  

#### 译
近期，我发现自己用JavaScript做了越来越多的函数式编程，尤其是最近的几天里我正在做一个基于WinJS的项目，并且大量的使用到了Promise，它对我来说是一个挑战。这也让我感到有点奇特，考虑到当时我正对使用`Backbone.js`构建面向对象的JavaScript应用有热情。但在此同时，JavaScript中有一些非常自然的函数式风格，你可以用函数式风格来做许多非常有趣的东西，我鼓励大家去研究它。

Partial Function Application（部分函数应用）
在我研究函数式风格的过程中，我学习到了一个工具 -- 部分函数应用，它源自于Wikipedia(维基百科)，允许我们给一个函数提供一个参数，从而产生出一个参数较少的函数。注意部分函数应用不同于柯里化。这两个概念有点类似，但可以确定地是，它们是两个种东西，不能互相颠倒。  

部分函数应用的真正意思是，我们可以把一个预期有2个参数的函数，通过固定第一个参数，先计算第一个参数，然后创建一个部分应用的函数，这样的结果会是返回一个它本身且预期只有一个参数的函数。当再一次调用的时候，将产生与上一次计算的结果。

例：  
```javascript
function add(a, b){
  return a + b;
}

// partially apply this function with the "a" parameter set to 1
// 部分应用将这个函数的参数 "a" 设置成了1

var partial = add.bind(null, 1);

// complete the function application by providing the "b" parameter
// with a value of "2". 1 + 2 = 3, so this results in 3.
// 通过给参数b赋值2，完成函数的应用，会和上一次的执行计算。因此则结果是3。
partial(2); //=> 3
```

这里，我们使用到了ES5标准中得`bind`函数来实现 `部分应用`。`bind`功能的是：
1）绑定一个函数的上下文（`this` 参数）
2）创建一个`部分应用函数`

当调用`部分函数`时，最终你会得到原本两个参数调用时的结果，`部分函数`可以被多次使用，并且可以有多个参数。你可以提供一个或多个参数并且可以用`bind`实现`部分应用函数`，并且你同样能够对`部分应用的函数`实现`部分应用`,当然部分函数的概念也许有点荒谬，但是过了一段时间，它依旧能够完成所有的操作。  

Implementing Partial Application(实现部分函数应用)
最基本的方法实现部分函数应用是在支持ECMAScript5标准的浏览器或者解释器上(如NodeJS WinRT)编写代码  
（注：自从我用WinJS和WinRT在win8系统上工作后，我就坚持一直使用ECMAScript5来编JavaScript写。IE10对这个标准的实现支持的很好，在相当支持ECMAScript5标准的浏览器上写程序十分的有趣。这样你也不用担心跨浏览器兼容性问题，构建WinJS的代码也会十分的整洁）  

尽管你使用的浏览器或者解释器不支持ECMAScript5标准，所以没办法用`bind`函数轻松实现partial application（`部分应用`）。  
自己实现一个这类的函数也不会花费太多的精力，并且有许多种现成的实现方案 - 例如 [this_one_from_John_ Resig](http://ejohn.org/blog/partial-functions-in-javascript/)  
```JavaScript
// John Resig's "curry" function from
// http://ejohn.org/blog/partial-functions-in-javascript/
// which is unfortunately named, as this is not currying -
// it's partial function application.

Function.prototype.curry = function() {
    var fn = this, args = Array.prototype.slice.call(arguments);
    return function() {
      return fn.apply(this, args.concat(
        Array.prototype.slice.call(arguments)));
    };
  };


// a sample use

function add(a, b){
  return a + b;
}

var partial = add.curry(1);

partial(2); //=> 3

```
现在你也许不会想去修改JavaScript的原生`Function`原型链按照以上方式这样扩展（我个人不建议）。但不用担心，
你可以用更简单的方式 - 用单独的函数调用来实现同样的效果，例如你可以用Underscore.js这样的提供了partial application
类似`bind`这样的函数，而不是通过污染`Function` prototype来做到:
```javascript
function add(a, b){
  return a + b;
}

// using underscore.js "bind" function
var partial = _.bind(add, null, 1);

partial(2); //=> 3
```

可能有更多的实现方式，在这里我就大致的列出这两种实现。

### 案例
目前为止我已经知道了一些关于`Partial Function Application`的一些知识。但是我至今并没有使用它的理由，也不
理解它的用处在哪里，在什么情况下用它是明智的。对于我正在用WinJS/WinRt构建基于微软 P&P group(名词可以忽视...)，
更像是一种学术研究，然而我还是找到了一些应用场景，编写了一些比不用`Partial Function Application`更加简洁的代码  

这里有一段来自bennage和我一起用WinJS/WinRT做app的例子：
```javascript
function buildThumbnails(files) {
    var localFolder = applicationData.current.localFolder;

    // Promise to build the thumbnails and return the list of local file paths
    var whenFolderCreated = localFolder.createFolderAsync(thumbnailFolderName, replaceExisting);

    return whenFolderCreated
        .then(function(folder){
          Tiles.copyImages(files, folder);
        })
        .then(Tiles.getFolderNamesFromFiles)
        .then(Tiles.update)
}
```

这并不是很糟糕。实际上，它相当的简洁和易读。以上代码使用了大量的`Promise`和链式调用。
每个函数调用后都会返回一个`Promise`，从而实现链式的调用`then`函数。这里除了中间那段代码用了
内联的函数作为参数是为了通过闭包的方式来得到第二个函数，我想试试有没有办法不需要这样做，来得到
一样的效果，我发现`Partial Application`解决了我的问题：

```javascript
function buildThumbnails(files) {
    var localFolder = applicationData.current.localFolder;

    // Promise to build the thumbnails and return the list of local file paths
    var whenFolderCreated = localFolder.createFolderAsync(thumbnailFolderName, replaceExisting);

    // partially apply the image copier to carry the files parameter with it,
    // allowing it to be used as a promise/callback that only needs to have
    // the `folder` parameter supplied.
    var copyThumbnailsToFolder = Tiles.copyImages.bind(null, files);

    return whenFolderCreated
        .then(copyThumbnailsToFolder)
        .then(Tiles.getFolderNamesFromFiles)
        .then(Tiles.update)
}
```
在这个版本的代码里，内联函数一个用`bind`方法实现了`部分应用的函数`局部变量代替了，
我通过应用了第一个参数，这样会返回一个只需要一个参数的函数。因此这个参数是由`then`方法中得Promise链返回的所需值，
我可以提供这些`部分应用的函数`调用，一切按预期执行。

#### 不，真是这样吗。还是仅有这一种案例？
当然这是一个极其简单的例子，很可能认为是不必要的。原先的代码并不是相当的糟糕，可读性可能对一些人来说不是很差，
但是在结尾使用`then`链式语句会十分优雅，它有着截然不同的函数式风格和流，这让我感到十分的易读，我像是在
看一序列代码的图解，这让我爱不释手。
