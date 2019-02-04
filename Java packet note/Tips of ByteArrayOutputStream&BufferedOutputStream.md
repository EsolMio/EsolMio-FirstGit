# The differents of ByteArrayOutputStream & BufferedOutputstream
## Same:
ByteArrayOutputStream & BufferedOutputStream 均有缓冲功能，对象成员中均有byte[] buf充当缓冲区（缓冲区：将上层输出流中的数据缓存在其中）
## Difference:
### ByteArrayOutputStream
ByteArrayOutputStream为底层输出流，即ByteArrayOutputStream封装的上层输出流最后只能停留在此类中，其构造器为`ByteArrayOutputStream();`无参数
p.s. 使用ByteArrayOutputStream后需要继续使用用其所创造的对象方法`handle.toByteArray();`，此方法返回byte[]类型，用于创建一个新数组
