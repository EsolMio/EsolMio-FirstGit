# Class 对象

## `Class`的使用
Tips：构建数组时可用：
1. `Test[] testArray={new Test(1), new Test(2), ...};`
2. `Test[] testArray=new Test[4];`
3. `new Test[]{ new Test(1), new Test(2), ... }`//无等号下创建数组。

`Class`拥有的几个常用方法：
1. `Class.forName(String className)`，`static`方法 ，通过`className`加载对应的类，可用于运行期加载额外类。
**注意**：
	1. 类需要放在环境变量`PATH`设定的位置中。
	2. `className`为类全称，如：`java.io.InputStream`

2. `.getConstructor(Class<?>... parameterTypes)`，成员方法。
	- 其中`Class<?>...`表示`Class<?>`数组，通过指定的`parameterTypes`类型参数获得对应的构造器，返回`Constructor`。
	- 功能相似的方法：`getConstructors()`，将全部的构造器对象返回，返回`Constructor[]`。
	- Tips：使用`Constructor`实例创建新对象（使用`newInstance(Object... iniargs)`）时注意要在方法中传输构造方法所需的参数，以`Object[]`的形式，**对于传输基本类型参数，需将其封装于封装类内部才可传输**。

3. `.getMethod(String name, Class<?>... parameterTypes)`成员方法。
	- 此方法将通过`name`参数查找方法，并需要和`patameterTypes`匹配，倘若查找不到对应的方法，则会在超类上递归调用本方法，直至找到。最终返回一个`Method`实例。
	- 功能相似的方法：`getMethods()`，将全部方法返回，返回`Method[]`。

## 关于基本类型的Class对象
- 对于基本类型，如：int，double，char，包括void，都可以生成对应的Class对象，通过`.class`。
	- Example: `int.class`, `void.class`
- **注意**：对于基本类型的封装类，Integer，Double等等，若想生成与`int.class`, `double.class`相同的class对象，需要使用`.TYPE`
	- Example:
          
          Integer.TYPE == int.class return true
          Integer.class == int.class return false
- 基本类型和封装类在Java 5中支持自动装箱/拆箱：
	- “装箱”：将基本类型的值用`=`赋值给封装类对象j，JVM自动调用Integer的构造器。
		- Example:`Integer i=10;`
	- “拆箱”：将封装类对象的值（成员变量）通过`=`赋值给基本类型。JVM自动调用Integer对象的
		- Example：`Integer i = new Integer(10); int ii=i;`



