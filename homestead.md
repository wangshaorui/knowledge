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
 3. 拷贝Homestead仓库
 `git clone https://github.com/laravel/homestead.git Homestead`
 然后进入目录Homestead，运行`bash init.sh`创建Homestead.yaml配置文件。
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


 ### 以下是我在使用过程中遇到的问题的汇总 ###

 	#### Laravel代码发生项目库启动错误 ####
	1. 使用国内数据源: composer config -g repositories.packagist composer http://packagist.phpcomposer.com
	2. rm -Rf vendor/ (删除项目库)
	3. php composer.phar update -vvv

	#### Laravel debugger学习 ####
	1. 安装
		1. 在项目目录下执行: composer require barryvdh/laravel-debugbar
		2. 到config/app.php文件中, providers配置下添加Barryvdh\Debugbar\ServiceProvider::class,
			aliases配置下'Debugbar' => Barryvdh\Debugbar\Facade::class,
		3. php artisan vendor:publish
		4. 如有必要可进行缓存和配置缓存清除:
			1. php artisan cache:clear 
			2. php artisan config:clear
	2. 使用：
		1. 可以在View中或Controller写入调试代码，比如:
		<?php Debugbar::info(‘Debug'); ?>
		就可以在浏览器端看到调试条了。

	#### 可以使用国内的pip源 ####
	pip install --trusted-host pypi.mirrors.ustc.edu.cn -i http://pypi.mirrors.ustc.edu.cn/simple/ {包名}

	#### 创建新的larvel项目 ####
	1. 使用命令：laravel new project_name
	2. 修改homestead的site map （在.homestead下的Homestead.yaml(cd ~/.homestead)）
	3. 修改host里面的主机映射（在虚拟机/etc下的hosts）
	4. 查看虚拟机下nginx下启动了哪些程序: cd /etc/nginx/sites-available/  ls


	#### vagrant操作事项：####
	1. 连接到vagrant： vagrant ssh
	2. 查看状态： vagrant status
	3. 创建项目时需要重启环境：vagrant provision


	#### 数据库迁移 ####
	php artisan migrate(在项目目录下)

	#### Mac下启动redis ####
	redis-server /usr/local/etc/redis.conf
	_(homestead下默认有安装redis)_
