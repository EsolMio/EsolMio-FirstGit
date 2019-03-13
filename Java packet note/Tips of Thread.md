# Tips of Thread
### 1. Thread的用法
1. 需要先创建一Thread子类，重构其`构造方法`和`run()`
2. 在需要的地方（方法）中构造子类的实例，并调用其start()方法
3. 关于`run()`的overwrite，可在`run()`内部实现自己需要的功能，任意均可，JVM只根据`start()`创建线程并执行`run()`中的语句

### 2. 调用Thread的方法与Thread的关系
在特定的地方（如`main（）`）构建Thread对象，会生成新的线程，但

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


tips：进入Runnable状态的对象均有机会争抢CPU资源以运行，根据优先级和获得CPU的speeeeeed
但进入阻塞blocked的均需要返回至Runnable方有机会

### 5. 线程进入阻塞(Blocked)状态的实现方法 (待补充 unfinished)
#### 运行优先级(priority)
在Thread中有三个常量(static final)表示优先级：
1. **`MIN_PRIORITY`** = 1，最小优先级值。
2. **`MAX_PRIORITY`** = 10，最大优先级值。
3. **`NORM_PRIORITY`** = 5，优先级默认值。

进入阻塞的方法有三个：（同步锁：每个Thread对象均有）
1. **`Thread.sleep(long millis)`**，Thread类方法，在任何Thread对象/main()中均可调用。调用后**运行中的线程**进入阻塞，**且原本持有的同步锁（对方的）不释放（如果有）**
2. **`Object.wait()`**，继承自Object，调用后**本线程**进入**等待池**等待，直到其他线程对象调用了本线程对象的`notify()/notifyAll()`，**会释放原本持有的同步锁**。
3. **`Thread.yield()`**，暂停**当前执行**的线程，转让给相同或更高优先级的线程，运行中的对象会返回至Runnable状态，让出CPU，实际上可理解为：检查是否有高优先级/同优先级的等待状态线程，若有则自己返回Runnable运行权给与前者，若无则继续运行。
4. **Thread对象的`join()`方法**，在当前对象中调用其他线程对象的`join()`方法，当前线程对象会进入等待直到被调用的对象结束运行，随后进入就绪(Runnable)状态。

### 6. synchronized
#### 对于对象/句柄而言：
code:
```
//code 1
void f(){
    synchronized(this){
        //do code
    }
}
```
same as:
```
//code 2
void sycnhronized f(){
    //code...
}
```
code 1 中使用了`synchronized(object handle)`，括号中使用对象的句柄，本人简称为锁对象，不需要加类型修饰符，表示锁住的对象——即只针对相同的对象，一线程中的对应锁对象成功调用此synchronized修饰的代码块后，其他线程中拥有的锁对象则无法调用此代码块，直到前者运行完代码块并释放。
code 2 也是相同的道理。成员方法的调用需要对应的对象。

Tips from 《TIJ》:

>每个对象都包含了一把锁（也叫作“监视器”），它自动成为对象的一部分（不必为此写任何特殊的代码）。调用任何synchronized 方法(补充：/语句块)时，对象就会被锁定，不可再调用那个对象的其他任何synchronized 方法（/语句块），除非第一个方法（/语句块）完成了自己的工作，并解除锁定。

#### 对于类而言：
```
public static void f(){
    synchronized(Test.class){
        //code
    }
}
```
实际效果与上相同，锁对象的作用效果为类的对象（.class），针对类型起作用（同类型使用则上锁）。

### 7. Big Tips From "Head First Servlet & JSP"
See code first:
```
//class define ignore
//...
public void doGet(HttpServletRequest request,
 HttpServletResponse response) throws IOException, ServletException{
    response.setContentType("text/html");
    PrintWriter out = =response.getWrite();
    out.println("text...<br>");
    
    //Attention! Big Tips!!!
    synchronized(getServletContext()){
        getServletContext().setAttribute("foo","22");
        //...
    }
}
```
- 根据本文上面的介绍，"使用synchronized(this){}" / "方法中直接使用synchronized修饰符的方法/代码块" 均是对同一定义本方法的对象采取同步限制。
- 但synchronized(object)亦可使用其他对象的句柄(handle)，不局限于this。
- 故可使用其他句柄当作锁变量，因限制于java语言规范，括号内调用何种句柄需代码中已申明（如定义，参数传入等等）或使用其他方法返回一个对象。
- 本代码的精髓在于使用了对象成员方法返回一个对象的方式申明了synchronized对哪个对象同步锁定。
- 虽然线程对象运行到此方法时依然使用的是封装此方法的对象（其实不一定，比如servlet中线程本质为容器制造的，并非只用封装此方法的对象，此对象只是线程运行中的一环），但因在synchronized使用了对象中特定的方法获得了一个对象（此对象可能为进程中使用的对象的一个成员变量，亦或者通过其他QY技巧），同步锁只对特定方法获得的对象起作用。

**Total**： 上述代码意图于，进程对象通过某种方式（如上述的`getServletContext()`）获得一对象，若此对象对应synchronized()中定义的同步锁对象，将会触发同步锁对象的"监视器",若有其他线程使用了此synchronized语句块，则后者线程阻塞，等待前者运行完成。

