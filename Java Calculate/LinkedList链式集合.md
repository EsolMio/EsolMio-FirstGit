# LinkedList链式集合
## 需要成员
1. 自定义的私有嵌套节点类`Node`，内部有`Node`类型的前指针`Node prev`和后指针`Node next`以及构造方法`Node(Node prev, E element, Node next)`。
2. `LinkedList`本身。
3. `LinkedListIterator`类，实现`Iterator`接口。

## LinkedList
1. `LinkedList`本身需要有两个`Node`类型的handle分别指向头元素和尾元素（头指针&尾指针）。
2. 为了实现可以使用index索引获得对应的node，需要特地申明一int成员变量，用于存储当前node数量
3. 删除节点的本质是：将删除的node所指向的前后node分别指向对反即可（若需要可以将remove的node设为`null`）
4. 

## 注意事项：
1. 需要预备两个`Node`类型的handle分别指向头元素和尾元素（头指针&尾指针）
2. 初始化时将头尾指针和尾指针同时指向`null`。
