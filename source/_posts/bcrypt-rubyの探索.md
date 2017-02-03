title: bcrypt-rubyの探索
date: 2016-12-28 11:42:20
tags:
  - rails
  - gem
  - bcrypt-ruby
---

## bcrypt-ruby  
  这是一个`简而美`的gem，用于实现加密功能：（如用户密码）。因为简单易用的特性，被rails官方团队加入了Gemfile里。  

#### 简而美  
  ```ruby
  # Gemfile

  # Use ActiveModel has_secure_password
  gem 'bcrypt', '~> 3.1.7'
  ## 这是一段在rails里简单的使用demo
  # app/models/user.rb
  class User < ApplicationRecord
    has_secure_password # 默认需要有一个password_digest字段来存密码
  end

  User.find(1).password_digest 
  # $2a$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy
  ```

### WTF就这样？  
  你可能会吐槽，握草！居然不需要使用者指定salt，莫非是公用同一个salt？这种加密跑个简单的[rainbow table](https://en.wikipedia.org/wiki/Rainbow_table)不就好了？  
  握草太可怕了，我再也不敢用gem了，我要去造轮子。骚等，先喝杯咖啡静静。
  再回到之前的一段密文字符串里：  
  ```ruby
  "$2a$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy"
  ```
  根据wikipedia里的描述，bcrypt加密后是由于`$2a`，`$10`，`$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy`这3段字符串构成的密文密码。
  `$2a`表示所用的bcrypt算法的的版本，`$10`表示需要计算的次数。下面一段字符串逼格比较高，也是本例密文里的核心部分。这段密文其实是一段混合密文密码+salt的字符串。  
  需要换个姿♂势解读我们把`$`忽略，留下`N9qo8uLOickgx2ZMRZoMye`，`IjZAgcfl7p92ldGxad68LJZdL17lhWy`。前者是一段密文，而后者是一段salt。  
  WTF？日了狗了？这段salt哪里来的？！莫非是有人黑了我的代码？！莫方。它的诞生其实是来源于我们的`bcrypt-ruby`gem。  
  ```ruby
  # lib/bcrypt/engine.rb
  def self.generate_salt(cost = self.cost)
    cost = cost.to_i
    if cost > 0
      if cost < MIN_COST
        cost = MIN_COST
      end
      if RUBY_PLATFORM == "java"
        Java.bcrypt_jruby.BCrypt.gensalt(cost)
      else
        prefix = "$2a$05$CCCCCCCCCCCCCCCCCCCCC.E5YPO9kmyuRGyh0XouQYb4YMJKvyOeW"
        __bc_salt(prefix, cost, OpenSSL::Random.random_bytes(MAX_SALT_LENGTH))
      end
    else
      raise Errors::InvalidCost.new("cost must be numeric and > 0")
    end
  end
  ```
  是不是瞬间被这个gem所折服了？  
  ![http://okh71i0eg.bkt.clouddn.com/yangwangjugenshaonv.jpg](http://okh71i0eg.bkt.clouddn.com/yangwangjugenshaonv.jpg)

## 参考资料  
  [https://en.wikipedia.org/wiki/Bcrypt](https://en.wikipedia.org/wiki/Bcrypt)  
  [http://stackoverflow.com/a/6833165](http://stackoverflow.com/a/6833165)  
  [https://github.com/codahale/bcrypt-ruby](https://github.com/codahale/bcrypt-ruby)