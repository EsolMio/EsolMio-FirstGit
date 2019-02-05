# 对类的clone()控制
tips: 
1. 对本类的克隆均是调用Object.clone()实现（需要先实现Cloneable接口），其返回值类型为Object
2. Base class: `class Ordinary{}`
## 1. 扩增权限范围，不实现Cloneable接口
解释：将clone()的权限范围从protected改为public，让其子类继承此方法，且任何类都可以调用此方法。返回值为super.clone();注意不实现Cloneable接口。

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
## 通过抛出错误"关闭"克隆方法
解释：改写clone()为public，但在方法中直接加入掷出错误CloneNotSupoortedException。在没有添加自定义catch错误处理方法时JVM会自动调用printStackTrace()并退出程序。阻止克隆，若

Code:
```
class TurnOffClone extends Ordinary{
    public Object clone(){
        throw new CloneNotSupportesException();
    }
}
```