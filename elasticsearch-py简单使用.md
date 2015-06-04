# Elasticsearch-py

标签（空格分隔）： Elasticsearch

---

##简单介绍
这是官方为Elaticsearch提供的 低级别的(low-level)客户端。它的目标是在Python中为所有和Elasticsearch相关的代码提供一样的功能(common ground)。

安装：
`pip install elasticsearch`

##简单使用：
```python
>>> from datetime import datetime
>>> from elasticsearch import Elasticsearch

# by default we connect to localhost:9200
>>> es = Elasticsearch()

# datetimes will be serialized
>>> es.index(index="my-index", doc_type="test-type", id=42, body={"any": "data", "timestamp": datetime.now()})
{u'_id': u'42', u'_index': u'my-index', u'_type': u'test-type', u'_version': 1, u'ok': True}

# but not deserialized
>>> es.get(index="my-index", doc_type="test-type", id=42)['_source']
{u'any': u'data', u'timestamp': u'2013-05-12T19:45:31.804229'}
```

##特征
这个客户端的特征包括：

 - 转化基础的python数据结构到json数据结构，或者把json的数据结构转化成python的数据结构(由于性能的原因，datetimes类型并没有转化)
 - 自动发现集群结点可配置
 - 持久连接
 - 从可知的结点中负载均衡（通过pluggable selection strategy）
 - 连接失败处罚(基于时间，也就是说连接失败了不会再次尝试连接直到timeout)
 - 线程安全
 - pluggable architecture

##API 文档
为和了Python整个环境相适应，参数的名字使用`from_`而不是`from`，用`doc_type`而不是`type`。
###全局变量
有一些变量是客户端自己添加进去的，这些变量可以在任何API调用的时候使用。
**Ignore**
如果elasticsearch返回一个2XX的应答，我们就认为这个API调用是成功的。否则就会产生一个`TransportError`的错误实例。如果你不想报错的话，你可以直接在参数`ignore`中传递一个单值或者一个列表的值：
```python
from elasticsearch import Elasticsearch
es = Elasticsearch()

# ignore 400 cause by IndexAlreadyExistsException when creating an index
es.indices.create(index='test-index', ignore=400)

# ignore 404 and 400
es.indices.delete(index='test-index', ignore=[400, 404])
```

**Timeout**
全局变量timeout可以在构建客户端的时候设定，或者在每个请求时用`request_timeout`作为API调用的一部分，这个值将会被传递到connection这个类中的`perform_request`方法中：
```python
# only wait for 1 second, regardless of the client's default
es.cluster.health(wait_for_status='yellow', request_timeout=1)
```

**备注**：有一些API调用也有`timeout`参数，而这些参数是传递到Elasticsearch上去的。这些timeout是内部参数，它并不能保证请求会在它指定的时间里面结束。

##Elasticsearch
```python
class elasticsearch.Elasticsearch(hosts=None, transport_class=<class 'elasticsearch.transport.Transport'>, **kwargs)
```

Elasticsearch low-level client，直接提供一个从Python到ES REST端的映射。

这个实例有这些属性：`cat`,`cluster`,`indices`,`nodes`及`smapshot`，而这些又各自提供了访问`CatClient`，`ClusterClient`，`IndicesClient`，及`SnapshotClient`实例的途径，这是最好的，也是唯一的方法可以得到那些类的实例及类里面的方法。

你可以通过参数connection_class指定自己的connection class:
```python
# create connection to localhost using the ThriftConnection
es = Elasticsearch(connection_class=ThriftConnection)
```

如果你想开启`Sniffing`，有几个办法：
```python
# create connection that will automatically inspect the cluster to get
# the list of active nodes. Start with nodes running on 'esnode1' and
# 'esnode2'
es = Elasticsearch(
    ['esnode1', 'esnode2'],
    # sniff before doing anything
    sniff_on_start=True,
    # refresh nodes after a node fails to respond
    sniff_on_connection_fail=True,
    # and also every 60 seconds
    sniffer_timeout=60
)
```

不同的主机有不同的参数，用一个字典来对每一个结点做配置：
```python
# connect to localhost directly and another node using SSL on port 443
# and an url_prefix. Note that ``port`` needs to be an int.
es = Elasticsearch([
    {'host': 'localhost'},
    {'host': 'othernode', 'port': 443, 'url_prefix': 'es', 'use_ssl': True},
])
```
**Parameters**:

 - hosts - 我们要连的一个结点列表。结点应该是字典类型的({“host”: “localhost”, “port”: 9200})，这整个字典都会被当成一个参数传到Connection类中，或者像这样形式的字符串`host[:port]`，不过它也会被自动得转化成字典。
 - transport_class - Transport的字类。
 - kwargs - 所有附加的参数都会被传递到`Transport`类中和`Connection`实例中。

```python
bulk(*args, **kwargs)
```

