# Docker

标签（空格分隔）： Docker

---
## 什么是 Docker
Docker 项目的目标是实现轻量级的操作系统虚拟化解决方案。 Docker 的基础是 Linux 容器（LXC）等技术。
在 LXC 的基础上 Docker 进行了进一步的封装，让用户不需要去关心容器的管理，使得操作更为简便。用户操作 Docker 的容器就像操作一个快速轻量级的虚拟机一样简单，且是在操作系统层面上实现虚拟化，直接复用本地主机的操作系统，而传统方式则是在硬件层面实现。
### Docker几个重要的概念

1. Docker 镜像  
Docker 镜像就是一个只读的模板。
例如：一个镜像可以包含一个完整的 ubuntu 操作系统环境，里面仅安装了 Apache 或用户需要的其它应用程序。
镜像可以用来创建 Docker 容器。
Docker 提供了一个很简单的机制来创建镜像或者更新现有的镜像，用户甚至可以直接从其他人那里下载一个已经做好的镜像来直接使用。

2. Docker 容器  
Docker 利用容器来运行应用。
容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。
可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。
*注：镜像是只读的，容器在启动的时候创建一层可写层作为最上层。

3. Docker 数据卷  
数据卷是一个可供一个或多个容器使用的特殊目录，它绕过 UFS，可以提供很多有用的特性：
 * 数据卷可以在容器之间共享和重用
 * 对数据卷的修改会立马生效
 * 对数据卷的更新，不会影响镜像
 * 数据卷默认会一直存在，即使容器被删除  
*注意：数据卷的使用，类似于 Linux 下对目录或文件进行 mount，镜像中的被指定为挂载点的目录中的文件会隐藏掉，能显示看的是挂载的数据卷。

4. Dockerfile   
使用 Dockerfile 可以允许用户创建自定义的镜像。
一般的，Dockerfile 分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令。

5. Docker-compose  
Compose 定位是“defining and running complex applications with Docker”，前身是 Fig，兼容 Fig 的模板文件。
Dockerfile 可以让用户管理一个单独的应用容器；而 Compose 则允许用户在一个模板（YAML 格式）中定义一组相关联的应用容器（被称为一个 project，即项目），例如一个 Web 服务容器再加上后端的数据库服务容器等。

## 美特好项目展示服务器的部署
*注：作为一个演示用的服务器，直接用php里面自带的server，没有配置nginx或者apache容器。
需要三个基本镜像：

 - mdap_web主体程序
 - mysql
 - es:1.6

首先，利用Dockerfile配置mdap_web容器：
```
FROM ubuntu:14.04

RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
RUN sed -i 's/security.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

RUN apt-get update && apt-get install -y \
    php5-cli php5-fpm php5-apcu php5-redis php5-curl php5-mcrypt php5-json php5-xdebug php5-mysql php5-pgsql php5-gd \
 && apt-get -y autoremove \
 && apt-get clean \
 && php5enmod mcrypt

ADD . /var/mdap

RUN cp /var/mdap/web/.env.docker /var/mdap/web/.env

WORKDIR /var/mdap/web
VOLUME /var/mdap/web/storage

CMD php artisan serve --host 0.0.0.0 --port 80

```
其中最后一行就是在容器中运行php自建的一个服务器。

这样，最主要的mdap项目容器配置完毕，可以直接进行bulid，而es和mysql并不需要自己build，直接从容器仓库取image就行了，用docker-compose进行容器管理：

```yaml
version: "2"
services:
 es:
   image: elasticsearch:1.6
   volumes:
     - es_data:/usr/share/elasticsearch/data
   ports:
     - "9200"
     - "9300"
 web:
   build: .
   links:
     - es
     - mysql
   volumes:
     - web_data:/var/mdap/web/storage
   ports:
     - "58080:80"
 mysql:
   image: mysql
   volumes:
     - mysql_data:/var/lib/mysql
   ports:
     - "3306"
   environment:
     MYSQL_DATABASE: "mdap"
     MYSQL_USER: "mdap"
     MYSQL_PASSWORD: "mdap"
     MYSQL_ALLOW_EMPTY_PASSWORD: "yes"

volumes:
  es_data:
    external:
      name: mdap_es_data
  web_data:
    external:
      name: mdap_web_data
  mysql_data:
    external:
      name: mdap_mysql_data
```

这就是docker-compose对.yaml直接进行docker的配置，其中的配置项build需要上面的Dockerfile文件在当前目录下，不能在其他目录（上层目录识别不了）。

其中，比较值得注意的是数据和应用都隔离开了，如ES中存放着整个项目的演示数据是以volumes的形式挂载在ES的容器外部，即es_data映射到ES容器的/usr/share/elasticsearch/data目录，而es_data则是映射root@default:/var/lib/docker/volumes/mdap_es_data。

docker-machine 有直接的scp命令可直接将本地的数据copy至docker-machine中。
