# mysql常用变量

## mysql暂时去掉外键检查

由于外键关联无法删除，则可用下面的方法进行删除

```sql
SET FOREIGN_KEY_CHECKS = 0;

drop table ... ; 
delete from ... ; 

SET FOREIGN_KEY_CHECKS = 1;
```

