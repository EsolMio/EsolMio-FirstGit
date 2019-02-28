# Tips of Thread
### Thread的用法
1. 需要先创建一Thread子类，重构其`构造方法`和`run()`
2. 在需要的地方（方法）中构造子类的实例，并调用其start()方法
3. 关于`run()`的overwrite，可在`run()`内部实现自己需要的功能，任意均可，JVM只根据`start()`创建线程并执行`run()`中的语句

### 调用Thread的方法与Thread的关系
在特定的地方（如`main（）`）构建Thread对象，意味着调用此方法会有一个主线程（相对于`Thread`创建的线程），而Thread会生成一子进程。创建的Thread对象越多，生成的子线程就越多

### Interesting Code
对于一个按钮button，若需要在按下button后生成一个非幂等线程并执行，需要考虑到不能使用户多次按下button而产生多个线程导致结果错误
《Thing in java》C14_1_3_Counter3.java code 给出了解决方法：
```
public class Counter3 extends Applet implements Runnable
```