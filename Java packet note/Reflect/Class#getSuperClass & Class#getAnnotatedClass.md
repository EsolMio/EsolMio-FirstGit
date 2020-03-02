# Class#getSuperClass() & Class#getAnnotatedClass() different
## Class#getSuperClass()
- `getSuperClass()`直接返回本类的超类.
- 若本类为`Object`, `Interface`, `Integer等基本类型`, `void`，则调用此方法后返回`null`.
- 如果本类为数组对象，则返回`Object.class`.

# Class#getAnnotatedClass()
- 返回指代本类的超类——参数类的`Type`类型实例(return type: Interface-`Type`).实际返回类型为`ParameterizedType`，由`sun.reflect.generics.reflectiveObjects.ParameterizedTypeImpl`提供实现。但在通过标准输出流`System.out.println()`输出的信息为`父类类名<参数类名>`.
- 若本类(class)代表为object，interface，基本类型，void，则此方法返回`null`。
- 若为数组，则返回java.lang.Object

Example:
```java
package com.example.test;

public class User{

}

```
```java
package com.example.repository;

public class BaseHibernateDao<T, ID extends Serializable>{
    private Class<T> clazz;

    public BaseHibernateDao(){
        Type genericSuperClass = getClass().getGenericSuperClass();
        if(genericSuperclass instanceof ParameterizedType){
            ParameterizedType parameterizedType=(ParameterizedType)generciSuperClass;
            Type[] actualTypeArgs=parameterizedType.getActualArguments
            this.clazz=(Class<T>)actualTypeArgs[0];
        }else{
            this.clazz=(Class<T>)genericSuperClass;
        }
    }

    public Class<T> getClass(){
        return clazz;
    }
}
```
```java
public Class UserService extends BaseHibernateDao<User,Integer>{

}
```

```java
public Class MainRun{
    public static void main(String[] args){
        UserService service=new UserService();
        System.out.println(service.getClass());
    }
}
```
OutPut: `class com.example.test.User`