# MySQL uuid()函数生成的UUID的长度问题

---

## 错误重新

有一user表，表结构信息如下：

![image-20200521203851344](markdown/MySQL uuid()函数生成的uuid的长度问题.assets/image-20200521203851344.png)

现在尝试执行以下SQL语句，向user表插入一条数据：

```sql
INSERT INTO `user` VALUES ( UUID(),"肖zy",22 )
```

上述SQL语句执行时产生以下错误信息：

```
INSERT INTO `user` VALUES ( UUID(),"肖zy",22 )
> 1406 - Data too long for column 'id' at row 1
> 时间: 0s
```

## 错误分析

从错误信息看，我们插入id字段的数据太长了。user表中id字段的最大长度被设置为32，也就是说UUID()函数返回的UUID的长度超过32。

下面是官方文档中关于UUID()函数的一段说明：

> [`UUID()`](https://dev.mysql.com/doc/refman/8.0/en/miscellaneous-functions.html#function_uuid) returns a value that conforms to UUID version 1 as described in RFC 4122. The value is a 128-bit number represented as a `utf8` string of five hexadecimal numbers in `aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee` format:

从官方文档可以看出，<font color = orange>UUID()函数返回的UUID的长度为36</font>。

## 错误解决

1. 修改user表中id字段的限定长度
2. 修改UUID()函数的返回值

> 使用REPLACE()函数，把UUID()函数返回值中的 “-”符号替换掉：
>
> ```sql
> REPLACE( UUID(),"-","")
> ```
>
> 这样可以将UUID()函数产生的UUID的长度缩短到32，且UUID的唯一性并不会被破坏。

---

官方文档：https://dev.mysql.com/doc/refman/8.0/en/miscellaneous-functions.html#function_uuid