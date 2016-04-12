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

`local_variable`是一个普通变量，它的作用域仅限当前`block`(代码块)，不包括当前块里嵌套的`block`:

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
以上代码中更好看到，第一行里的`local_variable`和`fork`方法里`local_variable`是两个不同的变量。
解释器会分配两个不同的内存空间来存储这两个对象，第一行的`local_variable`只会在当前的代码块里被操作。但是不包括`fork`方法里的代码块，这就是ruby里普通变量的特点

`@instance_variable`是一个实例变量，它的访问作用域比较特殊依赖于定义它的上下文。

```ruby
class Ruby
  @poker = "poker_2"

  def initialize(val)
    @poker = "Ruby##{val}"
  end

  def poker
    [@poker, @poker.object_id]
  end

  def self.poker
    [@poker, @poker.object_id]
  end
end

r = Ruby.new("ins")
p r.poker
# ["Ruby#ins", 70226716708620]
p Ruby.poker
# ["poker_2", 70226716708840]
p r.instance_variable_get(:@poker)
# "Ruby#ins"
p Ruby.instance_variable_get(:@poker)
# "poker_2"

```
以上代码中。`poker`是一个`instance_method`, `self.poker`则是一个`self`对象定义的`singleton_method`.
ruby里的self表示当前class的上下文，要理解`instance` 和 `singleton`的区别还是得从它们的作用域来分析.
以上代码可以改为如下：
```ruby
  # ruby class
  class Ruby
    @poker = "poker_2"

    def initialize(val)
      @poker = "Ruby##{val}"
    end

    def poker
      [@poker, @poker.object_id]
    end
  end

  class << Ruby
    def poker
      [@poker, @poker.object_id]
    end
  end

  r = Ruby.new("ins")
  p r.poker
  # ["Ruby#ins", 70226716708620]
  p Ruby.poker
  # ["poker_2", 70226716708840]
  p r.instance_variable_get(:@poker)
  # "Ruby#ins"
  p Ruby.instance_variable_get(:@poker)
  # "poker_2"
```
对比两段代码，可以看出。`self.poker` 和 `class << Ruby` 是等价的。
第二种写法可以看出他们的代码块并不是同一个作用域，因此`@poker`在内存里是有两块不同的存储区来表示的。

#### ruby源码的基本结构（只介绍相关的,源码参考ruby2.3.0）：
```c
// Basic结构体 :=> 用于记录对象的状态
struct RBasic {
    VALUE flags; // 通过mask来表示当前对象的状态
    const VALUE klass; //指向class
}
// Class结构体：用于表示ruby里的class
struct RClass {
    struct RBasic basic;
    VALUE super;         //指向父类
    rb_classext_t *ptr;  //指向类变量
    struct rb_id_table *m_tbl; //指向类方法
};
// Object结构体：用于表示ruby里的对象
struct RObject {
    struct RBasic basic;
    union {
    	struct {
    	    long numiv; /* only uses 32-bits */
    	    VALUE *ivptr;  //指向对象的变量
                void *iv_index_tbl;
    	} heap;
	    VALUE ary[ROBJECT_EMBED_LEN_MAX];
    } as;
};
```

#### 追踪instance_variable的Ruby源码实现

```ruby
  # ruby class
  class Ruby
    @poker = "poker_2"
  end
```
当执行上面这段ruby代码的时候，会调用`rb_ivar_set(Ruby, poker, "poker_2")`

