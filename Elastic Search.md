



# Elastic Search🌸

The Elastic Stack，包括 ElasticSearch、Kibana、Beats 和 Logstash（也称为 ELK Stack），能够安全可靠地获取任何来源、任何格式的数据，然后实时地对数据进行搜索、分析和可视化。

ElaticSearch，简称为 ES， ES 是一个开源的高扩展的分布式全文搜索引擎，是整个 Elastic Stack 技术栈的核心。它可以近乎实时的存储、检索数据；本身扩展性很好，可以扩展到上百台服务器，处理 PB 级别的数据。

ElasticSearch 基于 Lucene 的搜索服务器，通过 RESTful web接口提供了一个分布式多用户能力的全文搜索引擎



## 参考文档

👉  [Elastic Search 官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/index.html)



## 安装

### archive

Linux 以及 Mac OS 通用

```sh
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.13.0-linux-x86_64.tar.gz
tar -zxvf elasticsearch-7.13.0-linux-x86_64.tar.gz
./bin/elasticsearch
```



### Homebrew

```sh
brew tap elastic/tap
brew install elastic/tap/elasticsearch-full
```

https://www.elastic.co/guide/en/elasticsearch/reference/7.12/brew.html#brew-layout



## 环境变量

ElasticSearch 从7.0开始默认安装了 Java 运行环境，以便在没有安装 Java 的机器上运行。如果配置了环境变量`JAVA_HOME` ，则ElasticSearch 启动时会使用 `JAVA_HOME` 作为路径，否则使用使用根目录下 jdk 目录为 Java 路径。

如果  `JAVA_HOME`  的 JDK 版本小于 JDK 11 ，则会报错，可以配置环境变量让 ElasticSearch 使用其自带环境。

```sh
# elastic search
export ES_HOME=/Users/yorua/opt/elasticsearch-7.12.1/
export ES_JAVA_HOME=$ES_HOME/jdk.app/Contents/Home
```



## 运行

直接从命令行运行：

```sh
cd elasticsearch-7.12.1
./bin/elasticsearch
```



以守护进程的方式运行：

```sh
# -d 表示以 daemon 守护进程运行，-p 指定端口
./bin/elasticsearch -d -p pid

# 关闭进程
pkill -F pid
```



## 配置

配置文件位于 `$ES_HOME/config` 目录下：

- `elasticsearch.yml` ：ElasticSearch 相关配置
- `jvm.options` ：Elasticsearch JVM 配置
- `log4j2.properties`：Elasticsearch 日志配置





## ES相关概念

| Elasticsearch     | MySQL                      |
| ----------------- | -------------------------- |
| 索引库 Indices    | Database 数据库            |
| 类型 Type（弃用） | Table 数据表               |
| 文档 Document     | Row 行                     |
| 域字段 Field      | Columns 列                 |
| 映射配置 Mappings | 每个列的约束（类型、长度） |



## 倒排索引

倒排索引（Inverted index）也叫反向索引，有反向索引就存在正向索引。通俗的说，正向索引是通过 key 找 value，反向索引则是通过 value 找 key。



### Term

Term（单词）：一段文本经过分析器分析以后就会输出一串单词，这一个一个的就叫做 Term



### Term Dictionary

Term Dictionary（单词字典）：ElasticSearch 为了能快速找到某个 Term，将所有的 Term 排序，二分法查找 Term，这就是 Term Dictionary。

Term Dictionary 采用了分块技术，将含有共同前缀的 Term 存储在多个 Block 之中。



### Term Index

如果 Term 太多，Term Dictionary 也会很大，全部放在内存不现实，只能部分存储到磁盘上。但是磁盘寻道次数太多也会严重影响查找效率，为了减少磁盘寻道次数来提高查询性能，于是设置 Term Index 作为 Term 的索引。

Term Index 是一个 B+Tree，但它的节点并不是 Term，而是 Term 的一些前缀，Term Index 中的节点存储了 Term Dictionary 的 Block 的地址。

