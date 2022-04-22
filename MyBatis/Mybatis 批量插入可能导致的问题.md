# Mybatis 批量插入可能导致的问题

阅读文章：https://blog.csdn.net/moonpure/article/details/80759880



例句：

```xml
<insert id="batchInsert" parameterType="list">
  insert into Adv_permeability values
  <foreach collection="permeabilityList" separator="," item="permeability">
    (#{permeability.areaId}, #{permeability.areaName}, #{permeability.flag})
  </foreach>
</insert>
```



**总结1**：<mark>通过阅读 Mybatis 解析动态 SQL 的源码，可以看到 Mybatis 没有对填充的记录条数以及参数个数进行限制。</mark>

```java
// 开始解析
public void parse() {
    if (!configuration.isResourceLoaded(resource)) {
        configurationElement(parser.evalNode("/mapper"));
        configuration.addLoadedResource(resource);
        bindMapperForNamespace();
    }
 
    parsePendingResultMaps();
    parsePendingChacheRefs();
    parsePendingStatements();
}
// 解析mapper
private void configurationElement(XNode context) {
    try {
        String namespace = context.getStringAttribute("namespace");
        if (namespace.equals("")) {
            throw new BuilderException("Mapper's namespace cannot be empty");
        }
        builderAssistant.setCurrentNamespace(namespace);
        cacheRefElement(context.evalNode("cache-ref"));
        cacheElement(context.evalNode("cache"));
        parameterMapElement(context.evalNodes("/mapper/parameterMap"));
        resultMapElements(context.evalNodes("/mapper/resultMap"));
        sqlElement(context.evalNodes("/mapper/sql"));
        buildStatementFromContext(context.evalNodes("select|insert|update|delete"));
    } catch (Exception e) {
        throw new BuilderException("Error parsing Mapper XML. Cause: " + e, e);
    }
}
 
// 创建 select|insert|update|delete 语句
private void buildStatementFromContext(List<XNode> list, String requiredDatabaseId) {
    for (XNode context : list) {
        final XMLStatementBuilder statementParser = new XMLStatementBuilder(configuration, builderAssistant, context, requiredDatabaseId);
        try {
            statementParser.parseStatementNode();
        } catch (IncompleteElementException e) {
            configuration.addIncompleteStatement(statementParser);
        }
    }
}
 
// 填充参数，创建语句
public BoundSql getBoundSql(Object parameterObject) {
    DynamicContext context = new DynamicContext(configuration, parameterObject);
    rootSqlNode.apply(context);
    SqlSourceBuilder sqlSourceParser = new SqlSourceBuilder(configuration);
    Class<?> parameterType = parameterObject == null ? Object.class : parameterObject.getClass();
    SqlSource sqlSource = sqlSourceParser.parse(context.getSql(), parameterType, context.getBindings());
    BoundSql boundSql = sqlSource.getBoundSql(parameterObject);
    for (Map.Entry<String, Object> entry : context.getBindings().entrySet()) {
        boundSql.setAdditionalParameter(entry.getKey(), entry.getValue());
    }
    return boundSql;
}
```



**总结2**：<mark>经过测试，可以发现不同的数据库，不同的数据库版本对单条 SQL 的参数个数、语句长度有着不用的限制。</mark>

>   SQLServer
>
>   >   com.microsoft.sqlserver.jdbc.SQLServerException: 传入的请求具有过多的参数。该服务器支持最多 2100 个参数。请减少参数的数目，然后重新发送该请求。
>
>   
>
>   MySQL
>
>   >   com.mysql.jdbc.PacketTooBigException: Packet for query is too large  (8346602 > 4194304). You can change this value on the server by  setting the max_allowed_packet’ variable.
>   >
>   >   MySQL 的限制方式与 SQLServer 不同，MySQL 对执行的单条 SQL 语句的字符串长度进行了限制，默认情况下不能超过 4M
>   >
>   >   >   查询长度限制：
>   >   >
>   >   >   ```mysql
>   >   >   select @@max_allowed_packet
>   >   >   ```
>   >   >
>   >   >   ```
>   >   >   +----------------------+
>   >   >   | @@max_allowed_packet |
>   >   >   +----------------------+
>   >   >   |              4194304 |
>   >   >   +----------------------+
>   >   >   1 row in set (0.00 sec)
>   >   >   ```
>   >   >
>   >   >   修改长度限制：256M
>   >   >
>   >   >   ```mysql
>   >   >   SET GLOBAL max_allowed_packet=268435456
>   >   >   ```
>   >   >
>   >   >   ```
>   >   >   Query OK, 0 rows affected (0.00 sec)
>   >   >   ```
>   >   >
>   >   >   