# HashMap Tips

## show Code
```java
static final int hash(Object key){
    int h;
    // 向左位移加异或
    return (key == null) ? 0 : (h = key.hashcode()) ^ (h >>> 16)
}

// from java.util.HashMap
// @param key的hash
// -------------------------------------------------
// 使用了链表(Node)和红黑树(TreeNode extends LinkedHashMap.Entry)进行存储key，value
// 同时还有"table"filed用于存储节点(Node & TreeNode)
// -------------------------------------------------
// putVal()中，hash值用于查找对应于table中的位置
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        // tab获得table(HashMap.Node<K,V>[])句柄
        // 倘若初始长度为0则resize()数组
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length; // 默认初始长度为"1<<4=16"
        // --------------------------------------------------
        // 在"(n-1) & hash"中使用hash确定对应key在table中的索引。
        // "(n-1) & hash"在n为以2为底的幂的结果时(2^i)，结果与"hash % n"等效。
        // 若存在计算后索引相同，则表示hash值相同。
        // 若table对应索引为空直接放值，且该值为Node节点类型。
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {
            // --------------------------------------------------
            // @param e cache @param k key from tab`s entity key
            Node<K,V> e; K k;
            // --------------------------------------------------
            // Other Tips: 使用两种方法判断p是否与置入值相等，通过两种方法判断:
            // 1. 置入值的key的hash值和从tab中获得(p)的key值比较，及将key实例与tab中获得的key值使用"=="比较
            // "=="比较的是Java标识，即两个引用在内存中的位置，对于基础类型(int, char, double, float...)以及String类型，"=="与equal()意义相同
            // 2. 判断key是否为空且key是否等于(equals())tab中的key值
            // --------------------------------------------------
            // 此处第一个判断用于判断置入的key是否存在于表中，若是则跳至下个if
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            // 判断table中的p元素是否为TreeNode（红黑树）节点，若为TreeNode，则将值放入TreeNode中
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                for (int binCount = 0; ; ++binCount) {
                    // 不断查找是否有next为空的节点，若存在则创建新值
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        // 若成功创建新值则判断链表是否超过链表限定值TREEIFY_THRESHOLD=8，若超过则将链表转化为红黑树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    if (e.hash == hash &&
                        ( == key || (key != null && key.equals(k))))
                        break;(k = e.key)
                    p = e;
                }
            }

            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null) // 存在oldValue可为空，即HashMap可存放value为空值
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        // HashMap修改次数增加
        ++modCount;
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
}

// 获得目标节点，在传入key之前先对key做hash运算
final Node<K,V> getNode(int hash, Object key) {
        // tab: cache of table; first: first node of LinkedList or red-black tree; e: node back of first; n: length of table; k: 
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        // 首先判断table是否为空，长度是否为0，通过hash值获得的索引在table中是否存在对应值
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            // 若next不为null
            if ((e = first.next) != null) {
                // 判断first节点是否为TreeNode
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                // 不为TreeNode下进行遍历查找
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
}
```

### Tips:
1. hash值与`Node<T>[]`之间的映射关系：通过映射函数"(n-1) 7 hash"确定`Node<T>[]`上对应索引。
   - 但需要明确：相同的索引并不一定`hash(Node.key)`的值相同，因为映射函数为"且"运算，如：在jdk 7的`HashMap`中使用`transfer()`对数组扩容后转移元素，会出现以下情况：
   ```
    Before           After

   [0] [1]   |  [0] [1]  [2] [3]       reason of key:11
        ^    |       ^        ^         
      key:5  |    key:9    key:11      before:
    Value:A  |  value:B  value:C       bit 11:1011
        ^    |       ^        ^        bit  1:0001
      key:9  |    key:5     null            & ----
    Value:B  |  value:A                       0001 = 1 
        ^    |       ^                 after:
      key:11 |     null                bit 11:1011
    Value:C  |                         bit  3:0011
        ^    |                              & ----
       Null  |                                0011 = 3
   ```
   - ^^^ 在扩容之前"Key:11"在`Node<T>[1]`上。但在扩容之后，"Key:11"位置移动至`Node<T>[3]`上。源码如下：
   ```java
    // Code in jdk 7
    void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;
        for (Entry<K,V> e : table) {
            while(null != e) {
                Entry<K,V> next = e.next;// thread 1 block in here
                if (rehash) {
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }
   ```
   - 上方的代码在多线程中将有可能出现死循环：
     1. 倘若线程1在执行至`Entry<K,V> next = e.next;`因时间片用完而阻塞，此时线程2完成了`transfer()`:
     ```
     Thread 1
     block by time:

                  Node[]
                   [0]       
                   [1]->(key:5; value:A)->(key:9; value:B)->null
                               ^                 ^
                         Thread 1 "e"      Thread 1 "next"
     -----------------------------------------------------------
     Thread 2
     finish transfer():

                  Node[]              
                   [0]
                   [1]->(key:9; value:B)->(key:5; value:A)->null
                   [2]         ^                  ^
                   [3]   Thread 1 "next"      Thread 1 "e"
     ------------------------------------------------------------
     Tread 1
     excute function continute:
     
                  Node[]              
                   [0]   Thread 1 "next"      Thread 1 "e"
                   [1]->(key:5; value:A)->(key:9; value:B)
                   [2]         ^                 |
                   [3]         |_________________|

     in this part linkedlist change into a loop,
     this is the reason why in "transfer()" used by mulity thread jdk-7`s HashMap will fall into dead-circle 
     ```