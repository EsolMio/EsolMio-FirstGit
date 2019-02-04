# The differents of ByteArrayOutputStream & BufferedOutputstream
## Same:
ByteArrayOutputStream & BufferedOutputStream 均有缓冲功能，对象成员中均有byte[] buf充当缓冲区（缓冲区：将上层输出流中的数据缓存在其中）
## Difference:
### ByteArrayOutputStream
ByteArrayOutputStream为底层输出流，即ByteArrayOutputStream封装的上层输出流最后只能停留在此类中，其构造器为`ByteArrayOutputStream();`无参数
p.s. 使用ByteArrayOutputStream后需要继续使用用其所创造的对象方法`handle.toByteArray();`，此方法返回byte[]类型，用于创建一个新数组以承载输出流。将此方法返回值放置于`ByteArraytInputStream(byte[] buf)`构建器中以使用缓冲区数据
### BufferedOutptuStream

BufferedOutputStream为修饰器，用来封装其他的底层输出流类（比如FileOutputStream），其将上层输出流需要输出的信息存放至自身的buf中，当buf已用容量达到上限时将buf中的数据传输到底层输入流中，相较于普通输出流，不必针对每次传输的字节调用底层系统资源（底层输出流可能会将数据存放至辅存中）
