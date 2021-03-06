# LinkedList
[arraylist&linkedlist](https://www.cnblogs.com/lxq0309/p/3655742.html)

- **优势**：更快的插入删除，仅改变指针（注意，时间非O(1)，而是O(n)，因为有查找的时间，在使用int查找时速度比用object更快，如remove，使用类似二分查找法）。适合用于实现队列和栈
- **劣势**：不适合查找

## 需要成员
1. 自定义的私有嵌套节点类`Node`，内部有`Node`类型的前指针`Node prev`和后指针`Node next`以及构造方法`Node(Node prev, E element, Node next)`。
2. `LinkedList`本身。
3. `LinkedListIterator`类，实现`Iterator`接口。

## LinkedList
1. `LinkedList`本身需要有两个`Node`类型的handle分别指向头元素和尾元素（头指针&尾指针）。
2. 为了实现可以使用index索引获得对应的node，需要特地申明一int成员变量`theSize`，用于存储当前node数量。实现index获得node的本质是通过index值相对`theSize`的量通过`for`循环获得。
3. 删除节点的本质是：将删除的node所指向的前后node分别指向对反即可（若需要可以将remove的node设为`null`）

## LinkedListIterator
1. 需要声明一`Node`成员变量current指针，默认值指向`LinkedList`的first指针所指向的对象（`current=list`）。通过此指针以通过`next()`获得下一个node所存储的成员。


