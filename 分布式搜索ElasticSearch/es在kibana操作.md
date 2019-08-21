[TOC]

### 一、ES基本增删改查功能

##### 1. 增

###### （1）添加索引

```
PUT /liushengyuan/
{
  "settings": {
    
  "index":{
      
      "number_of_shards":5,
      
      "number_of_replicas":0
      
    }
    
  }
}
```

###### （2）新增数据 可以分别使用put和post方式

```put方式   需要指定id   如果id相同就是更新 版本号_version会增加
PUT /liushengyuan/user/1
{
  "first_name":"lsy",
  "last_name":"shengyuan",
  "age":26,
  "about":"play run sleep",
  "interests":["eat"]
}
```

```#不需要指定id  id是随机的UUID
POST /liushengyuan/user
{
  "first_name":"lsy",
  "last_name":"shengyuan",
  "age":26,
  "about":"play run sleep",
  "interests":["eat"]
}
```

##### 2. 查

###### （1）查找某个id下的数据

```#查询 GET/某个索引下/某个类型/某个ID的对应数据
GET /liushengyuan/user/qnAMrWwB9_cpgs4nyhOd
```

*result:*

```{
  "_index" : "liushengyuan",
  "_type" : "user",
  "_id" : "qnAMrWwB9_cpgs4nyhOd",
  "_version" : 1,
  "_seq_no" : 4,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "first_name" : "lsy",
    "last_name" : "shengyuan",
    "age" : 26,
    "about" : "play run sleep",
    "interests" : [
      "eat"
    ]
  }
}
```

###### （2）查找某个id下的具体某个字段的数据

``` 
GET /liushengyuan/user/qnAMrWwB9_cpgs4nyhOd?_source=age,about
```

*result：*

```{
  "_index" : "liushengyuan",
  "_type" : "user",
  "_id" : "qnAMrWwB9_cpgs4nyhOd",
  "_version" : 1,
  "_seq_no" : 4,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "about" : "play run sleep",
    "age" : 26
  }
}
```

##### 3. 改

（1）可以使用put针对 某ID所对应的数据进行修改，version会增加

（2）也可以使用POST直接修改,如下将：liushengyuan/user/1的数据的年龄更新为100

```
POST /liushengyuan/user/1/_update
{
  "doc": {
    "age":100
  }
}
```

##### 4. 删

```
DELETE /liushengyuan/user/1
```



### 二、使用multiGet批量获取文档

##### 1. 批量获取数据   索引 类型 id

```
GET /_mget 
{
  "docs":[
    {
      "_index":"liushengyuan",
      "_type":"user",
      "_id":1
    },{
      "_index":"liushengyuan",
      "_type":"user",
      "_id":2
    },{
      "_index":"liushengyuan",
      "_type":"user",
      "_id":3
    }
  ]
}
```

***result:***

```{
{
  "docs" : [
    {
      "_index" : "liushengyuan",
      "_type" : "user",
      "_id" : "1",
      "found" : false
    },
    {
      "_index" : "liushengyuan",
      "_type" : "user",
      "_id" : "2",
      "_version" : 1,
      "_seq_no" : 0,
      "_primary_term" : 1,
      "found" : true,
      "_source" : {
        "first_name" : "linning",
        "last_name" : "hahaha1",
        "age" : 30,
        "about" : "sleep",
        "interests" : [
          "eat"
        ]
      }
    },
    {
      "_index" : "liushengyuan",
      "_type" : "user",
      "_id" : "3",
      "_version" : 1,
      "_seq_no" : 0,
      "_primary_term" : 1,
      "found" : true,
      "_source" : {
        "first_name" : "luyunyang",
        "last_name" : "hahaha2",
        "age" : 29,
        "about" : "sleep1",
        "interests" : [
          "eatting"
        ]
      }
    }
 
 ]
}
```

##### 2. 批量获取数据，只获取具体字段

```GET /_mget 
{
  "docs":[
    {
      "_index":"liushengyuan",
      "_type":"user",
      "_id":2,
      "_source":"interests"
    },{
      "_index":"liushengyuan",
      "_type":"user",
      "_id":3,
      "_source":["interests","first_name"]
    }
  ]
}
```

***result***

