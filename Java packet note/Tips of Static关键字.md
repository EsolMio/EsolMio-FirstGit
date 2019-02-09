# Tips of Static关键字
在Java中，static关键字可以修饰变量（句柄）和方法，修饰后则在意义上直接属于对应类的一部分。
## 类方法（static修饰的方法）
使用类方法的**目的**：
1. 为对应的类生成特定的对象
如java.io.FileDescriptor.standarStream()
   ```
   private static FileDescriptor standar
   ```