### 8. 关于`main()`线程与其他线程的关系, `sleep()`, `run()`
#### 8.1 `main()`
- JVM在运行期间只有一个线程占用CPU（如仅`main()`运行，其他位于Runnable/Blocking），即并发式处理线程，多个线程争抢一个CPU资源。
- 在JVM Running 时，`main()`拥有的优先级最高，JVM最先执行`main()`，倘若此时建立了其他线程，线程也会停留于Runnable状态。
#### 8.2 `sleep()`, `run()`
- 若在mian()运行期间使用了`Thread.sleep(long millies)`，会将运行中的线程（只有main()一个，于JVM中）进入sleep阻塞。随后运行位于Runnable的线程（根据优先级/speeeeeed）
- Tips of `sleep(long millies)`：当`sleep(long millies)`中的秒数跑完后，阻塞的线程不一定会立刻运行，会先进入Runnable，通过优先级判断是否继续（如`main()`阻塞结束后会立刻运行）。
- Tips of `run()`：Thread创建的线程对象在运行时均是执行`run()`语句，`run()`结束表示线程终结。

### 9. `wait()`, `notify()`, `notifyAll()`
- `wait()`, `notify()`, `notifyAll()`三个方法均位于Object对象中，使用于全部的对象。
  位于Object的原因：
  >wait()和 notify()比较特别的一个地方是这两个方法都属于基础类 Object 的一部分，不象sleep()，
suspend()以及resume()那样属于Thread 的一部分。尽管这表面看有点儿奇怪——居然让专门进行线程处理的东西成为通用基础类的一部分——但仔细想想又会释然，因为它们操纵的对象锁也属于每个对象的一部分。
From "Thing in Java"
- 在指定对象上使用`wait()`, `notify()`, `notifyAll()`时，使用此方法时当前线程**必须**需拥有此**对象的监视器(Monitor)**。**Tips**：此时无论synchronized()中的锁对象句柄的名称为何，只要句柄指向的对象一致，线程即可获得此对象的监视器。synchronized中的锁对象需要先在适合的作用域中定义（即类型需确定），后才可在synchronized()的括号中使用。

  **获得对象监视器的方法**：
  - 线程执行指定对象的同步方法（synchronized修饰的成员方法）
  - 线程执行指定对象的synchronized代码块，且锁对象为指定对象
  - 对于`Class`类对象，可执行synchronized修饰的类方法。
- 正式调用以上三个方法时也需要在synchronized修饰的方法/代码块中使用
- 对于指定对象调用`wait()`时，获得此对象监视器的进程**均会**受到影响。
- 使用`notify()`时，会**仅唤醒一个**在此对象的监视器上执行等待的线程，若有多个线程则随机唤醒。（即凡是执行了/执行到对应对象的synchronized方法/代码块的线程且处于wait状态）
- `notifyAll()`为唤醒当前在此对象的监视器上执行等待的全部线程。
- **Attention**: 使用`notify()` / `notifyAll()`激活的线程均会重新回到Runnable，等到执行时会**重新运行** `run()` 。

### 10. `ThreadGroup`
**ATTENTION**, 留意`com.TIJ_C14_Multithread.TIJCode_TestAccess_ThreadGroup`文件中的代码，里面有遗留下来的继承的致命性问题
- **Attention:** `ThreadGroup`允许线程访问有关其自己的线程组的信息，但不允许访问有关其线程组的父线程组或任何其他线程组的信息。即不可向上或跳出访问
- 使用`ThreadGroup(String groupName)` / `ThreadGroup(ThreadGroup parentGroup, String groupName)`创建`ThreadGroup`对象。
- 每个`Thread`对象中均有获得对应/包含于的`ThreadGroup`对象方法：`getThreadGroup()`
- 通过`getParent()`获得上一级（父）的线程组

**`ThreadGroup`中一些方法的解释**：
- 在构建子类线程组时，子类线程组会继承父类的线程组的可设置最高优先级，且对子类调用`setMaxPriority()`设置高于父类的最高优先级将不可行。
- 新创建的线程组将自动归属与系统线程组下。
- 新建一线程置于一线程组中，新线程的优先级无法高过所在线程组的最高优先级，**除了组最高级低于线程默认优先级，此时使用默认优先级**（使用`setPriority()`）。此时若对新建线程调用`setPriority()`，其结果会受到组最高级影响。
- 对于原本存在于线程组内的线程，组最高级调低至线程以下时不会影响组内线程的优先级（无论高低）。


### 11. IO堵塞
- 在对同一底层文件调用输入流和输出流时（如`Reader`, `Writer`）(均在不同线程中)，一下情况会造成拥堵
    1. 底层文件无字符时，此时拥有`Reader`的线程调用此文件( `read()` )，会堵塞。
    2. 多个拥有`Reader` / `Writer`的线程同时涌向一个底层文件读取/输出时，会根据优先级和速度评判堵塞状况。

### 12. `interrupt()`, Thread对象中断
若有发生线程死锁的可能，可设置指定的方法内调用`interrupt()`以终止死锁的线程



