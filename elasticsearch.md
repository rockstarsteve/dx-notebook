# elasticsearch笔记

一些概念：结构化数据-》数据库  ；  非结构化数据：邮件，word文档   半结构化数据：xml，html



## 和数据库做对比

> --> db ->   database    ->      table    ->      row              ->      coloumn        表结构定义
>
> -->es  ->     index          ->      type      ->   document    ->         filed                  mapping

7.x后一个索引只能创建一个type-"_doc"

## 增删改查基本命令：

### 创建索引库

规范：必须小写开头，不能用下划线开头

```
PUT /dx_hello
{
  "settings": {
    "index": {
      "number_of_shards": 5,
      "number_of_replicas": 1,
      "analysis .analyzer .default .type": "ik_max_word"
    }
  }
}
```

### 删除索引库

```
DELETE /dx_hello
```

### 创建映射

```
POST /dx_hello/_doc/_mapping?include_type_name=true
{
  "properties": {
    "id": {
      "type": "text"
    },
    "name": {
      "type": "keyword"
    },
    "info": {
      "type": "text"
    }
  }
}
```

### 查询文档信息

```
GET /dx_hello            # 索引库信息

GET /dx_hello/_mapping   # mapping的信息

GET /_cat/shards/dx_hello  # 分片状态
```

### 插入数据

```
POST /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595
{
  "id": "002",
  "name": "tom",
  "info": "tom is a goo boy , he study work is hard"
}
```

### 查询数据

```
# 查询所有的数据
GET /dx_hello/_doc/_search



# 根据id查询
GET /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595

# 搜索
GET /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595

# 带条件搜索
# 上面的是term全内容查询，后面的是模糊匹配查询
GET /dx_hello/_search
{
  "query": {
    "term": {
      "ifno": {
        "value": "jack"
      }
    }
  }
}

GET /dx_hello/_search
{
  "query": {
    "match": {
      "ifno": "jack"
    }
  }
}






```

### 修改数据

```
# 这种修改是把之前的整个覆盖，也就是把之前的删除后进行插入的效果   
# POST更新必须要带id，POST插入可以不带id给es去生成

POST /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595
{
  "info": "tom is a good boy , he study work is hard"
}

# _create如果已经有这个id再插入就会报错
POST /dx_hello/_create/420c0bdd-b19b-457d-a7bb-a8cacf28d595
{
  "info": "tom is a good boy , he study work is hard"
}


PUT /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595
{
  "info": "tom is a good boy , he study work is hard"
}




# 局部更新
POST /dx_hello/_update/420c0bdd-b19b-457d-a7bb-a8cacf28d595
{
	"doc":{
    "info": "tom 是一个好孩子"
	}
}

# 查询更新文档
POST /dx_hello/_update_by_query
{
  "query":{
    "match": {
      "_id": "420c0bdd-b19b-457d-a7bb-a8cacf28d595"
    }
  },
	"script":{
    "source": "ctx._source.info='汤姆是一个好男孩'"
	}
}

```



### 批量操作

```

# 批量操作的创建  已经存在对的id，文档会产生冲突
POST _bulk
{"create":{"_index":"article","_type":"_doc","_id":3}}
{"id":3,"title":"fox老师","content":"fox老师666","tags":["java","面向对象"],"create_time":1554015482530}
{"create":{"_index":"article","_type":"_doc","_id":4}}
{"id":4,"title":"mark老师","content":"mark老师NB","tags":["java","面向对象"],"create_time":1554015482530}

# 批量操作的覆盖更新
POST _bulk
{"index":{"_index":"article","_type":"_doc","_id":3}}
{"id":3,"title":"fox老师","content":"fox老师666","tags":["java","面向对象"],"create_time":1554015482530}
{"index":{"_index":"article","_type":"_doc","_id":4}}
{"id":4,"title":"mark老师","content":"mark老师NB","tags":["java","面向对象"],"create_time":1554015482530}


# 批量操作的删除
POST _bulk
{"delete":{"_index":"article","_type":"_doc","_id":3}}
{"delete":{"_index":"article","_type":"_doc","_id":4}}


# 批量操作的局部更新
POST _bulk
{"update":{"_index":"article","_type":"_doc","_id":3}}
{"doc":{"title":"fox老师修改"}}
{"update":{"_index":"article","_type":"_doc","_id":4}}
{"doc":{"title":"tom老师修改"}}




# 批量获取
GET _mget
{
  "docs": [
    {
      "_index": "dx_hello",
      "_id": "1"
    },
    {
      "_index":"article",
      "_id":"4"
    }
  ]
}

# 简化后
GET /article/_mget
{
  "ids":["3","4","6"]
}


# 批量读取
GET /article/_msearch
{}
{"query":{"match_all":{}},"from": 0,"size":2}
{"index":"article"}
{"query":{"match_all":{}}}

```

### 分页

```
GET /article/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 2
}

```



### Scroll深分页

