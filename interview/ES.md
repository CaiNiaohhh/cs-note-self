Elasticsearch入门教程
https://segmentfault.com/a/1190000038895410

hinzzz
发布于 2021-01-08
一、什么是Elasticsearch?
官网

Elasticsearch 是一个分布式的开源搜索和分析引擎，适用于所有类型的数据，包括文本、数字、地理空间、结构化和非结构化数据。Elasticsearch 在 Apache Lucene 的基础上开发而成，由 Elasticsearch N.V.（即现在的 Elastic）于 2010 年首次发布。Elasticsearch 以其简单的 REST 风格 API、分布式特性、速度和可扩展性而闻名，是 Elastic Stack 的核心组件；Elastic Stack 是适用于数据采集、充实、存储、分析和可视化的一组开源工具。人们通常将 Elastic Stack 称为 ELK Stack（代指 Elasticsearch、Logstash 和 Kibana），目前 Elastic Stack 包括一系列丰富的轻量型数据采集代理，这些代理统称为 Beats，可用来向 Elasticsearch 发送数据
着重功能就是用来做数据的检索和分析

应用程序搜索
网站搜索
企业搜索
日志处理和分析
基础设施指标和容器监测
应用程序性能监测
地理空间数据分析和可视化
安全分析
业务分析
二、mysql也能实现Elasticsearch的功能 为什么还需要Elasticsearch
mysql当然也能实现数据的搜索和分析 例如求年龄的平均值 avg 分组group by 等等 但是我们说术业有专攻，

而mysql专攻于持久化的存储与管理 ，也就是crud 。如果真的使用mysql做海量数据的检索和分析，Elasticsearch更在行，能在秒级给我们响应我们感兴趣的数据，而mysql单表达到百万数据，我们需要进行一些检索和查询都是一些比较慢的操作，比较浪费性能。例如在一些电商系统里面，需要对商品的不同属性按照不同的关键字来检索商品，我们如果用mysql来做，mysql肯定承受不了那么大的压力，而Elasticsearch可以帮我们做到这些。

三、底层
Elastisearch的底层是开元库Lucene。但是你没法直接调用lucene,必须自己写代码去调用他的接口。Elasticsearch是lucene的封装，提供了REST API的操作接口 开箱即用。

[官方文档] https://www.elastic.co/guide/...

四、基本概念
1、Index（索引）
动词，相当于mysql中的insert

名词，相当于mysql的Database

2、Type（类型）7.0废弃
在index（索引）中，可以定义一个或多个类型，类似于mysql中的table，同一种类型的数据放在一起

3、 Document（文档）
保存在某个索引（Index）下、某种类型（Type）的一个数据（Document），文档是json格式的数据，Document1相当于mysql中某个table数据

elasticsearch概念

4、倒排索引
分词：将整句拆分为单词

保存的记录

红海行动
探索红海行动
红海特别行动
红海纪录片
特工红海特别探索
词

得分

红海

1 2 3 4 5

行动

1 2 3

探索

2 5

特别

3

纪录片

4

特工

5

检索：

红海特工行动
红海行动
五、安装
注意elasticsearch和kibana的版本最好一致 不然容易出现错误

1、下载

docker pull elasticsearch:7.4.2 #存储和检索数据
docker pull kibana:7.4.2 #可视化检索工具

2、配置

mkdir -p /home/elasticsearch/config
mkdir -p /home/elasticsearch/data
echo "http.host: 0.0.0.0" >/home/elasticsearch/config/elasticsearch.yml
chmod -R 777 /home/elasticsearch/

3、运行

9200：http协议 外部通讯 外部访问

9300：tcp协议 内部通讯 用于集群

docker run --name elasticsearch -p 9200:9200 -p 9300:9300
-e  "discovery.type=single-node"
-e ES_JAVA_OPTS="-Xms64m -Xmx256m"
-v /home/elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
-v /home/elasticsearch/data:/usr/share/elasticsearch/data
-v /home/elasticsearch/plugins:/usr/share/elasticsearch/plugins
-d elasticsearch:7.4.2

开机自启动

docker update elasticsearch --restart=always

4、启动kibana

docker run --name kibana -e ELASTICSEARCH_HOSTS=http://localhost:9200 -p 5601:5601 -d kibana:7.4.2

开机自启动

docker update kibana  --restart=always

5、安装完成 访问http://ip:9200/，http://ip:5601/

版本信息

