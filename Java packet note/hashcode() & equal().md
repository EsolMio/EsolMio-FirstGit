# hashcode() & equals()
- 两方法均来自于`Object`
- 若修改继承下来的`equals()`方法，则需要连同`hashcode()`一同修改，否则在使用"key-value"集合类的`get()`方法时会得到`null`
	- Reason：因为在大部分“键-值”的集合类对象中，通过key获得value需要同时用到`equals()`和`hashcode()`，`hashcode()`默认为映射对象内存地址。

如，修改`equals`
```
...
 public boolean equals(Object obj) {
        return this.name.equals(((People)obj).name) && this.age== ((People)obj).age;
    }
...
```
但在HashMap中，`get()`的具体实现为：
```
public V get(Object key) {
    if (key == null)
        return getForNullKey();
    // 获得key的hash值
    int hash = hash(key.hashCode());
    for (Entry<K,V> e = table[indexFor(hash, table.length)];
        e != null;
        e = e.next) {
        Object k;
        // 通过hash值
        if (e.hash == hash && ((k = e.key) == key || key.equals(k)))
            return e.value;
        }
        return null;
    }
```
