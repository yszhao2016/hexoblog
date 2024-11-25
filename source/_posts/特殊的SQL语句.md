---
title: 特殊的SQL语句
abbrlink: ee8708d7
categories:
  - mysql
date: 2023-11-08 17:54:07
tags:
---

# 使用逗号分割拆分

查询出field字段中有value的记录
SELECT * FROM table WHERE FIND_IN_SET('value', field)>0
    
 FIND_IN_SET('value', field)<font color=Red>>0</font>  查询的是字段中<font color=Red>包含了</font>查询字符value的
 FIND_IN_SET('value', field)<font color=Red>=0</font>   查询的是字段中<font color=Red>不包含</font>了查询字符value的
 
 
 
# SUBSTRING_INDEX 函数
 
 
 
string：用于截取目标字符串的字符串。可为字段，表达式等。

sep：分隔符，string存在且用于分割的字符，比如“，”、“.”等。

num：序号 <font color=Red>负数 从右边取   正数从左边取 取几个值</font>
 
示例 field  a1,b2,c3  
SUBSTRING_INDEX(field, ',', 1) 取值就是 a1
<font color=Red>SUBSTRING_INDEX(field, ',', 2) 取值就是 a1,b2</font>

<font color=Red>根据，分割拆分多条数据sql：</font>

    SELECT SUBSTRING_INDEX(field, ',', 1) AS value1,
        SUBSTRING_INDEX(SUBSTRING_INDEX(field, ',', 2), ',', -1) AS value2,
        SUBSTRING_INDEX(SUBSTRING_INDEX(field, ',', 3), ',', -1) AS value3
        SUBSTRING_INDEX(field, ',', -1) AS value4
    FROM table
 
## 根据，隔开字段 分组统计


根据，隔开字段拆分成多行数据 多会拆分成3行了

SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(comInputField163, ',',numbers.n), ',', -1) AS tag ,numbers.n,a.*
      #,COUNT(*) AS count
FROM mida_gd_2023101116015948 a
JOIN (
  SELECT 1 AS n UNION ALL SELECT 2 UNION ALL SELECT 3                                   <font color=Red>#这边SELECT 数 根据，隔开最多项来，写到SELECT 5只有数据中只有2项它也要关联5个出来</font>
) AS numbers
WHERE CHAR_LENGTH(comInputField163) - CHAR_LENGTH(REPLACE(comInputField163, ',', '')) >= numbers.n-1  #通过这个来限制,<font color=Red>统计，数量</font> 判断需要几个
GROUP BY tag;
 
 
# 两表连接的SQL语句


select A.c1,A.c2,B.c1,B.c2
from table1 A,table2 B
where A.id=B.id

select A.c1,A.c2,B.c1,B.c2
from table1 A join table2 B
on A.id=B.id

select A.c1,A.c2,B.c1,B.c2
from table1 A inner join table2 B
on A.id=B.id


join on与inner join on是相同

默认JOIN就是指的INNER JOIN

内连接推荐 join 方式 通过where 的方式 性能较差
有的数据库底层 认为是交叉 连接

