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
![pic 1](https://raw.githubusercontent.com/EsolMio/EsolMio-FirstGit/master/Note_pic%20store/thread%20running%20pic.png?token=AhIdeIymJ5ZDwvOv2Og3cxEScQRiWgCxks5cfnPKwA%3D%3D)
![pic 2](https://raw.githubusercontent.com/EsolMio/EsolMio-FirstGit/master/Note_pic%20store/thread%20running%20pic_cn.jpg?token=AhIdeG3qXoyYSEFMKPYvmFDTi_pcbKDaks5cfnRSwA%3D%3D)

tips：进入Runnable状态的对象均有机会争抢CPU资源以运行，根据优先级和获得CPU的speeeeeed
但进入阻塞blocked的均需要返回至Runnable方有机会

### 5. 线程进入阻塞(Blocked)状态的实现方法 (待补充 unfinished)
#### 运行优先级(priority)
在Thread中有三个常量(static final)表示优先级：
1. **`MIN_PRIORITY`** = 1，最小优先级值。
2. **`MAX_PRIORITY`** = 10，最大优先级值。
3. **`NORM_PRIORITY`** = 5，优先级默认值。

进入阻塞的方法有三个：（同步锁：每个Thread对象均有）
1. Thread.sleep(long millis)，Thread类方法，在任何Thread对象/main()中均可调用。调用后**本线程**进入阻塞，**且原本持有的同步锁（对方的）不释放（如果有）**
2. Object.wait()，继承自Object，调用后**本线程**进入**等待池**等待，直到其他线程对象调用了本线程对象的`notify()/notifyAll()`，**会释放原本持有的同步锁**。
3. Thread.yield()，暂停**当前执行**的线程，转让给相同或更高优先级的线程，运行中的对象会返回至Runnable状态，实际上可理解为：检查是否有高优先级/同优先级的等待状态线程，若有则自己返回Runnable运行权给与前者，若无则继续运行。
4. Thread对象的join()方法，在当前对象中调用其他线程对象的`join()`方法，当前线程对象会进入等待直到被调用的对象结束运行，随后进入就绪(Runnable)状态。

### synchronized
#### 对于对象/句柄而言：
code:
```
//code 1
void f(){
    synchronized(object handle){
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

### Big Tips From "Head First Servlet & JSP"
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
根据本文上面的介绍，"使用synchronized(this){}" / "方法中直接使用synchronized修饰符的方法/代码块" 均是对同一定义本方法的对象采取同步限制。
但synchronized(object)亦可使用其他对象的句柄(handle)，不局限于this。
故可使用其他句柄当作锁变量，因限制于java语言规范，括号内调用何种句柄需代码中已申明（如定义，参数传入等等）或使用其他方法返回一个对象。
本代码的精髓在于使用了对象成员方法返回一个对象的方式申明了synchronized对哪个对象同步锁定。
虽然线程对象运行到此方法时依然使用的是封装此方法的对象，但因在synchronized使用了对象中特定的方法获得了一个对象（此对象可能为进程中使用的对象的一个成员变量，亦或者通过），同步锁只对特定方法获得的对象起作用。