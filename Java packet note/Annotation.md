# Annotation 注解 
[Java Annotation认知(包括框架图、详细介绍、示例说明)](https://www.cnblogs.com/skywang12345/p/3344137.html)
- 声明一个注解类时需要用到`@interface`修饰符，Annotation本质为接口，默认继承了`Annotation`接口。

- 对于`Annotation`接口，有两个辅助类，分别为`ElementType`和`RetentionPlicy`

- 对于一个`Annotation`，有n个`ElemtenType`，但只有一个`RetentionPolicy`

![关系图](../.local/static/2019/4/3/1557914268720.1557914268825.png)

### 详细代码：
- `Annotation`
```
package java.lang.annotation;
public interface Annotation {

    boolean equals(Object obj);

    int hashCode();

    String toString();

    Class<? extends Annotation> annotationType();
}
```
- `ElementType`
```
package java.lang.annotation;

public enum ElementType {
    TYPE,               /* 类、接口（包括注释类型）或枚举声明  */

    FIELD,              /* 字段声明（包括枚举常量）  */

    METHOD,             /* 方法声明  */

    PARAMETER,          /* 参数声明  */

    CONSTRUCTOR,        /* 构造方法声明  */

    LOCAL_VARIABLE,     /* 局部变量声明  */

    ANNOTATION_TYPE,    /* 注释类型声明  */

    PACKAGE             /* 包声明  */
}
```
- `RetentionPolicy`
```
package java.lang.annotation;
public enum RetentionPolicy {
    SOURCE,            /* Annotation信息仅存在于编译器处理期间，编译器处理完之后就没有该Annotation信息了  */

    CLASS,             /* 编译器将Annotation存储于类对应的.class文件中。默认行为  */

    RUNTIME            /* 编译器将Annotation存储于class文件中，并且可由JVM读入 */
}
```
## 声明一个自定义`Annotation`
需要有最基本的两个注释`@Target`, `@Retention`，注解内的属性分别对应了`ElementType`和`RetentionPolicy`两个类。
- `@Target`对应声明本注解需要注解的对象
- `@Retention`对应声明本注解可存在的阶段

### 在Annotation中声明方法
先上例子
```
package com.selfAnnotation

@Target(ElementType.Method)
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation {
    // 1.
    String[] value() default "unknown";
}
```
```
package com.testAnnotation

import com.selfAnnotation.MyAnnotation

class Test{
    // 2.
    @MyAnnotation(value = {"test1", "test2"})
    class test(String name, int num){
        System.out.println("test: "+name+", "+num);
    }
}
```
```
package other

import com.testAnnotation.Test

public class AnnotationTest {

    public static void main(String[] args) throws Exception {
        Test testO = new Test();
        Class<Test> t = Test.class;
        // 获取 test() 方法的Method实例
        Method test = t.getMethod("test", new Class[]{String.class, int.class});
        // 执行该方法
        test.invoke(testO, new Object[]{String.class, int.class});        
        iteratorAnnotations(test);
    }

    public static iteratorAnnotation(Method method){
        if(method.isAnnotationPresent(MyAnnotation.class)==true){
            // 判断此方法是否有被MyAnnotation标注
            // 3.
            MyAnnotation myAnnotation = method.getAnnotation(MyAnnotation.class);
            // 打印MyAnnotation中的"value"值
            String[] elements = myAnnotation.value();
            for(String element:elements)
                System.out.print("the element of value is: "+element);
        }
    }
    
    
}
```
1. 在自定义的注解中声明的成员方法均为抽象，无实际执行方法。因注解类**本质为接口**。

	- 使用关键字`default`指定对应方法默认返回的值（默认值类型和返回类型相同）

2. 在对应的位置上设置注解，因注解的`@Target`设置为`ElementType.METHOD`，故只能注解在方法上；设置了`value`值，此值将会体现在`value()`方法的返回值中。

3. 通过反射技术获得对应方法的注解

	- 获得注解中设置的值的方法和调用普通方法的方式相同


## 反射机制中的`Annotation`
对于`Class`, `ConStructor`, `Method`, `Field`类中，均有获得注解对应类的注解类的方法`getAnnotation(Class<T> annotation):<T extends Annotation>`，此方法会获得对应参数指明的注解类。若无对应注解，则返回`null`。此方法可用于验证是否拥有对应注解。