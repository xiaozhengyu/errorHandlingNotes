**java.lang.UnsupportedOperationException: A TupleBackedMap cannot be modified.**

## 思考

1. 为什么有的方法会返回不可修改的Map呢？

```
这个问题其实是想问，为什方法不想我们修改返回回的结果？因为有些数据结构是在类内部定义的，对于类外部的使用者来说可能不太清楚这些数据结构，任由他们对方法返回的结果进行修改时很危险的。
```

2. 解决方法：以不可修改的数据创建可修改的数据。

```java
//不可修改
List<Map<String, Object>> canNotModifyList = xxxRepository.findxxxById(id);
List<Map<String, Object>> newList = new LinkedList<>();
for (Map<String, Object> canNotModify : canNotModifyList) {
    //以不可修改创建可修改
    Map<String, Object> newMap = new HashMap<>(canNotModify); 
    newMap.put("xxx", "xxx");
    newList.add(newMap);
}
return newList;
```