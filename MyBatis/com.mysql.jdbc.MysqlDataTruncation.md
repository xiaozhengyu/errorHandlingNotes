**异常信息**：com.mysql.jdbc.MysqlDataTruncation: Data truncation: Incorrect datetime value…



**问题描述**：

| 接收参数类型 | 实体字段类型   | JDBC类型  | 数据库字段类型 |
| ------------ | -------------- | --------- | -------------- |
| Long         | java.util.Date | timestamp | datetime       |

1.  系统使用 Long 类型接收外部传入的时间戳数据
2.  将时间戳转换成 java.util.Date 类型的数据，传入实体的指定字段
3.  调用 Dao 接口保存实体信息，结果 Mybatis 抛出异常



**问题分析**：

1.  在 Mybatis 的 XML 文件中，对应字段的 jdbcType 被设置成了 TIMESTAMP

    ```xml
    <insert id="xxx" parameterType="com.xxx.Xxx">
        insert into xxx (
            ...
            op_time,
            ...
        )
        values (
            ....
            #{opTime,jdbcType=TIMESTAMP},
            ....
        )
    </insert>
    ```

2.  在 MySQL 中不同时间类型的存储范围是不同的，详情可见[官方文档](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)

    | 类型      | 范围                                              |
    | --------- | ------------------------------------------------- |
    | DATE      | 1000-01-01 ~ 9999-12-31                           |
    | DATETIME  | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59         |
    | TIMESTAMP | 1970-01-01 00:00:01 UTC ~ 2038-01-19 03:14:07 UTC |

    

3.  外部传入的数值为 1650282030584731，转换后得到的数据为 54265-05-10 19:09:44.731，已经超出 TIMESTAMP 类型的存储范围了，所以报错了

4.  沟通后发现，调用方传入的是以**微秒**为单位的时间戳，而 java.util.Date 的构造方法传入的是以**毫秒**为单位的时间戳……



**总结**：对外提供接口时最好把一些数据的单位进行约定

