## Method threw 'java.lang.StackOverflowError' exception. Cannot evaluate XXX.toString()

![image-20200422224356346](E:\学习笔记\errorHandlingNotes\Intellij IDEA\markdown图片\image-20200422224356346.png)

### 原因：

实例间相互应用。由于使用双向链表实现二叉树，父结点包含子结点的引用，子结点包含父结点的引用。Debugger会调用对象的toString()方法以显示对象的所有数据信息，这意味着会在父结点和子结点的toString()方法中陷入死循环。

![image-20200422224717543](E:\学习笔记\errorHandlingNotes\Intellij IDEA\markdown图片\image-20200422224717543.png)

### 办法

修改toString()方法，不打印previous（或不打印left和right）。



