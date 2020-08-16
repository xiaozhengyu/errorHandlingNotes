# Spring MVC 数据返回问题记录

## 1. 返回值解析异常——“No converter found…”

- **异常信息：**

    Resolved [org.springframework.http.converter.HttpMessageNotWritableException: No converter found for return value of type: class com.example.demo.entity.Student]

- **过程回顾：**

     有一Student类，代码如下：

    ```java
    /**
     * @author xzy
     * @date 2020-08-16 16:02
     * 说明：
     */
    public class Student {
        private String id;
        private String studentName;
        private String studentNo;
    
        public Student() {
        }
    
        public Student(String id, String studentName, String studentNo) {
            this.id = id;
            this.studentName = studentName;
            this.studentNo = studentNo;
        }
    }
    ```
    
    Controller类相关代码如下：
    
    ```java
        @GetMapping("getStudent")
        public Student getStudent() {
            return new Student("sd8f9sd89f", "李白", "A10001");
        }
    ```
    
- **问题分析：**

    将某一对象作为返回值是，框架需要调用对象中的 `get` 方法，将对象解析成JSON格式。由于上述代码中的student类没有定义任何 `get` 方法，因此数据解析失败。

- **解决办法：** 

    为相关字段添加`get`方法

    修改后的student类：
    
    ```java
    /**
     * @author xzy
     * @date 2020-08-16 16:02
     * 说明：
     */
    public class Student {
        private String id;
        private String studentName;
        private String studentNo;
    
        public String getId() { return id;}
    
        public String getStudentName() { return studentName;}
    
        public String getStudentNo() { return studentNo;}
        
        public Student() {}
    
        public Student(String id, String studentName, String studentNo) {
            this.id = id;
            this.studentName = studentName;
            this.studentNo = studentNo;
        }
    }
    ```
    
    调用接口，接收到的数据如下：
    
    ```json
    {
    "id": "sd8f9sd89f",
        "studentName": "李白",
    "studentNo": "A10001"
    }
    ```

## 2. 返回值解析异常——字段缺失

- **问题重现**：

    修改上文的Student类，源码如下：

    ```java
    public class Student {
        private String id;
        private String studentName;
        private String studentNo;
    
        public String getId() { return id;}
    
        public String getStudentName() { return studentName;}
        
        // 注意：此处没有为studentNo属性设置对应的get方法
        
        public Student() {}
    
        public Student(String id, String studentName, String studentNo) {
            this.id = id;
            this.studentName = studentName;
            this.studentNo = studentNo;
        }
    }
    ```

    调用接口，接收到的数据如下：

    ```json
    {
        "id": "sd8f9sd89f",
        "studentName": "李白"
    }
    ```

    从接收到的数据看，studentNo属性的值并没有被后端发送出来。

- **问题分析：**

    将某一对象作为返回值是，框架会逐个调用对象中的 `get` 方法，将对象解析成JSON格式。由于上述代码的student类中没有为studentNo属性设置对应的`get`方法，因此，框架没有将studentNo属性的值解析到返回值中。

- **解决办法：**

    为需要的字段添加`get`方法。