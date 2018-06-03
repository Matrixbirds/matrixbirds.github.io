title: 肥宅のrust初试
date: 2018-06-03 20:54:36
tags:
  - rust
  - programming language
---


#### rust的由来  
  rust最初是Mozilla公司（我们写前端的当然知道啦）的一位肥宅 - Graydon Hoare 的个人项目，后来公司在09年开始赞助项目，2010年被公布于世，所以说肥宅一定要多做个人项目，梦想总是要有的嘛，说不定哪天就实现了，成为万人瞩目的肥宅之王，成为Gayhub的明星程序员，被万人f**k，被肥宅圈粉。这大概是我们现代肥宅的最大梦想吧。

#### 我和rust的情缘  
  说到rust很多人不陌生，这是2010年生产的一门新新语言，然而这门语言在我刚接触编程的时候(2012年)就已经听说了，当时加的某c++群的一个群友就在布道rust，只可惜当年的我还处于对力量一无所知，按部就班的学习主流的系统级语言，没有勇气和能力去探索。没想到六年后的今天，我终于觉悟了，封印解除，buff全开，打算好好驯养一下这位伟大的现代化系统级编程程语言。

#### 肥宅 の Rust FIRST IMPRESSION  
  哎，这个标题是不是很眼熟呢？! 没错下♂面开始进入正题。  
  博主当前rust版本是stable 1.26.1，在macos下brew install rust会安装一个叫做cargo的包管理，cargo的包管理会按照一个叫做toml的文件格式，做相关的包管理，以及编译链接等功能。cargo是一个加入了rust standard的包管理，这对于以前手写makefile或者cmake等构建工具来构建c/c++环境，你只需要配置toml，然后简单的cargo build / cargo run 就可以编译连接并且运行rust程序，真的是一种解脱，（x肥宅快乐水再也不是我加班的动力，写rust就是肥宅的我最高快乐G点）。  
  你以为这样就满足了？naive。
  让我们看看rust中的特性： 
  1.表达式和语句    
  ```rust
  fn expression_or_statement(a: i32) -> i32 {
    let mut b = a; // 由于rust中默认赋值之后，都是不可修改的，所以这里要用mut修饰符表示这是一个变量
    b = {
        let a = 2; // 这里通过{} 打开了一段局部作用域，这里的b只在当前的代码段里占用b的变量名
        a // 这里我们没有加分号，表示这是一个表达式，表达式会把值返回给调用者。
    }; // 因此这里内部的a表达式值会返回并且赋值给b变量
    b // 这里也是一个表达式 ，作为函数的返回值。
  }  
  ```

  在rust中末行以表达式方式返回结果，当我们需要提前结束某段操作的时候，才会用到return返回。这点是不是和ruby coding style有点像呢？  

  2.安全  
  ```rust  
    let a = 'a' // rust具备类型推导功能，这里'a'字面值是String类型，因而let a没有声明类型也可以完成定义a的操作。
    let a = 1   // 这条语句也是let a = x，区别是它会覆盖之前的语句let a = 'a' 这样以前的a就不复存在，let a = 1会作为新生儿存在。
    // 以下的这段代码会在编译时报错
    let i = if true { // 由于没有显示的表面i量的类型 但是这里赋值操作是由if else流程控制，if true会是String类型,else会给一个integer类型，导致i推导不出类型，rust机智的把它透过编译器，捕获这个明显的不安全赋值操作。
      "1"
    } else {
      1
    }
  ```
  ownership？move？borrowing?  
  首先让我们了解一下一个主流系统级编程语言具备的内存管理方案。  
  ```c
  int a = 1; // stack区
  int *p1 = malloc(4*sizeof(int)); // heap区
  ```
  例如c语言中，存放在当前线程中的静态区域资源，会被统一放入一个叫做stack(栈)的内存区域中，然而每一个stack的空间是预先分配好的。并且是固定的，因而它会存入固定好的数据，例如常见的内建类型，int char，数组，字符串，字符等等。当我们需要在运行时动态的存放一些数据的时候，我们需要调用系统api，向操作系统申请一块来自heap(堆)区域的内存，这块的内存往往是程序运行时，我们向操作系统申请的，它能给多少就给多少，总的来说heap区的资源往往比stack区的容量更大。并且它对于stack来说是一个全局的概念，也就是说它可以跨越线程，而不是局限于线程内部。然而heap是靠地址申请来的内存，当你做内存操作的时候，你得去遍历到你对应的内存区域，然后做引用和修改，步骤比较繁琐，执行速度会略慢于stack，stack则是保持一种先进后出的执行顺序，它不听的将新来的内存向上推，当收到出栈指令的时候，它会快速的把最上层的挤出栈，从而在速度上超越了heap，或许这就是大佬吧。  
  回到当下，我们来看一看rust中的方案  
  ```rust
  // ownership
  // rust里每一个值都有一个owner作为它的主人管理。  
  let a: i32 = 1; // 这里1的字面值赋给了a a就是1的owner a的生命周期是由作用域决定的，当程序执行离开a所在的作用域时，会调用drop函数把a的内存给释放。这个特性被称之为RAII

  let b: i32 = a; // 把a赋值给b会产生一个copy操作，因为a、b的值都是字面值，rust会视为这是一个"move"操作，讲a的值copy move到b里。

  let s_a: String = String::from("heap string"); // 这里通过String::from动态的创建了一个heap区的值，赋给s_a
  let s_b: String = s_a; // 这里同样会被视为move操作，在rust中move操作对于heap区的值来说，是一次浅拷贝，并且会把这里s_a标记为非法值，不能再次访问它，如果再次访问它否则会产生一个编译时报错。

  // move
  fn execute_convert(s: String) -> usize {
    s.len()
  }
  execute_convert(s_b); // 这行代码会产生move，s_b的值会被move到execute_convert的形参中，导致s_b被标记为非法值，再次访问就会报编译时错误。
  // 想避免move，可以通过深拷贝，s_b.clone()的方式避免s_b被move成为一个非法值。

  // rust中对于动态创建的对象还可以通过borrow的方式，防止s_b作为非法值存在。例如:
  execute_convert_borrow(s_b); // 这里s_b会借给execute_convert_borrow函数执行，当函数执行结束后会安全的返回它的结果给s_b的作用域。
  fn execute_convert_borrow(&s: String) -> usize {
    s.len()
  }

  // borrow的限制
  let mut s0: String = String::from("heap_s0"); // 定义了一个可变的s0
  let mut s1: String = &mut s_b; // 把s0借给s1 
  let mut s2: String = &mut s_b; // 同一作用域又借给了s2 这时候会报一个编译时错误
  // rust中不允许把同一个定义的值，同时借给两个不同的量，因为rust考虑到这样会出现data race(数据竞争)，两个mut的指针对于指向的相同内存区的数据可能会发生不同操作，导致出现脏数据的情况。
  // 对于不可变的值不存在这种约束 因为是只读的
  let s3: String = &s0;
  let s4: String = &s0; // 不会报错 因为借给的是不可变的量


  // rust编译器会避免悬垂引用 
  // 例如：
  fn main() {
    let reference_to_nothing = dangle();
  }

  fn dangle() -> &String {
    let s = String::from("hello"); //由于s是定义于dangle函数的作用域中，按照raii策略，rust会在dangle执行结束的时候，销毁s
    &s // 然而这里把s的引用作为返回值，会造成本该销毁的对象的地址被返回出来，让其他作用域的人继续持有它。这就是悬垂指针。
  } // 这里会抛出编译时错误。

  // slices
  // 作为写javascript的熟手应该很熟悉数组的slice方法，rust通过类游标卡尺的方式可以针对String这种序列对象做c里的指针偏移操作，来实现取一部分数据。  
  // 例如:  
  let s = String::from("hello world");
  let hello = &s[0..5];
  let world = &s[6..11];
  ```
  rust中怎么定义一个对象。
  ```rust
  // 作为一个rails程序员用过factory_girl（现在叫做factory_bot）的人应该都难以忘怀ruby的dsl居然可以写出如此优雅的代码及注释。
  // 在rust里，struct的设计就是类似这样的写法
  struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
  }
  fn build_user(email: String, username: String) -> User {
    User {
        email: email, // 这里也可以直接写email, 和javascript里一样省略重复名称
        username,
        active: true,
        sign_in_count: 1,
    }
  }
  
  let user1 = build_user(email: '2333', username: 'nickname');
  let user2 = User {
    email: 'user2@rust.com',
    ..user1 // 通过..名称 可以实现剩余未显示定义的字段会集成与user1量中。
  }

  ```

#### 总结
  嘛，先写这么多，等有空我继续补完。  
  待续...