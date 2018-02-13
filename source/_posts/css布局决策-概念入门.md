title: css布局决策-概念入门
date: 2018-02-13 13:29:18
tags:
    - css  
    - box  
    - bfc  
    - w3c  
---

## 为什么要把CSS当做一门技术学习  
  在我刚接触web开发的时候，认为页面大部分源码都直接获取，并且网上还有各大公司开发了很多图形化的桌面软件简化写样式的时间。这门完全没有逻辑符，纯语法块和属性的语言要有必要去学习吗？  
  后来工作了，忙碌的后端功能设计之后，因为对CSS的了解甚少，因而导致不能完成比较符合设计师或者说体验更好的前端应用。  

## 重新认识页面  
  我们现代化的HTML页面可以简单的分为两个组成部分。`UI`和`UX`。  
  浏览器则把它`UI`分为CSS+HTML，把`UX`分为JavaScript。  
  HTML是一种描述页面的文档。CSS是一种浏览器实现的布局DSL，能够辅助HTML更优雅的描述`文档`一个页面布局的核心基本上是由CSS掌握。CSS通过修饰`文档`的行为，来实现一个页面的布局。  

## 从认识文档开始  
  写文章和写文档对于大多数人来说，不是一件陌生的事情。  
  一篇有意义值得珍藏的文章，要有中心主题，每个段落和修辞它们之间会通过衔接上下文，来突出作者想要表达的中心主题。  
  我们可以把HTML当做干巴巴的文字，CSS则可以认为是纯修辞的写作手法，为了让浏览器更明确知道作者的中心思想，突出一个页面的主题。  

### CSS"修辞套路"  
#### 布局方案  
  在CSS中有这几种常用的写作手法会影响文档的布局： 1.文档流，2.浮动，3.绝对定位。  
  文档流：浏览器中的HTML默认的布局方式是按照我们自顶向下的方式来实现元素的布局。  
  浮动：浮动会，根据HTML中元素的文档流，再根据浮动的属性向浮动方向偏移，会影响周围的元素。  
  绝对定位：绝对定位是一种特殊的布局方式。可以把绝对定位理解为浮在普通文档流上的一种比较灵活的定位，它不会影响原来文档流的元素。  
#### BFC(Block Formatting Contexts)  
  简单的理解BFC: 是一个独立的修辞手法，它可以被认为是一个可以装东西的箱子，箱子里物品的拜访是不受外界的影响的。  
  对于浏览器而言，BFC中的元素的布局是不收外界的影响（也就是说内部的元素是不会因为float, 或者绝对定位等影响文档流的布局受到牵连），这样而言我们可以更方便的定位布局在哪一个箱子里出了问题。`不同BFC的元素不会出现外边距重叠现象`，`同一个BFC的相邻元素仍存在重叠现象`。BFC的具体规则请[参考](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context/)    

## 总结  
  在我们写CSS的时候，要去设计这个页面的文档（HTML）之后再去考虑如何用CSS去修饰它，设计CSS的时候应当去围绕盒子模型的概念，以及DRY的原则去设计我们的class和组件。当对布局方式和盒模型有一定概念的时候也会方便我们写出更有可读性和扩展性的代码，能高效高质量的还原UI & UE。    

## 参考资料  
  [http://www.topcss.org/visual-formatting-model/](visual-formatting-model)  
  [http://kayosite.com/block-formatting-contexts-in-detail.html](block-formatting-contexts-in-detail)  
  [https://developer.mozilla.org/en-US/docs/Web/CSS/Visual_formatting_model](mdn vistual formatting model)  
  [http://blog.doyoe.com/2015/03/09/css/%E8%A7%86%E8%A7%89%E6%A0%BC%E5%BC%8F%E5%8C%96%E6%A8%A1%E5%9E%8B%E4%B8%AD%E7%9A%84%E5%90%84%E7%A7%8D%E6%A1%86/](视觉格式化模型中的各种框)  
  [http://layout.imweb.io/article/box-type.html](box-type)  
