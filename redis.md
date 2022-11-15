

# redis

> 单线程，排队处理请求任务.

## 和spring整合

- mavne仓库地址：

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

> 注意：Springboot2后默认使用Lettuce作为访问redis的客户端

> 旧版本lettuce存在堆外内存溢出的bug， 5.3版本修复了这个bug, 还有 6.1 

- application.yml配置（如果使用jedis只要加下客户端就行，对应的客户端添加依赖）

```
spring:
  redis:
    host: localhost
    port: 6379
```

- StringRedisTemplate和RedisTemplate

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


- 



























