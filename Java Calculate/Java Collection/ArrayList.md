# ArrayList
- 实现了`List<E>`接口，存储的方式为array数组
- `ArrayList`有自身的容量（此处指容纳的量），容量的大小由数组中存储的数据量决定，容量至少会同list的长度（即类内部数组的长度）相同。
- 当向ArrayList添加元素时，其容量会自动增加
- 可以通过`ensureCapacity(int minCapacity):void `调整ArrayList的容量，但有必要在调整前先明确ArrayList的最小容量大小。否则会调整失败
- **优势**：更快的顺序查找速度，但在插入和删除时速度会变慢（往中间插值时需要对后方影响），