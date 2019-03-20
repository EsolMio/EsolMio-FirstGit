# Class 对象

## `Class`的使用
`Class`拥有的几个常用方法：
1. `Class.forName(String className)`，`static`方法 ，通过`className`加载对应的类。
**注意**：
	1. 类需要放在环境变量`PATH`设定的位置中。
	2. `className`为类全称