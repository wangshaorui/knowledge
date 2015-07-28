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

### 建立库与对应用户

```
sudo -u postgres psql
# 进入 pg 管理命令行
create database da_test;
create user da_test with password 'da_test';
grant all privileges on database da_test to da_test;
```


## 基本使用

### 查询当前工作库

```
# 在 psql 中
\c
```

```
# sql 中
SELECT current_database();
```

### 引入 uuid 生成相关函数

```
#选择数据库
CREATE EXTENSION pgcrypto;
#SELECT gen_random_uuid();
```

建表时指定生成 uuid

```
CREATE TABLE sometable(
   id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
   name TEXT,
   email TEXT
);
```


### 获取当前可用的 extension

select * from pg_extension;



## SQL 范例


```
-- 将 时间 与 properties->>'p2' 分组计数
select to_char(date_trunc('week', created_at), 'YYYY-MM-DD') as time, properties->>'p2', count(id) from "1_event" where name ='event-a' group by time, properties->>'p2' order by time;
```

```
-- 按照不同的 name 值进行累计技术
select to_char(date_trunc('week', created_at), 'YYYY-MM-DD') as time,
sum(case when name = 'event-a' then 1 else 0 end) as "event-a",
sum(case when name = 'event-b' then 1 else 0 end) as "event-b",
sum(case when name = 'event-c' then 1 else 0 end) as "event-c"
from "1_event"
group by time
order by time
```