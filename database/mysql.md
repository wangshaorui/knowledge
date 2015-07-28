# MySQL

*操作系统: ubuntu-server-14.04*

## 安装

```
sudo apt-get install mysql-server
```

- Tips 安装时不需要输入密码

方法一：

```
sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password my_password'
sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password my_password'
sudo apt-get -y install mysql-server
```