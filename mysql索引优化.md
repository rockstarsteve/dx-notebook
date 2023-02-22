# Mysql索引优化

## Mysql索引

> 索引是帮助高效获取数据的一种数据结构

mysql索引分类：

- 主键索引
- 单值索引
- 唯一索引
- 复合索引



## 性能分析

> sql中对大量数据进行比较、关联、排序、分组时CPU的瓶颈
>
> 实例内存满足不了缓存数据或排序等需要，导致产生大量的物理IO。查询数据扫描过多，导致查询效率低。

### explain

>使用EXPLAIN关键字可以模拟优化器执行SOL 查询语句,从而知道MYSQL 是如何处理SQL语句的。可以用来分析查询语句或是表的结构的性能瓶颈。

**其作用:**

1. 表的读取顺序

2. 哪些索引可以使用

3. 数据读取操作的操作类型

4. 那些索引被实际使用

5. 表之间的引用

6. 每张表有多少行被优化器查询

EXPLAIN 关键字使用起来比较简单:explain + SOL 语句

| id   | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra |
| ---- | ----------- | ----- | ---- | ------------- | ---- | ------- | ---- | ---- | ----- |
|      |             |       |      |               |      |         |      |      |       |

id:

> id相同时，执行顺序是从上往下;不同，id越大越先被执行

select_type：

> SIMPLE：简单的select查询，查询中不包含子查询或者UNION
>
> PRIMARY：查询中若包含任何复杂的子部分，最外层查询则被标记为primary
>
> DERIVED：在FROM列表中包含的子查询被标记为DERIVED(衍生),MySQL会递归执行这些子查询，把结果放在临时表里。
>
> SUBQUERY:在SELECT 或 WHERE 列表中包含了子查询。

table：

type：

> system:表只有一行记录 (等于系统表)这是 const 类型的特列，平时不会出现，这个也可以忽略不计。
>
> Const: 表示通过索引一次就找到了,const 用于比较 primary key 或者 unique 索引。因为只匹配一行数据，所以很快，如将主键置于 where 列表中，MySOL 就能将该查询转换为个常量。
>
> eq_ref: 唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描
>
> Ref:非唯一性索引扫描，返回匹配某个单独值的所有行。本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而，它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体。
>
> Range: 只检索给定范围的行,使用一个索引来选择行。key 列显示使用了哪个索引般就是在你的 where 语中出现了 between、<、>、in 等的查询这种范围扫描索引扫描比全表扫描要好，因为它只需要开始于索引的某一点，而结束语另一点，不用扫描全部索引。
>
> Index: Full Index Scan，index与ALL 区别为 index 类型只遍历索引树。这通常比ALL快，因为索引文件通常比数据文件小。也就是说虽然 al1和Index 都是读全表，但 index是从索引中读取的，而a11 是从硬盘中读的。
>
> All: Full Table Scan，将遍历全表以找到匹配的行。

从最好到最差依次是:system>const>eq_ref>ref>range>index>A11。一般来说，最好保证查询能达到 range 级别，最好能达到 ref。

possible_keys：

> 显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上如果存在索引，则改索引将会被列出来，但不一定会被查询实际使用上。

key：

> 显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上如果存在索引，则改索引将会被列出来，但不一定会被查询实际使用上。

key_len：

> 使用索引的字节数，越大表示使用索引越充分

ref：

> 显示索引的哪一列被使用了。哪些列或常量被用于查找索引列上的值

rows：

>rows 列显示 MySQL 认为它执行查询时必须检查的行数。一般越少越好

Extra：

> 些常见的重要的额外信息:
>
> Using filesort: MySQL 无法利用索引完成的排序操作称为“文件排序”。
>
> Using temporary: Mysql在对查询结果排序时使用临时表，见于排序order by 和分组查询 group by。
>
> Using index: 表示索引被用来执行索引键值的查找，避免访问了表的数据行，效率不错
>
> Using where: 表示使用了 where 过滤





## 查询优化

### 单表查询优化

**索引匹配规则：**

1. 最佳左前缀法则: 如果索引了多列，要遵循最左前缀法则，指的是查询从索引的最左前列开始并且不跳过索引中的列。
2. 不在索引列上做任何计算、函数操作，会导致索引失效而转向全表扫描
3. 存储引擎不能使用索引中范围条件右边的列。
4. Mysq1在使用不等于时无法使用索引会导致全表扫描
5. is null 可以使用索引，但是 is not null 无法使用索引。
6. like 以通配符开头会使索引失效导致全表扫描
7. 字符串不加单引号索引会失效

**建议:**

1. 对于单值索引,尽量选择针对当前查询字段过滤性更好的索引。
2. 对于组合索引当前 where 查询中过滤性更好的字段在索引字段顺序中位置越靠前越好。
3. 对于组合索引尽量选择能够包含在当前查询中 where 子句中更多字段的索引。
4. 尽可能通过分析统计信息和调整 query的写法来达到选择合适索引的目的



### 关联查询优化





### 排序优化

1. 尽量避免使用 Using FileSort 方式排序

2. order by 语句使用索引最左前列或使用 where 子句与order by 子句条件组合满足索引最左前列。

3. where 子句中如果出现索引范围查询会导致 order by 索失效




### 分组优化





## 使用存储过程添加测试数据

```
-- 建表
DROP TABLE IF EXISTS person;
CREATE TABLE person (
    PID int(11) AUTO_INCREMENT COMMENT '编号',
    PNAME varchar(50) COMMENT '姓名',
    PSEX varchar(1) COMMENT '性别',
    PAGE int(11) COMMENT '年龄',
    SAL decimal(7,2) COMMENT '工资',
    PRIMARY KEY (PID)
);


-- 创建存储过程
create procedure insert_person(in max_num int(10))
begin
    declare i int default 0;
    set autocommit = 0;
    repeat
    set i = i + 1;
    insert into person (PID,PNAME,PSEX,PAGE,SAL) values (i,concat('test',floor(rand()*10000000)),IF(RAND()>0.5,'男','女'),FLOOR((RAND()*100)+10),FLOOR((RAND()*19000)+1000));
    until i = max_num
		end repeat;
    commit;
end;

-- 调用存储过程
call insert_person(3000000);

```









