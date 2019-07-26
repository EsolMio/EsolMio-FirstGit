# Tips of 内部类
### 使用内部类的目的：
因为Java本来拥有对类的隐藏机制(e.g. package-包，访问权限protected-只允许继承的子类访问以及同一包内的成员)，故单纯使用内部类的private机制意义不大
但在上溯造型到一个基础类/接口时，内部类private的隐藏机制便发挥作用，**上溯造型后内部类为不可见状态——对任何对象均是如此**
### 方法：
对内部类使用private修饰，并使用**封装类**的**成员方法(public)** 返回内部类新建对象(new/newInstance())
tips: 内部类依赖封装类的**对象**，有**封装类**对象才可以使用内部类
e.g.
```
class Test{
    private class InsideClass extends Cloneable{}
    public Cloneable returnInsideClass(){
        
    }
}
```
# 内部类 & 嵌套类
## 基本
1. 嵌套类（nested class），内部类的一种，需要有`static`修饰。
2. 内部类，无`static`修饰。
3. 命名：对于这两者，其全名均为`packageName.OutClassName$InsertedClassName`，使用"$"符号表示。


## 两者的区别：
1. `static`修饰的class无法使用实例（instance）的任何内容。
2. 在外部类中可直接使用内部类（无论是否是嵌套类）的构造器名构造实例，但在外部有区分（嵌套类：`new InsertClassTest.TestClass2(test)` 和调用静态方法相类似, 内部类：`test.new TestClass()`）

即：嵌套类与外部类之间的关系为相互独立的，内部类可使用外部类的实例成员。但对于外部类而言，都不可以直接访问嵌套/内部类，需要创建对应的实例才可访问。

Tips：在内部类中可使用`外部类名.this`的方式使用到外部类的实例（嵌套类不可），无法直接使用`this`来代表外部类的实例（只能表示“自己”）。

# 内部类调用外部类的实例
当内部类的方法定义了需使用外部类的实例，
**ATTENTION:不能在内部类中直接使用`this`字符**
**应当使用:`外部类类名.this`以调用外部类实例**
****
tips：内部类中的`this`指示为内部类实例，故需要使用另外的方法来指代外部/嵌套类的实例：`外部类类名.this`
tips2：此处的提醒仅适用于内部类，包括未被`static`修饰的内部类和隐藏类。对于`static`修饰的内部类-统称嵌套类，无法使用`外部类类名.this`调用外部类实例，只能通过其他渠道获得后者使用外部类构造器创建一个新外部类实例。
