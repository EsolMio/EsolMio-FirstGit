# Note of com.TIJ_C12_2_Clone.TIJCode_Compete
### Reason:为何将`clone()`重写，且修改修饰符为`public`?
**因**：`Object`中的`clone()`为`protected`，`protected`仅对`Object`所在的包和其子类可见，若子类不与`Object`在同一包（根本就不在），则无法直接在于`Object`子类同一包的其他类中调用`clone()`方法。
**所以**：所以需要将`clone()`改为public，并在内部调用`super.clone()`
***
### Reason:为何调用`super.clone()`?
因为查看`Object`的源码可知`clone`有一修饰符为`native`，调用了第三方C语言文件，重写`clone()`方法后其修饰符`native`作用会消失，失去本身作用。