{
"name" : "244bb2701c87",
"cluster_name" : "elasticsearch",
"cluster_uuid" : "4pa8UebPT4ye9hFNy2vNUA",
"version" : {
"number" : "7.4.2",
"build_flavor" : "default",
"build_type" : "docker",
"build_hash" : "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
"build_date" : "2019-10-28T20:40:44.881551Z",
"build_snapshot" : false,
"lucene_version" : "8.2.0",
"minimum_wire_compatibility_version" : "6.8.0",
"minimum_index_compatibility_version" : "6.0.0-beta1"
},
"tagline" : "You Know, for Search"
}

六、初步检索
1、_cat
GET _cat/nodes 查看所有节点

GET _cat/health 查看es健康状态

Get _cat/master 查看主节点

GET _cat/indices 查看所有索引

参数名

例子

作用

Verbose

GET /_cat/XXX/?v

开启详细输出

Help

GET /_cat/XXX/?help

输出可用的列

Headers

GET /_cat/XXX/?h=column1,column2

指定输出的列

Sort

GET /_cat/XXX/?v&s=column1,column2:desc,column3

指定输出的列进行排序，默认按照升序排序

Format

GET /_cat/XXX?format=json

指定响应返回的数据格式：text（默认）,json,yaml,smile,cbor（通过设置 Accept的HTTP头部的多媒体格式的优先级更高）

七、索引一个文档
1、Post
POST /mall/user/1
{
"name":"hinzzz",
"age":18
}

在mall索引user类型下保存id为1的数据xxx，id可以不指定
id已存在 执行修改操作 id不存在 新增 ，常用于新增
Post文档更新：

POST /mall/user/1/_update
{
"doc":{
"name":"hinzzz1",
"age":18
}
}
​
POST /mall/user/1/
{
"doc":{
"name":"hinzzz1",
"age":18
}
}

1）带_update的时候会比较doc里面的值 和已存在的值是否一样 如果不一样执行修改操作 如果一样 不进行任何操作。重复执行更新操作，数据不会更新
2）不带_update，重复执行更新操作，数据也能更新成功。
应用场景：
对于大并发更新，不带update
对于大并发查询少更新，带update,对比更新，重新计算分配规则
2、Put
PUT /mall/user/1
{
"name":"wlq",
"age":18
}

mall索引user类型下保存或者修改id为1的数据xxx ，id必须制定
id已存在 执行修改操作 id不存在 新增 ，常用于修改操作
PUT /mall/user/1?if_seq_no=5&if_primary_term=1

可带条件 修改seq_no=5 primary_term=1的数据
执行结果

“_index”: “mall” 表明该数据在哪个数据库下；

“_type”: “user” 表明该数据在哪个类型下；

“_id”: “1” 表明被保存数据的id；

“_version”: 1, 被保存数据的版本

“result”: “created” 这里是创建了一条数据，如果重新put一条数据，则该状态会变为updated，并且版本号也会发生变化。

3、Get
GET /mall/user/1

获取索引为mall类型为user并且id为1的数据
4、Delete
DELETE mall/user/1 #根据id删除
DELETE mall #删除整个索引

5、bulk(批量操作)
语法格式

{action:{metadata}}n
{request body }n
​
{action:{metadata}}n
{request body }n
​

action：(行为)，包含create（文档不存在时创建）、update（更新文档）、index（创建新文档或替换已用文档）、delete（删除一个文档）。
create和index的区别：如果数据存在，使用create操作失败，会提示文档已存在，使用index则可以成功执行。
metadata：(行为操作的具体索引信息)，需要指明数据的_index、_type、_id。
​
POST /mall/user/_bulk
{"delete":{"_index":"mall","_type":"user","_id":"4"}} /#删除的批量操作不需要请求体
{"create":{"_index":"mall","_type":"user","_id":"100"}}
{"name":"hinz100"} #请求体
{"index":{"_index":"mall","_type":"user"}} #没有指定_id，elasticsearch将会自动生成_id
{"name":"hinz"} #请求体
{"update":{"_index":"mall","_type":"user","_id":"1"}} #/更新动作不能缺失_id，文档不存在更新将会失败
{"doc":{"name":"hinz1"}} #请求体
​
bluk一次最大处理多少数据量
bulk会将要处理的数据载入内存中，所以数据量是有限的，最佳的数据量不是一个确定的数据，它取决于你的硬件，你的文档大小以及复杂性，你的索引以及搜索的负载。
​
一般建议是1000-5000个文档，大小建议是5-15MB，默认不能超过100M，可以在es的配置文件（即$ES_HOME下的config下的elasticsearch.yml）中，bulk的线程池配置是内核数+1。
​
bulk批量操作的json格式解析
bulk的格式：
{action:{metadata}}n
{requstbody}n (请求体)
​
不用将其转换为json对象，直接按照换行符切割json，内存中不需要json文本的拷贝。
对每两个一组的json，读取meta，进行document路由。
直接将对应的json发送到node上。
为什么不使用如下格式：
[{"action":{},"data":{}}]
这种方式可读性好，但是内部处理就麻烦；耗费更多内存，增加java虚拟机开销：
​
将json数组解析为JSONArray对象，在内存中就需要有一份json文本的拷贝，宁外好友一个JSONArray对象。
解析json数组里的每个json，对每个请求中的document进行路由。
为路由到同一个shard上的多个请求，创建一个请求数组。
将这个请求数组序列化。
​

