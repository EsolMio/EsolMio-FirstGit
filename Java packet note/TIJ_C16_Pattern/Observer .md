# Observable 观察器范式
## 使用`Observable`
在MVC模型中的“模型-视图”结构等价于“文档-视图结构”，当文档发生改变时/数据发生改变时，需要通知到每个“视图结构”数据发生改变，需要更新。此时使用观察器`Observer`
**Tips**：`Observer`为接口，`Observable`为完整类。

- 准备一类继承`extends Observable`类，虽然`Observable`本质为完整类，但若直接使用组合的方式使用其对象将无效果，故使用继承。
- 将”视图“类实现`implements Observer`，并定义`update(Observable ob, Object o)`。
- 将`Observable`类组合进被观察的类中，并在适当的地方（被观察类内部/重写的`Observable`构造器中）
- 当需要”观察“的类数据发生改变后，调用