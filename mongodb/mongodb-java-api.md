# mongoDB java API

## 1，添加maven依赖

```markup
<dependency>
<groupId>org.mongodb</groupId>
<artifactId>mongo-java-driver</artifactId>
<version>${mongodb.version}</version>
</dependency>
```

## 2，连接

```java
ServerAddress seed1 = new ServerAddress("localhost", 27017);
        MongoClientSettings settings = MongoClientSettings.builder()
                .applyToClusterSettings(builder -> builder.hosts(Arrays.asList(seed1)))
                .build();
MongoClient mongoClient = MongoClients.create(settings);
```

## 3，CRUD

```java
// 获取数据库
MongoDatabase db = mongoClient.getDatabase("db1");

// 获取集合
MongoCollection<Document> collection = db.getCollection("c1");

// 新增测试
collection.insertOne(new Document()
        .append("name", "xiaoming")
        .append("age", 16)
        .append("sex", "F"));
        
// 修改测试
collection.updateOne(
        Filters.eq("sex", "F"),
        new Document("$set",
            new Document()
            .append("age", 155)
            .append("sex", "M")));

// 删除测试

// 根据sex=F进行删除
collection.deleteMany(Filters.eq("sex", "F"));
        
// 根据name=xiaoming进行删除
collection.deleteMany(new Document().append("name", "xiaoming")); 

// 遍历
for (String collectionName: db.listCollectionNames()) {
    System.out.println("集合名称："+collectionName);
    MongoCollection<Document> collection = db.getCollection(collectionName);
    for (Document document: collection.find()) {
        System.out.println("集合里面的数据："+document);
    }
}                       
```



