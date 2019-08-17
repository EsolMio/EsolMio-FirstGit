#Enum 枚举

## 创建枚举类
1. 使用关键字`enum`：`public enum EnumClassName{...}`
2. 声明内部成员：`public enum ExampleEnum{MON,THU,WED}`
	- 注意，内部成员的值需指定为大写

- 在创建枚举类时，类将继承`java.lang.Enum`抽象类，枚举类型符合通用范式`Class Enum<E extends Enum<E>>`。
- 枚举内部的成员均会在使用枚举类时映射到`java.lang.Enum#Enum(String name, int ordinal)`构造器中，如上述例2：`new Enum<ExampleEnum>("")`

