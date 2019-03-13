# Tips of Reader/Writer
## java.io.Reader/Writer
Reader类位于java.io包中，其实现了Closeable和Readable接口。直接子类有**InputStreamReader**, BufferedReader,  StringReader, CharArrayReader, FilterReader, PipedReader.
Writer类详见JDK
****
Reader类的主要用于读取输入流中的字符流，专门用于处理字符流
Writer类主要用于将字符流写入输出流中，也是用于处理字符流

p.s.字节流&字符流的区别：
1. 字符流的基本单元为两个字节的Unicode字符，主要为操作字符、字符数组(char[])和字符串(String)，字符流由JVM将1字节转换为两个字节的Unicode字符，适用于多国语言（如中文）
2. 字节流即基本单元为单个字节，可应用于任何类型的对象，包括二进制对象，以及音频，图片等等。但不适用于Unicode对象。
3. 字符流由Reader/Wirter处理，字节流由InputStream/OutputStream处理
***
## 使用Reader/Writer时...
在使用`Reader/Writer`处理输入流时，均需要用到`InputStream/OutputStream`，先使用`InputStreamReader/OutputStreamWriter`接收`InputStream/OutputStream`，再在需要使用的Reader子类的构造器中将前者产生的对象封装进去.

e.g.
```
InputStreamReader in=new InputStreamReader(
    new FileInputStream(
        new File("D:\testDir\testdir\test.txt")));
BufferedReader buffer=new BufferedReader(in);
```
## `PrintWriter`不用手动调用`flush()`的类
在输出流中，无论是`OutputStream`亦或者`Writer`，都需要自行调用`flush()`将输出流输出出去。
除了`PrintWriter`，其在