```c
VALUE
rb_ivar_set(VALUE obj, ID id, VALUE val)
{
    struct ivar_update ivup;
    long i, len;

    rb_check_frozen(obj);

    switch (BUILTIN_TYPE(obj)) {
      case T_OBJECT:
        ivup.iv_extended = 0;
        ivup.u.iv_index_tbl = iv_index_tbl_make(obj);
        iv_index_tbl_extend(&ivup, id);
        len = ROBJECT_NUMIV(obj);
        if (len <= (long)ivup.index) {
            VALUE *ptr = ROBJECT_IVPTR(obj);
            if (ivup.index < ROBJECT_EMBED_LEN_MAX) {
                RBASIC(obj)->flags |= ROBJECT_EMBED;
                ptr = ROBJECT(obj)->as.ary;
                for (i = 0; i < ROBJECT_EMBED_LEN_MAX; i++) {
                    ptr[i] = Qundef;
                }
            }
            else {
                VALUE *newptr;
                long newsize = iv_index_tbl_newsize(&ivup);

                if (RBASIC(obj)->flags & ROBJECT_EMBED) {
                    newptr = ALLOC_N(VALUE, newsize);
                    MEMCPY(newptr, ptr, VALUE, len);
                    RBASIC(obj)->flags &= ~ROBJECT_EMBED;
                    ROBJECT(obj)->as.heap.ivptr = newptr;
                }
                else {
                    REALLOC_N(ROBJECT(obj)->as.heap.ivptr, VALUE, newsize);
                    newptr = ROBJECT(obj)->as.heap.ivptr;
                }
                for (; len < newsize; len++)
                    newptr[len] = Qundef;
                ROBJECT(obj)->as.heap.numiv = newsize;
                ROBJECT(obj)->as.heap.iv_index_tbl = ivup.u.iv_index_tbl;
            }
        }
        RB_OBJ_WRITE(obj, &ROBJECT_IVPTR(obj)[ivup.index], val);
	       break;
      case T_CLASS:
      case T_MODULE:
    	   if (!RCLASS_IV_TBL(obj)) RCLASS_IV_TBL(obj) = st_init_numtable();
    	    rb_class_ivar_set(obj, id, val);
              break;
      default:
    	generic_ivar_set(obj, id, val);
    	break;
    }
    return val;
}
```
大致的浏览一下，发现上面的源码里，会根据`Class`和`Object`来判断
执行不同的`set`操作
如果是`Object`则会把实例变量放入对象的内存里
如果是`Class`则会执行`rb_class_ivar_set`。
继续追踪这个函数：
```c
/* tbl = xx(obj); tbl[key] = value; */
int
rb_class_ivar_set(VALUE obj, ID key, VALUE value)
{
    st_table *tbl = RCLASS_IV_TBL(obj);
    int result = st_insert(tbl, (st_data_t)key, (st_data_t)value);
    RB_OBJ_WRITTEN(obj, Qundef, value);
    return result;
}
```
可以得出结论 类和对象的变量是分成了两块不同的存储空间，分开存放的。

#### 回到原先的代码，继续读源码
```ruby
class Ruby
  @poker = "poker_2"

  def initialize(val)
    @poker = "Ruby##{val}"
  end

  def poker
    [@poker, @poker.object_id]
  end

  def self.poker
    [@poker, @poker.object_id]
  end
end # 定义了一个Ruby Class
```
会触发这样的函数
```c
/*!
 * Defines a new class.
 * \param id     ignored
 * \param super  A class from which the new class will derive. NULL means \c Object class.
 * \return       the created class
 * \throw TypeError if super is not a \c Class object.
 *
 * \note the returned class will not be associated with \a id.
 *       You must explicitly set a class name if necessary.
 */
VALUE
rb_define_class_id(ID id, VALUE super)
{
    VALUE klass;

    if (!super) super = rb_cObject;
    klass = rb_class_new(super); //这时解释器会给`Ruby`这个class创建一个RClass的结构
    rb_make_metaclass(klass, RBASIC(super)->klass); //并且给`Ruby`创建一个内置的metaclass(singleton_class) :=> class << Ruby

    return klass;
}
```
当执行一下代码时
```ruby
   r = Ruby.new("ins") //解释器会去寻找以`Ruby` class结构体初始化的对象，把值赋给`r`
   p r.poker           //解释器会去执行poker这个方法，由于调用者`r`是一个RObject，因此它只会从RObject的存储空间里寻找@poker这个实例变量
   # ["Ruby#ins", 70226716708620]  
   p Ruby.poker        //由于调用者`Ruby`是一个RClass，因此它只会从RClass的存储空间里寻找@poker这个实例变量
   # ["poker_2", 70226716708840]
   p r.instance_variable_get(:@poker)
   # "Ruby#ins"
   p Ruby.instance_variable_get(:@poker)
   # "poker_2"
```
所以这里`@poker`的值是不一样的

```ruby
  class A; @@class_variable = "scope"; end
  class B < A; def change=(val); @@class_variable = val; end
  p A.class_variable_get(:@@class_variable) //解释器会调用`rb_cvar_get`
  # "scope"
  B.new.change= "changed"                   //解释器会调用`rb_cvar_set`
  p A.class_variable_get(:@@class_variable)
```
分析了一下`rb_cvar_get`和`rb_cvar_set`发现，解释器会去寻找整个继承链的每个class的存储空间：
```ruby
   B.new.change= "changed" # B -> A -> Object -> Kernel -> BasicObject
```
因此`@@class_variable`类变量可以在它的类中任何地方，包括继承它的类。

```ruby
$global_variable = "global"
```
执行以上代码会调用`rb_gvar_val_setter`，把全局对象放入一个公共的存储空间。
```c
void rb_gvar_val_setter(VALUE val, ID id, void *data, struct rb_global_variable *var)
{
  var->data = (void*)val;
}
```

#### 参考资料

[http://ruby-hacking-guide.github.io/](http://ruby-hacking-guide.github.io/)
[ruby-2.3.0][https://github.com/ruby/ruby/tree/v2_3_0]
