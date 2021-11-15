使用 Spring Data Mongodb：

```java
xxxRepository.save(xxx);
```

报错：

```
14:15:09.490 [scheduling-1] ERROR org.springframework.scheduling.support.TaskUtils$LoggingErrorHandler - Unexpected error occurred in scheduled task
java.lang.NoSuchMethodError: com.mongodb.client.MongoCollection.replaceOne(Lorg/bson/conversions/Bson;Ljava/lang/Object;Lcom/mongodb/client/model/ReplaceOptions;)Lcom/mongodb/client/result/UpdateResult;
	at org.springframework.data.mongodb.core.MongoTemplate$2.doInCollection(MongoTemplate.java:1558) ~[spring-data-mongodb-2.2.3.RELEASE.jar:2.2.3.RELEASE]
	at org.springframework.data.mongodb.core.MongoTemplate.execute(MongoTemplate.java:585) ~[spring-data-mongodb-2.2.3.RELEASE.jar:2.2.3.RELEASE]
	at org.springframework.data.mongodb.core.MongoTemplate.saveDocument(MongoTemplate.java:1543) ~[spring-data-mongodb-2.2.3.RELEASE.jar:2.2.3.RELEASE]
	at org.springframework.data.mongodb.core.MongoTemplate.doSave(MongoTemplate.java:1479) ~[spring-data-mongodb-2.2.3.RELEASE.jar:2.2.3.RELEASE]
	at org.springframework.data.mongodb.core.MongoTemplate.save(MongoTemplate.java:1421) ~[spring-data-mongodb-2.2.3.RELEASE.jar:2.2.3.RELEASE]
	at org.springframework.data.mongodb.repository.support.SimpleMongoRepository.save(SimpleMongoRepository.java:87) ~[spring-data-mongodb-2.2.3.RELEASE.jar:2.2.3.RELEASE]
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:1.8.0_73]
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[?:1.8.0_73]
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:1.8.0_73]
	at java.lang.reflect.Method.invoke(Method.java:497) ~[?:1.8.0_73]
    ...

```

原因：pom.xml 文件存在两个不同版本的依赖，版本低的可能无法正常操作MongoDB数据库

```xml
<!--java-mongodb驱动-->
<dependency>
    <groupId>org.mongodb</groupId>
    <artifactId>mongo-java-driver</artifactId>
    <version>3.5.0</version>
</dependency>
<!--Mongo-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
    <version>2.2.2.RELEASE</version>
    <exclusions>
        <exclusion>
            <artifactId>log4j-to-slf4j</artifactId>
            <groupId>org.apache.logging.log4j</groupId>
        </exclusion>
        <exclusion>
            <artifactId>spring-boot-starter-logging</artifactId>
            <groupId>org.springframework.boot</groupId>
        </exclusion>
    </exclusions>
</dependency>
```

解决：移除低版本的依赖

```xml
        <!--java-mongodb驱动-->
<!--        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>mongo-java-driver</artifactId>
            <version>3.5.0</version>
        </dependency>-->
        <!--Mongo-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-mongodb</artifactId>
            <version>2.2.2.RELEASE</version>
            <exclusions>
                <exclusion>
                    <artifactId>log4j-to-slf4j</artifactId>
                    <groupId>org.apache.logging.log4j</groupId>
                </exclusion>
                <exclusion>
                    <artifactId>spring-boot-starter-logging</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
        </dependency>
```