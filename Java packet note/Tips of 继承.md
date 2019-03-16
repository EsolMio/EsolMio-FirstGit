# Tips of 继承/上溯造型
## 继承/上溯造型
1. son只能继承father的default，protected，public成员值和方法。
2. 从son上溯造型后，若son修改了从father继承来的成员值或方法，造型后依旧使用修改后的值和方法，即使用的依旧是自己的属性，若在son的方法中调用了super以访问可访问的成员值，其值依旧是：
   - son未修改从father继承来的值，则继续使用原本的值
   - son修改了从father继承来的值，则使用son修改过的值
3. 若在son中通过super使用了father的方法，则通过super调用方法的具体实施过程将会延续原方法，但值使用的是son的值。**可理解为：** 将father的方法整体搬过来使用但father方法内部涉及的值依旧使用son的

**总结：** 上溯造型后依旧使用son的定义

## 