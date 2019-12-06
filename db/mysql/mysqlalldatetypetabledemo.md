# mysqlAllDataTypeTableDemo

mysql all datatype table 

包含了大部分数据类型字段的mysql测试用表

```sql
CREATE TABLE `mysqlalltype` (
  `id` int(11) NOT NULL,
  `testInt` int(11) DEFAULT NULL,
  `testVarchar_` varchar(100) DEFAULT NULL,
  `testDecimal` decimal(9,2) DEFAULT NULL,
  `testDatetime` datetime DEFAULT NULL,
  `testBlob` blob,
  `testBinary` binary(255) DEFAULT NULL,
  `testBlob_` blob,
  `testLongBlob` longblob,
  `testMediumBlob` mediumblob,
  `testTinyBlob` tinyblob,
  `testVarBinary_` varbinary(1000) DEFAULT NULL,
  `testDate` date DEFAULT NULL,
  `testTime` time DEFAULT NULL,
  `testTimestamp_` timestamp(6) NULL DEFAULT NULL,
  `testYear_` year(4) DEFAULT NULL,
  `testGeometry` geometry DEFAULT NULL,
  `testGeometrycollection` geometrycollection DEFAULT NULL,
  `testLinestring` linestring DEFAULT NULL,
  `testMultiLinestring` multilinestring DEFAULT NULL,
  `testMultiPoint` multipoint DEFAULT NULL,
  `testMutiPolygon` multipolygon DEFAULT NULL,
  `testPoint` point DEFAULT NULL,
  `testPolygon` polygon DEFAULT NULL,
  `testBigint_` bigint(20) DEFAULT NULL,
  `testDouble` double DEFAULT NULL,
  `testFloat` float DEFAULT NULL,
  `testInt_` int(11) DEFAULT NULL,
  `testMediumInt_` mediumint(11) DEFAULT NULL,
  `testReal` double DEFAULT NULL,
  `testSmallInt_` smallint(11) DEFAULT NULL,
  `testTinyInt_` tinyint(11) DEFAULT NULL,
  `testChar_` char(10) DEFAULT NULL,
  `testJson` json DEFAULT NULL,
  `testNchar_` char(10) DEFAULT NULL,
  `testNvarchar_` varchar(1000) DEFAULT NULL,
  `testLongText` longtext,
  `testMediumText` mediumtext,
  `testText_` text,
  `testTinyText` tinytext,
  `testBit_` bit(10) DEFAULT NULL,
  `testEnum_` enum('A','B') DEFAULT NULL,
  `testSet_` set('A','B') DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

```

