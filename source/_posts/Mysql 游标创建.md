---
title: Mysql 游标创建
date: 2019-2-19 15:14
tags: Mysql、游标
---
 # Mysql 游标创建

- 需求：分表数据。 把一张表的数据根据需求分离，创建不同的表 并写入数据。

```mysql
drop procedure if exists dataMove; /*删除已有的存储过程*/
create procedure dataMove()
begin
    declare tablename_fix varchar(64); /*定义表的尾号*/
   declare flag boolean  default true;/*判断游标是否结束*/
    
   declare fix_cursor cursor for 
    select fix from tablea group by fix; /*定义游标 把table_suffix列分组出来 放到游标中*/
        
   declare continue handler for not found set flag = false;/*游标结束时 标识改为false*/
    open fix_cursor;/*打开游标*/
    fetch fix_cursor into tablename_fix; /*把游标里的数据取出来放到这个变量中*/
    while flag do
       set @tablename = concat('tablea',tablename_fix);/*concat() 拼接方法 就是+''+， 表名 原始名字+尾号列*/
        /*根据表名创建表，把对应满足的数据放到创建的表中 如果已经有了表 就得改方式*/
        set @sqlstr =concat('create table ', @tablename,'( SELECT * FROM tablea where fix=',tablename_fix,');'); 
        PREPARE STMT FROM @sqlstr; /*这三句执行销毁sql*/
        EXECUTE STMT;   
        DEALLOCATE PREPARE STMT;
   
   fetch fix_cursor into tablename_fix; /*游标指针往下一行*/
    
   end while;
    close fix_cursor;
end;

/*调用*/
call dataMove()
```




效果

![img](http://tnblog.net/arcimg/cz/45356b7a439a432fb91dc9bc391fb558.png)

tablea主表，

![img](http://tnblog.net/arcimg/cz/459c954ffd1042e388e72c195a73fbe6.png)



12345是游标自动创建的

