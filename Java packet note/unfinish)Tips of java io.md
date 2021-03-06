# Tips of Java IO

## 屏幕打印及读取
通常，对于在屏幕中输出和通过键盘输入，在Java中是使用`System.out`和`System.in`实现的，这两个量均由`staic`修饰，前者属于`PrintStream`类型，后者属于`InputStream`类型。故在输出时使用的的方法`println()`, `printf()`均由PrintStream提供。在输入时，则需要使用其他类封装/获得System.in以使用输入流。

## `Input/OutputStream`，`Reader/Writer`的区别
***
InputStream类的主要用于读取输入流中的**字节**流。
OutputSream类主要用于往输出流写入**字节**。
***
Reader类的主要用于读取输入流中的**字符**流。
Writer类主要用于将**字符**流写入输出流中。
***

p.s.字节流&字符流的区别：
1. 字符流的基本单元为两个字节的Unicode字符，主要为操作字符、字符数组(char[])和字符串(String)，字符流由JVM将1字节转换为两个字节的Unicode字符，适用于多国语言（如中文）
2. 字节流即基本单元为单个字节，可应用于任何类型的对象，包括二进制对象，以及音频，图片等等。但不适用于Unicode对象。
3. 字符流由Reader/Wirter处理，字节流由InputStream/OutputStream处理

## 常见的底层输入流
FileInputStream/FileOutputStream
ByteArrayInputStream/ByteArrayOutputStream

## `OutputStream` 中的 `flush()`
- 使用`flush()`后，原本存储于Byte的字节数据会**被直接强制输出至目的地**，需注意！

## `PipedWriter`, `PipedReader`进程间的管道流
- 构建`PipedWriter`, `PipedReader`十分简单，只需要前者直接使用`PipedWriter out = new PipedWriter();`, 而**只有**后者需要用到前者的对象作为参数以实现管道通信`PipedReader in = new PipedReader(out)；`

# Tips of Reader/Writer
## java.io.Reader/Writer
Reader类位于java.io包中，其实现了Closeable和Readable接口。直接子类有**InputStreamReader**, BufferedReader,  StringReader, CharArrayReader, FilterReader, PipedReader.
Writer类详见JDK



## 使用Reader/Writer时...
- 在使用`Reader/Writer`处理输入流时，均需要用到`InputStream/OutputStream`，先使用`InputStreamReader/OutputStreamWriter`接收`InputStream/OutputStream`，再在需要使用的Reader子类的构造器中将前者产生的对象封装进去.

  e.g.
  ```
  InputStreamReader in=new InputStreamReader(
      new FileInputStream(
          new File("D:\testDir\testdir\test.txt")));
  BufferedReader buffer=new BufferedReader(in);
  ```
- 。。。

## `BufferedOutputStream`同普通`OutputStream`的区别
- `BufferedOutputStream`会将上层数据流写入自己的`Byte[] buf`中，无论调用多少次的`write()`，只要Byte长度不超过`buf`则均写入`buf`中，直到长度len超过`buf`。

- 普通`OutputStream`会直接写入输出流中。

## `flush`方法
`flush()`方法的使用需要同`BufferedOutputStream`一同配合调用才会有效。


## `PrintWriter`不用手动调用`flush()`的类
- 在输出流中，无论是`OutputStream`亦或者`Writer`，都需要自行调用`flush()`将输出流输出出去。
- 但`PrintWriter`除外，它在调用`print()`/`println()`后会自动flush。
- 但是，需要在构造`PrintWriter`对象时使用`new PrintWriter(Writer/OutputStream out, boolean autoFlush);`，其中参数`autoFlush`使用`true`。
- `PrintWriter`拥有此特性其中一原因即支持直接对底层文件输出，不需通过构造器注入底层输出流。

## `BufferedReader`可以直接读取一行字符
- 因Buffered本身拥有缓冲区，可使用`new BufferedReader(Reader in ,int size)`更改缓冲区大，则可以一次性容纳更多的字符。
`readLine()`实现以上功能，`return String`。



