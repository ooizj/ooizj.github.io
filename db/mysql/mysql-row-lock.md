# mysql行级锁

添加行级锁的方法

```sql
select ... for update ;
```

假设在事物A中执行了“select ... for update”，对应行“R”：  
case 1，事物B中也执行了“select ... for update”，也对应行“R”，那事物B就会在此查询处就如等待，只能等待事物A提交后，事物B才能获取到查询的返回结果。  
case 2，事物B中执行了“update ...”，也对于行“R”，则同上  
case 3，事物B执行了“select ...”，也对于行“R”，不会等待，能直接能获取查询结果

e.g.

```sql
--在事物提交前，表“t1”中“id=1”的这条记录其他事物是不能访问的
select from t1 where id = 1 for update ; 

--在事物提交前，表“t1”中“id>0 & id < 5”的记录（多条）其他事物是不能访问的
select from t1 where id > 0 and id < 5 for update ; 

--下面的操作会锁住“t1”和“t2”两张表的满足条件的记录（可以是多条）
select from t1 left join t2 on(t1.id = t2.relatedid) where t2.name = 'ff' and t2.phone='176xxx' for update
```

