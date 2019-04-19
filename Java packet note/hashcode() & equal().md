# hashcode() & equals()
- 两方法均来自于`Object`
- 若修改继承下来的`equals()`方法，则需要连同`hashcode()`一同修改
	- Reason：因为在大部分“键-值”的集合类对象中，通过key获得value需要同时用到`equals()`