# mongoDB springboot

## 1，新增maven依赖

```markup
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

## 2，application.yml配置

```yaml
# mongoDB 配置
spring:
  data:
    mongodb:
      # mongodb://user:secret@server1:port1,server2:port2/集合名
      uri: mongodb://localhost:27017/c1
      
```

### 3，CRUD

### 新增一个POJO

```java
import org.springframework.data.mongodb.core.mapping.Document;
...
@Document("person")
public class Person {

  private String id;
  private String name;
  private int age;
  ...
}
```

### 继承MongoRepository进行

```java
import org.springframework.data.mongodb.repository.MongoRepository;
...
public interface PersonRepository extends MongoRepository<Person, String/*ID的类型*/> {
    List<Person> findByNameLike(String name);
}
```

### CRUD

```java
@Autowired
private PersonRepository personRepository;
@Autowired
private MongoTemplate mongoTemplate;
...

// 新增
Person p = ...
personRepository.save(p);
mongoTemplate.save(p);

// 修改
UpdateResult updateResult = mongoTemplate.updateMulti(new Query(Criteria.where("name").is("xm")),
                new Update().set("name", "xh"));
System.out.println(updateResult);

//

```









