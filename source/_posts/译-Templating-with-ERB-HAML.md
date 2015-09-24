title: '[译] - 使用ERB&HAML更好的使用模板引擎'
date: 2015-09-15 14:37:33
tags:
---

[Better Views Templating with ERB & HAML](http://tutorials.jumpstartlab.com/topics/better_views/erb_and_haml.html)

  Rails默认的模板引擎语言叫`Embedded Ruby`(简称`ERB`)。模板文件存放在`/app/views/`目录下并且它们的名字与controller和action相互对应。
每当你需要用`ERB`的时候Rails已经帮你默认配置好了。

  ## 回顾一下ERB
  在ERB中 我们有3个主要的标记型元素:  
    · HTML和文本没有使用这些元素，直接展示在页面上
    · 使用`<%=` 和 `%>` 包含Ruby的代码，并且会在标记元素中输出  
    ·
