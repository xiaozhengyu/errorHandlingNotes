# MySQL中文排序问题

MySQL在默认情况下只支持对日期、时间和英文字符串进行排序，如果对中文进行ORGER BY可能得不到想要的排序结果。对此我们可以使用CONVERT(columnName USING GBK)将中文转换成GBK编码形式，然后再进行排序。

例子：

```mysql
SELECT * FROM user ORDER BY CONVERT(userName USING GBK) ASC；
```