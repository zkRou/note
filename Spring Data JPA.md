# Spring Data JPA

> 是Spring基于Hibernate开发的一个JPA框架。

#### 工程依赖
```pom
<dependency
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

#### 工程配置
```xml
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

spring.jpa.properties.hibernate.hbm2ddl.auto=create-drop //自动创建表结构设置
```

#### JpaRespository
通过编写一个继承自`JpaRespository`的接口就能完成数据访问。`JpaRespository`接口本身已经实现了创建(save)、更新(save)、删除(delete)、查询（findAll、findOne)等基本操作的函数。

在实际开发中，`JpaRespository`接口定义的接口往往还不够或者性能不够优化。
而spring-data-jpa的一大特性：**通过解析方法名创建查询**,`spring-data-jpa`规定,方法以`findBy`开头,如：
```java
User findByName(String name)  //按name查询User实体
User findByNameAndAge(String name, Integer age)   //按name和age查询User实体
```
`语法规定`：
Keyword | Sample | JPQL snippet
--- | --- | --- | ---
And | findByLastnameAndFirstname | ...where x.lastname = ? and x.firstname = ?
Or | findByLastnameOrFirstname | ...where x.lastname = ? or x.firstname = ?
Between | findByStartDateBetween | ...where x.startDate between ? and ?
LessThan | findByAgeLessThan | ...where x.age < ?
GreaterThan | findByAgeGreaterThan | ...where x.age > ?
Is,Equals | findByFirstname | ...findByFirstname = ?
IsNull | findByAgeIsNull | ...where x.age is null
isNotNulll,NoNull | findByAge(Is)NotNull | ...where x.age not null
Like | findByFirstnameLike | ...where x.firstname like ?
NotLike | findByFirstnameNotLike | ...where x.firstname not like ?
OrderBy | findByAgeOrderByLastnameDesc | ...where x.age = ? order by x.lastname desc
Not | findByLastnameNot | ...where x.lastname <> ?
In | findByAgeIn | ...where x.age in ?
True | findByActiveTrue | ...where x.active = true
False | findByActiveFalse | ...where x.active = false
IgnoreCase | findByFirstnameIgnoreCase | ...where UPPER(x.firstame) = UPPER(?)


除了通过解析方法名创建查询外，它也提供使用`@Query`注解来创建查询。只需要编写JPQL语句，并通过类似`:name`来映射`@Param`指定的参数，如：
```java
@Query("from User u where u.name=:name")
User findUser(@Param("name") String name);
```

Entity中不映射成列的字段加@Transient注解