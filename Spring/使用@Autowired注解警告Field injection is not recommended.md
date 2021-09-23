# 使用@Autowired注解警告Field injection is not recommended

原文地址：https://blog.csdn.net/zhangjingao/article/details/81094529

---

**代码：**

```java
@Autowired
private XXXService xxxservice;
```



**警告：**Field injection is not recommended（不推荐使用变量注入的方式）



**原因：**

idea解决策略是这样的：Always use constructor based dependency injection in your beans. Always use assertions for mandatory dependencies（使用构造器的方式强制注入）



依赖注入有多种方式：

-   变量注入

    ```java
    @Autowired
    private XXXService xxxservice;
    ```

    

-   构造器注入

    ```java
    @Autowired
    public XXX(XXXService xxxservice){
        this.xxxservice = xxxservice;
    }
    ```

    

-   setter注入

    ```java
    @Autowired
    public void setXXX(XXXService xxxservice){
        this.xxxservice = xxxservice;
    }
    ```



变量注入的优点：简洁

变量注入的缺点：项目和Spring容器紧密耦合——如果现在需要你手动管理依赖，但是目前采用的是变量注入的方式，那你就不得不新增setter或构造器。