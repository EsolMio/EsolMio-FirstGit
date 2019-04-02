# LinkedList链式集合
## 需要成员
1. 自定义的私有嵌套节点类`Node`，内部有`Node`类型的前指针`Node prev`和后指针`Node next`以及构造方法`Node(Node prev, E element, Node next)`。
2. `LinkedList`本身。
3. `LinkedListIterator`类，实现`Iterator`接口。

## 注意事项：
1. 需要预备两个`Node`类型的handle分别指向头元素和尾元素（头指针&尾指针）
2. 初始化时将头尾指针
