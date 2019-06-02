title: 数据库简单笔记
author: zansy
tags: []
categories:
  - 读薄专业书计划
date: 2018-06-30 22:21:00
---
整理自《数据库系统基础教程》
<!--more-->
## 概述
- 通常将一个或一组数据库操作组成一个事务。正确实现的事务通常满足`ACID性质`（原子性 atomicity、独立性 isolation、持久性 durability、一致性 consistency）

## 基本命令
- 创建表：`create table name(attribute1 datatype1, attribute2 datatype2);`
- 删除表：`drop table name;`
- 修改表：`alter table name` 后可以跟 `add attribute3 datatype3;`或者`drop arribute2;`
- 两种声明键的方法：


1. create table name(attribute1 datatype1 `primary key`, attribute2 datatype2);
2. create table name(attribute1 datatype1 , attribute2 datatype2, `primary key(arribute1)`);

## 代数
- 一门代数由一些操作符和一些原子操作数组成的。
- 常见的操作：


1. 通常的关系操作：并∪、交∩、差-
2. 除去某些行或列：选择、投影
3. 组合两个关系元组：笛卡尔积
4. 重命名 ρ dbname(attributename)


- **投影**：π属性1、属性2（表名），筛选出表中的相关列 
- **选择**：σ条件（表名） 筛选出符合条件的元组，如σlength>100(movies)   
- **笛卡尔积 x**：结果关系模式是两关系模式的并
![笛卡尔积](http://www.dbhom.com/upload/2012-08/12082704209618.jpg)

- **自然连接⋈**：仅仅把两模式中某共同属性中具有相同的值的元组配对
![自然连接](http://ok43olqzw.bkt.clouddn.com/18-3-4/56059751.jpg)

- **θ连接⋈c**：


1. 先得到两表的笛卡尔积
2. 从中筛选符合条件c的元组
![θ连接](/images/pasted-20.png)

## 设计理论
- **函数依赖 Functional Dependency**：ABC—>DEF（如果两元组在属性 ABC 上一致，则它们在 DEF 上也一致）
- **超键**：一个包含键的属性集
- **分解/结合规则**：ABC—>DEF 等价于 ABC—>D，ABC—>E，ABC—>F。
- **平凡函数依赖**：如果关系上的一个约束对所有关系实例都成立，且与其他约束无关，则称其为平凡的。平凡FD的右边是左边的子集。如 ABC—>A 
- **传递规则**：ABC—>DEF，DEF—>GHI，则 ABC—>GHI
- **Boyce-Codd 范式（BCNF）**：每个非平凡 FD 的左边必须包含键（不一定是全部的键），即：若关系中的非平凡 FD 指明某个超键函数决定一个或其他多个属性，则该关系属于 BCNF
- **第三范式**：对于每个非平凡 FD，或者其左边是超键，或者其右边仅由主属性构成

## 代数和逻辑查询语言
- **外连接**：连接操作的一个性质就是有可能产生`悬浮元组`，即这些元组的连接属性不能跟另外关系的任何一个元组的连接属性匹配，因此产生外连接


1. 先进行笛卡尔积操作
2. 然后再把来自两个关系的悬浮元组加入其中
3. 加入的元组用 null  符号补齐那些出现在结果中但不具有值的属性

![表A](http://www.xker.com/xkerfiles/allimg/1207/11302UD1-0.jpg)![表B](http://www.xker.com/xkerfiles/allimg/1207/11302UZ3-1.jpg)

![完全外连接](http://www.xker.com/xkerfiles/allimg/1207/11302T123-4.jpg)

- **左外连接**：类似于外连接，但是只有左变量的悬浮元组被补齐
![左外连接](http://www.xker.com/xkerfiles/allimg/1207/11302RN1-2.jpg)

- **右外连接**：只有右变量的悬浮元组被补齐
![右外连接](http://www.xker.com/xkerfiles/allimg/1207/11302WD6-3.jpg)

- **θ外连接**：


1. 先进行θ连接（笛卡尔积后筛选符合条件的）
2. 将不能匹配其他关系的元组用 null 补齐

## SQL 语言
- **投影**：`select attribute as otherName from tableName`
- **选择**：`select attribute1 from tableName where attribute2=condition`//比较运算符`=`等于 `<>`不等于

- 模式匹配：`attribute like pattern`


1. `_`匹配一个：`where title like 'STAR __'`
2. `%`匹配任意长度的字符 `where title like '%''s%'` //查询名中有's的标题 （注意：字符串中两个连续的单引号表示一个单引号）
3. 转义字符：`s like 'x%%x%' escape 'x'` 通过escape，则 x% 表示 %


- 输出排序：`order by <list of attributes>` 后可跟`DESC`表示降序，没有则默认升序
- 积和连接：`from table1, table2 where …`
- 交并差：`union` `intersect` `except` 例如：`(select title from movie) union (select movieTitle as title from Star)`
- 子查询：


1. `exists R`当且仅当 R 非空时为真
2. `s in R`当且仅当 s 为 R 中的一个值时为真
3. `s > ALL R` 当且仅当 s 大于 R 中的任何一个值
4. `s > ANY R` 当且仅当 s 至少大于 R 中的某个值

例如：
```
select title
from movies old
where year < ANY
	(select year
	from movies
	where title = old.title
	);
```
- from 中也可以使用子查询，例如
```
select name
from MovieExec, (select producerC#
				from movies, StarIn
				where title = movieTitle and
				starName = 'RF'
				)Prod
where cert# = Prod.producerC#;
```

- **自然连接**：对两个关系中具有相同名字并且其值相同的属性作连接，除此之外再没有其他的条件 `Movies NATURAL JOIN StarIn;`
- **积**：`Movies CROSS JOIN StarIn;`
- **θ连接**：`Movies JOIN StarIn ON title = movieTitle;`
- **外连接**：`Movies NATURAL FULL OUTER JOIN StarIn;` 想要改成左右外连接则将 full 关键字改掉就行了
- **消除重复**：希望结果中不出现重复的元组 可以在保留字`select`后跟上`distinct`
- **五个聚集操作符**：`SUM` `AVG` `MIN` `MAX` `COUNT`
例如：找出所有电影制片人资产的平均值
```
select AVG(netWorth)
from MovieExec;
```

- **分组**：保留字`group by`后面跟着一个分组属性列表
例如：从关系 Movies 中找出每个电影公司制作的电影总长度
```
select studioName, SUM(length)
from Movies
group by studioName;
```

- **HAVING 子句**：如果需要对基于某些分组聚集的性质选择分组，可以在 GROUP BY 子句后面加上一个 HAVING 子句
例如：输出至少曾在 1930 以前制作过一部电影的制片人制作的电影总长度
```
select name, SUM(length)
from MovieExec, Movies
where producerC# = cert#
group by name
having MIN(year) < 1930
```

- **插入**：`insert into StarsIn(title, year, starName) values('TNF', 1942, 'BG')`
- **删除**：`delete from R where <c>`
- **修改**：`update MovieExec SET name = 'Pre' || name WHERE cert# in (SELECT preC# FROM studio)`//其中`||`运算符表示字符串的连接，效果是把'Pre'加在了 name 字段的旧值前面
- **声明外键**：


1. 适用于单个属性：可以在 create table 语句中，在此属性的名字和类型之后，声明其引用某个表的某个属性（被引用的属性必须有主键或唯一性声明）。例如：`create table Studio(preC# INT REFERENCES MoviesExec(cert#) );`
2. 适用于多值：可以在 create table 语句的属性列表上追加一个或多个声明，来说明一组属性是一个外键，然后给出外键引用的表和属性（这些属性必须是键）。例如：`create table Studio(preC# INT, FOREIGN KEY (preC#) REFERENCES MovieExec(cert#) );`