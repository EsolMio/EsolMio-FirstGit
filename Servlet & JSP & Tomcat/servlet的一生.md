# servlet的一生
- web容器（服务器）加载类（load .class file）
- 调用servlet的构造函数（不需要自行编写构造函数），此时的servlet尚不完整，无法正常使用
- web容器调用