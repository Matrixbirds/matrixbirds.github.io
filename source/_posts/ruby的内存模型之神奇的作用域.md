title: ruby的内存模型之神奇的作用域
date: 2016-04-10 22:56:18
tags:
 - ruby
---

#### ruby常见的几种变量
```ruby
  local_variable = "scope"   # 当前块变量
  @instance_variable = "scope"  # 当前实例的块变量
  @@class_variable = "scope" # 当前类的块变量
  $global_variable = "global" # 全局对象
```

`local_variable`是一个普通变量，它的访问作用域仅限当前block(代码块)，不包括当前块里嵌套的`block`:

```ruby
  local_variable = "yooo"
  def fork(args)
    local_variable = args
    puts local_variable
  end
  fork("changed")
  # "changed"
  puts local_variable
  # "yooo"
```
以上代码中更好看到，第一行里地`local_variable`和`fork`方法里`local_variable`是俩个不同的变量。解释器会分配两个不同的空间来表示这两个对象，第一行的`local_variable`只会在当前的代码块里被操作。但是不包括`fork`方法里的代码块，这就是ruby里普通变量的特点

`@instance_variable`是一个实例变量，它的访问作用域比较特殊依赖于定义它的上下文。

```ruby
  class Ruby
    @poker = "poker_2"

    def poker
      [@poker, @poker.object_id]
    end

    def self.poker
      [@poker, @poker.object_id]
    end
  end

  r = Ruby.new
  puts r.poker
  # [nil, 8]
  puts Ruby.poker
  # ["poker_2", 70269578351860]
```
以上代码中。`poker`是一个`instance_method`, `self.poker`则是一个`self`对象定义的`singleton_method`,
