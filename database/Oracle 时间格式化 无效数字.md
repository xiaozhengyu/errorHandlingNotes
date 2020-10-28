# Oracle 时间格式化 无效数字

---

| 字段名     | 类型    | 备注                        |
| ---------- | ------- | --------------------------- |
| flightDate | varchar | 格式：yyyy-mm-dd hh24:mi:ss |

1. 查询语句：

    ```sql
    select
       TO_CHAR(xxx.flightDate,'hh24:mi')
    from 
        xxx
    ```

2. 查询结果：

    ```
    SELECT
    	TO_CHAR(HANDOVER_TIME,'hh24:mi') 
    FROM
    	"T_DOA_FLIGHT_BIZ" 
    WHERE
    	HANDOVER_TIME IS NOT NULL
    > ORA-01722: 无效数字
    
    > 时间: 0.026s
    ```

3. 错误分析：

    TO_CHAR 函数官方说明

    `TO_CHAR` (datetime) converts <u>**a datetime or interval value**</u> of `DATE`, `TIMESTAMP`, `TIMESTAMP` `WITH` `TIME` `ZONE`, or `TIMESTAMP` `WITH` `LOCAL` `TIME``ZONE` datatype to a value of `VARCHAR2` datatype in the format specified by the date format `fmt`.

    TO_CHAR 函数接收只能接收不接收VARCHAR类型的数据！

4. 解决办法：

    使用TO_DATE函数将VARCHAR类型的数据转为DATETIME类型，然后再使用TO_CHAR。

    ```sql
    select
       TO_CHAR(TO_DATE(xxx.flightDate,'yyyy-mm-dd hh24:mi:ss'),'hh24:mi')
    from 
        xxx
    ```

5. 总结

    ```mermaid
    graph TB
    日期_varchar --TO_DATE--> 日期_datetime--TO_CHAR-->时间_varchar
    ```

    脱裤子放屁….

