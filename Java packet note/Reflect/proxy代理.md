# Proxy
置于java.lang.reflect包中

## 代理类的实现
代理的实现需要通过调用`Proxy`类：
`newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler handel):Object`方法，此方法获得的返回值为Object