八、检索
通过REST request uri 发送搜索参数 （uri +检索参数）；
通过REST request body 来发送它们（uri+请求体）；
1、Query DSL
elasticsearch提供了一个可以执行查询的Json风格的DSL，这个被称为Query DSL，查询语句非常全面

基本语法格式
QUERY_NAME:{
ARGUMENT:VALUE,
ARGUMENT:VALUE,...
}

GET mall/_search
{
"query": {
"match_all": {}
},
"from": 0,
"size": 5,
"sort": {
"age": {
"order": "desc"
}
},
"_source": ["name","age"]
}

match_all 查询类型：意思是查询所有的所有,
from,size 分页查询，
sort排序 可组合多字段
_source 指定查询结果字段 可支持多个
match 匹配查询
基本类型 精确匹配
查询返回age=18的数据
GET /mall/_search
{
"query":{
"match": {
"age": "18"
}
}
}

文本类型 会将查询条件进行分词， 全文检索：最终会按照评分进行排序 会对检索条件进行分词匹配
查询name 包含 "hinzzz"或者name 包含 "wlq"或者name 包含 "hinz100"的数据
GET /mall/_search
{
"query":{
"match": {
"name": "hinzzz wlq hinz100"
}
}
}

match_phrase 短句匹配
不会对查询条件进行分词检索
查询name 包含 "hinzzz wlq"的结果
GET /mall/_search
{
"query":{
"match_phrase": {
"name": "hinzzz wlq"
}
}
}

keyworkd 精确匹配
匹配的条件就是要显示字段的全部值，要进行精确匹配的。
查询name="hinzzz wlq"
GET /mall/_search
{
"query":{
"match_phrase": {
"name.keyword": "hinzzz wlq"
}
}
}

multi_math 多字段匹配
查询name和address 匹配sz的结果
GET /mall/_search
{
"query":{
"multi_match": {
"query": "sz",
"fields": ["name","address"]
}
}
}

bool 用来做符合查询
复合语句可以合并，任何其他查询语句，包括符合语句。这也就意味着，复合语句之间 可以互相嵌套，可以表达非常复杂的逻辑。

must：必须达到must所列举的所有条件
must_not：必须不匹配must_not所列举的所有条件。
should：应该满足should所列举的条件。 类似or
应该达到should列举的条件，如果到达会增加相关文档的评分，并不会改变查询的结果。如果query中只有should且只有一种匹配规则，那么should的条件就会被作为默认匹配条件而去改变查询结果

GET /mall/_search
{
"query":{
"bool": {
"should": [
{
"match": {
"name": "hinzzz"
}
},
{
"match": {
"age": "18"
}
}
]
}
}
}

filter 结果过滤，不计算相关得分
并不是所有的查询都要产生分数，特别是那些仅用于filering的文档。为了不计算分数，elasticsearch会自动查询场景别切优化查询结果
先查询name匹配hinzzz的结果 再从结果中获取 age>10 并且 age < 20
GET /mall/_search
{
"query": {
"bool": {
"must": [
{
"match": {
"name": "hinzzz"
}
}
],
"filter": {
"range": {
"age": {
"gte": 10,
"lte": 20
}
}
}
}
}
}

term 查找倒排索引中确切的term
并不知道分词器的存在。这种查询适合keyword 、numeric、date
导入数据

POST /test/term
{
"name":"hinz",
"address":"bj sz gz"

}
POST /test/term
{
"name":"hinz",
"address":"sz"

}
POST /test/term
{
"name":"Hinz",
"address":"sz"

}

查询1
GET /test/_search
{
"query": {
"term": {
"name": {
"value": "Hinz"
}
}
}
}

返回结果，无记录

{
"took" : 2,
"timed_out" : false,
"_shards" : {
"total" : 1,
"successful" : 1,
"skipped" : 0,
"failed" : 0
},
"hits" : {
"total" : {
"value" : 0,
"relation" : "eq"
},
"max_score" : null,
"hits" : [ ]
}
}

