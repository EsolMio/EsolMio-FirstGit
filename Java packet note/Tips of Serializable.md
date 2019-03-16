# Serializable
- 实现序列化需要对应的类实现implement(接口) Serializable（可以将此接口当作为一个标志，毕竟没有什么抽象方法需要实现的）
- 要序列化一个对象，需要使用输出流抽象OutputStream的子类ObjectOutputStream，此类将对象从内存输出到硬盘中。
- 在创建ObjectOutputStream时：
`ObjectOutputStream ob = new ObjectOutputStream(OutputStream out);`
若需要输出到文件，可将out指代为FileOutputStream
- 输出/序列化对象时，需使用`obostream.writeObject(Object a)`
- 反序列化对象时，需要使用`obistream.readObject()`
- 若需要自定义序列化方式，有两种选择：
   - 实现Externalizable接口，并具体化`writeExternal(ObjectOutput out)` 和 `readExternal(ObjectInput in)`
   - 创建两个private修饰的方法：
     - `private void writeObject(ObjectOutputStream stream) throws IOException`
     - `private void readObject(ObjectInputStream stream) throws IOException, ClassNotFoundException`

   ObjectOutput/ObjectInput会自动先寻找是否有这两个方法（即使是`private`），若无则使用默认序列化方法。

## `ObjectOutPutStream` & `ObjectInputStream`

关于被序列化的对象：
- 被序列化对象`private void writeObject(ObjectOutputStream stream)`**TIPS**：`ObjectOutputStream`的`defaultWriteObject()`只能在此方法中被调用，在其他地方调用会抛出`NotActiveException`。
- 被序列化对象`private void readObject(ObjectInputStream stream stream)`**TIPS**：也有`ObjectInputStream`的`defaultReadObject()`。


`ObjectOutPutStream` & `ObjectInputStream`本身的方法
- 两类中均含有大量的对基本类型数据的写/读方法，这些方法一般在`writeObject()`/`readObject()`后使用（规范，无硬性要求）。可以自定义在输出对象的同时输出其他的数据。
  - example：
    被序列化对象中有`tranisent`修饰的字段，这些字段的值不会被序列化，出于安全考虑使用上述的其他方法将其输出到于被序列化对象相同的流中。


