title: 大数据计算服务 MaxCompute（原名ODPS）
author: zansy
tags: []
categories:
  - 读书摘录
date: 2018-07-01 09:52:00
---
是一种快速、完全托管的TB/PB级数据仓库解决方案。MaxCompute 提供了多种经典的分布式计算模型，能够更快速地解决海量数据计算问题。
<!--more-->
## 查看删除修改表
### 创建表
#### 语句
```
CREATE TABLE [IF NOT EXISTS] table_name
[(col_name data_type [COMMENT col_comment],…)]
[COMMENT table_comment]
[PARTITIONED BY (col_name data_type [COMMENT col_comment],…)]
[LIFECYCLE days]
[AS select_statement]
CREATE TABLE [IF NOT EXISTS] table_name
LIKE existing_table_name
```
#### 注意
- 大小写不敏感
- 如果指定 `if not exists`，无论是否存在同名表，即使原表结构与要创建的目标表结构不一致，均返回成功。已存在的原表信息不会被改动。
- Partitioned by：指定表的分区字段，目前仅支持String类型。当利用分区字段对表进行分区时，新增分区、更新分区内数据和读取分区数据均不需要做全表扫描，可以提高处理效率。
- lifecycle指明此表的生命周期，单位：天。`create table like`语句不会复制源表的生命周期属性。

#### 示例
- `create table test1 (key string) partitioned by (pt string, ds string) lifecycle 100` 创建分区表 字段名key，数据类型 string，带生命周期
- `create table test4 like test3;` 除生命周期属性外，test3 的其他属性（字段类型，分区类型等）均与 test4 完全一致 
- `create table test5 as select * from test2;`  创建 test5，但分区，生命周期信息不会被拷贝到目标表中。 此操作仅会将 test2 的数据复制到 test5 中。

#### 场景
假设需要创建一张用户表user，包括如下信息：
- user_id bigint类型：用户标识，唯一标识一个用户。
- gender bigint类型：性别（0，未知；1，男；2，女）。
- age bigint：用户年龄。

按照Region（区域）和dt（日期）进行分区，生命周期为365天。
```
CREATE TABLE user 
( user_id BIGINT, gender BIGINT COMMENT '0 unknow,1 male, 2 Female', age BIGINT) 
PARTITIONED BY (region string, dt string) LIFECYCLE 365;
```

### 创建分区
#### 语句
```
alter table table_name add [if not exists] partition partition_spec partition_spec: 
: (partition_col1 = partition_col_value1, partition_col2 = partiton_col_value2, ...)
```
#### 场景
给用户表user添加区域为hangzhou，日期为20150923的分区
```
Alter table user add if not exists partition(region='hangzhou',dt='20150923');
```

### 查看表信息
可以通过`desc <table_name>;`命令查看表的信息

### 删除分区
#### 语句
```
alter table table_name drop [if exists] partition_spec; partition_spec: 
: (partition_col1 = partition_col_value1, partition_col2 = partiton_col_value2, ...)
```
#### 场景
删除区域为hangzhou，日期为20150923的分区
```
Alter table user drop if exists partition(region='hangzhou',dt='20150923');
```
### 删除表
#### 语句
```
DROP TABLE [IF EXISTS] table_name;
```
## 运行 SQL
MaxCompute SQL就是用于查询和分析MaxCompute中的大规模数据。目前SQL的主要功能如下所示：
- 支持各类运算符。
- 通过DDL语句对表、分区以及视图进行管理。
- 通过Select语句查询表中的记录，通过Where语句过滤表中的记录。
- 通过Insert语句插入数据、更新数据。
- 通过等值连接Join操作，支持两张表的关联。支持多张小表的mapjoin。
- 支持通过内置函数和自定义函数来进行计算。
- 支持正则表达式。

注意：
- MaxCompute SQL不支持事务、索引及Update/Delete等操作，同时MaxCompute的SQL语法与Oracle，MySQL有一定差别。
- 在使用方式上，MaxCompute作业提交后会有几十秒到数分钟不等的排队调度，所以适合处理跑批作业，一次作业批量处理海量数据，不适合直接对接需要每秒处理几千至数万笔事务的前台业务系统。

### DDL 语句
简单的DDL操作包括创建表、添加分区、查看表和分区信息、修改表、删除表和分区

#### Select 语句
- group by语句的key可以是输入表的列名，也可以是由输入表的列构成的表达式，不可以是Select语句的输出列。
- order by必须与limit连用。
- sort by前必须加distribute by
- order by/sort by/distribute by的key必须是Select语句的输出列，即列的别名。
`select col2 as c from tbl order by col2 limit 100;`//不可以，order by的key不是select语句的输出列，即列的别名
`select col2 from tbl order by col2 limit 100;`//可以，当select语句的输出列没有别名时，使用列名作为别名。

### Insert 语句
- 向某个分区插入数据时，分区列不可以出现在Select列表中。
```
insert overwrite table sale_detail_insert partition (sale_date='2013', region='china')  
    select shop_name, customer_id, total_price, sale_date, region  from sale_detail;
```
报错返回，sale_date，region为分区列，不可以出现在静态分区的insert语句中。

