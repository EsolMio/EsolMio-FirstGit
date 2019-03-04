# Tips of Thread
### Thread的用法
1. 需要先创建一Thread子类，重构其`构造方法`和`run()`
2. 在需要的地方（方法）中构造子类的实例，并调用其start()方法
3. 关于`run()`的overwrite，可在`run()`内部实现自己需要的功能，任意均可，JVM只根据`start()`创建线程并执行`run()`中的语句

### 调用Thread的方法与Thread的关系
在特定的地方（如`main（）`）构建Thread对象，意味着调用此Thread对象的方法会生成一个主线程（相对于`Thread`创建的线程），而一Thread对象会生成一子进程。创建的Thread对象越多，生成的子线程就越多

#### Interesting Code
对于一个按钮button，若需要在按下button后生成一个非幂等线程并执行，需要考虑到不能使用户多次按下button而产生多个线程导致结果错误
《Thing in java》C14_1_3_Counter3.java code 给出了解决方法：
```
public class Counter3 extends Applet implements Runnable{
    ...
    private Thread selfThread = null
    ...
    class StartL implements ActionListener{
        public void actionPerformed(ActionEvent e){
            if(selfThread == null){
                selfThread=new Thread(Counter3.this);
                selfThread.start(); 
            }
        }
    }
}
```
tips:成员方法使用之前在JVM中已完整生成调用成员方法的对象，无需顾虑对象初始化问题。
tipss：Thread和Runnable不为同一类型（很傻的提示...）

**可以看出，定义一`Thread`成员变量`selfThread`初始值为`null`,意义上为“按钮并未按下”。在监听器中，使用了`if`判断`selfThread`是否为空以检测是否按下过按钮。若按下，则跳过**
**亮点：将`selfThread`初始值定位`null`,表示并未使用过按钮**

### Thread.sleep()
Thread。sleep()为Thread的类方法（static），无需Thread对象，可适用于任何进程对象中

### 关于`main()`和JVM启动时的线程状况
`main()`方法在运行时会生成一个针对此方法的线程。而在启动时会同时生成`main()`线程和自定义（Thread）线程，执行顺序由哪个线程先获得CPU决定。

### Java环境下进程运行示意图

