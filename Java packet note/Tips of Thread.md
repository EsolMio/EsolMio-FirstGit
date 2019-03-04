# Tips of Thread
### 1. Thread的用法
1. 需要先创建一Thread子类，重构其`构造方法`和`run()`
2. 在需要的地方（方法）中构造子类的实例，并调用其start()方法
3. 关于`run()`的overwrite，可在`run()`内部实现自己需要的功能，任意均可，JVM只根据`start()`创建线程并执行`run()`中的语句

### 2. 调用Thread的方法与Thread的关系
在特定的地方（如`main（）`）构建Thread对象，意味着调用此Thread对象的方法会生成一个主线程（相对于`Thread`创建的线程），而一Thread对象会生成一子进程。创建的Thread对象越多，生成的子线程就越多

#### 2.1 Interesting Code
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

### 3. 关于`main()`和JVM启动时的线程状况
`main()`方法在运行时会生成一个针对此方法的线程。而在启动时会同时生成`main()`线程和自定义（Thread）线程，执行顺序由哪个线程先获得CPU决定。

### 4. Java环境下进程运行示意图
![thread running pic.png](0)
![thread running pic_cn.jpg](1)

### 5. 线程进入阻塞(Blocked)状态的实现方法
#### 运行优先级(priority)
在Thread中有三个常量(static final)表示优先级：
1. **`MIN_PRIORITY`** = 1，最小优先级值。
2. **`MAX_PRIORITY`** = 10，最大优先级值。
3. **`NORM_PRIORITY`** = 5，优先级默认值。

进入阻塞的方法有三个：（同步锁：每个Thread对象均有）
1.Thread.sleep(long millis)，Thread类方法，在任何Thread对象中均可调用。调用后**本线程**进入阻塞，**且原本持有的同步锁（对方的）不释放（如果有）**
2. Object.wait()，继承自Object，调用后**本线程**进入**等待池**等待，直到其他线程对象调用了本线程对象的`notify()/notifyAll()`，**会释放原本持有的同步锁**。
3. Thread.yield()，暂停**当前执行**的线程，转让给相同或更高优先级的线程。
4. Thread对象的join()方法，在当前对象中调用其他线程对象的`join()`方法，当前线程对象会进入等待直到被调用的对象结束运行，

