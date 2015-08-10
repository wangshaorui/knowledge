＃ Homestead 学习笔记
----

##Homebrew

Homebrew是一个包管理器，就类似于Redhat中的yum，它可以自动解决软件包中的依赖。  
另外，Homebrew相较于OS X中其他的包管理器它有一个很好的特性，就是它尽可能的利用系统自带的各种库，这样就使得软件包的编译时间大为缩短，同时它也几乎不会造成冗余，软件包的管理也清晰灵活了许多。

## Homebrew 安装

终端粘贴下面一条命令即可：
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

### Homebrew 隐藏命令

#### 1.安装扩展

由于 brew 和包含的包源都是通过 github 来管理，人为的维护管理，除了自己的源还允许别人的源添加进来。
`$ brew tap <gihhub_user/repo>`
这个命令并没有包含任何的帮助说明，其实它只接受上面的这个参数。
如下：
`$ brew tap josegonzalez/php`
`$ brew install php55`
## Laravel Homestead
Laravel Homestead是一个官方预载的Vagrant「封装包」，你不需要在你的本机端安装PHP、HHVM、网页服务器或任何服务器软件。它和win系统中的wamp干的活有着一些类似。
### VirtualBox 与 Vagrant
Vagrant用于创建和部署虚拟化开发环境，就是可以通过Vagrant封装一个开发环境，然后将这个环境分发给团队成员，而且是跨平台的，也就是说，成员可以在自己喜欢的桌面系统上开发程序，不管是Mac还是Windows还是Linux。
## 安装Lavavel Homestead
 1. 在启动Homestead环境之前，必须先安装VirtualBox和Vagrant。
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

