# Java日期使用，切勿忽略毫秒！

---

学生信息表现有如下数据：

```
              id                 student_name   create_date
8a8190dc719fe2ce0171a5125d660003	张思涵	  2020-11-04 00:00:00
8a8190dc719fe2ce0171a522e73d0004	毕福剑	  2020-11-04 00:00:00
8a8190dc71a9b6270171aa4c69c60011	测试	   2020-11-04 00:00:00
8a8190dc71a9b6760171a9e794300000	黑土	   2020-11-04 00:00:00
8a8190dc71b460ec0171c00cc5c700cf	白云	   2020-11-04 00:00:00
4028c181719b84cd01719bebef990000	董文婷	  2020-11-03 01:00:00
4028c181719b84cd01719c2d501d0002	白居易	  2020-11-03 00:01:00
8a81904e6e640273016ef29adf873b87	李白	   2020-11-03 00:00:01
8a8190dc7153fd7e0171588b277a0007	孙中山	  2020-11-03 00:00:00    注意
8a8190dc719fccc50171a1251e560011	李远明	  2020-11-03 00:00:00    注意
8a8190dc719fccc50171a130917f0012	张思	   2020-11-03 00:00:00    注意
8a8190dc719fe2ce0171a15add890000	孙思邈	  2020-11-03 00:00:00    注意
8a8190dc719fe2ce0171a4855d370001	安东尼	  2020-11-03 00:00:00    注意
8a8190dc719fe2ce0171a4a443d20002	松岛枫	  2020-11-03 00:00:00    注意
8a8190dc71b460ec0171c010684200d2	宋丹丹	  2020-11-03 00:00:00    注意
8a8190dc71c569980171c8a581700000	张无忌	  2020-11-03 00:00:00    注意
8a8190dc71c569980171c8a701320001	梁玉华	  2020-11-03 00:00:00    注意
8a8190dc71f8375d0171f8ae8691001c	张华	  2020-11-03 00:00:00    注意
8a8190dc720d396001724ef3cce4012d	港龙	  2020-11-03 00:00:00    注意
8a8190dc720d396001724f9e07f30214	吴文俊	 2020-11-03 00:00:00    注意
```

假设当前日期为2020-11-03，现需要查询**创建日期大于等于今天的学生数据**。

SQL语句如下：

```sql
SELECT
	* 
FROM
	`student` 
WHERE
	create_date >= '2020-11-03 00:00:00'
```

Java代码如下：

```java
@Repository
public interface StudentRepository extends JpaRepository<StudentEntity, String>, JpaSpecificationExecutor<StudentEntity> {

    /**
     * 查询创建日期大于等于指定日期的学生数据
     *
     * @param date - 日期下限
     * @return - 创建日期大于等于指定日期的学生数据
     */
    List<StudentEntity> findAllByCreateDateGreaterThanEqual(Date date);
}
```

```java
@SpringBootTest
@RunWith(SpringRunner.class)
@NoArgsConstructor
class StudentServiceImplTest {
    @Autowired
    private StudentRepository repository;

    @Test
    void findAllByCreateDate() {

        // 1. 获取今天的日期（年月日）
        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.HOUR_OF_DAY, 0);
        calendar.set(Calendar.MINUTE, 0);
        calendar.set(Calendar.SECOND, 0);
        Date today = calendar.getTime();

        // 2.查询创建时间大于等于今天的数据
        List<StudentEntity> studentEntityList = repository
            .findAllByCreateDateGreaterThanEqual(today);

        // 输入查询到的数据的日期
        for (StudentEntity studentEntity : studentEntityList) {
            System.out.println("createDate:" + studentEntity.getCreateDate() +
                    " studentName:" + studentEntity.getStudentName());
        }

        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println("\n查询条件：createDate >= " + dateFormat.format(today));
    }
}
```

指定上述SQL语句后，从学生表中查询出了全部的数，但执行上述Java语句时，得到和SQL语句一样的查询结果。执行Java程序时的控制台输出信息如下：

```
createDate:2020-11-03 01:00:00.0 studentName:董文婷
createDate:2020-11-03 00:01:00.0 studentName:白居易
createDate:2020-11-03 00:00:01.0 studentName:李白
createDate:2020-11-04 00:00:00.0 studentName:张思涵
createDate:2020-11-04 00:00:00.0 studentName:毕福剑
createDate:2020-11-04 00:00:00.0 studentName:测试
createDate:2020-11-04 00:00:00.0 studentName:黑土
createDate:2020-11-04 00:00:00.0 studentName:白云

查询条件：createDate >= 2020-11-03 00:00:00

```

仔细观察后发现：

1. 学生表中create_date等于 2020-11-03 00:00:00的数据全部没有查询出来;
2. 学生表中create_date字段，日期等于2020-11-03，但时间不等于00:00:00的时间全部可以查询出来。



**问题出在哪？**

Java中日期是精确到毫秒的，上述Java代码中，虽然将“时、分、秒”置0，但没有将“毫秒置零”，将上述代码做如下修改（打印出毫秒数）：

```java
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
改为：
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS");
```

再次运行代码，控制台输出信息如下：

```
createDate:2020-11-03 01:00:00.0 studentName:董文婷
createDate:2020-11-03 00:01:00.0 studentName:白居易
createDate:2020-11-03 00:00:01.0 studentName:李白
createDate:2020-11-04 00:00:00.0 studentName:张思涵
createDate:2020-11-04 00:00:00.0 studentName:毕福剑
createDate:2020-11-04 00:00:00.0 studentName:测试
createDate:2020-11-04 00:00:00.0 studentName:黑土
createDate:2020-11-04 00:00:00.0 studentName:白云

查询条件：createDate >= 2020-11-03 00:00:00:168
```

可以发现，我们传递到数据库用于查询的时间其实是带有毫秒的。



**如何解决？**

将毫秒置零。