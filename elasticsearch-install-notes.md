# elasticsearch的安装和一个小实验

标签（空格分隔）： Elasticsearch

---

##Elasticsearch简介和安装
Elasticsearch不仅仅是Lucene和全文搜索，还可以这样说：

 - 分布式的实时文件存储，每个字段都被索引并可被搜索
 - 分布式的实时分析搜索引擎
 - 可以扩展到上百台服务器，处理PB级结构化或非结构化数据

而且，所有的这些功能都被集成到一个服务里，你的应用可以通过简单的RESTful API、各种语言的客户端甚至命令行与之交互。

###Java环境的安装（这是运行Elasticsearch前提）
首先，要知道JRE和JDK的区别：
JDK就是Java Development Kit，这是面向开发者使用的SDK，它提供了Java的开发环境和运行环境。SDK是Software Development Kit 一般指软件开发包，可以包括函数库、编译程序等。
JRE是Java Runtime Enviroment是指Java的运行环境，是面向Java程序的使用者，而不是开发者。
所以，这里我们安装一个JRE即可，在UBUNTU下：

    sudo apt-get install default-jre

###安装Elasticsearch（通过APT的方式安装）：
下载安装公匙：

    wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

将源添加入`/etc/apt/sources.list.d/elasticsearch-{branch}.list`:

    echo "deb http://packages.elastic.co/elasticsearch/1.6/debian stable main" | sudo tee -a /etc/apt/sources.list.d/elasticsearch-1.6.list
    
执行apt-get update然后新添加的源就可以用了，接着进行安装：

    sudo apt-get update && sudo apt-get install elasticsearch

###运行Elasticsearch：

执行以下命令可以在前台启动：

    ./bin/elasticsearch

如果想在后台以守护进程模式运行，添加-d参数。

打开另外一个终端进行测试：

    curl 'http://localhost:9200/?pretty'

会得到以下信息：
```json
{
  "status" : 200,
  "name" : "Ghost Rider 2099",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "1.5.2",
    "build_hash" : "62ff9868b4c8a0c45860bebb259e21980778ab1c",
    "build_timestamp" : "2015-04-27T09:21:06Z",
    "build_snapshot" : false,
    "lucene_version" : "4.10.4"
  },
  "tagline" : "You Know, for Search"
}
```
安装成功！
##Elasticsearch基础概念
###Near Realtime(NRT)
ES是一个近乎实时搜索平台，意思就是从你开始索引一个文件到它变成可以搜索的时间，它的延迟很低（正常情况下一秒）。

###Cluster
一个集群(cluster)，即将一个或者多个节点（服务）集合起来，上面有你的所有数据，并且从所有的节点上都可以进行索引(feddrated indexing)和搜索。一个集群默认情况下是以"elasticsearch"命名(它必须是唯一的)。这个名字非常重要，因为一个节点只有通过一个集群的名字才可以加入这个集群。在生产环境中修改集群的名字是一个很好的practice，但是在测试或者开发中，使用默认的就好了。
可以通过修改config/目录下的elasticsearch.yml文件中cluster.name的名字，然后重启elasticsearch。直接使用ctrl-c快捷键即可终止。

###Node
一个节点(Node)是一个独立的服务，它是你集群中的一部分，存储你的数据，也参与集群的索引和搜索服务。它是一个运行着Elasticsearch的实例。

##开始第一步——magacorp公司需要建立一个员工目录
通过一个简单的例子，可以大根了解Elasticsearch的工作情况。
这个员工目录有以下需求：

 - 数据能够包含多个值的标签、数字和纯文本。
 - 检索任何员工的所有信息。
 - 支持结构化搜索，例如查找30岁以上的员工。
 - 支持简单的全文搜索和更复杂的短语搜索。
 - 高亮搜索结果中的关键字。
 - 能够利用图表管理分析这些数据。

###索引员工文档
我们首先要做的是存储员工数据，每个文档代表一个员工。在Elasticsearch中存储数据的行为就叫作索引，不过在索引之前，我们需要明确数据应该存储在哪里。
在Elasticsearch中，文档归属一种类型，而这些类型存在于索引中，我们可以这样类比传统关系型数据库：

    Relational DB -> Databases -> Tables -> Rows -> Columes
    ElasticSearch -> Indexes   -> Types  -> Documents -> Fields
    
即，Elasticsearch集群可以包含多个索引(indices)(数据库)，每一个索引可以包含多个类型(types)(表)，每一个类型包含多个文档(documents)(行)，然后每个文档包含多个字段(fields)(列)。

