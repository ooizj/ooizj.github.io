# mysql常用函数

## 查询自增长字段insert之后的值

LAST\_INSERT\_ID\(\)  
返回的是相对于当前connection的，所以不用担心并发问题

```sql
insert into tb(...) values(...);
set 新增的值 = LAST_INSERT_ID() ;
```

