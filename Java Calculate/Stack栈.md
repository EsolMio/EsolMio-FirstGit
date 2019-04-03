# Stack栈
整体操作可参照`LinkedList`，`ArrayList`的操作，但区别在于Stack的可操作限制于栈顶（TOP）：
1. 相对`ArrayList`：需独立申明一个`int topOfStack`（与`ArrayList`一样拥有数组类型theArray存储数据），指明当前栈顶的index索引，默认值为-1。且需要对其和存储数据的数组设立private。