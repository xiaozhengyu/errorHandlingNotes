# Spring Data JPA 控制台打印格式化SQL

---

```yaml
# 开启控制台SQL打印
spring.jpa.show_sql = true
# 开启SQL格式化
spring.jpa.properties.hibernate.format_sql = true
```

使用效果：

```
Hibernate: 
    select
        studentent0_.id as id1_2_,
        studentent0_.age as age2_2_,
        studentent0_.create_by as create_b3_2_,
        studentent0_.create_date as create_d4_2_,
        studentent0_.delete_flag as delete_f5_2_,
        studentent0_.sex as sex6_2_,
        studentent0_.student_name as student_7_2_,
        studentent0_.student_no as student_8_2_,
        studentent0_.update_by as update_b9_2_,
        studentent0_.update_date as update_10_2_ 
    from
        student studentent0_ 
    where
        studentent0_.create_date>=?
```

