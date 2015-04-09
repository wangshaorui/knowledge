# server

## info

121.201.8.182

管理账号（root）key: qingcloud-gd1-key

使用账号（datartisan）


## 记录

```
# 管理登录
# 更新源
apt-get update -y
# 系统更新
apt-get upgrade -y
# 重启
shutdown -r now

# 安装所需要的工具、依赖
apt-get install -y git python-virtualenv python-dev libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev

# 建立工作账号
adduser datartisan

# 修改 ssh 配置，可以远程密码登录
vim /etc/ssh/sshd_config
# 配置 PasswordAuthentication yes 

# 重启 ssh 服务
service ssh restart

# 安装 mysql 相关
apt-get install mysql-server

# 修改 mysql 配置，可以外网访问
vim /etc/mysql/my.cnf
# 配置 bind-address = 0.0.0.0
# 配置 port = 33060 ，注意 [client] 与 [mysqld] 中都需要修改
# 重启 mysql 服务
service mysql restart
# !!需要在 qingcloud 开放 33060 端口，防火墙配置


# 建立 使用 库、账号（与代码内一致）
# 登录 mysql 
mysql -uroot -p
>create database estate;
>grant all privileges on estate.* to homestead@'%' identified by 'secret';
>grant all privileges on estate.* to homestead@'localhost' identified by 'secret';
>flush privileges;

# 安装 supervisor
apt-get install supervisor

# 配置 supervisor
vim /etc/supervisor/supervisord.conf
# 添加
[inet_http_server]
port = *:9001
username = datartisan
password = {supervisor_pw}
# !!需要注意，要打开防火墙对应端口
# 重启 supervisor 服务

# 部署项目
# 使用 datartisan 登录
# 配置 git ，name 与 email

# 建立虚拟环境
mkdir -p ~/works/python/envs/
cd ~/works/python/envs/
virtualenv scrapy

# 拉取代码
cd scrapy
git clone https://G_will@bitbucket.org/Glosure/scrapy-estate.git

# 安装依赖
cd scrapy-estate
pip install cython
pip install service_identity
pip install -r requirements.txt -U --force-reinstall


# 配置 supervisor 任务
# 使用 root 登录
ln -s /home/datartisan/works/python/envs/scrapy/scrapy-estate/estate.conf /etc/supervisor/conf.d/estate.conf
# 重启 supervisor



# 文本处理
# 安装 goose 

git clone https://github.com/grangier/python-goose.git
cd python-goose
pip install -r requirements.txt
python setup.py install

# 安装 jieba ujson readability
pip install jieba
pip install ujson
pip install readability-lxml

```


## 后续处理

1. 内容提取


```
# 进入虚拟环境
cd ~/works/python/envs/scrapy/scrapy-estate
export PYTHONPATH=`pwd`
python scripts/extract_content.py {start_id} {end_id} 
# 可以分 id 段，起多进程

```

2. 检查处理 where content = "[extract_error]"; 的记录
    - 重新提取
    - 删掉无法提取的 "[extract_error]" 记录

3. 对于 where content = "[no_cleaned_text]"; 的记录使用 readability 再提取

```
python scripts/extract_content_with_readability.py
```

4. 分词

```
python scripts/word_segment.py
```

5. 删除 where seg_freq = "{}"; 的记录

6. 进行话题标注

```
python scripts/mark_topic.py
```

7. 导出数据

```
python scripts/export_csv.py
```