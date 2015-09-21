# virtualenv-notes

标签（空格分隔）： virtualenv

---
## Virtualenv
Virtualenv 是一个虚拟环境程序，可以把开发环境隔离。基本思想是建立不同的环境目录，其中装有独立的各类包，甚至也可以是独立的不同版本python程序。  


- 创建虚拟环境：

    virtualenv [虚拟环境目录]

默认情况下，虚拟环境会依赖系统环境中的site packages，就是说系统中已经安装好的第三方package也会安装在虚拟环境中，如果不想依赖这些package，那么可以加上参数 --no-site-packages建立虚拟环境:

    virtualenv --no-site-packages [虚拟环境目录]

-  启动虚拟环境：

    source ./bin/activate

注意此时命令行会多出一个pythonenv，pythonenv为虚拟环境名称，接下来所有模块都只会安装到该目录中去。  
  
- 退出虚拟环境：

    deactivate
    
- 多个版本创建virtualenv

当主机上安装有不同版本的Python，如Python 2.6、Python 2.7或者Python 3.x，它们会共享相同的bin主目录。

virtualenv支持--python选项，该选项可以直接指定虚拟环境运行的Python环境。如：

    virtualenv --python=/usr/local/python2.7/bin/python2.7 py27ENV
    
查看：

    ls py27ENV
    bin/    lib/

可以看出virtualenv创建了一个相对的bin目录和lib目录，bin目录中是Python解释器，lib目录是它自己的本地site-packages目录。

**或者**，也可以直接由`pyenv local`指定当前目录下的python版本。

具体使用都可查看［Virtual Environments］(http://docs.python-guide.org/en/latest/dev/virtualenvs/)