> “索引”含义的区别 在Elasticsearch中，索引(index)有着不同的含义：
> 
>  - 索引(名词)如上文所述，一个索引就像是RDB中的数据库，它是相关文档存储的地方，index的复数是indices或者indexes。
>  - 索引(动词)，“索引一个文档”，表示把一个文档存储到索引(名词)里，以便它可以被检索或者查询，这可以类比到sql中的insert关键字。
>  - 倒排索引，传统数据为特定列增加一个索引，例如B-Tree索引来加速检索，在Elasticsearch和Lucene使用一种叫倒排索引(inverted
> index)的数据结构来达到相同目的。

现在，我们要进行如下操作：

 - 为每个员工的文档(document)建立索引，每个文档包含了相应员工的所有信息。
 - 每个文档的类型为employee。
 - employee类型归属于索引megacorp。
 - meagcorp索引存储在Elasticsearch集群中。

这些步骤可以通过一个命令执行完成：
```bash
curl -XPUT 'localhost:9200/megacorp/employee/1' -d '
{
"first_name":"john",
"last_name":"Smith",
"age":25,
"about":"I love to go rock climbing",
"interests":["sports","music"]
}'
```
其中，path:/megacorp/employee/1包含三部分信息：
|名字|说明|
|:---|:---|
|megacorp|索引名|
|employee|类型名|
|1|这个员工的ID|

请求实体(json文档)，包含了这个员工的所有信息。
然后再插入两组数据（不记了，下面搜索到的就是）。
接下来，就可以这样进行搜索：

    curl -XGET 'localhost:9200/megacorp/employee/1'
    
响应的内容包含一些元信息，John Smith的原始Json文档包含在_source字段中。
```json
{
"_index":"megacorp",
"_type":"employee",
"_id":"1",
"_version":1,
"found":true,
"_source":{"first_name":"john",
           "last_name":"Smith",
           "age":25,
           "about":"I love to go rock climbing",
           "interests":["sports","music"]
           }
}
```
我们通过HTTP方法GET来检索文档，同样的，我们可以使用DELETE方法删除文档，使用HEAD方法检查某文档是否存在。如果想更新已存在的文档，只需要再PUT一次。

###简单搜索
我们尝试一个最简单的搜索全部员工的请求：

    curl -XGET 'localhost:9200/megacorp/employee/_search'
默认情况下搜索会返回前10个结果：
```json
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 3,
        "max_score": 1,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 1,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 1,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 35,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "3",
                "_score": 1,
                "_source": {
                    "first_name": "Douglas",
                    "last_name": "Fir",
                    "age": 32,
                    "about": "I like to build cabinets",
                    "interests": [
                        "forestry"
                    ]
                }
            }
        ]
    }
}
```

接下来，搜索姓氏中包含“Smith”的员工，要做到这点，在命令行中使用轻量级的搜索方法。这种方法常被称作查询字符串(query string)搜索，就像传递URL参数一样去传递查询语句：

    curl -XGET 'localhost:9200/megacorp/employee/_search?q=last_name:Smith'
    
即将查询语句传递给参数q=，这样就得到了：
```json
{
    "took": 97,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 0.30685282,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 0.30685282,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 0.30685282,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 35,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            }
        ]
    }
}
```

###使用DSL语句查询
Elasticsearch提供另外一种查询方法叫作DSL查询(Query DSL)，它允许你构建更加复杂、强大的查询。
DSL（Domain Specific Language特定领域语言）以JSON请求体的形式出现，可以这样表示之前关于"Smith"的查询：
 

    curl -XGET 'localhost:9200/megacorp/employee/_search' -d     '{
        "query":{
            "match":{
                "last_name":"Smith"
            }
        }
    }'

###更复杂的搜索
###全文搜索
一种传统数据库很难实现的搜索功能。
我们将会搜索所有喜欢"rock climbling"的员工：

    curl -XGET 'localhost:9200/megacorp/employee/_search' -d     '{
        "query":{
            "match":{
                "about":"rock climbing"
            }
        }
    }'

这里使用之前使用的match查询，从about字段中搜索"rock climbing"，得到了两个匹配文档：
```json
{
    "took": 52,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 0.16273327,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 0.16273327,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 0.016878016,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 35,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            }
        ]
    }
}
```

默认情况下，Elasticsearch根据结果相关性评分来对结果进行排序，所谓的“结果相关性评分”就是文档与查询条件的匹配程度。很显然，排名第一的John Smith的about字段明确写到了"rock climbing"。高于第二位员工的评分。

###短语搜索
有时候我们想要确切的匹配若干个单词或者短语(phrases)。例如我们想要查询同时包含"rock"和"climbing"(并且是相邻)的员工记录。
要做到这个，将match发放成match_phrase查询即可：

    curl -XGET 'localhost:9200/megacorp/employee/_search' -d     '{
        "query":{
            "match_phrase":{
                "about":"rock climbing"
            }
        }
    }'
    