```{
 {
  "docs" : [
    {
      "_index" : "liushengyuan",
      "_type" : "user",
      "_id" : "2",
      "_version" : 1,
      "_seq_no" : 0,
      "_primary_term" : 1,
      "found" : true,
      "_source" : {
        "interests" : [
          "eat"
        ]
      }
    },
    {
      "_index" : "liushengyuan",
      "_type" : "user",
      "_id" : "3",
      "_version" : 1,
      "_seq_no" : 0,
      "_primary_term" : 1,
      "found" : true,
      "_source" : {
        "interests" : [
          "eatting"
        ],
        "first_name" : "luyunyang"
      }
    }
  ]
}

```

##### 3.索引 类型都相同可以采用简写的写法：

```#索引相同类型相同  可以简写
GET /liushengyuan/user/_mget 
{
  "docs":[
    {
      "_id":2,
      "_source":"interests"
    },{
      "_id":3,
      "_source":["interests","first_name"]
    }
  ]
}
```

也可以直接根据id批量查询：

```
GET /liushengyuan/user/_mget 
{
  "ids":["1","2","3"]
}
```



### 三、bulk实现数据批量操作，（增删改）

#批量操作

#{action:{metadata}}
#{requestbody}
#action:有四种 create update index delete
#create和index区别 create不能创建已存在的文档  index是创建或者替换

##### 1.  新增

```
POST /liushengyuan2/user/_bulk
{"index":{"_id":3}}
{"about":"pp","age":31}
{"index":{"_id":2}}
{"about":"pping","age":33}
```

#####  2. 创建  修改  删除

 ```
POST /liushengyuan2/user/_bulk
{"create":{"_index":"test1","_type":"type1","_id":"100"}}
{"name":"LSY"}
{"index":{"_index":"test2","_type":"type2"}}
{"name":"LSY"}
{"delete":{"_index":"liushengyuan2","_type":"user","_id":2}}
{"update":{"_index":"test1","_type":"type1","_id":"100"}}
{"doc":{"name":"LSY1"}}
 ```



### 四、mapping

##### 1. 查看 /索引/类型 的，mapping（结构）

自动生成对应的数据结构 

```
boolean date long text float 
```

例如：

```
PUT /lsy2/type2/2
{
  "class_name":"num1",
  "head_count":60,
  "man":45,
  "woman":15,
  "normal":true,
  "avg":85.1,
  "time":"2019-08-21",
  "content":"today is a good day"
}
```

***result***

```{
{
  "lsy2" : {
    "mappings" : {
      "type2" : {
        "properties" : {
          "avg" : {
            "type" : "float"
          },
          "class_name" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          },
          "content" : {
            "type" : "text",
            "fields" : {
              "keyword" : {
                "type" : "keyword",
                "ignore_above" : 256
              }
            }
          },
          "head_count" : {
            "type" : "long"
          },
          "man" : {
            "type" : "long"
          },
          "normal" : {
            "type" : "boolean"
          },
          "time" : {
            "type" : "date"
          },
          "woman" : {
            "type" : "long"
          }
        }
      }
    }
  }
}

```

text类型会自动生成倒排索引，但是date等类型  必须搜索全部字段才能查询到,如下两个查询：

```GET /lsy2/type2/_search?q=time:2019-08-21```

```GET /lsy2/type2/_search?q=content:day ```



##### 2. 手动创建mapping

  **analyzer**：分词器 **index**：是否创建倒排索引

```
PUT /lsy3
{
  "mappings": {
    "type3":{
      "properties":{
        "title":{"type":"text"},
        "name":{"type":"text","analyzer":"standard"},
        "date":{"type":"date","index":false}
      }
    }
  }
}
```



### 五、基本查询（query查询）

##### 1. 英文查询

###### （1）基本查询 并且**排序**

`GET /lsy4/user/_search?q=other:some&sort=age:desc`

###### （2）**term**查询和**terms**查询

```
GET /lsy4/user/_search/
{
  "query": {
    "term": {
      "address": "内"
    }
  }
}

#terms 支持数组
GET /lsy4/user/_search/
{
  "query": {
    "terms": {
      "address": ["gu","ya"]
    }
  }
}

# 分页
GET /lsy4/user/_search/
{
  "from": 0,
  "size": 2, 
  "query": {
    "terms": {
      "address": ["gu","ya"]
    }
  }
}

#terms 查询版本号
GET /lsy4/user/_search/
{
  "version": true, 
  "query": {
    "terms": {
      "address": ["gu","ya"]
    }
  }
}
```

