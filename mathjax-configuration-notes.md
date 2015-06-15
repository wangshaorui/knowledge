# mathjax基础知识

标签（空格分隔）： mathjax

---

##MathJax简介
MathJax是一款运行在浏览器中的开源的数学符号渲染引擎，使用MathJax可以方便在浏览器中显示数学公式，不需要使用图片，目前，MathJax可以解析Tex、LaTex、MathML和ASCIIMathML的标记语言。 

##开始
有两种方式可以获取到MathJax，最简单的方式就是使用CDN链接的方式，也就是在你的网页中的`<head>`块区域嵌入如下代码即可：
```html
<script type="text/javascript"
  src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

另外一种方式就是在你的服务器上下载和安装MathJax：
要实现这种方式，做以下的事情：

 1. 下载得到一份MathJax的拷贝。
 2. 配置MathJax来适应你的网站。
 3. 在包含数学公式的网页中链接MathJax。
 4. 数学公式就能显示了。

###下载和安装MathJax
MathJax的源码在[GitHub][1],要安装MathJax下载最新发布的[版本][2]，然后解压。解压完后，使用`test`目录的文件来测试安装是否正常工作：

> test/
      index.html
      index-images.html
      sample.html
      examples.html

在你的浏览器中打开这些文件检查它们是否正常工作。如果把MathJax安装在服务器上，要使用网络地址访问而不是直接访问文件。

###配置MathJax
 当按照上文的方法在网页中引入了MathJax，它将会加载文件`config/Tex-AMS-MML_HTMLorMML.js`。这个文件预加载了很多MathJax常用组件，可以处理Tex、LaTex或者MathMl格式的数学公式。

###常用配置
在目录`MathJax/config`中，有很多个预加载的配置文件。其中，`default.js`文件包含了几乎所有可能的配置选项及对这些配置的注释。所以，可以这样简单的加载它：
```html
<script type="text/javascript" src="path-to-MathJax/MathJax.js?config=default"></script>
```
目录`MathJax/config`中的其它文件不只是配置参数，还有些是MathJax加载那些配置文件的必要文件。

####TeX-MML-AM_HTMLorMML配置文件
这个配置文件是最常用的预加载配置文件。它加载了全部主要的MathJax的组件，包括Tex、MathML和AsciiMath预处理器和输入处理器，以及AMSmath、 AMSsymbols、noErrors和noUndefined TeX扩展，还MathML和HTML-with-CSS输出处理定义。

####TeX-AMS-MML_HTMLorMML配置文件
加载Tex和MathML预处理器和输入处理器。

####TeX-AMS_HTML配置文件
这个配置文件用于那些只用TeX格式的网站。

####MML_HTMLorMML配置文件
这个配置文件用于那些只用MathML格式的网站。

####AM_HTMLorMML配置文件
这个配置文件用于那些只用AsciiMath格式的网站。
















  [1]: https://github.com/mathjax/MathJax/
  [2]: https://codeload.github.com/mathjax/MathJax/zip/master