毫无疑问，返回John Smith的文档：
```json
{
    "took": 278,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 1,
        "max_score": 0.23013961,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 0.23013961,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            }
        ]
    }
}
```

###高亮我们的搜索
只需要在之前的语句上增加highlight参数：

    curl -XGET 'localhost:9200/megacorp/employee/_search' -d     '{
        "query":{
            "match_phrase":{
                "about":"rock climbing"
            }
        },
        "highlight":{
            "fields":{
                "about":{}
            }
        }
    }'

这个返回结果中会有一个新的部分叫作highlight，这里包含了来自about字段中的文本，并且用`<em></em>`来标识匹配的单词。
```json
{
    "took": 107,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 1,
        "max_score": 0.23013961,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 0.23013961,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                },
                "highlight": {
                    "about": [
                        "I love to go <em>rock</em> <em>climbing</em>"
                    ]
                }
            }
        ]
    }
}
```
###分析
最后，还有一个需求：允许管理者在职员目录中进行一些分析。
Elasticsearch有一个功能叫作聚合(aggregations)，它允许你在数据上生成复杂的分析统计。它很像SQL中的GROUP BY。
举个例子，让我们找到所有职员中最大的共同点（兴趣爱好）是什么：

    curl -XGET 'localhost:9200/megacorp/employee/_search' -d     '{
        "aggs":{
            "all_interests":{
                "terms":{"field":"interests"}
            }
        }
    }'

暂时先忽略语法看结果：
```json
{
    "took": 155,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 3,
        "max_score": 1,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 1,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 1,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 35,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "3",
                "_score": 1,
                "_source": {
                    "first_name": "Douglas",
                    "last_name": "Fir",
                    "age": 32,
                    "about": "I like to build cabinets",
                    "interests": [
                        "forestry"
                    ]
                }
            }
        ]
    },
    "aggregations": {
        "all_interests": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": "music",
                    "doc_count": 2
                },
                {
                    "key": "forestry",
                    "doc_count": 1
                },
                {
                    "key": "sports",
                    "doc_count": 1
                }
            ]
        }
    }
}
```
我们可以看到两个职员对音乐有兴趣，一个喜欢林学，一个喜欢运动。这些数据并没有被预先计算好，它们是实时的从匹配查询语句的文档中动态计算生成的。如果我们想知道所有姓"Smith"的人最大共同点是什么，只要增加合适的语句即可：

    curl -XGET 'localhost:9200/megacorp/employee/_search' -d     '{
        "query":{
            "match":{
                "last_name":"Smith"
            }
        },
        "aggs":{
            "all_interests":{
                "terms":{"field":"interests"}
            }
        }
    }'
    
all_interests聚合已经变成只包含和查询语句相匹配的文档了：
```json
{
    "took": 3,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 0.30685282,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 0.30685282,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 0.30685282,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 35,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            }
        ]
    },
    "aggregations": {
        "all_interests": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": "music",
                    "doc_count": 2
                },
                {
                    "key": "sports",
                    "doc_count": 1
                }
            ]
        }
    }
}
```

聚合也允许分级汇总，例如，让我们统计每种兴趣下的职员的平均年龄：

    curl -XGET 'localhost:9200/megacorp/employee/_search' -d     '{
        "aggs":{
            "all_interests":{
                "terms":{"field":"interests"},
                "aggs":{
                    "avg_age":{
                        "avg":{"field":"age"}
                    }
                }
            }
        }
    }'
虽然返回的结果有点复杂，但是很容易理解：
```json
{
    "took": 62,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 3,
        "max_score": 1,
        "hits": [
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "1",
                "_score": 1,
                "_source": {
                    "first_name": "john",
                    "last_name": "Smith",
                    "age": 25,
                    "about": "I love to go rock climbing",
                    "interests": [
                        "sports",
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "2",
                "_score": 1,
                "_source": {
                    "first_name": "Jane",
                    "last_name": "Smith",
                    "age": 35,
                    "about": "I like to collect rock albums",
                    "interests": [
                        "music"
                    ]
                }
            },
            {
                "_index": "megacorp",
                "_type": "employee",
                "_id": "3",
                "_score": 1,
                "_source": {
                    "first_name": "Douglas",
                    "last_name": "Fir",
                    "age": 32,
                    "about": "I like to build cabinets",
                    "interests": [
                        "forestry"
                    ]
                }
            }
        ]
    },
    "aggregations": {
        "all_interests": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": "music",
                    "doc_count": 2,
                    "avg_age": {
                        "value": 30
                    }
                },
                {
                    "key": "forestry",
                    "doc_count": 1,
                    "avg_age": {
                        "value": 32
                    }
                },
                {
                    "key": "sports",
                    "doc_count": 1,
                    "avg_age": {
                        "value": 25
                    }
                }
            ]
        }
    }
}
```
