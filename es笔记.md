# elasticsearch笔记

## 增删改查基本命令使用：

### 创建索引库

```
PUT /dx_hello
{
  "settings": {
    "index": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  }
}
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
GET /dx_hello
GET /dx_hello/_mapping
```

### 创建文档（插入数据）

```
POST /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595
{
  "id": "001",
  "name": "tom",
  "info": "tom is a goo boy , he study work is hard"
}
```

### 搜索文档

```
1:根据id查询
GET /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595

2：搜索
GET /dx_hello/_doc/420c0bdd-b19b-457d-a7bb-a8cacf28d595

3：带条件搜索
GET /dx_hello/_doc/_search?q=info:good

```





## 分词器

### 查询分词能力

```
POST /_analyze
{
  "text": "张三是一个好学生，他学习很刻苦"
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



















