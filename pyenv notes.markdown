# pyenv notes


---
##pyenv 
pyenv可以安装/管理多个python。

`pyenv install -l` 显示可以安装的python
`pyenv install <version>` 安装某个python
`pyenv versions` 显示所有已安装的python 
`pyenv version` 显示当前使用的python
`pyenv which python` 显示当前python的安装路径
`pyenv global <version>` 设置默认的python版本
`pyenv local version` 在当前目录创建一个.python-version，以后进入这个目录自动切换为该版本
`pyenv shell <version>` 在当前的shell的session里启用某个python版本，优先级高于global, local

pyenv 有个插件叫作：virtualenv
### virtualenv
virtualenv用于创建独立的python环境，多个python相互独立，互不影响。
`virtualevn ENV` 创建ENV的虚拟环境
默认情况下，虚拟环境会依赖系统环境中的site packages，就是说系统中已经安装好的第三方package也会安装在虚拟环境中，如果不想依赖这些package，那么可以加上参数 --no-site-packages建立虚拟环境

virtualenv --no-site-packages [虚拟环境名称]






