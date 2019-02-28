# Note of com.TIJ_C12_2_Clone.TIJCode_Compete
### Reason:为何将clone()重写，且修改修饰符为public
因Object中的clone()为protected，protected仅对Object所在的包和其子类可见，若子类不与Object在同一包（根本就不在），则无法直接在于Object子类同一包的其他类中调用clone()方法。
