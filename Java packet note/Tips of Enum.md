#Enum 枚举

## 创建枚举类
1. 使用关键字`enum`：`public enum EnumClassName{...}`
2. 声明内部成员：`public enum ExampleEnum{MON,THU,WED}`
	- 注意，内部成员的值需指定为大写

- 在创建枚举类时，类将继承`java.lang.Enum`抽象类，枚举类型符合通用范式`Class Enum<E extends Enum<E>>`。
- 枚举内部的成员均会在使用枚举类时映射到`java.lang.Enum#Enum(String name, int ordinal)`构造器中，如上述例2：`new Enum<ExampleEnum>("MON", 1); new Enum<ExampleEnum>("THU", 2); new Enum<ExampleEnum>("WED", 3);`内部成员的名称实际均为`String`值。

## 对枚举类枚举
- 可以使用foreach`for(Enumxx e:Enumxx.values())`，从此可看出内部每个成员的类型实际上均为声明时的类型（Enumxx）
- 可以直接使用`Enumxx.成员名`直接调用其值，如`ExampleEnum.MON`
- 

