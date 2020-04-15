# MySQL + Date类型 + 0000-00-00 + Hibernate  =  ?

日期：2020年4月15日20点57分

描述：

1. 有一MySQL数据库表

![image-20200415205841285](E:\学习笔记\errorHandlingNotes\Database\markdown图片\image-20200415205841285.png)

<center>图1 表view_plan_taskfinishedday部分数据展示</center>

2. 如图1所示，表中有一Date类型的字段finishedday，该字段的值可能为0000-00-00
3. 在数据库中使用SQL语句查询 planid = 2 的记录

![image-20200415210653567](E:\学习笔记\errorHandlingNotes\Database\markdown图片\image-20200415210653567.png)

<center>图2 查询结果</center>

注意，该语句查询到了55条记录，而且第一条记录finishedday字段的值为0000-00-00。

4. 使用Spring Data JPA 进行相同的操作

![image-20200415211030176](E:\学习笔记\errorHandlingNotes\Database\markdown图片\image-20200415211030176.png)

<center>图3 使用Spring Data JPA实现相同的查询</center>

```java
@Data
@Entity
@Table(name = "view_plan_taskfinishedday")
@NoArgsConstructor
@AllArgsConstructor
@IdClass(PlanTaskFinishedDayPrimaryKey.class)
public class PlanTaskFinishedDayEntity {
    /**
     * 计划id
     */
    @Id
    @Column(name = "planid")
    private Integer planId;

    /**
     * 该计划包含的所有任务完成的日期（年-月-日）
     */
    @Id
    @Column(name = "finishedday")
    @JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd")
    private Date finishedDay;
}
```

5. 测试

```java
@Test
public void contextLoads() throws Exception {
    List<PlanTaskFinishedDayEntity> planTaskFinishedDays = repository.findByPlanId(2);
    System.out.println(planTaskFinishedDays.size());
    System.out.println(planTaskFinishedDays.get(0));
}
```

控制台打印：

```
55
null
```

6. DEBUG

![image-20200415211509660](E:\学习笔记\errorHandlingNotes\Database\markdown图片\image-20200415211509660.png)

<center>图4 调试结果</center>

从调试结果看，使用Spring Data JPA 明明也查到了55条记录，为什么第一条记录是null呢？

7. Hibernate 0000-00-00 null

在否定了多个猜测之后，我开始思考：是不是查询到数据以后，框架进行ORM的时候有什么特殊操作？Spring Data JPA 是基于Hibernate的，会不会是因为Hibernate对数据库中值为0000-00-00的date类型有特殊的处理呢？

![image-20200415212435130](E:\学习笔记\errorHandlingNotes\Database\markdown图片\image-20200415212435130.png)

<center>图5 </center>

上网查了一下，基本上我的猜测是对的，问题确实出在Hibernate，不过更准确的说法是出现在JDBC，因为Hibernate是基于JDBC的。

![image-20200415212710915](E:\学习笔记\errorHandlingNotes\Database\markdown图片\image-20200415212710915.png)

<center>图6<center>
突然想到，自己好像从来没有好好看过项目中数据库配置信息，然后赶紧去看了一下，果然......

![image-20200415213123593](E:\学习笔记\errorHandlingNotes\Database\markdown图片\image-20200415213123593.png)

<center>图7 本项目数据库配置信息</center>

