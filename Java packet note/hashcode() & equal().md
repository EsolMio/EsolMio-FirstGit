# hashcode() & equals()
- 两方法均来自于`Object`
- 在没有覆盖`equals()`和`hashCode()`时，`equals()`将通过`==`（内存地址）判断两值是否相等。`hashCode()`将通过特定的散列函数计算出散列值，因为可存储数值的有限性（如`Integer.Max_VALUE/Integer.MIN_VALUE`），将可能发生碰撞。

## 存在`hashcode()`的意义：
缓解大量检索相同值时反复调用`equals()`带来的高耗能。通过事先调用`hashcode()`获得的值判断相等性。

## `hashcode()`碰撞问题
因为存储范围有限，不同对象的hash可能碰撞，故存在以下定义：
1. 若两个句柄指向的对象相同，则其hash值相同，相同算法下，自变量值相同则结果相同。
2. 若hash值相同，两个句柄指向的对象**不一定相同**。
3. 若在`Hashtable`中出现碰撞，则通过`LinkedList`存储碰撞值（值本质上不相同）。

Tips：在`HashTable`，查找相同hash对象在`LinedList`中的位置将通过`equals()`遍历。

Tips: `hashcode()`**默认为映射对象内存地址**。

## `equals()`的要求
无论是继承`Object#equals()`亦或者Override，均需符合下述三点：
1. 对称：if x.equals(y) -> true, then y.equals(x )-> true
2. 自反：x.equals(x) -> true
3. 传递：if x.equals(y) -> true, y.equals(z) -> true, then x.equals(z) -> true
4. 一致：whatever, if nothing change of method `equals()`, multiple invocations of x.equals(y) consistently return true or false, unless `equals` has changed.

倘若无法满足以上全部点，将发成不可预料（exceptionable）情况

如，修改`equals`
```
...
 public boolean equals(Object obj) {
        return this.name.equals(((People)obj).name) && this.age== ((People)obj).age;
    }
...
```
但在HashMap中，`get()`方法会调用到`getNode(int hash, Object key)`，其中使用到了hash，即与是否修改`equals()`无关.

