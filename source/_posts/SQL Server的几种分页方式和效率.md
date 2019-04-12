---
title: SQL Server的几种分页方式和效率 
date: 2018/11/23 13:36
tags: SQLServer、分页
---
 # SQL Server的几种分页方式和效率

```sql
--top not in方式
select top 条数 *  from tablename
where Id not in (select top 条数*页数  Id from tablename)
 
 
 
--ROW_NUMBER() OVER()方式 
 select * from ( 
　　　　select *, ROW_NUMBER() OVER(Order by Id ) AS RowNumber from tablename
　　) as b
　　where RowNumber BETWEEN 当前页数-1*条数 and 页数*条数   
 
 
 
--offset fetch next方式
--SQL2012以上的版本才支持
select * from tablename
 order by Id offset 页数 row fetch next 条数 row only
```

分析：在数据量较大时

**top not in**方式：查询靠前的数据速度较快，不推荐not in

**ROW_NUMBER() OVER()**方式：查询靠后的数据速度比上一种较快，sql 2005以上可用

**offset fetch next**方式：速度稳定，优于前2种，sql2012及以上可用

