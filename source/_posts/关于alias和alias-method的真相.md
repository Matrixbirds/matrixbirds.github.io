title: 关于alias和alias_method的真相
date: 2017-02-03 15:55:41
tags:
  - ruby
---


#### alias和alias_method  
  alias是什么？它是一个方法么？当然不是啦，它是一个逼格很高的keyword并且它永远指向当前作用域的`self`。keyword是语言的一部分不可被ruby程序覆盖。例如有下面这段代码：  
  ```ruby
  def alias(&scope)
    scope.call('1024')
  end

  alias :io, :puts
  io "hola"
  # 输出 hola，可见这里alias并没有被覆盖
  ```
  永远指向当前作用区的`self`  
  ```ruby
  class A
    def hola
      "hola"
    end

    def self.add_hello
      alias :hello :hola
    end
    add_hello
  end

  class B < A
    def hola
      "你好"
    end
    add_hello
  end

  puts A.new.hello # 输出 "hola"
  puts B.new.hello # 输出 "hola"
  # 可见 add_hello 方法里的alias并没有因为调用的scope变了导致hello方法变了
  ```
  alias是引用还是创建？（以下是我的机器上输出的结果）：  
  ```ruby
  a = A.new
  a.method(:hola).object_id # 输出 70144731241200
  a.method(:hello).object_id # 输出 70144731223640
  # object_id并不一样 由此可以推断是创建了一个的对象
  ```
  聊聊alias_method。它是一个ruby标准库的方法（它默认写在Kernel的私有实例方法下面）。不是词法的一部分，是标准库的一部分。它很灵活，而且是一个on call的`self`绑定。它可以被重写（当然一般我们不这么乱用膜法）：  
  什么是on call?  
  ```ruby
  class A
    def hola
      "hola"
    end

    def self.add_hello
      alias_method :hello, :hola # 由于是方法因此这里是要给参数的，当参数和形参不匹配的时候会报错的
      add_hello
    end
  end

  class B < A
    def hola
      "你好"
    end
    add_hello
  end

  puts A.new.hello # 输出 "hola"
  puts B.new.hello # 输出 "你好"
  # 这就是on call 因为B类里重写了hola方法 所以B类里的alias_method会根据B的方法生成hello方法
  ```