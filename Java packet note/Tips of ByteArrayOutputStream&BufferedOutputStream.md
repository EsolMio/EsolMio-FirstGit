# The differents of ByteArrayOutputStream & BufferedOutputstream
## Same:
ByteArrayOutputStream & BufferedOutputStream 均有缓冲功能，对象成员中均有byte[] buf充当缓冲区（缓冲区：将上层输出流中的数据缓存在其中）
## Difference
ByteArrayOutputStream为底层输出流，即ByteArrayOutputStream封装的上层输出流最后只能停留在此类中，