# PostgreSQL

## 安装

*在 ubuntu-server-14.04 中*

```
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | \
  sudo apt-key add -

echo "deb http://apt.postgresql.org/pub/repos/apt/ trusty-pgdg main" | sudo tee -a /etc/apt/sources.list.d/pgdg.list

sudo apt-get update -y
sudo apt-get install postgresql-9.4 postgresql-contrib-9.4 -y
```

## 基本配置

### 访问范围控制

文件

/etc/postgresql/9.4/main/postgresql.conf

配置

listen_addresses = '*'

文件

/etc/postgresql/9.2/main/pg_hba.conf

配置

```
# 这里的 ip 是可访问范围
host    all             all             0.0.0.0/0               md5
```

设置 postgres 用户密码

```
sudo -u postgres psql
\password
```

重启 pg

```
service postgresql restart
```

## 建立库与对应用户

```
sudo -u postgres psql
# 进入 pg 管理命令行
create database da_test;
create user da_test with password 'da_test';
grant all privileges on database da_test to da_test;
```