# Proxy
置于java.lang.reflect包中
代理类实际上是在运行期生成一个实现指定接口（集）的类

## 代理类的实现
- 代理的实现需要通过调用`Proxy`类：
`newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler handel):Object`方法，此方法获得的返回值为代理类。
	- 创建代理类将依赖实现了`InvocationHandle`接口的类
- 所生成的代理类将会实现指定的接口（集），在调用所实现接口的方法时，本质上调用的是`InvocationHandler`中的`invoke()`方法。对于多个接口，`Proxy`将会针对不同的接口，将对应的方法对象`Method`和所需要的参数实例集合`Object[] args`传递给`invoke()`（然而`invoke()`只有一个）。
- 对于实现`InvocationHandler`接口的类，需要注入被代理的实例，此实例将会在`invoke()`声明具体实现时被使用于“实际方法调用”——通过`method.invoke(insertInstance, args)`，即调用代理对应的方法实质上是调用了被代理实例的方法。