```
# 第一次拉取
GET /article/_search?scroll=1m
{
  "query": {
    "match_all": {}
  },
  "size": 2
}

#第二次   ，scroll_id 为上一次查询到的scroll_id
GET /_search/scroll
{
  "scroll": "1m",
  "scroll_id": "FGluY2x1ZGVfY29udGV4dF91dWlkDXF1ZXJ5QW5kRmV0Y2gBFm1uZnA3RGRyUWg2clU3V3l0NF9JV2cAAAAAAAAPRhZxZ1JscXYySVNaQ2UzVVYzbm5iWVln"
}

```



### 排序

```
GET /article/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "id": {
        "order": "desc"
      }
    }
  ]
}

```



### 指定字段

```
GET /article/_search
{
  "query": {
    "match_all": {}
  },
  "_source": "title"
}
```



### match

```
# operator为关系  and，or
GET /article/_search
{
  "query": {
    "match": {
      "title": {
        "query": "老师",
        "operator": "and"
      }
    }
  }
}


#minimum_should_match 为最少匹配多少个词
GET /article/_search
{
  "query": {
    "match": {
      "title": {
        "query": "老师",
        "minimum_should_match": "1"
      }
    }
  }
}

```



### match_phrase短语匹配

```
# 类似 and 的效果,但是是需要对应连续
GET /article/_search
{
  "query": {
    "match_phrase": {
      "title": "老师"
    }
  }
}

```



### multi_match 多字段查询

```

GET /article/_search
{
  "query": {
    "multi_match": {
      "query": "老师",
      "fields": [
        "title","content"
      ]
    }
  }
}

```



### query_string

```
#未指定字段查询所有字段，允许单个查询字符串中指定AND/OR/NOT等条件  (条件要大写)
GET /article/_search
{
  "query": {
    "query_string": {
      "query": "老师 OR 6"
    }
  }
}

#这里指定字段
GET /article/_search
{
  "query": {
    "query_string": {
      "default_field": "title", 
      "query": "老师 OR 6"
    }
  }
}

#这里指定字段
GET /article/_search
{
  "query": {
    "query_string": {
      "fields": ["title","content"], 
      "query": "老师 OR (6 and nb)"
    }
  }
}


```



### simple_query_string

```
# +: and   |: or   -:not
GET /article/_search
{
  "query": {
    "query_string": {
      "default_field": "title", 
      "query": "老师 + 6"
    }
  }
}
```



### term查询

在es中，term查询，对输入不做分词。会将输入作为一个整体，在倒排索引中准确查找词项，并且用相关公式为每个包含词项的文档进行相关度算分。

```
GET /article/_search
{
  "query": {
    "term": {
      "content.keyword": {
        "value": "老师"
      }
    }
  }
}

```



### prefix前缀搜索

它不会分析要搜索的字符串，传入的前缀就是要找的前缀，不做缓存，要进行全部遍历（词条的匹配）

```
GET /article/_search
{
  "query": {
    "prefix": {
      "content": {
        "value": "老师"
      }
    }
  }
}
```



### wildcard通配符匹配

工作原理和prefix相同，只不过它不是比较开头，支持更为复杂的匹配模式，（词条的匹配）

```
GET /article/_search
{
  "query": {
    "wildcard": {
      "content": {
        "value": "*老*"
      }
    }
  }
}
```



### fuzzy模糊查询

允许写错或者差几个字，最大只允许容错2个

```
GET /article/_search
{
  "query": {
    "fuzzy": {
      "content": {
        "value": "老子",
        "fuzziness": 1
      }
    }
  }
}
```



### highlight高亮关键字

```
GET /article/_search
{
  "query": {
    "term": {
      "content": {
        "value": "老"
      }
    }
  },
  "highlight": {
    "pre_tags": ["<span style='color:red'>"], 
    "post_tags": ["</span>"],
    "fields": {
      "*":{}
    }
  }
}

```







## 文档映射Mapping

### 动态映射 



### 静态映射

















## 分词器

### 安装分词器

```
# 查看已经安装的分词器
elasticsearch-plugin list

## 在线安装

#安装analysis-icu分词器
elasticsearch-plugin install analysis-icu

#删除分词器
elasticsearch-plugin remove analysis-icu


## 离线安装

### ik分词器只能离线安装
地址：https://github.com/medcl/elasticsearch-analysis-ik

方法：下载后复制到plugins目录下


注意：安装和删除完插件需要重启es服务才能生效

```

### 查询分词能力

```
# 查询默认分词器能力
POST /_analyze
{
  "text": "张三是一个好学生，他学习很刻苦"
}

#ES默认的分词器设置是standard，下面是ik分词器
POST _analyze
{
  "analyzer": "ik_max_word",
  "text": "中华人民共和国"
}

```




### DSL搜索

term

ids

description

multi_match

bool - must

设置分词在文档中的占比





### IK分词器

代码地址：https://github.com/medcl/elasticsearch-analysis-ik

下载地址：https://github.com/medcl/elasticsearch-analysis-ik/releases

**使用：**

把下载的对应版本的解压文件放入es的plugins目录下

分词器有不同粒度

"analyzer": "ik_smart",

"analyzer": "ik_max_word",



















