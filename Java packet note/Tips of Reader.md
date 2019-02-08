# Tips of Reader
## java.io.Reader
Reader类位于java.io包中，其实现了Closeable和Readable接口。直接子类有**InputStreamReader**, BufferedReader,  StringReader, CharArrayReader, FilterReader, PipedReader.
****
Reader类的主要用处即读取输入流中的字符流，专门用于处理字符流
p.s.字节流&字符流的区别：
1. 字符流的基本单元为两个字节的Unicode字符，主要为操作字符