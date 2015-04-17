# html+css+xpath

标签（空格分隔）： html

---
##html+css
1. `<span>`标签是没有语义的，它的作用就是为了设置单独的样式用的。
2. 当代码为一行代码时，你就可以使用`<code>`标签了，如下面例子：
<code>var i=i+300;</code>  
注意：在文章中一般如果要插入多行代码时不能使用`<code>`标签了。
注：如果是多行代码，可以使用`<pre>`标签。
3. `<ul><li>`是无序的。有序可以用`<ol><li>`
4. CSS类选择器语法：<br /><code>.类选器名称{css样式代码;}</code><br />注意是.起头
5. ID选择器语法：<br /><code>#类选器名称{css样式代码;}</code><br />注意是#起头
6. ID选择器和类选择器的区别：<br />ID选择器是只能用一次的，意思是id=""，这个id只能标识一个标签里面的内容，而类则没有这个限制。<br />另外，可以使用类选择器词列表方法为一个元素同时设置多个样式，而ID选择器不行。<br />如下面代码是正确的：<pre>.stress{
    color:red;
}
.bigsize{
    font-size:25px;
}
`<p>到了<span class="stress bigsize">三年级</span>下学期时，我们班上了一节公开课...</p>`</pre>

<br />下面的代码就是错误的：<pre>#stressid{
    color:red;
}
\#bigsizeid{
    font-size:25px;
}
`<p>到了<span id="stressid bigsizeid">三年级</span>下学期时，我们班上了一节公开课...</p>` </pre>

##xpath
以下是一个 XML 范例文档，下面如有例子都引用这个 XML 范例。
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<bookstore>

<book>
  <title lang="eng">Harry Potter</title>
  <price>29.99</price>
</book>

<book>
  <title lang="eng">Learning XML</title>
  <price>39.95</price>
</book>

</bookstore>
```
###选取节点

| 表达式   |          描述          |   
|:------:|         :-----            |  
|/       |从根节点选取。|
|nodename|选取此节点的所有子节点。|
|//|从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。|
|.|选取当前节点。|
|..|选取当前节点的父节点。|
|@|选取属性。|	

###谓语
谓语用来查找某个特定的节点或者包含某个指定的值的节点。  
谓语被嵌在方括号中。  


| 路径表达式   | 结果      |
| :-------| :-----|
|/bookstore/book[1]|选取属于 bookstore 子元素的第一个 book 元素。|
|/bookstore/book[last()]|	选取属于 bookstore 子元素的最后一个 book 元素。|
|/bookstore/book[last()-1]|	选取属于 bookstore 子元素的倒数第二个 book 元素。|
|/bookstore/book[position()<3]|	选取最前面的两个属于 bookstore 元素的子元素的 book 元素。|
|//title[@lang]	|选取所有拥有名为 lang 的属性的 title 元素。|
|//title[@lang='eng']|	选取所有 title 元素，且这些元素拥有值为 eng 的 lang 属性。|
|/bookstore/book[price>35.00]|	选取 bookstore 元素的所有 book 元素，且其中的 price 元素的值须大于 35.00。|
|/bookstore/book[price>35.00]/title	|选取 bookstore 元素中的 book 元素的所有 title 元素，且其中的 price 元素的值须大于 35.00。|

###XPath 轴
轴可定义相对于当前节点的节点集。  
|轴名称|	结果|
|:-----|:-----|
|ancestor	|选取当前节点的所有先辈（父、祖父等）。|
|ancestor-or-self|	选取当前节点的所有先辈（父、祖父等）以及当前节点本身。|
|attribute|	选取当前节点的所有属性。|
|child|	选取当前节点的所有子元素。|
|descendant|	选取当前节点的所有后代元素（子、孙等）。|
|descendant-or-self|	选取当前节点的所有后代元素（子、孙等）以及当前节点本身。|
|following|	选取文档中当前节点的结束标签之后的所有节点。|
|namespace|	选取当前节点的所有命名空间节点。|
|parent	|选取当前节点的父节点。|
|preceding|	选取文档中当前节点的开始标签之前的所有节点。|
|preceding-sibling|	选取当前节点之前的所有同级节点。|
|self|	选取当前节点。|


###步（step）包括：
**轴（axis）**
定义所选节点与当前节点之间的树关系
**节点测试（node-test）**
识别某个轴内部的节点
**零个或者更多谓语（predicate）**
更深入地提炼所选的节点集  

步的语法：(**每个步均根据当前节点集之中的节点来进行计算。**)
轴名称::节点测试[谓语]

|例子|	结果|
|:---|:-----|
|child::book|	选取所有属于当前节点的子元素的 book 节点。|
|attribute::lang|	选取当前节点的 lang 属性。|
|child::*	|选取当前节点的所有子元素。|
|attribute::*	|选取当前节点的所有属性。|
|child::text()	|选取当前节点的所有文本子节点。|
|child::node()	|选取当前节点的所有子节点。|
|descendant::book	|选取当前节点的所有 book 后代。|
|ancestor::book	|选择当前节点的所有 book 先辈。|
|ancestor-or-self::book|	选取当前节点的所有 book 先辈以及当前节点（如果此节点是 book 节点）|
|child::*/child::price	|选取当前节点的所有 price 孙节点。|

