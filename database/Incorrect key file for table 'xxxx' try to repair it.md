```
Caused by: java.sql.SQLException: Incorrect key file for table 'C:\ZenTao\xampp\tmp\#sql56c_820e6_2.MYI'; try to repair it
	at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:129)
	at com.mysql.cj.jdbc.exceptions.SQLError.createSQLException(SQLError.java:97)
	at com.mysql.cj.jdbc.exceptions.SQLExceptionsMapping.translateException(SQLExceptionsMapping.java:122)
	at com.mysql.cj.jdbc.ClientPreparedStatement.executeInternal(ClientPreparedStatement.java:955)
	at com.mysql.cj.jdbc.ClientPreparedStatement.executeQuery(ClientPreparedStatement.java:1005)
	at com.zaxxer.hikari.pool.ProxyPreparedStatement.executeQuery(ProxyPreparedStatement.java:52)
	at com.zaxxer.hikari.pool.HikariProxyPreparedStatement.executeQuery(HikariProxyPreparedStatement.java)
	at org.hibernate.engine.jdbc.internal.ResultSetReturnImpl.extract(ResultSetReturnImpl.java:60)
	... 145 more
```

1. MYI是什么文件？

    MySQL Index File，MySQL索引文件。

2. 为什么会有 'C:\ZenTao\xampp\tmp\#sql56c_820e6_2.MYI' 这么奇怪的 table ？

    ```mysql
    -- 查询MySQL默认临时数据地址
    show variables like 'tmpdir'
    ```

    | Variable_name | Value               |
    | ------------- | ------------------- |
    | tempdir       | C:\ZenTao\xampp\tmp |

3. 查询缓存限制

    ```mysql
    -- 查询缓存限制
    show variables like 'query_cache_limit'
    ```

    | Variable_name     | Value   |
    | ----------------- | ------- |
    | query_cache_limit | 1048576 |

    1048576b = 1024kb =  1M

    query_cache_limit 指单个查询能够使用的缓冲区大小，默认为1M。

**排查结果**：在执行SQL的时候，产生的临时数据占满了 C:\ZenTao\xampp\tmp 磁盘空间。