<img src="http://store.secretcamp.cn/uPic/image-20210602162946554202106021629461622622586Ar9iT9Ar9iT9.png" alt="image-20210602162946554" style="zoom:50%;" />

Term Index 不需要存下所有的 Term，而仅仅是它们的一些前缀与 Term Dictionary 的 Block 之间的映射关系，再结合相关的压缩技术，可以使 Term Index 缓存到内存中。从 Term Index 查到对应 Term Dictionary 的 Block 地址之后，再去磁盘上找Term，大大减少了磁盘随机读的次数。

<img src="http://store.secretcamp.cn/uPic/image-20210602161556716202106021615571622621757H6ZDMeH6ZDMe.png" alt="image-20210602161556716" style="zoom:50%;" />



### Posting List

Posting List（倒排列表）：倒排列表记录了出现过某个单词的所有文档的文档列表及单词在该文档中出现的位置信息，每条记录称为一个倒排项（Posting）。根据倒排列表，即可获知哪些文档包含某个单词。



### 与MySQL索引的对比

Elasticsearch/Lucene  的检索可以比 MySQL 更快，因为 MySQL 只有 Term Dictionary 一层，是以 B+Tree 排序的方式存储在磁盘上的。检索一个 Term 需要若干次的随机读的磁盘操作。

而 Lucene 在 Term Dictionary 的基础上添加了 Term Index 来加速检索，Term Index 以树的形式缓存在内存中。从 Term Index 查到对应的 Term Dictionary 的 Block 位置之后，再去磁盘上找 Term，大大减少了磁盘的 Random Access 次数。

Term Index 在内存中是以 FST（Finite State Transducers）的形式保存的，其特点是非常节省内存。Term Dictionary 在磁盘上是以分 Block 的方式保存的，一个 Block 内部利用公共前缀压缩，比如都是 Ab 开头的单词就可以把 Ab 省去。这样 Term Dictionary 可以比 B-Tree 更节约磁盘空间。





### 示例

存在以下数据：

| id   | name | gender | age  | address      |
| ---- | ---- | ------ | ---- | ------------ |
| 1    | 张三 | 男     | 21   | 北京市朝阳区 |
| 2    | 李四 | 女     | 22   | 上海市徐汇区 |
| 3    | 王五 | 男     | 23   | 上海市虹口区 |
| 4    | 赵六 | 男     | 23   | 上海市虹口区 |



ElasticSearch 分别为每个字段都建立了一个倒排索引，建立的索引大致如下：

| Term（name） | Posting List |
| ------------ | ------------ |
| 张三         | [1]          |
| 李四         | [2]          |
| 王五         | [3]          |
| 赵六         | [4]          |

| Term（gender） | Posting List |
| -------------- | ------------ |
| 男             | [1, 3, 4]    |
| 女             | [2]          |

| Term（age） | Posting List |
| ----------- | ------------ |
| 21          | [1]          |
| 22          | [2]          |
| 23          | [3, 4]       |

| Term（address） | Posting List |
| --------------- | ------------ |
| 北京市          | [1]          |
| 上海市          | [2, 3, 4]    |
| 朝阳区          | [1]          |
| 徐汇区          | [2]          |
| 虹口区          | [3, 4]       |



以 address 为例，通过 value 去寻找 key 就很高效，例如查询所有 “上海市” 的记录，可以直接找到 [2, 3, 4]



# Kibana🌸

Kibana 是一个开源的分析与可视化平台，用于和 ElasticSearch 一起使用。可以用 Kibana 搜索、查看存放在 ElasticSearch 中的数据。





## 安装

### archive

Linux 可以直接使用，但 Mac OS 需要对目录或者安装包进行安全性验证

```
xattr -d -r com.apple.quarantine <archive-or-directory>
```



## 运行

默认端口：5601

```sh
cd kibana-7.12.1-darwin-x86_64
./bin/kibana
```



## devtools

```
command + i 自动缩进

command + enter 提交请求

down 打开自动补全菜单

enter 或 tab 选中项自动补全

esc 关闭补全菜单
```





# Logstash🌸

