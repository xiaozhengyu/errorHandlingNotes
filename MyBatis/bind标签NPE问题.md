# bind标签NPE问题

---

```java
List<Student> listByNameLike(@Param("name") String name);
```

```xml
<select id="xxx" parameterType="String" resultMap="xxx">
    <bind name="nameLike" value="'%' + name + '%'"/>
    SELECT * FROM tbl_student WHERE name LIKE #{nameLike}
</select>
```

调用该方法时传入的参数值为NULL，最终导致程序抛出NPE：

```
org.mybatis.spring.MyBatisSystemException: nested exception is org.apache.ibatis.exceptions.PersistenceException: 
### Error querying database.  Cause: java.lang.NullPointerException: Can't add values % , null
### Cause: java.lang.NullPointerException: Can't add values % , null
```

### 方案一：在XML中处理NULL

```xml
<choose>
    <when test="name">
        <bind name="nameLike" value="'%' + name + '%'"/>
    </when>
    <otherwise>
        <bind name="nameLike" value="'%%'"/>
    </otherwise>
</choose>
```

### 方案二：在Java中处理NULL