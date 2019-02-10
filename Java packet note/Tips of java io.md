# Tips of Java IO
## 屏幕打印及读取
通常，对于在屏幕中输出和通过键盘输入，在Java中是使用`System.out`和`System.in`实现的，这两个量均由`staic`修饰，前者属于`PrintStream`类型，后者属于`InputStream`类型。故在输出时使用的的方法`println()`, `printf()`均由PrintStream提供。在输入时，则需要使用其他类封装/获得System.in以使用输入流。
## 字节流和字符流
在Java中，最基本的IO流即字节流，这是一切流的**基本**，以**字节Byte**为单位。
字节流承载着任意一个对象，无论是文字，图片，音频。其对应的类为`InputStream`和`OutputStream`。
但对于多国语言，