分析 ：string数据存到es中时，默认是text,被standard analyzer分词器分词，大写字母全部转为了小写字母

GET /test/_analyze
{
"field": "name",
"text": "Hinz"
}

结果
{
"tokens" : [
{
"token" : "hinz",
"start_offset" : 0,
"end_offset" : 4,
"type" : "<ALPHANUM>",
"position" : 0
}
]
}

查询2
GET /test/_search
{
"query": {
"term": {
"address": {
"value": "bj sz"
}
}
}
}

结果

GET /test/_search
{
"query": {
"term": {
"address": {
"value": "bj sz"
}
}
}
}

分析：因为term查询并不会对查询条件进行分词，而默认存进去的“bj sz ”则被默认分词器分词了，所以在倒排索引中并不存在整个词“bj sz ”

GET /test/_analyze
{
"field": "address",
"text": "bj sz"
}

结果
{
"tokens" : [
{
"token" : "bj",
"start_offset" : 0,
"end_offset" : 2,
"type" : "<ALPHANUM>",
"position" : 0
},
{
"token" : "sz",
"start_offset" : 3,
"end_offset" : 5,
"type" : "<ALPHANUM>",
"position" : 1
}
]
}

Aggregation 聚合查询
聚合提供了数据分组和提取数据的能力，类似于mysql中的group by ,avg，max等聚合函数
语法格式
"aggs":{
"aggs_name这次聚合的名字，方便展示在结果集中":{
"AGG_TYPE聚合的类型(avg,term,terms)":{}
}
}

查询1
address中包含sz的所有人的年龄分布以及平均年龄，但不显示这些人的详情
准备数据
POST /testaggs/user
{
"name":"hinz1",
"address":"bj sz gz",
"age":"18"

}
POST /testaggs/user
{
"name":"hinz2",
"address":"bj sz",
"age":"19"

}
POST /testaggs/user
{
"name":"hinz3",
"address":"sz",
"age":"20"

}

查询
GET /testaggs/_search
{
"query": {
"match": {
"address": "sz"
}
},
"aggs": {
"ageAvg": {
"avg": {"field": "age"}
}
},
"size": 0 //不显示搜索数据
}
​

查询结果
{
"took" : 16,
"timed_out" : false,
"_shards" : {
"total" : 1,
"successful" : 1,
"skipped" : 0,
"failed" : 0
},
"hits" : {
"total" : {
"value" : 3,
"relation" : "eq"
},
"max_score" : null,
"hits" : [ ]
},
"aggregations" : {
"ageAvg" : {
"value" : 19.0
}
}
}

2、Mapping
（1）字段类型
字符串（string）

text,keyword

数字类型（Numberic）

long，integer，short，double，byte，float，half_float，scaled_float

日期类型（Date）

date

布尔类型（Boolean）

boolean

二进制类型（Binary）

binary

数组类型（Array）

array

对象类型（Object）

object用于单json对象

嵌套类型（Nested）

用于json对象数组

地理坐标（Geo-points）

geo_point用于描述经纬度坐标

地理图形（Geo-Shape）

geo_shape用于描述复杂类型，如多边形

（2）映射
Mapping用于定义一个文档（document）,以及他所包含的属性（field）是如何存储和索引的。比如：使用Mapping来定义：

那些字符串属性应该被全文检索（full test fields）
那些属性包含数字、日期、地理位置信息
文档中的所有数据是否都能被索引（all配置）
日期的格式
自定义映射规则来执行动态增加属性
查看Mapping信息
查看Mapping信息
GET /testaggs/_mapping

结果
{
"testaggs" : {
"mappings" : {
"properties" : {
"address" : {
"type" : "text",
"fields" : {
"keyword" : {
"type" : "keyword",
"ignore_above" : 256
}
}
},
"age" : {
"type" : "long"
},
"name" : {
"type" : "text",
"fields" : {
"keyword" : {
"type" : "keyword",
"ignore_above" : 256
}
}
}
}
}
}
}
​

设置属性是否被全文检索
PUT /my_index
{
"mappings": {
"properties": {
"age": {
"type": "integer"
},
"email": {
"type": "keyword"
},
"name": {
"type": "text"
}
}
}
}

结果
{
"acknowledged" : true,
"shards_acknowledged" : true,
"index" : "my_index"
}

查看
GET /my_index

