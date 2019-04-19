# hashcode() & equals()
- 两方法均来自于`Object`
- 若修改继承下来的`equals()`方法，则需要连同`hashcode()`一同修改，否则在使用"key-value"集合类的`get()`方法时会得到`null`
	- Reason：因为在大部分“键-值”的集合类对象中，通过key获得value需要同时用到`equals()`和`hashcode()`
	
Tips: `hashcode()`默认为映射对象内存地址。


如，修改`equals`
```
...
 public boolean equals(Object obj) {
        return this.name.equals(((People)obj).name) && this.age== ((People)obj).age;
    }
...
```
但在HashMap中，`get()`方法会调用到`getNode(int hash, Object key)`，其中使用到了hash，即与是否修改`equals()`无关.