Logstash 是 Elastic Stack 的中央数据流引擎，用于收集、丰富和统一所有数据，而不管格式或模式。







# 查询参数🌸

对于查询参数，如果不跟值，则表示 true ，例如 v 等价于 v=true

## help

help：显示可能输出的行，help 无法和其他参数同时使用

```
GET /_cat/master?help
```



## v

v（verbose）：是否显示列标题

```
GET /_cat/master?v
```



## [bytes](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/common-options.html#byte-units)

bytes：大小的表示形式

- `bytes=b`  表示用字节表示

```
GET /_cat/indices?v&bytes=b
```



## [time](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/common-options.html#size-units)

time：时间表示形式



[size](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/common-options.html#size-units)：尺寸表示形式



## s

s (sort)：排列

- `s=column:acs`

- `s=column:desc`

```
GET /_cat/indices?v&s=store.size:desc
```



## format

format：输出格式

- `format=json`
- `format=yaml`

```
GET /_cat/master?v?format=json
```



## pretty

pretty：美化输出



## h

h（header）：选择要输出的行

- `h=ip,port,name,load*` ，`load*` 表示所有以 load 开头的字段

```
GET /_cat/master?h=id,host,ip,node
```





# QuickStart🌸

## [_cat](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/cat.html#cat)

### 查看集群信息



### 查看健康状况

```
GET /_cat/health
```



### 查看主节点

```
GET /_cat/master?v
```



### 查看所有索引

```
GET /_cat/indices
```





## 创建索引

```
PUT /index
```





## 创建文档

1. PUT方法，必须要指定 id

   ```sh
   PUT /mobile/_doc/{id}
   {
       "brand": "苹果",
       "model": "iphone 12",
       "price": 6799.00,
       "storage": 128
   }
   ```

   ```sh
   {
     "_index" : "mobile",
     "_type" : "_doc",
     "_id" : "1",           # 保存数据的id
     "_version" : 2,        # 数据的版本，即修改过的次数
     "result" : "created",  # 本次操作的结果，创建是"created"，更新是"updated"
     "_shards" : {       # 分片信息
       "total" : 2,
       "successful" : 1,
       "failed" : 0
     },
     "_seq_no" : 7,
     "_primary_term" : 1
   }
   ```

   



2. POST 方法，可以不指定id，如果指定，则效果和 PUT 相同

   ```
   POST /index/_doc/{id}
   ```
   
   如果不指定 id，系统会自动分配唯一标识，例如 `_id: EJeP3kB8kHw5lGC9Bs9`，每次调用都是创建，都会分配一个不同的 id
   
   ```
   POST /index/_doc
   ```
   
   



## 查询文档

```
GET /index/_doc/{id}
```



```sh
{
  "_index" : "mobile",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 0,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "brand" : "苹果",
    "model" : "iphone 12",
    "price" : 6799.0,
    "storage" : 128
  }
}
```



## 更新文档

1. 局部更新

   使用请求路径中带 `_update`，请求体用 `doc` 包裹

   这种方式下，更新操作会对比原来数据，如果与原来一样，则什么都不做，version 和 no_seq 都不会增加 

   ```sh
   POST /index/_update/{id}
   {
     "doc": {
       "brand": "苹果",
       "model": "iphone 12",
       "price": 6799,
       "storage": 128
     }
   }
   ```

   

2. 全量更新

   类似于创建文档，不会检查原数据是否与更新数据相同，POST  和 PUT 方法都可以

   ```sh
   POST /index/_doc/{id}
   {
       "brand": "苹果",
       "model": "iphone 12",
       "price": 6799,
       "storage": 128
   }
   ```

   

## 删除文档

ElasticSearch 中有删除文档和删除索引，但没有删除类型操作

1. 删除文档，需要指定 id

   ```
   DELETE /index/_doc/{id}
   ```

   

2. 删除索引

   ```
   DELETE /index
   ```

   





## 批量操作

`_bulk` 可以批量进行数据操作

在 kibana 的 devtools 中执行

```sh
POST /bank/_bulk
{"index":{"_id":"1"}}
{"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
{"index":{"_id":"6"}}
{"account_number":6,"balance":5686,"firstname":"Hattie","lastname":"Bond","age":36,"gender":"M","address":"671 Bristol Street","employer":"Netagy","email":"hattiebond@netagy.com","city":"Dante","state":"TN"}
{"index":{"_id":"13"}}
{"account_number":13,"balance":32838,"firstname":"Nanette","lastname":"Bates","age":28,"gender":"F","address":"789 Madison Street","employer":"Quility","email":"nanettebates@quility.com","city":"Nogal","state":"VA"}
```



# [搜索方法](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/search-your-data.html)🌸

## 过滤搜索结果



## 高亮



## 字段折叠

`collapse` 允许根据字段值折叠搜索结果。通过按折叠键选择顶部排序文档来完成折叠。

也可以使用 `inner_hits` 选项扩展每个折叠的顶部命中

👉  [参考博客](https://blog.csdn.net/ctwy291314/article/details/82759215)







# [QueryDSL](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/query-dsl.html)🌸

查询表达式（Query DSL，Query Domain Specific Language）是基于 json，用于定义查询条件的表达式。



## 查询字句类型

1. 叶查询字句

   叶查询字句寻找一个特定的属性以及特定的值，例如 match、term、range

2. 复合查询字句

   复合查询字句组合其他的叶查询字句以及复合查询字句，用于描述复杂查询条件，例如 bool；还可以改变查询的行为，例如 constant_score 



## 搜索机制

### relevance scores

相关性得分（relevance scores）用于衡量文档与搜索结构的匹配程度

对于查询语句，会返回 `_score` 字段表示分数



### query context

查询上下文（query context）用于回答 "查询语句如何去匹配相关文档" 的问题，除了确定文档是否被匹配之外，查询子句还计算相关性得分，并用 `_score` 返回。



### filter context

过滤器上下文（filter context）用于回答 "文档是否应该被匹配" 这个问题，只有 yes or no 两个答案。

bool 查询中的 filter 以及 must_not 会作用于 filter context



## 全部匹配

### match_all

最简单的检索，`match_all` 检索出的所有文档的 `_score` 都是 1.0

```sh
GET _search
{
  "query": {
    "match_all": {}
  }
}
```

也可以用 boost 参数修改分数

```sh
GET _search
{
  "query": {
    "match_all": {
       "boost": 1.2
    }
  }
}
```

### match_none

与 `match_all` 相对的是 `match_none`，表示不匹配文档



## 全文检索

### intevals

`intevals` 根据匹配项的接近程度和顺序返回文档

最高级参数是 `<field>` ，即想要搜索的字段

`<field>` 的合法的二级参数有：

- match

- prefix

- wildcard
- fuzzy

- all_of：返回符合所有子规则的匹配项

- any_of：返回符合任何子规则的匹配项



以下匹配规则可与匹配到 "my favourite food is cold porridge"，不可以匹配到 "when it's cold my favourite food is porridge"，因为"cold" 和 "porridge" 出现在了 "my favourite food" 两边，

```sh
POST _search
{
  "query": {
    "intervals": {
      "my_text": {   # field
        "all_of": {  # 二级参数
          "ordered": true,  # 二级参数的参数
          "intervals": [    # 二级参数的参数，一个intervals数组，开始套娃
            {
              "match": {    # intervals的参数
                "query": "my favorite food",
                "max_gaps": 0,
                "ordered": true
              }
            },
            {
              "any_of": {   # intervals的参数
                "intervals": [
                  {
                    "match": {
                      "query": "hot water"
                    }
                  },
                  {
                    "match": {
                      "query": "cold porridge"
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    }
  }
}
```



### match

match 根据提供的字符串、数字、布尔值、日期等返回文档

最高级参数是 `<field>` ，即想要搜索的字段

`<field>` 的合法参数如下：

- query
- analyzer
- auto_generate_synonyms_phrase_query
- fuzziness
- max_expansions
- prefix_length
- fuzzy_transpositions
- fuzzy_rewrite
- lenient
- operator
- minimum_should_match
- zero_terms_query



### match_phrase

`match_phrase`  表示匹配短语









## 复合查询

### boolean

boolean 通过一个或多个布尔字句匹配文档，每个字句都带有一个类型，有以下四种类型：

- must：表示与，查询符合全部条件的文档，会贡献分数
- filter：和 must 类似，但是 filter 不会贡献分数
- should：表示或，查询符合一个或多个条件的文档，会贡献分数
- must_not：表示取反，查询一定不符合条件的文档，must_not 不会贡献分数



### boosting

匹配含有 `positive` 相关信息的文档，但又减少匹配到 `negative` 的文档的得分

Top 参数：

- `positive`
- `negative`
- `negative_boost`：减分权值

```sh
GET /_search
{
  "query": {
    "boosting": {
      "positive": {
        "term": {
          "text": "apple"
        }
      },
      "negative": {
        "term": {
          "text": "pie tart fruit crumble tree"
        }
      },
      "negative_boost": 0.5
    }
  }
}
```



### constant score query



## 精确查询

### term

[term](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-term-query.html) 建议用于检索精确字段，例如 id、价格、用户名，避免使用 term 检索 text 字段

```sh
POST /bank/_search
{
  "query": {  
    "term": {
      "age": {  // field
        "value": 22,
        "boost": 1.0
      }
    }
  }
}
```



### terms

```sh
GET /bank/_search
{
  "query": {
    "terms": {
      "age": [ 20 , 21 ], 
      "boost": 1.0
    }
  }
}
```







# [Aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/search-aggregations.html)🌸

## 桶聚合

桶聚合（Bucket Aggregations）

### terms

```sh
GET /bank/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "ageAggs": {
      "terms": {
        "field": "age",
        "size": 100
      }
    }
  },
  "size": 0
}
```





## 指标聚合

指标聚合（Metrics Aggregations）

### [avg](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/search-aggregations-metrics-avg-aggregation.html#search-aggregations-metrics-avg-aggregation)

```sh
GET /bank/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {
    "ageAvg": {
      "avg": {
        "field": "age"
        "missing" : 20  // 如果有缺失值，按照 20 处理，默认是不处理
      }
    }
  },
  "size": 0
}
```



## 管道聚合

管道聚合（Pipeline Aggregations）





## 子聚合

每一个查询只能有一个 aggs ，每一个 aggs 也只能有一个 aggs

```sh
GET /bank/_search
{
  "query": {
    "match_all": {}
  },
  "aggs": {  // 第一层聚合 
    "ageAggs": {  // 对各年龄段进行聚合
      "terms": {
        "field": "age",
        "size": 100
      },
      "aggs": {  // 第二层聚合 
        "genderAggs": {  // 在各年龄段聚合的基础上，对性别进行聚合
          "terms": {
            "field": "gender.keyword",
            "size": 10
          },
          "aggs": {  // 第三层聚合
            "genderAvg": {  // 在对各年龄段性别进行聚合的基础上，对余额进行集合
              "avg": {
                "field": "balance"
              }
            }
          }
        },
        "balanceAvg": {  // 在各年龄段聚合的基础上，对余额进行聚合
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  },
  "size": 0
}
```





# [Search APIs](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/search.html)🌸

## [Search](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/search-search.html)

进行一个搜索命令并返回搜索结果

```
GET /<target>/_search
GET /_search
POST /<target>/_search
POST /_search
```



### 查询参数

- from：分页的起始点
- size：返回的文档数量
- q： lucene 查询语法
- sort：排序，模式为 `<field>:<direction>` 
- _source：决定 `hit._source` 中返回的字段
  - true：返回
  - false：不返回
  - string or string[]：决定哪些字段返回，
- timeout：指定超时时间





### 请求体

- query：使用 QueryDSL 定义的请求体
- from：分页的起始点
- size：返回的文档数量
- _source：决定 `hit._source` 中返回的字段
  - true：返回
  - false：不返回
  - 通配符表达式：决定哪些字段需要返回
  - object：string or string[]
    - excludes
    - includes



### 响应体

- took：查询花费的毫秒数
- timed_out
  - true：超时，返回结果可能是部分或为空
  - false：未超时
- _shards：请求的分片数
  - total
  - successful
  - skipped
  - failed
- hits：包含返回的文档对象和元数据
  - total：返回文档的数量的元数据
    - value：返回文档的数量
    - relation：表明返回文档的数量是精确的还是有下线的（`eq` 、 `gte`）
  - max_score：返回文档中的最大得分
  - hits：返回文档对象的数组
    - _index：文档所属的索引
    - _type：文档所属的类型（过时，es 8.0 将被正式移除）
    - _id：文档的唯一标识
    - _score：相关性得分
    - _source：json返回值
    - fields（optional）：只有请求体中设置了以下参数才会返回
      - docvalue_fields
      - script_fields
      - stored_fields



### 示例



```sh
GET /_search  # 直接梭哈
GET /index/_search  # 检索index中所有数据

GET /bank/_search?from=40&size=20
{
  "query": {
    "term": {
      "user.id": "kimchy"
    }
  }
}
```





## [Mutil Search](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/search-multi-search.html)

单次  API  中执行多次搜索请求

```
GET /<target>/_msearch
```



## [async search](https://www.elastic.co/guide/en/elasticsearch/reference/7.12/async-search.html)







# Mapping🌸

## 查询索引

```sh
GET /bank/_mapping
```



## 创建映射

1. PUT 方法

   ```sh
   PUT /newbank
   {
     "mappings": {
       "properties": {
         "account_number": {
           "type": "long"
         },
         "address": {
           "type": "keyword"
         },
         "age": {
           "type": "long"
         },
         "balance": {
           "type": "long"
         },
         "city": {
           "type": "keyword"
         },
         "email": {
           "type": "keyword"
         },
         "employer": {
           "type": "text",
           "fields": {
             "keyword": {
               "type": "keyword",
               "ignore_above": 256
             }
           }
         },
         "firstname": {
           "type": "keyword"
         },
         "gender": {
           "type": "keyword"
         },
         "lastname": {
           "type": "keyword"
         },
         "state": {
           "type": "keyword"
         }
       }
     }
   }
   ```

   

2. POST  方法

   ```sh
   PUT /bank
   
   POST  /bank/_mapping
   "properties": {
     "account_number": {
       "type": "long"
     },
     "address": {
       "type": "keyword"
     },
     "age": {
       "type": "long"
     },
     "balance": {
       "type": "long"
     },
     "city": {
       "type": "keyword"
     },
     "email": {
       "type": "keyword"
     },
     "employer": {
       "type": "text",
       "fields": {
         "keyword": {
           "type": "keyword",
           "ignore_above": 256
         }
       }
     },
     "firstname": {
       "type": "keyword"
     },
     "gender": {
       "type": "keyword"
     },
     "lastname": {
       "type": "keyword"
     },
     "state": {
       "type": "keyword"
     }
   }
   ```

   



## 添加映射



## 修改映射



## 数据迁移

如果是旧版本数据，还可以在 source 中添加 type ，指定响应的类型

```sh
POST _reindex
{
  "source": {
    "index": "bank"
  },
  "dest": {
    "index": "newbank"
  }
}
```





# 临时归档🌸

## Type的弃用

```
#! [types removal] Specifying types in document index requests is deprecated, use the typeless endpoints instead (/{index}/_doc/{id}, /{index}/_doc, or /{index}/_create/{id}).
```



## 测试数据

👉  [点击下载](https://download.elastic.co/demos/kibana/gettingstarted/accounts.zip)





# Java-ElasticSearch🌸

## 参考文档

👉  [Transport Client （Deprecated）](https://www.elastic.co/guide/en/elasticsearch/client/java-api/7.13/index.html)

👉  [Java Rest Client](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/index.html)



## Java-low-level-client

### 引入依赖

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-client</artifactId>
    <version>7.14.0</version>
</dependency>
```







## Java-high-level-client

### 引入依赖

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.14.0</version>
</dependency>
```
