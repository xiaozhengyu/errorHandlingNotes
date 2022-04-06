# Spring MVC 字符串返回值双引号问题

## 一、问题重新

**后端代码：**

```
@RestController
public class XxxController{

    @PostMapping("xxx")
    public String instanceCallback() throws IOException {
        return "success"
    }
}
```

**前端实际获得的数据：**

```
"success"
```



## 二、问题分析

**问题原因：**返回的字符串默认被 org.springframework.http.converter.json.MappingJackson2HttpMessageConverter 处理，转换成了 JSON 字符串，所以被添加上了双引号。



## 三、问题解决

方案1：直接通过 HttpServletResponse 返回数据

```java
@RestController
public class XxxController{

    @PostMapping("xxx")
    public void instanceCallback( HttpServletResponse httpServletResponse) throws IOException {
        httpServletResponse.getWriter().print("success");
    }
}
```



方案2：修改 Spring MVC 配置



## 四、总结

在实际项目中，考虑到对其他模块的影响不能随意修改配置，所以优先考虑对其他模块没有影响的方案1