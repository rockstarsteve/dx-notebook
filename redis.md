# redis
[学习网站](https://www.w3cschool.cn/redis/redis-ydwp2ozz.html)
特点:

>- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
>- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
>- Redis支持数据的备份，即master-slave模式的数据备份。

优点:
> - 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
> - 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
> - 原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
> - 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

## 基本类型及操作

类型查询

```
本地:0>type user:2
"hash"
```



### String
>`string`是`redis`最基本的类型，你可以理解成与`Memcached`一模一样的类型，一个`key`对应一个`value`。
>
>`string`类型是二进制安全的。意思是`redis`的`string`可以包含任何数据。比如`jpg`图片或者序列化的对象 。
>
>`string`类型是`Redis`最基本的数据类型，一个键最大能存储512MB。
>
>**常用命令:** set,get,decr,incr,mget 等。
>
>**注意：**一个键最大能存储512MB。
```
redis 127.0.0.1:6379> SET name "w3cschool.cn"
OK
redis 127.0.0.1:6379> GET name
"w3cschool.cn"
```

### Hash
>`Redis hash` 是一个键值 `(key=>value)` 对集合。
>
>`Redis hash` 是一个 `string` 类型的` field` 和 `value` 的映射表，`hash` 特别适合用于存储对象。
>
>**常用命令：** hget,hset,hgetall 等。
```
redis 127.0.0.1:6379> HMSET user:1 username w3cschool.cn password w3cschool.cn points 200
OK
redis 127.0.0.1:6379> HGETALL user:1
1) "username"
2) "w3cschool.cn"
3) "password"
4) "w3cschool.cn"
5) "points"
6) "200"
redis 127.0.0.1:6379>
```
以上实例中 hash 数据类型存储了包含用户脚本信息的用户对象。实例中我们使用了 Redis HMSET, HGETALL 命令，user:1 为键值。每个 hash 可以存储 232 - 1键值对（40多亿）。

### List
>Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。
>
>list 就是链表
>
>**常用命令:** lpush,rpush,lpop,rpop,lrange等
```
redis 127.0.0.1:6379> lpush w3cschool.cn redis
(integer) 1
redis 127.0.0.1:6379> lpush w3cschool.cn mongodb
(integer) 2
redis 127.0.0.1:6379> lpush w3cschool.cn rabitmq
(integer) 3
redis 127.0.0.1:6379> lrange w3cschool.cn 0 10
1) "rabitmq"
2) "mongodb"
3) "redis"
redis 127.0.0.1:6379>
```

列表最多可存储 232 -1元素 (4294967295, 每个列表可存储40多亿)。


### Set
>`Redis zset` 和` set` 一样也是 `string` 类型元素的集合,且不允许重复的成员。
>
>不同的是每个元素都会关联一个 `double` 类型的分数。`redis` 正是通过分数来为集合中的成员进行从小到大的排序。
>
>`zset` 的成员是唯一的,但分数`(score)`却可以重复。
```
redis 127.0.0.1:6379> sadd w3cschool.cn redis
(integer) 1
redis 127.0.0.1:6379> sadd w3cschool.cn mongodb
(integer) 1
redis 127.0.0.1:6379> sadd w3cschool.cn rabitmq
(integer) 1
redis 127.0.0.1:6379> sadd w3cschool.cn rabitmq
(integer) 0
redis 127.0.0.1:6379> smembers w3cschool.cn

1) "rabitmq"
2) "mongodb"
3) "redis"
```

注意：以上实例中 rabitmq 添加了两次，但根据集合内元素的唯一性，第二次插入的元素将被忽略。

集合中最大的成员数为 232-1 (4294967295, 每个集合可存储40多亿个成员)。

### ZSet(sorted set)
>Redis 有序集合和集合一样也是 string 类型元素的集合,且不允许重复的成员。
>
>不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序。
>
>有序集合的成员是唯一的,但分数(score)却可以重复。
>
>集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。
>
>和set相比，sorted set增加了一个权重参数score，使得集合中的元素能够按score进行有序排列。
>
>**常用命令：** zadd,zrange,zrem,zcard等
```
redis 127.0.0.1:6379> zadd w3cschool.cn 0 redis
(integer) 1
redis 127.0.0.1:6379> zadd w3cschool.cn 0 mongodb
(integer) 1
redis 127.0.0.1:6379> zadd w3cschool.cn 0 rabitmq
(integer) 1
redis 127.0.0.1:6379> zadd w3cschool.cn 0 rabitmq
(integer) 0
redis 127.0.0.1:6379> ZRANGEBYSCORE w3cschool.cn 0 1000

1) "redis"
2) "mongodb"
3) "rabitmq"
```

## 发布和订阅
>Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。
>
>Redis 客户端可以订阅任意数量的频道。

- client1(订阅):
```
redis 127.0.0.1:6379> SUBSCRIBE redisChat

Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "redisChat"
3) (integer) 1
```

- client2（发布）:
```
redis 127.0.0.1:6379> PUBLISH redisChat "Redis is a great caching technique"

(integer) 1

redis 127.0.0.1:6379> PUBLISH redisChat "Learn redis by w3cschool.cn"

(integer) 1

# 订阅者的客户端会显示如下消息
1) "message"
2) "redisChat"
3) "Redis is a great caching technique"
1) "message"
2) "redisChat"
3) "Learn redis by w3cschool.cn"
```

## 事务
>Redis 事务可以一次执行多个命令， 并且带有以下两个重要的保证：
>
>- 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
>- 事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。
>
>一个事务从开始到执行会经历以下三个阶段：
>
>- 开始事务。
>- 命令入队。
>- 执行事务。


## 应用场景
### 缓存



### 分布式锁



### 计数器

```
本地:0>incr article:read:101
"1"
本地:0>incr article:read:101
"2"
本地:0>incr article:read:101
"3"
本地:0>keys *
1) "name"
2) "article:read:101"
```

### 自增id



### 购物车（hash）

购物车里面的商品结构:

用户商品类别

商品1：数量

```
为用户购物车添加一个商品:hset cart:1001 10088 1
为用户添加的商品加1：hincrby cart:1001 10088 1
商品总数： hlen cart:1001
删除商品：hdel cart:1001 10088
获取所有的商品：hgetall cart:1001

```



### 消息队列（list）



### 微博的关注列表，粉丝列表，消息列表（list）



### 交并补业务处理（set）



### 共同关注、共同粉丝、共同喜好（set）



### 时排行信息包含直播间在线用户列表，各种礼物排行榜，弹幕消息（zset）





## 和spring整合

### mavne仓库地址：

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

> 注意：Springboot2后默认使用Lettuce作为访问redis的客户端

> 旧版本lettuce存在堆外内存溢出的bug， 5.3版本修复了这个bug, 还有 6.1 

### application.yml配置（如果使用jedis只要加下客户端就行，对应的客户端添加依赖）

```
spring:
  redis:
    host: localhost
    port: 6379
```

### StringRedisTemplate和RedisTemplate

> 介绍：StringRedisTemplate和RedisTemplate是两种性质一样的东西，区别就是前一个指定了泛型是String类型，后一个指定的泛型是Object类型的

```
	@Autowired
    private RedisTemplate redisTemplate;
    @Autowired
    private StringRedisTemplate stringRedisTemplate;

	//泛型到reids中是以二进制存储的
    @Test
    void set() {
         redisTemplate.opsForValue().set("age",41);
    }
    @Test
    void get() {
        Object age = redisTemplate.opsForValue().get("age");
        System.out.println(age);
    }


    @Test
    void setString() {
        stringRedisTemplate.opsForValue().set("hight","175");
    }
    @Test
    void getString() {
        String hight = stringRedisTemplate.opsForValue().get("hight");
        System.out.println(hight);
    }
```

> set/get    对应代码.set()  .get()     (上面)
>
> setnx     对应代码setIfAbsent()   （下面）
```
	@Test
    void setNxString() {
        Boolean lockkey = stringRedisTemplate.opsForValue().setIfAbsent("lockkey", "1232werrer");
        System.out.println("是否设置setnx成功" + lockkey);
    }
```
> delete 对应.delete()
```
    @Test
    void delete() {
        Boolean isDelte = stringRedisTemplate.opsForValue().getOperations().delete("lockkey");
        System.out.println("是否删除" + isDelte);
    }
```
> 过期时间设置
```
    @Test
    void setExpire() {
        //可以再设置值得时候直接设置超时时间
        boolean isset = stringRedisTemplate.opsForValue().set("hight", "175",10,TimeUnit.SECONDS);
        //单独设置超时时间为10d
        Boolean hight = stringRedisTemplate.expire("hight", 10, TimeUnit.SECONDS);
        System.out.println("是否设置成功" + hight);
    }
```





## redisson

>redis的java客户端，更多的时候是分布式的时候使用
>
>官网解释如下：Redisson是一个在Redis的基础上实现的Java驻内存数据网格（In-Memory Data Grid）。它不仅提供了一系列的分布式的Java常用对象，还提供了许多分布式服务。其中包括(BitSet, Set, Multimap, SortedSet, Map, List, Queue, BlockingQueue, Deque, BlockingDeque, Semaphore, Lock, AtomicLong, CountDownLatch, Publish / Subscribe, Bloom filter, Remote service, Spring cache, Executor service, Live Object service, Scheduler service) Redisson提供了使用Redis的最简单和最便捷的方法。Redisson的宗旨是促进使用者对Redis的关注分离（Separation of Concern），从而让使用者能够将精力更集中地放在处理业务逻辑上。

### 依赖

```
<!-- https://mvnrepository.com/artifact/org.redisson/redisson -->
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.18.0</version>
</dependency>

```



### 锁续约

> 在主进程中开一个线程来监听锁是否还持有，如果持有，继续添加持有时间，直到锁被释放































