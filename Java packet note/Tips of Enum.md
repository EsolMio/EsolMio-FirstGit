#Enum 枚举

## 创建枚举类
1. 使用关键字`enum`：`public enum EnumClassName{...}`
2. 声明内部成员：`public enum ExampleEnum{MON,THU,WED}`
	- 注意，内部成员的名称需为大写

- 在创建枚举类时，类将继承`java.lang.Enum`抽象类，枚举类型符合通用范式`Class Enum<E extends Enum<E>>`。
- 枚举内部的成员均会在使用枚举类时映射到`java.lang.Enum#Enum(String name, int ordinal)`构造器中，如：`new Enum<ExampleEnum>("MON", 1); new Enum<ExampleEnum>("THU", 2); new Enum<ExampleEnum>("WED", 3);`内部成员的名称实际均为`String`值。
- 枚举类内部成员可理解为主类的子类，如：
  ```java
  public interface RoleOpreation{
	  String option();// 通过接口确定每个实现此接口的“角色”均做同一件事，做法不同。
  }
  public enum RoleEnuy implements RoleOpreation{
	  // 可见两个成员均需要根据接口要求实现相应的方法，可理解为各为个体
	  ROLE_ROOT_ADMIN{
		  @Override
		  public String option(){
			  return "ROLE_ROOT_ADMIN";
		  }
	  }

	  ROLE_ORDER_ADMIN{
		  @Override
		  public String option(){
			  return "ROLE_ORDER_ADMIN";
		  }
	  }
  }

  // 使用时：
  public class JudgeRole {
	  public String judge(String roleName){
		  return RoleEnum.valueOf(roleName).op();
	  }
  } 
  ```

  - 与上述相似的方法：工厂方法
  ```java
  // 定义不同的角色
  public class RootAdminRole implements RoleOperation {
	  private String roleName;
	  public RootAdminRole(String roleName){
		  this.roleName = roleName;
	  }

	  @Override
	  public String option(){
		  return roleName + " has xxx.";
	  }
  }

  public class OrderAdminRole implements RoleOperation {
	  // ... same as upon
  }
  ```
  工厂类(聚合上述角色 Map)：
  ```java
  public class RoleFactory {
	  static Map<String, RoleOperation> roleOperationMap =new HashMap<>();

	  static{
		  roleOperationMap.put("ROLE_ROOT_ADMIN", new RootAdminRole("ROLE_ROOT_ADMIN"));
		  roleOperationMap.put("ROLE_ORDER_ADMIN", new OrderAdminRole("ROLE_ORDER_ADMIN"));
	  }

	  public static RoleOperation getOp(String roleName){
		  return roleOperationMap.get(roleName);
	  }
  }
  ```

## 对枚举类枚举
- 可以使用foreach `for(Enumxx e:Enumxx.values())`，从此可看出内部每个成员的类型实际上均为声明时的类型（Enumxx）
- 可以直接使用`Enumxx.成员名`直接调用其值，如`ExampleEnum.MON`
- 对于自行实现的枚举类，因继承 `extends Enum` 类，拥有static方法 `valueOf()`，在自定义的枚举类中使用此方法可直接通过成员名获得相应的对象，并调用相应的值 

