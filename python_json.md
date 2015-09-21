# Python Json #
## 为什么选择Json ##
我们把变量从内存中变成可存储或传输的过程称之为序列化。

序列化之后，就可以把序列化后的内容写入磁盘，或者通过网络传输到别的机器上，

反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化。

一般的Python序列化，pickle只能用于Python，并且可能不同版本的Python彼此都不兼容,

我们要在不同的编程语言之间传递对象，就必须把对象序列化为标准格式，比如XML，但更好的方法是序列化为JSON，它比XML更快， 

所以我们选择Json。

## Python数据类型与Json数据类型的对应 ##
        JSON类型	     Python类型
        {}           dict
        []	         list
        "string"	 'str'或u'unicode'
        1234.56	     int或float
        true/false	 True/False
        null	     None


## 序列化操作 ##

`json.dumps()` 序列化
`json.loads()` 反序列化，_得到的所有字符串对象默认都是unicode而不是str_。

正常情况下，类的序列化是会报错的，
那么如何把类序列化？
假设有个学生Student类，

        class Student(object):
            def __init__(self, name, age, score):
                self.name = name
                self.age = age
                self.score = score

在类里面写一个转化函数，之后传入dumps()里，

        def student2dict(std):
            return {
                'name': std.name,
                'age': std.age,
                'score': std.score
            }

        print(json.dumps(s, default=student2dict))

如何反序列化？
在类里面写一个转化函数，之后传入loads()里，

        def dict2student(d):
            return Student(d['name'], d['age'], d['score'])


## 几个不同json库的比较 ##          

cjson是用C语言实现，

[yajl](https://github.com/lloyd/yajl)是Cython版本的JSON实现。 

[simplejson](https://github.com/simplejson/simplejson)与标准库JSON的区别不大，但更新可能更快([查看文档](http://simplejson.readthedocs.org/en/latest/))。

    It is pure Python code with no dependencies, but includes an optional C extension for a serious speed boost

#### [anyjson](https://bitbucket.org/runeh/anyjson): ####

    Anyjson loads whichever is the fastest JSON module installed and provides a uniform API regardless of which JSON implementation is used.

安装命令：**sudo apt-get install python-anyjson**

看了一下源码，其实它就是整合了
`yajl`，`jsonlib2`，`jsonlib`， `simplejson`， `json`， `django.utils.simplejson`，`cjson`，
然后把所有的序列化和反序列化接口统一起来。