结果
{
"my_index" : {
"aliases" : { },
"mappings" : {
"properties" : {
"age" : {
"type" : "integer"
},
"email" : {
"type" : "keyword"
},
"name" : {
"type" : "text"
}
}
},
"settings" : {
"index" : {
"creation_date" : "1609748702511",
"number_of_shards" : "1",
"number_of_replicas" : "1",
"uuid" : "09AdAliQTYq0JxuE7dk_KQ",
"version" : {
"created" : "7040299"
},
"provided_name" : "my_index"
}
}
}
}

添加新的字段映射

PUT /my_index/_mapping
{
"properties": {
"employee-id": {
"type": "keyword",
"index": false
}
}
}
​
​

增加数据

POST /my_index/_doc
{
"name":"hinz",
"email":"aa@qq.com",
"age":18,
"employee-id":1

}

更新映射

对于已经存在的字段映射，我们不能进行更新。更新必须创建新的索引，进行数据迁移

POST _reindex [固定写法]
{
"source":{
"index":"twitter"
},
"dest":{
"index":"new_twitters"
}
}

将my_index索引的数据迁移到my_new_index去

步骤：

创建新的索引
put /my_new_index
{
"mappings":{
"properties": {
"age": {
"type": "long",
"index": true
},
"name":{
"type": "keyword",
"index": true
},
"email":{
"type": "text",
"index": true
},
"employee-id" : {
"type" : "keyword",
"index" : false
}
}
}
}

执行迁移

POST _reindex
{
"source":{
"index":"my_index"
},
"dest":{
"index":"my_new_index"
}
}
​

结果
{
"took" : 52,
"timed_out" : false,
"total" : 1,
"updated" : 0,
"created" : 1,
"deleted" : 0,
"batches" : 1,
"version_conflicts" : 0,
"noops" : 0,
"retries" : {
"bulk" : 0,
"search" : 0
},
"throttled_millis" : 0,
"requests_per_second" : -1.0,
"throttled_until_millis" : 0,
"failures" : [ ]
}
​

迁移完成

GET /my_new_index/_mapping

结果
{
"my_new_index" : {
"mappings" : {
"properties" : {
"age" : {
"type" : "long"
},
"email" : {
"type" : "text"
},
"employee-id" : {
"type" : "keyword",
"index" : false
},
"name" : {
"type" : "keyword"
}
}
}
}
}
​

（3）新版本改变
关系型数据库中两个数据表示是独立的，即使他们里面有相同名称的列也不影响使用，但ES中不是这样的。elasticsearch是基于Lucene开发的搜索引擎，而ES中不同type下名称相同的filed最终在Lucene中的处理方式是一样的。

两个不同type下的两个user_name，在ES同一个索引下其实被认为是同一个filed，你必须在两个不同的type中定义相同的filed映射。否则，不同type中的相同字段名称就会在处理中出现冲突的情况，导致Lucene处理效率下降。
去掉type就是为了提高ES处理数据的效率。
Elasticsearch 7.x URL中的type参数为可选。比如，索引一个文档不再要求提供文档类型
Elasticsearch 8.x 不再支持URL中的type参数。
解决：

将索引从多类型迁移到单类型，每种类型文档一个独立索引
将已存在的索引下的类型数据，全部迁移到指定位置即可
3、分词
[分词器] https://www.elastic.co/guide/...

一个tokenizer（分词器）接收一个字符流，将之分割为独立的tokens（词元，通常是独立的单词），然后输出tokens流。

例如：whitespace tokenizer遇到空白字符时分割文本。它会将文本“Quick brown fox!”分割为[Quick,brown,fox!]。

可以使用分词器对一个字符串进行分析，得出最后分词的结果。在我们使用Elasticsearch检索不到想要的结果时，善于应用分析器

POST _analyze
{
"analyzer": "standard",
"text": "The 2 QUICK Brown-Foxes jumped over the lazy dog's bone."
}

这段话被分词为
{
"tokens" : [
{
"token" : "the",
"start_offset" : 0,
"end_offset" : 3,
"type" : "<ALPHANUM>",
"position" : 0
},
{
"token" : "2",
"start_offset" : 4,
"end_offset" : 5,
"type" : "<NUM>",
"position" : 1
},
{
"token" : "quick",
"start_offset" : 6,
"end_offset" : 11,
"type" : "<ALPHANUM>",
"position" : 2
},
{
"token" : "brown",
"start_offset" : 12,
"end_offset" : 17,
"type" : "<ALPHANUM>",
"position" : 3
},
{
"token" : "foxes",
"start_offset" : 18,
"end_offset" : 23,
"type" : "<ALPHANUM>",
"position" : 4
},
{
"token" : "jumped",
"start_offset" : 24,
"end_offset" : 30,
"type" : "<ALPHANUM>",
"position" : 5
},
{
"token" : "over",
"start_offset" : 31,
"end_offset" : 35,
"type" : "<ALPHANUM>",
"position" : 6
},
{
"token" : "the",
"start_offset" : 36,
"end_offset" : 39,
"type" : "<ALPHANUM>",
"position" : 7
},
{
"token" : "lazy",
"start_offset" : 40,
"end_offset" : 44,
"type" : "<ALPHANUM>",
"position" : 8
},
{
"token" : "dog's",
"start_offset" : 45,
"end_offset" : 50,
"type" : "<ALPHANUM>",
"position" : 9
},
{
"token" : "bone",
"start_offset" : 51,
"end_offset" : 55,
"type" : "<ALPHANUM>",
"position" : 10
}
]
}

