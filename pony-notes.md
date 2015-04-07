# pony-notes

标签（空格分隔）： pony

---
主要参考[官网文档][1]
##Pony安装

    pip install pony
包导入：

    from pony.orm import *

##创建一个数据库

    db = Database('sqlite', ':memory:')
这条语句创建了一个数据库的连接对象，第一个参数指定了数据库的类型，目前Pony支持四个类型的数据库，分别是`sqlite`，`mysql`， `postgresql`和`oracle`。第二个参数对于每个数据库的类型都是特定的，除非使用DB-API的方式连接，那么它对所有的数据库类型，都是一样的。对于`sqlite`，根据数据库创建的位置(硬盘或者内存)，数据库的文件名或者字符串":memory:"作为参数。如果数据库在内存中创建，那么在python关闭的时候它也就被删除了，为了能使数据库保存在文件中，你可以这样：

    db = Database('sqlite', 'test_db.sqlite', create_db=True)

##定义实体
现在，我们创建两个实体——车和人。人有两个属性——名字和年龄，车也有两个属性——产家和型号。
```python
class Person(db.Entity):
    name = Required(str)
    age = Required(int)
    cars = Set("Car")

class Car(db.Entity):
    make = Required(str)
    model = Required(str)
    owner = Required(Person)
```
每个实体都必须包含一个主键，而主键是自动生成的，如果是自动生成的，那就是数值型的id。

##映射实体到数据库表
现在我们需要创建表来存储对象的数据了：

    db.generate_mapping(create_tables=True)
    
参数`create_tables=True`，表示如果表不存在，那么就会使用`create table`命令来创建表。  
所有的实体连接到数据库都必须调用`generate_mapping()`方法。

##延后数据库绑定（Late database binding）
之前的那种绑定方式是先指定数据库的连接，而后再进行绑定，而这种‘后期数据库绑定’是在实体都创建完毕之后，再进行数据库的连接然后绑定。
```python
### module my_project.my_entities.py
from pony.orm import *

db = Database()
class Person(db.Entity):
    name = Required(str)
    age = Required(int)
    cars = Set("Car")

class Car(db.Entity):
    make = Required(str)
    model = Required(str)
    owner = Required(Person)

### module my_project.my_settings.py
from my_project.my_entities import db

db.bind('sqlite', 'test_db.sqlite', create_db=True)
db.generate_mapping(create_tables=True)
```
这种方式可以让你的数据库绑定和实体的定义分开来，对测试来说是很有用的。

##创建实体实例填充数据库
实例化三个人、两辆车：
```python
p1 = Person(name='John', age=20)
p2 = Person(name='Mary', age=22)
p3 = Person(name='Bob', age=30)
c1 = Car(make='Toyota', model='Prius', owner=p2)
c2 = Car(make='Ford', model='Explorer', owner=p3)
commit()
```
Pony不会在对象创建的时候就将它们写入数据库，而是在`commit()`提交完之后。

##编写查询语句
前面我们在数据库中插入了5条数据，现在可以做一些查询，如下查询返回了一个年龄大于 20 的 person 的列表：

    select(p for p in Person if p.age > 20)
    <pony.orm.core.Query at 0x105e74d10>

select函数返回了一个Query类型的实例，获取对象的列表的一个方法是对它进行一次切片操作 [:]：

    select(p for p in Person if p.age > 20)[:]
    SELECT "p"."id", "p"."name", "p"."age"
    FROM "Person" "p"
    WHERE "p"."age" > 20
    [Person[2], Person[3]]

##获取对象
如果要通过主键查询对象，可以在方括号中填写主键。

    p1 = Person[1]
    print p1.name
    John
你可能会注意到，实际上并没有给数据库发送数据。那是因为这个对象已经在 session 缓存中存在了。缓存可以减少提交到数据库的请求数量。 通过其他属性获取对象：

    >>> mary = Person.get(name='Mary')
    SELECT "id", "name", "age"
    FROM "Person"
    WHERE "name" = ?
    [u'Mary']
    >>> print mary.age
    22
这种情况下，即使对象已经被缓存，查询依然会被发送到数据库，因为 name 不是唯一性的键。数据库 session 缓存只会在通过主键或唯一性键查询时起作用。

##更新一个对象

    mary.age += 1
    commit()
    
Pony 持续跟踪对象属性的变化。当 commit() 执行时，在此期间所有改变的对象都将同步到数据库。Pony 只会更新改变了的属性。  





















  [1]: http://doc.ponyorm.com/index.html
