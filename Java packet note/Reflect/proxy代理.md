# Proxy
置于java.lang.reflect包中
代理类实际上是在运行期生成一个实现指定接口（集）的类

## 代理类的实现
代理的实现需要通过调用`Proxy`类：
`newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler handel):Object`方法，此方法获得的返回值为代理类。
所生成的代理类将会实现指定的接口（集），在调用所实现接口的方法时，本质上调用的是`InvocationHandler`中的`invoke()`方法