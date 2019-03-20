# Class 对象

## `Class`的使用
`Class`拥有的几个常用方法：
1. `Class.forName(String className)`，`static`方法 ，通过`className`加载对应的类，可用于运行期加载额外类。
**注意**：
	1. 类需要放在环境变量`PATH`设定的位置中。
	2. `className`为类全称，如：`java.io.InputStream`

2. `.getConstructor(Class<?>... parameterTypes)`，成员方法。
	- 其中`Class<?>...`表示`Class<?>`数组，通过指定的`parameterTypes`类型参数获得对应的构造器，返回`Constructor`。
	- 功能相似的方法：`getConstructors()`，将全部的构造器对象返回，返回`Constructor[]`。

3. `.getMethod(Class)`