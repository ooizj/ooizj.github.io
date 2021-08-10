# mongoDB structure

## MongoDB与SQL描述上的区别

| SQL术语/概念 | MongoDB 术语/概念 |
| :--- | :--- |
| database | [database](https://docs.mongodb.com/manual/reference/glossary/#term-database) |
| table | [collection](https://docs.mongodb.com/manual/reference/glossary/#term-collection) |
| row | [document ](https://docs.mongodb.com/manual/reference/glossary/#term-document)或 [BSON ](https://docs.mongodb.com/manual/reference/glossary/#term-bson)document |
| column | [field](https://docs.mongodb.com/manual/reference/glossary/#term-field) |
| index | [index](https://docs.mongodb.com/manual/reference/glossary/#term-index) |
| table joins （表联接） | [$lookup ](https://docs.mongodb.com/manual/reference/operator/aggregation/lookup/#pipe._S_lookup), `embedded documents （嵌入式文档）` |
| primary key 指定任何唯一的列或者列组合作为主键 | [primary key ](https://docs.mongodb.com/manual/reference/glossary/#term-primary-key)在 MongoDB 中, 主键自动设置为 [\_id ](https://docs.mongodb.com/manual/reference/glossary/#term-id)字段 |
| aggregation \(如：group by\) | `aggregation pipeline （聚合管道）`参考： [SQL to Aggregation Mapping Chart](https://docs.mongodb.com/manual/reference/sql-aggregation-comparison/) |
| SELECT INTO NEW\_TABLE | [$out ](https://docs.mongodb.com/manual/reference/operator/aggregation/out/#pipe._S_out)参考： [SQL to Aggregation Mapping Chart](https://docs.mongodb.com/manual/reference/sql-aggregation-comparison/) |
| MERGE INTO TABLE | [$merge ](https://docs.mongodb.com/manual/reference/operator/aggregation/merge/#pipe._S_merge)（从MongoDB 4.2开始可用） 参考： [SQL to Aggregation Mapping Chart](https://docs.mongodb.com/manual/reference/sql-aggregation-comparison/) |
| transactions | [transactions](https://docs.mongodb.com/manual/core/transactions/) |

## BSON值支持的类型

| Type | Number | Alias | Notes |
| :--- | :--- | :--- | :--- |
| Double | 1 | "double" |  |
| String | 2 | "string" |  |
| Object | 3 | "object" |  |
| Array | 4 | "array" |  |
| Binary data | 5 | "binData" |  |
| Undefined | 6 | "undefined" | Deprecated. |
| ObjectId | 7 | "objectId" |  |
| Boolean | 8 | "bool" |  |
| Date | 9 | "date" |  |
| Null | 10 | "null" |  |
| Regular Expression | 11 | "regex" |  |
| DBPointer | 12 | "dbPointer" | Deprecated. |
| JavaScript | 13 | "javascript" |  |
| Symbol | 14 | "symbol" | Deprecated. |
| JavaScript code with scope | 15 | "javascriptWithScope" | Deprecated in MongoDB 4.4. |
| 32-bit integer | 16 | "int" |  |
| Timestamp | 17 | "timestamp" |  |
| 64-bit integer | 18 | "long" |  |
| Decimal128 | 19 | "decimal" | New in version 3.4. |
| Min key | -1 | "minKey" |  |
| Max key | 127 | "maxKey" |  |

