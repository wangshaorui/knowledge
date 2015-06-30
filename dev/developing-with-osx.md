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

#### VirtualBox 与 Vagrant

Vagrant用于创建和部署虚拟化开发环境，就是可以通过Vagrant封装一个开发环境，然后将这个环境分发给团队成员，而且是跨平台的，也就是说，成员可以在自己喜欢的桌面系统上开发程序，不管是Mac还是Windows还是Linux。

### Laravel Homestead

Laravel Homestead是一个官方预载的Vagrant「封装包」，你不需要在你的本机端安装PHP、HHVM、网页服务器或任何服务器软件。它和win系统中的wamp干的活有着一些类似。

具体使用查阅 Laravel 文档中 Homestead 部分。


## 非必须工具：

### zsh

> brew install zsh

### oh-my-zsh

https://github.com/robbyrussell/oh-my-zsh#basic-installation








