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



### 搜索文档

```



```





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



### ik分词器

分词器有不同粒度

"analyzer": "ik_smart",

"analyzer": "ik_max_word",

"analyzer": "ik_max_word",







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



