1、安装ik分词器
找到对应的elasticsearch版本https://github.com/medcl/elasticsearch-analysis-ik/releases



将解压的好的压缩包放到elasticsearch插件目录/home/elasticsearch/plugins，即上面安装elasticsearch时所挂在的plugins目录

tar -zxvf elasticsearch-analysis-ik-7.4.2.zip ik

重启Elasticsearch

docker restart elasticsearch

查看是否安装成功

docker exec -it elasticsearch sh
cd /usr/share/elasticsearch/bin
elasticsearch-plugin list

返回
ik
安装成功

测试分词

默认分词器

POST _analyze
{
"text": "阿里巴巴welcome"
}

结果
{
"tokens" : [
{
"token" : "阿",
"start_offset" : 0,
"end_offset" : 1,
"type" : "<IDEOGRAPHIC>",
"position" : 0
},
{
"token" : "里",
"start_offset" : 1,
"end_offset" : 2,
"type" : "<IDEOGRAPHIC>",
"position" : 1
},
{
"token" : "巴",
"start_offset" : 2,
"end_offset" : 3,
"type" : "<IDEOGRAPHIC>",
"position" : 2
},
{
"token" : "巴",
"start_offset" : 3,
"end_offset" : 4,
"type" : "<IDEOGRAPHIC>",
"position" : 3
},
{
"token" : "welcome",
"start_offset" : 4,
"end_offset" : 11,
"type" : "<ALPHANUM>",
"position" : 4
}
]
}
​

ik中文分词

POST _analyze
{
"analyzer": "ik_smart",
"text": "阿里巴巴welcome"
}

结果
{
"tokens" : [
{
"token" : "阿里巴巴",
"start_offset" : 0,
"end_offset" : 4,
"type" : "CN_WORD",
"position" : 0
},
{
"token" : "welcome",
"start_offset" : 4,
"end_offset" : 11,
"type" : "ENGLISH",
"position" : 1
}
]
}

POST _analyze
{
"analyzer": "ik_smart",
"text": "我是周树人"
}

结果
{
"tokens" : [
{
"token" : "我",
"start_offset" : 0,
"end_offset" : 1,
"type" : "CN_CHAR",
"position" : 0
},
{
"token" : "是",
"start_offset" : 1,
"end_offset" : 2,
"type" : "CN_CHAR",
"position" : 1
},
{
"token" : "周树人",
"start_offset" : 2,
"end_offset" : 5,
"type" : "CN_WORD",
"position" : 2
}
]
}
​

POST _analyze
{
"analyzer": "ik_max_word",
"text": "我是周树人"
}

结果
{
"tokens" : [
{
"token" : "我",
"start_offset" : 0,
"end_offset" : 1,
"type" : "CN_CHAR",
"position" : 0
},
{
"token" : "是",
"start_offset" : 1,
"end_offset" : 2,
"type" : "CN_CHAR",
"position" : 1
},
{
"token" : "周树人",
"start_offset" : 2,
"end_offset" : 5,
"type" : "CN_WORD",
"position" : 2
},
{
"token" : "树人",
"start_offset" : 3,
"end_offset" : 5,
"type" : "CN_WORD",
"position" : 3
}
]
}

2、自定义词库
修改/usr/share/elasticsearch/plugins/ik/config中的IKAnalyzer.cfg.xml 配置完要重启elasticsearch

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
<comment>IK Analyzer 扩展配置</comment>
<!--用户可以在这里配置自己的扩展字典 -->
<entry key="ext_dict"></entry>
<!--用户可以在这里配置自己的扩展停止词字典-->
<entry key="ext_stopwords"></entry>
<!--用户可以在这里配置远程扩展字典 nginx服务器-->
<entry key="remote_ext_dict">http://ip/fenci.txt</entry>
<!--用户可以在这里配置远程扩展停止词字典-->
<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>

