# Tips of 继承/上溯造型
## 继承/上溯造型
son只能继承father的default，protected，public成员值和方法。
son上溯造型后，若son修改了从father继承来的成员值或方法，造型后依旧使用修改后的值和方法，即使用的依旧是自己的属性，若在son的方法中调用了super以访问可访问的成员值，其值依旧是。