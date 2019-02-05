# 对类的clone()控制
## 1.扩增权限范围，不实现Cloneable接口
解释：将clone()的权限范围从protected改为public，让其子类继承此方法，任何类都可以调用