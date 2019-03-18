# 对类的clone()控制
tips: 
1. 对本类的克隆均是调用Object.clone()实现（需要先实现Cloneable接口），其返回值类型为Object
2. Base class: `class Ordinary{}`
3. 克隆的控制本质为在父类中重写clone()方法将子类对Object.clone()的调用截断，且在clone()中实现自定义的控制方法
## 1. 有条件克隆，不实现Cloneable接口
解释：将clone()的权限范围从protected改为public，让其子类继承此方法，且任何类都可以调用此方法。返回值为super.clone();注意不实现Cloneable接口。只有继承且实现Cloneable接口的对象才可正常克隆

Thinking：个人认为原本提供的实现克隆规则即可，不必大费周章使用此方法

Code：
```
class WrongClone extends Ordinary{
    public Object clone() throws CloneNotSupportedException{
        return super.clone();
    }
}
```
## 2. 完全支持克隆，实现Cloneable接口
解释：重写clone()为public，实现Cloneable接口。

Code:
```
class IsCloneable extends Ordinary implements Cloneable{
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```
## 3. 通过抛出错误"关闭"克隆方法
解释：改写clone()为public，但在方法中直接加入掷出错误CloneNotSupoortedException。在没有添加自定义catch错误处理方法时JVM会自动调用printStackTrace()并退出程序，阻止克隆。倘若捕捉了也无法克隆。

Code:
```
class TurnOffClone extends Ordinary{
    public Object clone(){
        throw new CloneNotSupportesException();
    }
}
```
## 4. 调用private方法返回一个新的对象
解释：在父类中创建一private方法，返回值为新创建的父类实例，虽然获得了同类型的值，但并不是克隆了原对象。

Tips:实例方法只有对应的对象才可调用

Code:
```
class BackOn extends TurnOffClone {
    private BackOn duplicate(BackOn b){
        return new BackOn();
    }
    public Object clone(){
        return duplicate(this);
    }
}
```

# `clone()`的Tips

## Reason:为何将`clone()`重写，且修改修饰符为`public`?
**因**：`Object`中的`clone()`为`protected`，`protected`仅对`Object`所在的包和其子类可见，若子类不与`Object`在同一包（根本就不在），则无法直接在于`Object`子类同一包的其他类中调用`clone()`方法。
**所以**：所以需要将`clone()`改为public，并在内部调用`super.clone()`
***

## Reason:为何调用`super.clone()`?
因为查看`Object`的源码可知`clone`有一修饰符为`native`，调用了第三方C语言文件，重写`clone()`方法后其修饰符`native`作用会消失，失去本身作用。

# `clone()`的控制
## 1. 支持`clone()`

