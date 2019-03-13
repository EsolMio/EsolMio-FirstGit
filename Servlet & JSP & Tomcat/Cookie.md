# Cookie
虽然可以通过使用`request.getSession()`的方式触发：
- 在request中无匹配/本身就无cookie下创建一个新Cookie对象
- request中有匹配上的cookie下获得一个对应的Cookie对象
但我们