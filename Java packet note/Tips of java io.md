# Tips of Java IO
## 屏幕打印及读取
通常，对于在屏幕中输出和通过键盘输入，在Java中是使用`System.out`和`System.in`实现的，这两个量均由`staic`修饰，前者属于`PrintStream`类型，后者属于`InputStream`类型。故在输出时使用的的方法`println()`, `printf()`均由PrintStream提供。