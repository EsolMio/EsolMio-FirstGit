# Tips of 继承/上溯造型

## 为何使用上溯造型呢？
最直观的来讲，如果不使用上溯造型，当方法需要通过参数的方式对自己传入继承自同一类的实例时，会出现方法冗余：
example：
```
public class Music2 {
   //大量且重复的方法（多次重载）
  public static void tune(Wind2 i) {
    i.play(Note2.middleC);
  }
  public static void tune(Stringed2 i) {
    i.play(Note2.middleC);
  }
  public static void tune(Brass2 i) {
    i.play(Note2.middleC);
  }
  
  public static void main(String[] args) {
    Wind2 flute = new Wind2();
    Stringed2 violin = new Stringed2();
    Brass2 frenchHorn = new Brass2();
    tune(flute); // No upcasting
    tune(violin);
    tune(frenchHorn);
}

```
而上溯造型，因子类的实例本质为同一类，拥有相同的实现方法（从父类继承下来），子类可因“个性”而对方法重写，但无论怎么修改均是同一种类的方法（方法同名）。故使用上溯造型时可快捷的调用同名的方法，节省代码。

## 继承/上溯造型
1. son只能继承father的default，protected，public成员值和方法。
2. son上溯造型至father后新建一实例：
    1. 若调用了从父类继承方法，未对方法修改（其中方法有使用成员变量/static变量），无论是否对继承来的成员变量/static变量进行修改，具体实现依旧是父类的


**总结：** 上溯造型后依旧使用son的定义

## `super`
在子类使用`super`**调用父类的方法**时，执行的是父类定义的具体实现方法，若父类的方法中调用了自己的成员变量/static变量，则**无论子类是否有更改继承下来的成员变量/static变量，或者是否使用了上溯造型**，使用的依旧是父类的值。

## `final`
使用`final`可令JVM取消对所修饰的句柄/变量取消“动态绑定”,转为“早期绑定”。此法可提升所修饰值的使用效率。
- 动态绑定：
  >解决的方法就是“后期绑定”，它意味着绑定在运行期间进行，以对象的类型为基础。后期绑定也叫作“动态绑定”或“运行期绑定”。若一种语言实现了后期绑定，同时必须提供一些机制，可在运行期间判断对象的类型，并分别调用适当的方法。也就是说，编译器此时依然不知道对象的类型，但方法调用机制能自己去调查，找到正确的方法主体。不同的语言对后期绑定的实现方法是有所区别的。但我们至少可以这样认为：它们都要在对象中安插某些特殊类型的信息。
- 早期绑定：
  >若在程序运行以前执行绑定（由编译器和链接程序，如果有的话），就叫作“早期绑定”。
