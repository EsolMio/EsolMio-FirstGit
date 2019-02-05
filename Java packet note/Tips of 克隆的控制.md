# 对类的clone()控制
tips: 
1. 对本类的克隆均是调用Object.clone()实现（需要先实现Cloneable接口），其返回值类型为Object
2. Base class: `class Ordinary{}`
## 1.扩增权限范围，不实现Cloneable接口
解释：将clone()的权限范围从protected改为public，让其子类继承此方法，且任何类都可以调用此方法。返回值为super.clone();注意不实现Cloneable接口。

Code：
```
class WrongClone extends Ordinary{}
```