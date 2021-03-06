# Observable 观察器范式

## 使用`Observable`
在MVC模型中的“模型-视图”结构等价于“文档-视图结构”，当文档发生改变时/数据发生改变时，需要通知到每个“视图结构”数据发生改变，需要更新。此时使用观察器`Observer`
**Tips**：`Observer`为接口，`Observable`为完整类。

- 准备一类继承`extends Observable`类，虽然`Observable`本质为完整类，但若直接使用组合的方式使用其对象将无效果，故使用继承。

- 将”视图“类实现`implements Observer`，并定义`update(Observable ob, Object o)`。

- 将`Observable`类组合进被观察的类中，并在适当的地方（被观察类内部/重写的`Observable`构造器/其他地方）使用`Observable`实例的`.addObserver(Observer ob)`方法添加实现`Observer`的实例。

- 当需要”观察“的类数据发生改变后，调用`Observable`实例的`.notifyObservers(Object arg)`（参数arg可将更改的数据传至每个`Observer`实例）以及`.setChange()`(**必须**)

- 随后将会在`Observable`的`.notifyObservers()`中调用到每个`Observer`实例的`update()`方法（前提是`!changed`为false，即之前已正确调用`setChanged()`，否则此方法将失效）。