# SQL-Tips



## 1 重复数据保留一条

```mysql
delete from goods_country
where (goods_no,country) in (
    select a.goods_no, a.country from (
    select goods_no, country from goods_country a group by goods_no, country  having count(*)>1
    ) a
)
and tid not in (
    select b.tid from (
        select min(tid) as tid from goods_country group by goods_no,country having count(*)>1
    ) b
);
```





## 2 存储过程，生成自增值

```mysql
delimiter ;;
CREATE PROCEDURE proc_batch_insert()
BEGIN
DECLARE i BIGINT;
SET i= 20200481953;
WHILE i < 20200492953 DO

    INSERT INTO `test`.`xxxx` (`tid`) VALUES (i);
    SET i=i+1;
END WHILE;
END ;;
delimiter ;  
call proc_batch_insert();  
```



## 3 插入重复问题

```mysql
insert ignore into table

INSERT INTO XXX ON DUPLICATE KEY UPDATE
```

- ignore：只关注主键对应记录是不存在，无则添加，有则忽略
- ON DUPLICATE KEY UPDATE：有则更新指定列，无则添加



## 4 统计每天单量

```mysql
-- 日均单量 365天

select count(DISTINCT order_no)/365 AS 日均单量
from order_info
where create_time >= '2020-01-01'
and status='C'


-- 每天单量
select DATE_FORMAT(create_time,'%Y-%m-%d') 日期
  ,count(DISTINCT order_no) 每天单量
from order_info
where create_time >= '2020-01-01' and status='C'
group by DATE_FORMAT(create_time,'%Y-%m-%d')


-- 按月统计
select 
  month(create_time) AS 月份
  ,count(DISTINCT order_no) AS 单量
from order_info
where create_time >= '2020-01-01' and status='C'
group by month(complete_time)

```



## 5 GROUP_CONCAT

GROUP_CONCAT将组中的字符串连接成为具有各种选项的单个字符串。

注意：当内容达到group_concat_max_len 时，会进行截取，默认是1024

如果单纯的连接，可以用CONCAT函数，比如写一个插入或者更新数据的SQL脚本时

```mysql
INSERT INTO t(v) VALUES('A'),('B'),('C'),('B');

+---------------------------------------------------------------------+
| GROUP_CONCAT(DISTINCT v
        ORDER BY v ASC
        SEPARATOR ';') |
+---------------------------------------------------------------------+
| A;B;C                                                               |
+---------------------------------------------------------------------+
1 row in set
```



## 6 \G

表示将查询结果进行按列打印，可以使每个字段打印到单独的行。即**将查到的结构旋转90度变成纵向；**

**注**：navicat不支持\G

```mysql
mysql> use test;
Database changed
mysql> select * from user\G
*************************** 1. row ***************************
      id: 1
 user_id: 6001
username: MF00001
    desc: 轻音少女- K-ON!
*************************** 2. row ***************************
      id: 2
 user_id: 6002
username: MF00002
    desc: NULL
*************************** 3. row ***************************
      id: 3
 user_id: 6003
username: MF00003
    desc: NULL
*************************** 4. row ***************************
      id: 4
 user_id: 6004
username: MF99999
    desc: NULL
4 rows in set (0.00 sec)
```



## 7 select 1

select 1返回成功， 说明数据库的进程还在



## 8 字符串截取的坑

如果substring是从0开始，则最后为空，应该从1开始

当然，也可以用right，left这些函数

```mysql
 update user set username = substring(username,1 ,5);
 
```

