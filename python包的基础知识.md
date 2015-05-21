# python包的基础知识

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
