# OS X 中开发环境配置指南

标签（空格分隔）： 开发环境配置

---

## 工作 MAC 电脑初次使用指南

1. 开机
2. 选择语言：简体中文
3. 选择地区：中国
4. 选择键盘布局：中文-拼音
5. 网络 Wifi ：选择 Datartisan-5G ，密码 datartisan 
6. 传输信息：不需要
7. 注册 Apple ID
  - 生日：1990-01-01
  - 邮件地址使用工作邮箱（即@datartisan.com结尾邮箱）
  - 密码使用简单好记的密码，因为有时工作需要别人会用你的电脑，所以不要使用和自己其他密码相同的密码
  - 授权邮箱：guopeng@datartisan.com
  - 密码找回问答：问题随便选择，回答依次为 10 个 a ，10 个 b，10 个 c，拍照记录留存，以便以后找回密码（图片发给 @GuoPeng）

8. 创建电脑用户：使用刚注册 apple id 信息
9. Find Mac：开启
10. 升级到 iCloud：同意升级
11. 系统诊断与用量：全部不需要

**进入系统后**：
12. 在系统偏好设置，键盘，快捷键：所有项都恢复默认
13. 在系统偏好设置，鼠标：滚动方向“自然”取消掉
14. 连接网络中的 datartisan 共享盘，在“共享”文件夹内有 office 和 chrome 的安装文件
15. 配置 vpn ，https://tower.im/s/c79Np

## 如何通过 AppStore 的验证，从而可以在 AppStore 下载软件？

- 验证时选择“香港”地区，无银行卡信息，填写地址等信息，提交
- 接受验证邮件，点击邮件内连接，跳转网页输入 Apple ID 邮箱地址和密码，完成验证
- 进入 AppStore 应用，在账户栏目，修改地址到“中国”，在填写信息的时候选择“银行卡”，并完善相关地址等信息，确认

这样就完成了，中国区 AppStore 的验证。

## Apple ID

第一次开机的时候需要注册，*使用公司邮箱（即 @datartisan.com 结尾）的邮箱注册*。

## 浏览器

推荐使用 chrome

## 编辑器

### 文本编辑器

- 推荐使用 sublimetext

### IDE

- 推荐使用 Jetbrains 系列
  https://www.jetbrains.com/

## 开发工具软件，按顺序安装

### iterm2

https://www.iterm2.com/

### Homebrew

Homebrew是一个包管理器，就类似于Redhat中的yum，它可以自动解决软件包中的依赖。  
另外，Homebrew相较于OS X中其他的包管理器它有一个很好的特性，就是它尽可能的利用系统自带的各种库，这样就使得软件包的编译时间大为缩短，同时它也几乎不会造成冗余，软件包的管理也清晰灵活了许多。
### Homebrew 安装
终端粘贴下面一条命令即可：
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

#### 安装扩展
由于 brew 和包含的包源都是通过 github 来管理，人为的维护管理，除了自己的源还允许别人的源添加进来。
`$ brew tap <gihhub_user/repo>`
这个命令并没有包含任何的帮助说明，其实它只接受上面的这个参数。
如下：
`$ brew tap josegonzalez/php`
`$ brew install php55`

### pyenv
安装命令：
`brew install pyenv`

pyenv可以安装/管理多个系统中的多个python版本。
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

### Laravel Homestead
Laravel Homestead是一个官方预载的Vagrant「封装包」，你不需要在你的本机端安装PHP、HHVM、网页服务器或任何服务器软件。它和win系统中的wamp干的活有着一些类似。
#### VirtualBox 与 Vagrant
Vagrant用于创建和部署虚拟化开发环境，就是可以通过Vagrant封装一个开发环境，然后将这个环境分发给团队成员，而且是跨平台的，也就是说，成员可以在自己喜欢的桌面系统上开发程序，不管是Mac还是Windows还是Linux。
### 安装Lavavel Homestead
 1. 在启动Homestead环境之前，必须先安装VirtualBox和Vagrant。
 对应的下载地址分别为：
https://www.virtualbox.org/wiki/Downloads
https://www.vagrantup.com/downloads.html
 2. 安装好了之后，增加Vagrant封装包，就是将`laravel/homestead`这个box安装进你的Vagrant安装程序中，在终端执行：  
 `vagrant box add laravel/homestead`  
*程序有点大，可以将下载地址拷贝出来放到外面的下载工具进行下载，然后进入所下载目录，在上条命令后面添加box的名字即可安装。*  
 3. 接着，通过homebrew安装PHP和Composer
 `brew tap homebrew/dupes`
 `brew tap homebrew/php`
 上面两条命令是往homebrew里添加库，然后就可以安装了：
 `brew install php56`
 `brew install composer`
 安装完Composer之后，可以通过Composer来安装Homestead CLI:
 `composer global require "laravel/homestead=~2.0"`
 接着把homestead加入环境变量：
 `PATH=$PATH:~/.composer/vendor/bin` *这个只是临时生效的，要永久生效，还是要写入shell的profile中*
 然后：
 `homestead init` *这里会成生Homestead.yaml 配置文件*
 `homestead edit` *进入配置环境*
### Homestead.yaml 配置
**Provider**，即设置成自己喜欢的virtualbox 或者 vmware_fusion
**authorize**，和key是成对存在的，通过ssh授权的方式和你的homestead环境进行连接。
**folders**：这个是共享目录，即你本地的这个map:目录会映射到homestead中即to:目录中去。
**sites**: 属性就是简单的对应一个域名到一个homestead环境目录中去。
其余的配置就不管了，我也搞不懂是些啥。
还有最后一步，就是在hosts中将域名添加进去，这样访问域名的时候，就会导向Homestead环境中去。
如下：
`192.168.10.10  homestead.app`
另外，就是如果要新加一个域名，在sites中添加对应的内容之后，要执行`homestead provision`。
接下来，就可以运行了：
 `homestead up`

非必须：

### zsh

> brew install zsh

### oh-my-zsh

https://github.com/robbyrussell/oh-my-zsh#basic-installation