fenci.txt内容 多个词汇换行填写

联拓宝

（1）指定词库前
POST /my_index/_analyze
{
"analyzer": "ik_max_word",
"text":"联拓宝项目"

}

结果
{
"tokens" : [
{
"token" : "联",
"start_offset" : 0,
"end_offset" : 1,
"type" : "CN_CHAR",
"position" : 0
},
{
"token" : "拓",
"start_offset" : 1,
"end_offset" : 2,
"type" : "CN_CHAR",
"position" : 1
},
{
"token" : "宝",
"start_offset" : 2,
"end_offset" : 3,
"type" : "CN_CHAR",
"position" : 2
},
{
"token" : "项目",
"start_offset" : 3,
"end_offset" : 5,
"type" : "CN_WORD",
"position" : 3
}
]
}

（2）指定词库后
POST /my_index/_analyze
{
"analyzer": "ik_max_word",
"text":"联拓宝项目"

}

结果
{
"tokens" : [
{
"token" : "联拓宝",
"start_offset" : 0,
"end_offset" : 3,
"type" : "CN_WORD",
"position" : 0
},
{
"token" : "项目",
"start_offset" : 3,
"end_offset" : 5,
"type" : "CN_WORD",
"position" : 1
}
]
}

九、整合SpringBoot
[官方api] https://www.elastic.co/guide/...

jar包版本与Elasticsearch保持一致

<dependency>
<groupId>org.elasticsearch.client</groupId>
<artifactId>elasticsearch-rest-high-level-client</artifactId>
<version>7.4.2</version>
</dependency>



引入7.4.2版本之后，发现实际导入的是6.8.5版本，

查看springboot的默认引用







所以需要重新制定elasticsearch的版本

<properties>
...
<elasticsearch.version>7.4.2</elasticsearch.version>
</properties>
​

1、初始化
/*集群可以配置多个HttpHost/
/*RestHighLevelClient client = new RestHighLevelClient(
RestClient.builder(
new HttpHost("localhost", 9201, "http"))
new HttpHost("localhost", 9200, "http"),
);*/
RestHighLevelClient client = new RestHighLevelClient(
RestClient.builder(
new HttpHost("120.79.48.191", 9200, "http"))
);

2、索引
@Test
void indexData() throws Exception{
IndexRequest indexRequest = new IndexRequest("user");
User user = new User("hinzzz",18,"亚历山大大西北出来");
String jsonString = JSON.toJSONString(user);
//设置要保存的内容
indexRequest.source(jsonString, XContentType.JSON);
//执行创建索引和保存数据
IndexResponse index = client.index(indexRequest, ElasticSearchConfig.COMMON_OPTIONS);
​
System.out.println(index);
//IndexResponse[index=user,type=_doc,id=76Sb2nYBEfd-vMN2ksrv,version=1,result=created,seqNo=2,primaryTerm=1,shards={"total":2,"successful":1,"failed":0}]
}

3、简单查询
@Test
void getData()throws Exception{
GetRequest getIndexRequest = new GetRequest("user","7aRw1XYBEfd-vMN2XMo_");
GetResponse getResponse = client.get(getIndexRequest, ElasticSearchConfig.COMMON_OPTIONS);
System.out.println("getResponse = " + getResponse);
//getResponse = {"_index":"user","_type":"_doc","_id":"7aRw1XYBEfd-vMN2XMo_","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":{"address":"亚历山大大西北出来","age":18,"name":"hinzzz"}}
//getResponse.getXXX 可获取索引信息
String index = getResponse.getIndex();
System.out.println(index);
String id = getResponse.getId();
System.out.println(id);
if (getResponse.isExists()) {
long version = getResponse.getVersion();
System.out.println(version);
String sourceAsString = getResponse.getSourceAsString();
System.out.println(sourceAsString);
Map<String, Object> sourceAsMap = getResponse.getSourceAsMap();//数据转map
System.out.println(sourceAsMap);
byte[] sourceAsBytes = getResponse.getSourceAsBytes();
}
}

4、复杂查询
搜索address中包含北京的所有人的年龄分布以及平均年龄，平均薪资

GET _search
{
"query": {
"match": {
"address": "北京"
}
},
"aggs": {
"ageAvg": {
"avg": {
"field": "age"
}
},
"allAge":{
"terms": {
"field": "age",
"size": 10
}
},
"maxAge":{
"max": {
"field": "salary"
}
}
}
}
​

