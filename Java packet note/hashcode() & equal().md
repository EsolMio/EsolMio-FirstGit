# hashcode() & equals()
- 两方法均来自于`Object`
- 若想实现通过类型相同对象不相同的key在散列表中获得相同的value，则需要在修改继承下来的`equals()`方法，同时修改`hashcode()`，否则将会得到`null`。
	- Reason：因为在大部分“键-值”的集合类对象中，通过key获得value需要同时用到`equals()`和`hashcode()`
	- 通过`hashcode()`验证两个对象是否不同（不验证相同），当hash值不同时则对象绝对不同。
	- 再通过`equals()`

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

