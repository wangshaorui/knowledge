# scrapy-notes

标签（空格分隔）： Scrapy学习

---

##安装 Scrapy
使用pip安装(用virtualenv虚拟出一个scrapy环境)：

    pip install scrapy
    
### Scrapy目录项说明
新建一个scrapy项目：

    scrapy startproject tutorial

之后，会在目录下面健一个tutorial目录，是这样的结构：

    tutorial/
    scrapy.cfg
    tutorial/
        __init__.py
        items.py
        pipelines.py
        settings.py
        spiders/
            __init__.py
            ...

这些文件分别是：

 - `scrapy.cfg`:项目的配置文件。
 - `tutorial/`:该项目的python模块，之后可以在这里加入代码。
 - `tutorial/items.py`:项目中的item文件。
 - `tutorial/pipelines.py`:项目中的pipelines文件。
 - `tutorial/settings.py`:项目中的设置文件。
 - `tutorial/spiders/`:放置spider代码的目录。
 
###定义Item
Item是保存爬到的数据的容器。  
在目录tutorial中有个items.py文件可以定义相应的字段：
```python
import scrapy

class DmozItem(scrapy.Item):
    title = scrapy.Field()
    link = scrapy.Field()
    desc = scrapy.Field()
```
### Spider
Spider可以通过接受参数来修改其功能。 spider参数一般用来定义初始URL或者指定限制爬取网站的部分。
`class scrapy.spider.Spider`
Spider是最简单的spider，每个其他的spider必须继承自该类(包括Scrapy自带的其他spider以及您自己编写的spider)。 Spider并没有提供什么特殊的功能。 其仅仅请求给定的 start_urls/start_requests ，并根据返回的结果(resulting responses)调用spider的 parse 方法。
`name`
定义spider名字的字符串，这个名字定义了scrapy如何定位并初始化spider，所以必须是唯一的。一般是以该网站的domain来命名spider。比如，spider要爬取`mywebsite.com`，通常可以命名：`mywebsite`。  
`allowed_domains`
可选。包含了spider允许爬取的域名(domain)列表(list)。 当 OffsiteMiddleware 启用时，域名不在列表中的URL不会被跟进。  
`start_urls`
URL列表。当没有制定特定的URL时，spider将从该列表中开始进行爬取。 因此，第一个被获取到的页面的URL将是该列表之一。 后续的URL将会从获取到的数据中提取。  
`start_request()`
该方法必须返回一个可迭代对象(iterable)。该对象包含了spider用于爬取的第一个Request。该方法的默认实现是使用 start_urls 的url生成Request。如果想要修改最初爬取某个网站的Request对象，您可以重写(override)该方法。  
`parse`
当response没有指定回调函数时，该方法是Scrapy处理下载的response的默认方法。`parse` 负责处理response并返回处理的数据以及(/或)跟进的URL。

####爬取规则rules
`rules`  
一个包含一个(或多个)Rule对象的集合(list)。每个Rule 对爬取网站的动作定义了特定表现。  
`class scrapy.contrib.spiders.Rule(link_extractor, callback=None, cb_kwargs=None, follow=None, process_links=None, process_request=None)`  

<a href = "#Extractors">`link_extractor`</a>是一个Link Extractor对象，定义了如何从爬取到的页面提取链接。  

`callback`是一个callable或string(该spider中同名的函数将会被调用)。从link_extractor中每获取到链接时将会调用该函数。该回调函数接受一个response作为其第一个参数，并返回一个包含Item 以及(或)Request对象(或者这两者的子类)的列表(list)。  

`cb_kwargs`包含传递给回调函数的参数(keyword argument)的字典。  

`follow`是一个布尔(boolean)值，指定了根据该规则从response提取的链接是否需要跟进。如果callback为None，follow默认设置为True，否则默认为False 。  

`process_links`是一个callable或string(该spider中同名的函数将会被调用)。从link_extractor中获取到链接列表时将会调用该函数。该方法主要用来过滤。  

`process_request` 是一个callable或string(该spider中同名的函数将会被调用)。 该规则提取到每个request时都会调用该函数。该函数必须返回一个request或者None。 (用来过滤request)

####Link Extractors <a name = "Extractors" id = "Extractors"> </a>
Link Extractors 是那些目的仅仅是从网页(scrapy.http.Response 对象)中抽取最终将会被follow链接的对象｡

##数据流(Data flow)
**Scrapy中的数据流由执行引擎控制**，其过程如下:

1. 引擎打开一个网站，找到处理该网站的Spider并向该spider请求第一个要爬取的URL(s)。
2. 引擎从Spider中获取到第一个要爬取的URL并在调度器以Request调度。
3. 引擎向调度器请求下一个要爬取的URL。
4. 调度器返回下一个要爬取的URL给引擎，引擎将URL通过下载中间件(请求(request)方向)转发给下载器(Downloader)。
5. 一旦页面下载完毕，下载器生成一个该页面的Response，并将其通过下载中间件(返回(response)方向)发送给引擎。
6. 引擎从下载器中接收到Response并通过Spider中间件(输入方向)发送给Spider处理。
7. Spider处理Response并返回爬取到的Item及(跟进的)新的Request给引擎。
8. 引擎将(Spider返回的)爬取到的Item给Item 
9. Pipeline，将(Spider返回的)Request给调度器。
(从第二步)重复直到调度器中没有更多地request，引擎关闭该网站。






