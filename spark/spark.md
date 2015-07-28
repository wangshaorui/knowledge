# Spark 


## 部署

操作系统：*ubuntu-server-14.04*


### 安装 jdk

方案一：安装 openjdk

```
sudo apt-get install openjdk-7-jdk
```

方案二：安装 oracle jdk

```
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update

# 自动选择交互式询问
echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | sudo /usr/bin/debconf-set-selections

sudo apt-get install -y oracle-java8-installer
sudo apt-get install -y oracle-java8-set-default
```

切换 jdk 版本

```
sudo update-java-alternatives -l
sudo update-java-alternatives -s java-8-oracle
```

### 下载 spark

http://spark.apache.org/downloads.html

选择 pre-build 高版本 hadoop 的版本下载

```
mkdir ~/spark
cd ~/spark
wget {url}
tar zxvf {spark-tar}
ln -s {path-with-version-number} spark
# 即以后切换 ./spark 的连接目标即可
```

## 使用

### standalone 集群搭建

*. 配置 ssh key

*. /etc/ssh/ssh_config

    配置

    StrictHostKeyChecking no

*. 每台机子上的私钥保存，公钥写入 ~/.ssh/authorized_keys

*. 各机子上的 /etc/hosts 要一致

*. 在 master 上

    sudo ./sbin/start-master.sh

*. 在各 slave 上

    sudo ./sbin/start-slave.sh spark://{master-domain}:7077