- 动态分区插入时，动态分区列必须在Select列表中
```
insert overwrite table sale_detail_dypart partition (sale_date='2013', region) 
select shop_name,customer_id,total_price from sale_detail; 
```
失败返回，动态分区插入时，动态分区列必须在select列表中。

### Join 操作
- MaxCompute SQL支持的Join操作类型包括：{LEFT OUTER|RIGHT OUTER|FULL OUTER|INNER} JOIN。
- 目前最多支持16个并发Join操作。
- 在mapjoin中，最多支持8张小表的mapjoin。

### Union All
- Union All可以把多个Select操作返回的结果，联合成一个数据集。它会返回所有的结果，但是不会执行去重。
- MaxCompute不支持直接对顶级的两个查询结果进行Union操作，需要写成子查询的形式。

#### 说明
- Union All连接的两个Select查询语句，两个Select的列个数、列名称、列类型必须严格一致。
- 如果原名称不一致，可以通过别名设置成相同的名称。

### SQL 优化示例
#### Join 语句中 Where 条件的位置
当两个表进行Join操作时，主表的Where限制可以写在最后，但从表分区限制条件不要写在Where条件中，建议写在ON条件或者子查询中。主表的分区限制条件可以写在Where条件中（最好先用子查询过滤）。
```
select * from A join (select * from B where dt=20150301)B on B.id=A.id where A.dt=20150301； 
select * from A join B on B.id=A.id where B.dt=20150301； --不允许。会先Join，后进行分区裁剪，数据量变大，性能下降。
select * from (select * from A where dt=20150301)A join (select * from B where dt=20150301)B on B.id=A.id；
```

#### 数据倾斜
产生数据倾斜的根本原因是有少数Worker处理的数据量远远超过其他Worker处理的数据量，从而导致少数Worker的运行时长远远超过其他的平均运行时长，从而导致整个任务运行时间超长，造成任务延迟。

##### Join 造成的数据倾斜
- 造成 Join 数据倾斜的原因是 Join on 的 key 分布不均匀。
- 如果在语句`select * from A join B on A.value= B.value;`中出现该情况，可做如下优化：


1. 由于表B是个小表并且没有超过512MB，可将上述语句优化为mapjoin语句再执行：`select /*+ MAPJOIN(B) */ * from A join B on A.value= B.value;`
2. 也可将倾斜的key用单独的逻辑来处理，例如经常发生两边的key中有大量null数据导致了倾斜。则需要在Join前先过滤掉null的数据或者补上随机数，然后再进行Join：
```
select * from A join B 
on case when A.value is null then concat('value',rand() ) else A.value end = B.value;
```

##### group by倾斜
- 造成group by倾斜的原因是group by的key分布不均匀。
- 假设表A内有两个字段（key，value），表内的数据量足够大，并且key的值分布不均，运行语句：`select key,count(value) from A group by key;`
- 解决：需要在执行SQL前设置防倾斜的参数，设置语句为`set odps.sql.groupby.skewindata=true`。

##### 错误使用动态分区造成的数据倾斜
- 动态分区的SQL，在MaxCompute中会默认增加一个Reduce，用来将相同分区的数据合并在一起。这样做的好处：


1. 可减少MaxCompute系统产生的小文件，使后续处理更快速。
2. 可避免一个Worker输出文件很多时占用内存过大。

但也正是因为这个Reduce的引入，导致分区数据如果有倾斜的话，会发生长尾。因为相同的数据最多只会有10个Worker处理，所以数据量大，则会发生长尾。
```
insert overwrite table A2 partition(dt) 
select
split_part(value,'\t',1) as field1,
split_part(value,'\t',2) as field2, 
dt 
from A 
where dt='20151010';
```

- 这种情况下，没有必要使用动态分区，所以可以改为如下语句：
```
insert overwrite table A2 partition(dt='20151010') 
select
split_part(value,'\t',1) as field1,
split_part(value,'\t',2) as field2
from A 
where dt='20151010';
```

##### 窗口函数的优化
- 如果SQL语句中用到了窗口函数，一般情况下每个窗口函数会形成一个Reduce作业。如果窗口函数略多，那么就会消耗资源。在某些特定场景下，窗口函数是可以进行优化的。


1. 窗口函数over后面要完全相同，相同的分组和排序条件。
2. 多个窗口函数在同一层SQL执行。

符合上述两个条件的窗口函数会合并为一个Reduce执行。示例：
```
select
rank()over(partition by A order by B desc) as rank,
row_number()over(partition by A order by B desc) as row_num
from MyTable;
```

##### 子查询改Join
- 假设存在子查询`SELECT * FROM table_a a WHERE a.col1 IN (SELECT col1 FROM table_b b WHERE xxx);`
- 当此语句中的 table_b 子查询返回的 col1 的个数超过1000个时，系统会报错为 `records returned from subquery exceeded limit of 1000`
- 可以使用Join语句来代替：`SELECT a.* FROM table_a a JOIN (SELECT DISTINCT col1 FROM table_b b WHERE xxx) c ON (a.col1 = c.col1)`