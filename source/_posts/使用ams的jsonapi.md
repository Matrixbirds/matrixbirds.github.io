title: 使用ams的jsonapi
date: 2016-04-10 13:42:50
tags:
  - rails
  - gem
  - active-model-serializers
---


#### 近期的项目中为了方便序列化JSON，采用了[active_model_serializers(简称ams)](https://github.com/rails-api/active_model_serializers)

#### 什么是active_model_serializers?
`ams`是一个扩展rails的`active_model`模块的序列化对象为JSON的gem

#### 为什么使用它？
虽然rails默认生成的Gemfile里有一个用于做序列化JSON操作的gem - 'jbuilder'，
但是由于那个是以template view的方式使用，需要自己维护JSON结构。
然而`ams`是已`object oriented programming`的方式，管理JSON的结构，
`ams`让你可以更好地使用面向对象编程里常用的`inherit`(继承)，复用一些一样的结构
`ams`还借鉴了rails的`active_record/associations`模块，实现了一套`has_*`的dsl，
可以更好的组织JSON结构。

#### 使用jsonapi adapter
为了统一json的格式，采用了`jsonapi`这个规范，这里不具体介绍它的规范，详情可以读官网的文档[json-api](http://jsonapi.org/format/)
`ams`已经在0.10.rc版本里实现了`jsonapi`的规范。只需要简单地写上

```rails
  ActiveModel::Serializer.config.adapter = ActiveModelSerializers::Adapter::JsonApi
```

就可以愉快的玩耍了

#### 0.10.rc版本使用fragment cache遇到的问题

##### fragment cache是什么
`fragment cache` 这里是指 `rails` 框架里的片段缓存。
片段缓存就是页面缓存的变体。它根据`cache_key`（缓存的键)把缓存对应的内容保存起来。用缓存来减少数据库的查询。减少数据库的压力。
`rails`里有好几种`cache store`的方案。
例如：可以直接把内容保存为文件。可以把内容存放在读写很快的内存数据库里(redis..)
当服务器收到请求时，会寻找需要的`cache_key`，然后根据`cache_key`拿到内容，然后把内容和response的结果拼成一个，返回给客户端

##### ams里cache的坑
由于一些特殊原因，需要做到同一个接口根据header的参数，来选择用哪个adapter（`jsonapi` or `json`)
提供不同的json格式
由于项目里只用到了ams，当前版本的ams里cache_key没法动态的指定。
造成了只能一次缓存一种json结构。

##### 解决办法
1.可以打`monkey patch`重写`active_model_serializers`的cache key方法
2.使用jbuilder，根据不同参数，动态的指定cache_key
