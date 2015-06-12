# python包路径的基础知识

标签（空格分隔）： python

---

##包的基础知识
python中的包是通过使用“点模块名称”创建模块命名空间的一种方法，例如，模块名称A.B表示一个在名为A的包下名为B的子模块。要在包pkg下定义一个简单的模块，这样：

 1. 在某目录下面建立一个目录，名为pkg
 2. 在pkg下建立一个`__init__.py`的模块，内容为空即可
 3. 在pkg目录下面建立一个`module_3.py`，内容如下：
 

```python
# -*- encoding:utf-8 -*-
'''pkg.module_3.py模块的内容'''
 
print("Hello World")
 
def func1():
    print("This is funciton one")
```
 注意第二步，必须要在每一个包目录下建立一个`__init__.py`模块，这个是python的规定，用来告诉python解释器将该目录当成一个内容包。因为包目录下的`__init__.py`模块是必须的，但内容是可选的，可以为空，也可以写一些代码或作其他用途，PVM在导入某个包下的模块时会先导入这个包下的`__init__.py`模块。
 
##python中的模块搜索路径
在一个模块被导入前，PVM会在后台从一系列路径中搜索该模块，其搜索过程如下：

 1. 在当前目录下搜索该模块；
 2. 在环境变量PYTHONPATH中指定的路径列表中依次搜索；
 3. 在python的安装路径中搜索。

事实上，PVM通过变量`sys.path`中包含的路径来搜索，这个变量里面包含的路径列表就是上面提到的这些路径信息，可以打印看看`sys.path`都包含哪些路径：
```python
>>>import sys
>>>print sys.path
['',
 '/home/vagrant/python/envs/roclaws/bin',
 '/home/vagrant/python/envs/roclaws/lib/python2.7',
 '/home/vagrant/python/envs/roclaws/lib/python2.7/plat-x86_64-linux-gnu',
 '/home/vagrant/python/envs/roclaws/lib/python2.7/lib-tk',
 '/home/vagrant/python/envs/roclaws/lib/python2.7/lib-old',
 '/home/vagrant/python/envs/roclaws/lib/python2.7/lib-dynload',
 '/usr/lib/python2.7',
 '/usr/lib/python2.7/plat-x86_64-linux-gnu',
 '/usr/lib/python2.7/lib-tk',
 '/home/vagrant/python/envs/roclaws/local/lib/python2.7/site-packages',
 '/home/vagrant/python/envs/roclaws/lib/python2.7/site-packages',
 '/home/vagrant/python/envs/roclaws/local/lib/python2.7/site-packages/IPython/extensions',
 '/home/vagrant/.ipython']
```
 
 不同的机器上显示的路径信息可能不一样，但至少都包含上面提到的3点。我们可以通过`sys.path.append()`方法，将模块添加到当前的包搜索路径中去。

##包的导入
通常模块为一个文件，直接用import来导入就好了。可以作为module的文件类型有：".py"、".pyo"、".pyc"、".pyd"、".so"、".dll"。
系统在导入模块时，要做以下三件事：

 1. 为源代码文件中定义的对象创建一个命名空间，通过这个命名空间可以访问到模块中定义的函数及变量。
 2. 为新创建的命名空间里面执行源代码文件。
 3. 创建一个名为源代码文件的对象，该对象引用模块的命名空间，这样就可以通过这个对象访问模块中的函数及变量，如：
 
```python        
import spam      #导入并运行模块spam
print spam.a     #访问模块spam的属性
spam.foo()
c = spam.bar()
...
```

用逗号分割模块名称就可以同时导入多个模块：
```python
import socket, os, regex
```
模块导入时可以使用as关键字来改变模块的引用对象名字：
```python
import os as system
import socket as net, thread as threads
system.chdir("..")
net.getthostname()
```

使用from语句可以将模块中的对象直接导入到当前的命名空间，from语句不创建一个到模块命名空间的引用对象，而是把被导入模块的一个或多个对象直接放入当前的命名空间：
```python
from socket import gethostname  #将gethostname放入当前命名空间
print gethostname()             #直接调用
socket.gethostname()            #引发异常NameError:socket
```

import语句可以在程序的任何位置使用，你可以在程序中多次导入同一个模块，但模块中的代码仅仅在该模块被首次导入时执行，后面的import语句只是简单的创建一个模块命名空间的引用而已。sys.modules字典中保存着所有被导入模块名到模块对象的映射，这个字典用来决定是否需要使用import语句来导入一个模块的最新拷贝。

from module import * 语句只能用于一个模块的最顶层。
**特别注意**，由于存在作用域冲突，不允许在函数中使用from语句。
每个模块都拥有`__name__`属性，它是一个内容为模块名字的字符串。最顶层的模块名称是`__main__`，命令行或者是交互模式下程序都运行在`__main__`模块内部。
