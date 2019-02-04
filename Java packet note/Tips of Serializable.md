# Serializable
- 实现序列化需要对应的类实现implement(接口) Serializable（可以将此接口当作为一个标志，毕竟没有什么抽象方法需要实现的）
- 序列化一个对象后，需要使用输出流抽象OutputStream的子类ObjectOutputStream将序列化的对象输出。
- 在创建ObjectOutputStream时：
`ObjectOutputStream ob = new ObjectOutputStream(OutputStream out);`
若需要输出到文件，可将out指代为FileOutputStream
