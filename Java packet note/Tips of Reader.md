# Tips of Reader/Writer
## java.io.Reader
Reader类位于java.io包中，其实现了Closeable和Readable接口。直接子类有**InputStreamReader**, BufferedReader,  StringReader, CharArrayReader, FilterReader, PipedReader.
****
Reader类的主要用处即读取输入流中的字符流，专门用于处理字符流
p.s.字节流&字符流的区别：
1. 字符流的基本单元为两个字节的Unicode字符，主要为操作字符、字符数组(char[])和字符串(String)，字符流由JVM将1字节转换为两个字节的Unicode字符，适用于多国语言（如中文）
2. 字节流即基本单元为单个字节，可应用于任何类型的对象，包括二进制对象，以及音频，图片等等。但不适用于Unicode对象。
3. 字符流由Reader/Wirter处理，字节流由InputStream/OutputStream处理
***
## 使用Reader/Writer时...
在使用Reader/Writer处理输入流时，均需要用到InputStream/OutputStream，