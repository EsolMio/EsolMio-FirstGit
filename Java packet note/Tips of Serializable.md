# Serializable
- 实现序列化需要对应的类实现implement(接口) Serializable（可以将此接口当作为一个标志，毕竟没有什么抽象方法需要实现的）
- 序列化一个对象后，需要使用输出流抽象OutputStream的子类ObjectOutputStream将序列化的对象输出。
- 在创建ObjectOutputStream时：
`ObjectOutputStream ob = new ObjectOutputStream(OutputStream out);`
若需要输出到文件，可将out指代为FileOutputStream
- 若需要自定义序列化方式，有两种选择：
   - 实现Externalizable接口，并具体化`writeExternal(ObjectOutput out)` 和 `readExternal(ObjectInput in)`
   - 创建两个private修饰的`private void writeObject(ObjectOutputStream stream) throws IOException`和`private void readObject(ObjectInputStream stream) throws IOException, ClassNotFoundException`方法，ObjectOutput/ObjectInput会自动先寻找是否有这两个方法（即使是private），