{
"took" : 22,
"timed_out" : false,
"_shards" : {
"total" : 11,
"successful" : 10,
"skipped" : 0,
"failed" : 1,
"failures" : [
{
"shard" : 0,
"index" : "db",
"node" : "GkkvBdNuS36wLEIG05ynJw",
"reason" : {
"type" : "illegal_argument_exception",
"reason" : "Fielddata is disabled on text fields by default. Set fielddata=true on [age] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead."
}
}
]
},
"hits" : {
"total" : {
"value" : 4,
"relation" : "eq"
},
"max_score" : 1.2127436,
"hits" : [
{
"_index" : "user",
"_type" : "_doc",
"_id" : "86Ta2nYBEfd-vMN24spZ",
"_score" : 1.2127436,
"_source" : {
"name" : "z6",
"age" : 27,
"salary" : 963,
"address" : "北京五环"
}
},
{
"_index" : "user",
"_type" : "_doc",
"_id" : "9KTb2nYBEfd-vMN2Qcq0",
"_score" : 1.2127436,
"_source" : {
"name" : "s7",
"age" : 25,
"salary" : 632,
"address" : "北京二环"
}
},
{
"_index" : "user",
"_type" : "_doc",
"_id" : "9aTb2nYBEfd-vMN2m8p_",
"_score" : 1.1082253,
"_source" : {
"name" : "g8",
"age" : 45,
"salary" : 1111,
"address" : "北京紫禁城"
}
},
{
"_index" : "user",
"_type" : "_doc",
"_id" : "8KTY2nYBEfd-vMN298qW",
"_score" : 0.9452891,
"_source" : {
"name" : "z3",
"age" : 20,
"salary" : 123,
"address" : "北京天安门广场"
}
}
]
},
"aggregations" : {
"ageAvg" : {
"value" : 29.25
},
"allAge" : {
"doc_count_error_upper_bound" : 0,
"sum_other_doc_count" : 0,
"buckets" : [
{
"key" : 20,
"doc_count" : 1
},
{
"key" : 25,
"doc_count" : 1
},
{
"key" : 27,
"doc_count" : 1
},
{
"key" : 45,
"doc_count" : 1
}
]
},
"maxAge" : {
"value" : 707.25
}
}
}
​

@Test
void complexSearch()throws Exception{
//搜索address中包含北京的所有人的年龄分布以及平均年龄，平均薪资
SearchRequest searchRequest = new SearchRequest();
​
//返回所有结果
//System.out.println("查询所有数据" + client.search(searchRequest, ElasticSearchConfig.COMMON_OPTIONS));
​
//指定索引
//searchRequest.indices("user","my_index");
//System.out.println("查询多个索引" + client.search(searchRequest, ElasticSearchConfig.COMMON_OPTIONS));
​
​
//查询地址
SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();
searchSourceBuilder.query(QueryBuilders.matchQuery("address","北京"));
​
//查询年龄分布
TermsAggregationBuilder termsAggregationBuilder = AggregationBuilders.terms("allAge").field("age").size(100);
searchSourceBuilder.aggregation(termsAggregationBuilder);
​
//查询平均年龄
AvgAggregationBuilder avgAggregationBuilder = AggregationBuilders.avg("ageAvg").field("age");
searchSourceBuilder.aggregation(avgAggregationBuilder);
​
//查询平均薪资
AvgAggregationBuilder avgAggregationBuilder1 = AggregationBuilders.avg("salaryAvg").field("salary");
searchSourceBuilder.aggregation(avgAggregationBuilder1);
​
searchRequest.source(searchSourceBuilder);
​
SearchResponse searchResponse = client.search(searchRequest, ElasticSearchConfig.COMMON_OPTIONS);
​
System.out.println("search = " + searchResponse);
​
​
//将检索结果封装为Bean
SearchHits hits = searchResponse.getHits();
SearchHit[] searchHits = hits.getHits();
for (SearchHit searchHit : searchHits) {
String sourceAsString = searchHit.getSourceAsString();
User user = JSON.parseObject(sourceAsString, User.class);
System.out.println(user);
​
}
​
//获取聚合信息
Aggregations aggregations = searchResponse.getAggregations();
​
Terms ageAgg1 = aggregations.get("allAge");
​
for (Terms.Bucket bucket : ageAgg1.getBuckets()) {
String keyAsString = bucket.getKeyAsString();
System.out.println("年龄："+keyAsString+" ==> "+bucket.getDocCount());
}
Avg ageAvg1 = aggregations.get("ageAvg");
System.out.println("平均年龄："+ageAvg1.getValue());
​
Avg balanceAvg1 = aggregations.get("salaryAvg");
System.out.println("平均薪资："+balanceAvg1.getValue());
​
}

