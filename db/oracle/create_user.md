## 创建新用户、创建表空间、授权示例


### 创建用户
```sql
create user NEW_USER_NAME identified by NEW_USER_NAME;

-- 如果返回“ORA-65096: 公用用户名或角色名无效”，需要先执行以下语句
alter session set "_ORACLE_SCRIPT"=true;  

```

### 授权
```sql
-- grant all privileges to NEW_USER_NAME;
grant connect, resource to NEW_USER_NAME;
grant select any table to NEW_USER_NAME;
GRANT CREATE VIEW TO NEW_USER_NAME;
```

### 创建表空间并分配给新用户
```sql
CREATE TABLESPACE NEW_USER_NAME_data DATAFILE 'tbs_NEW_USER_NAME_data.dat' SIZE 100M AUTOEXTEND ON;
alter user NEW_USER_NAME default tablespace NEW_USER_NAME_data;  
alter user NEW_USER_NAME quota unlimited on NEW_USER_NAME_data;
```
