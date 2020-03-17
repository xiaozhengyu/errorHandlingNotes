### [参考资料](https://www.cnblogs.com/xxjcai/p/java_compiler.html)

### 错误信息

```
.\src\main\java\com\xiao\designpattern\dynamicproxy\TimeProxy.java:6: 警告: Can't initialize javac processor due to (most likely) a class loader problem: java.lang.NoClassDefFoundError: com/sun/tools/javac/processing/JavacProcessingEnvironment
class TimeProxy implements Flyable {
^
    at lombok.javac.apt.LombokProcessor.getJavacProcessingEnvironment(LombokProcessor.java:419)
    at lombok.javac.apt.LombokProcessor.init(LombokProcessor.java:90)
    at lombok.core.AnnotationProcessor$JavacDescriptor.want(AnnotationProcessor.java:124)
    at lombok.core.AnnotationProcessor.init(AnnotationProcessor.java:177)
    at lombok.launch.AnnotationProcessorHider$AnnotationProcessor.init(AnnotationProcessor.java:73)
    at com.sun.tools.javac.processing.JavacProcessingEnvironment$ProcessorState.<init>(JavacProcessingEnvironment.java:500)
    at com.sun.tools.javac.processing.JavacProcessingEnvironment$DiscoveredProcessors$ProcessorStateIterator.next(JavacProcessingEnvironment.java:597)
    at com.sun.tools.javac.processing.JavacProcessingEnvironment.discoverAndRunProcs(JavacProcessingEnvironment.java:690)
    at com.sun.tools.javac.processing.JavacProcessingEnvironment.access$1800(JavacProcessingEnvironment.java:91)
    at com.sun.tools.javac.processing.JavacProcessingEnvironment$Round.run(JavacProcessingEnvironment.java:1035)
    at com.sun.tools.javac.processing.JavacProcessingEnvironment.doProcessing(JavacProcessingEnvironment.java:1176)
    at com.sun.tools.javac.main.JavaCompiler.processAnnotations(JavaCompiler.java:1170)
    at com.sun.tools.javac.main.JavaCompiler.compile(JavaCompiler.java:856)
    at com.sun.tools.javac.main.Main.compile(Main.java:523)
    at com.sun.tools.javac.api.JavacTaskImpl.doCall(JavacTaskImpl.java:129)
    at com.sun.tools.javac.api.JavacTaskImpl.call(JavacTaskImpl.java:138)
    at com.xiao.designpattern.dynamicproxy.JavaCompiler.compiler(JavaCompiler.java:19)
    at com.xiao.designpattern.dynamicproxy.Proxy.newProxyInstance(Proxy.java:62)
    at com.xiao.designpattern.dynamicproxy.Test.main(Test.java:10)
 Caused by: java.lang.ClassNotFoundException: com.sun.tools.javac.processing.JavacProcessingEnvironment
    at java.lang.ClassLoader.findClass(ClassLoader.java:530)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
    at lombok.launch.ShadowClassLoader.loadClass(ShadowClassLoader.java:530)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
    ... 19 more
1 个警告
```

### 解决方案

1. 

![img](https://img-blog.csdnimg.cn/20200116094027736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTE0MDMz,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

2. 

![img](https://img-blog.csdnimg.cn/20200116094121419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTE0MDMz,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

这里1.8版本的SDK是我现在项目在用的SDK，查看右侧的ClassPath，发现没有tools.jar，所以第三步就需要添加tools.jar

3. 

![img](https://img-blog.csdnimg.cn/20200116094405663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTE0MDMz,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

![img](https://img-blog.csdnimg.cn/20200116094437274.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NTE0MDMz,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

4. 再次运行程序，问题已经解决。