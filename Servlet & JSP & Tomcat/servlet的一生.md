# servlet的一生
- web容器（服务器）加载类（load .class file）
- 调用servlet的构造函数（不需要自行编写构造函数），此时的servlet尚不完整，无法正常使用
- web容器调用`init()`方法，此方法正式初始化servlet（因在使用默认构造函数初始化servlet对象时，容器也在初始化其他对象（如HttpServletRequest，ServletConfig，ServletContext））