###### （3）**match**查询 

<!--term查询适合查找没有分词的字段 如date keywords-->

```
#match 查询 会对查询的内容进行分词  感知不到分词器的存在
#但是用term可能会查不出来，会将查询的内容看成一个独立的词  不进行分词
#term适合精确查找  否则用match

GET /lsy4/user/_search/
{
  "query": {
    "match": {
      "name": "刘晟源"
    }
  }
}

#查询所有
GET /lsy4/user/_search/
{
  "query": {
    "match_all": {}
  }
}
```

###### （4）**multi_match**查询

```
#multi_match 匹配多个字段

GET /lsy4/user/_search/
{
  "query": {
    "multi_match": {
      "query": "duanlian",
      "fields": ["interests","name"]
    }
  }
}
```

###### （5）**match_phrase**查询

```
#match_phrase 短语匹配 短语的顺序也必须正确  gu nei meng 则无法查询
GET /lsy4/user/_search/
{
  "query": {
    "match_phrase": {
      "address": "nei meng gu"
    }
  }
}

```

###### （6）_source查询

```
#用_source可以只查询出某几个字段
GET /lsy4/user/_search/
{
  "_source": ["address","name"], 
  "query": {
    "match": {
      "interests": "run"
    }
  }
}
```

###### （7）一些匹配规则，以及排序方式

```
#includes 和 excludes 来指明包含哪些字段和排除哪些字段
GET /lsy4/user/_search/
{
  "query": {
    "match_all": {}
  },
  "_source": {
    "includes": ["name","address"],
    "excludes": ["interests"]
  }
}

#可以使用通配符 *
GET /lsy4/user/_search/
{
  "query": {
    "match_all": {}
  },
  "_source": {
    "includes": ["name","addr*"],
    "excludes": ["intere*"]
  }
}



#指明根据某字段排序
GET /lsy4/user/_search/
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "age": {
        "order": "desc"
      }
    }
  ]
}

#按照前缀查询  前缀匹配查询
GET /lsy4/user/_search/
{
  "query": {
    "match_phrase_prefix": {
      "name": {
        "query": "li"
      }
    }
  }
}

#范围查询 大于等于 或者小于等于 使用include_lower  include_upper 两个关键字
GET /lsy4/user/_search/
{
  "query": {
    "range": {
      "age": {
        "from": "21",
        "to": "35",
        "include_lower":false,
        "include_upper":false
      }
    }
  }
}
```



###### (8)  **wildcard**查询

```
# wildcard查询
# * 代表0个或者多个字符
# ? 代表任意一个字符


GET /lsy4/user/_search
{
  "query": {
    "wildcard": {
        "name": "li*"
    }
  }
}

GET /lsy4/user/_search
{
  "query": {
    "wildcard": {
        "name": "li??"
    }
  }
}
```

（9）fuzzy模糊查询

```
GET /lsy4/user/_search
{
  "query": {
    "fuzzy": {
        "name": "liushangyuam"
    }
  }
}

```

（10）查询结果高亮显示**highlight**

```
#搜索结果需要高亮显示的
#结果被增加了标签 ：<em>liushengyuan</em>
GET /lsy4/user/_search
{
  "query": {
    "fuzzy": {
        "name": "liushangyuam"
    }
  },
  "highlight": {
    "fields": {
      "name": {}
    }
  }
}
```



##### 2. 中文查询

（不设置分词器  则 “小明”会被分词成“小” “明” 两个词  用term查询的方式查询“小明” 会显示查不到）

需要设置分词器：

<!--Elasticsearch中文分词我们采用Ik分词，ik有两种分词模式，ik_max_word,和ik_smart模式-->

***ik_max_word***: 会将文本做最细粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,中华人民,中华,华人,人民共和国,人民,人,民,共和国,共和,和,国国,国歌”，会穷尽各种可能的组合。

***ik_smart***: 会做最粗粒度的拆分，比如会将“中华人民共和国国歌”拆分为“中华人民共和国,国歌”。







