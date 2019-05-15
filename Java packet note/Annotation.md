# Annotation 注解
- 声明一个注解类时需要用到`@interface`修饰符，Annotation本质为接口，默认继承了`Annotation`接口。

- 对于`Annotation`接口，有两个辅助类，分别为`ElementType`和`RetentionPlicy`

- 对于一个`Annotation`，有n个`ElemtenType`，但只有一个`RetentionPolicy`

![关系图](../.local/static/2019/4/3/1557914268720.1557914268825.png)

详细代码：
- `Anno`