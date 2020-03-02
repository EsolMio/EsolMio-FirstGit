# 面试准备TIPs

## `ArrayList` Note
- 无参构造器`ArrayList()`构造时使用`private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};`空数组进行初始化
- 在扩容主函数`private void grow(int minCapacity)`中，存在如下code的原因
  ```java
  int oldCapacity = elementData.length;
  int newCapacity = oldCapacity + (oldCapacity >> 1);
  if (newCapacity - minCapacity < 0)
      newCapacity = minCapacity;
  ```
  当使用无参构造时，`elementData`为空数组，`length`为0。而grow函数的通用扩容方法为将当前elementData的长度扩充1.5倍，此时可看到经过计算后`newCapacity`为0。即if的存在是为了处理0的存在
- `System.arrayCopy()` 和 `Arrays.copyOf()`的区别
  1. `System.arrayCopy(Object src, int srcPos, Object dest, int destPos, int length)` 可指明源数组和目标数组及对应的起始位置（srcPos为被复制的起始位置，destPos为目标的起始位置-将覆盖），src 和 dest 可为同一对象。且该函数为`native`，速度上会更快。
  2. `Arrays.copyOf(T[] original, int newLength)`

## 并发
- 原子性-将多种操作复合（如“读取-修改-写入”，这三种操作结果状态将**依赖**先前的结果状态，操作之间具有依赖性）-典型封装类`AtomicReference<T>`
  - java对基本类型的读和写都为原子操作
- 加锁机制：
  1. `synchronized(object)`
  2. 重入，线程请求未被持有的锁，jvm将记录锁信息并设计数器为1，同线程多次进入则计数器叠加，退出时减小，直到计数器为0才为无锁转态
- `volatile`: 可理解为上锁相同锁的`get(), set(xxx)`，可保证可见性，但内存可见性上比上锁(synchronized())要弱。
  - 可见性：使用关键字 `volatile `修饰的变量会将修改的值强制立即写入内存中，其他线程读取值时因缓存无效而去主存读取，因主存尚未更行而需等待
    > 在线程2修改stop值时（当然这里包括2个操作，修改线程2工作内存中的值，然后将修改后的值写入内存），会使得线程1的工作内存中缓存变量stop的缓存行无效，然后线程1读取时，发现自己的缓存行无效，它会等待缓存行对应的主存地址被更新之后，然后去对应的主存读取最新的值。
    
    可查阅 [Title: volatile 写 - 读的内存语义](https://www.infoq.cn/article/java-memory-model-4/)
  - 有序性：
    ```java
    // code转载自 https://www.cnblogs.com/dolphin0520/p/3920373.html
    //x、y为非volatile变量
    //flag为volatile变量
    
    x = 2;        //语句1
    y = 0;        //语句2
    flag = true;  //语句3
    x = 4;         //语句4
    y = -1;       //语句5
    ```
    - 语句3前后执行数不变，即{1,2}在flag之前执行，{4, 5}在flag之后执行（{1,2}在{4,5}前），但1、2顺序不确定，4、5顺序不确定。
    - 对`volatile`写操作发生在读（仅发生在多线程同时读和写时）
- 发布 & 逸出
  1. 发布：在当前作用域之外被使用
  2. 逸出：不应该发布的发布了，如：内部匿名类
     ```java
     public class ThisEscape{
         public ThisEscape(EventSource source){
             source.registerListener(
                 new EventListener(){
                     public void onEvent(Event e){
                         doSomething(e);
                     }
                 }
             )
         }
     }
     // 此时ThisEscape逃逸至EventSource中，因为内部类(非内部静态类)
     ```
- 不变性-`final`: 
  - `final`: 关键字`final`可保证对象的正确构造，Example code:
     ```java
     @ThreadSafe
     public class PersonSet{
       @GuardedBy("this")
       private final Set<Person> mySet = new HashSet<Person>();

       public synchronized void addPerson(Person p){
            mySet.add(p);
       }
     }
     ```
     - 若没有 `final`，多线程环境下可能其他线程获得 `PersonSet` 对象后直接调用了 `addPerson()` ，使得获得了空 `mySet`，导致错误。
     - 若设置 `final`，此关键字修饰的field将会先对
  - 不可变**对象**要求：1. 对象创建后不能修改（不可修改指对象的属性值不可修改，如不设置 `public set()`，field 设为`private final` ）；2.  对象的所有域都是 final；3. 对象正确创建，无逸出
    ```java
    // Example, 使用volatile发布不可变对象
    class OneCacheVaule{
      private final BigInteger lastNumber;

      public OneValueCache(BigInteger i){
        this.lastNumber = i;
      }

      public BigInteger getFactor(){
        if(lastNumber == null){
          return null;
        }
        else
          return lastNumber;
      }
    }
    ```
- 安全发布
  - 不安全发布：不安全例子如下
    ```java
    // 1. 使用init()成员函数初始化（在多线程环境下）
    // 单纯将field直接public暴露
    public Holder holder;

    public void initialize(){
        holder = new Holder(24);
    }

    /********************************************/
    public class Holder{
      private int i;

      public Holder(int n){ this.i = n; }

      public assertSanity(){
        if(i != i)
          throw new AssertionError("Error");
      }
    }

    // 不安全公布原因：
    // 1. 多线程获得holder，单线程通过init初始化，先分配内存空间并将对应地址传递至holder，随后Object构建，此时i = 0；
    // 一线程在初始化尚未构造完成时，使用holder并调用assertSanity()，有可能调用中途阻塞而使得构造完成，i前后获值不同
    ```
    故安全公布需要明确：正确初始化后再公布
  - 安全发布：
    1. 使用静态的初始化器初始化**对象的引用**：`public static Holder holder = new Holder(24);` 静态初始化器由JVM在类class的初始化阶段执行（先于Object()）,JVM内部存在同步，故可安全发布
    2. 使用线程安全类封装**对象的引用**：如`Hashtable`, `BlockingQueue`, `ConcurrentMap`等等
    3. 线程封闭/通过锁(synchronized)保护对象访问(`private`修饰field，`get()`, `set()`使用`synchronized`修饰或其域)
    4. 使用`final`, 注意构造时要正确（即构造时不会有其他线程影响）
- 使用多重锁时注意锁的统一性，外部所与内部锁的对象相同才可拥有原子性(synchorized)
    ```java
    public class ListHelper<E>{
      public List<E> list = Collections.synchronizedList(new ArrayList<E>());

    public synchronized boolean putIfAbsent(E x){
      boolean absent = !list.contains(x);
      ...
      }
    }
    ```
    注意，此时`putIfAbsent`内并非原子性，因put`putIfAbsent`使用`ListHelper`锁，`list`使用`list`锁，锁不相同。此时在`putIfAbsent`上锁时，外部获得`list`的对象可依旧访问并修改list
    ```java
    // 修改
    public class ListHelper<E>{
      public List<E> list = Collections.synchronizedList(new ArrayList<E>());

      public boolean putIfAbsent(E x){
          synchronized(list){
            boolean absent = !list.contains(x);
            ...
          }
      }
    }
    ```
    使用同锁，原子性
- `HashMap`线程不安全，`ConcurrentHashMap`：并非在每个方法上使用`synchronized`且仅限一个线程使用容器，而是使用细粒度更高的“分段锁”

- 同步工具类：
  - `ThreadLocal`: 类似线程的全局变量，能降低代码的可重用性并在类之间引入隐含的耦合性（Reason?）。
    - `ThreadLocal`可简单理解为`Map<Thread, V>`，但实际上是相反的，并非由`ThreadLocal`存储，而是由每个`Thread`中的`ThreadLocal.ThreadLocalMap`存储值，即`Map<ThreadLocal, V>`。
    - 在初始化`ThreadLocal`时，可通过`initialValue()`设定初始值（在初次访问`ThreadLcoal`时每个`Thread#ThreadLocal.ThreadLocalMap`均为null，需调用`initialValue()`获得值）
    - 通过`get()`, `set()`可对本线程设定专门的值。
    - 为防止内存泄漏（因`Map<ThreadLocal, V>`中，`ThreadLocal`为弱引用，`V`为强引用，垃圾回收时会将`ThreadLocal`回后而`V`存留导致出现key=null但值存在的情况，从而则持续使用`ThreadLocal`中出现内存泄漏），故使用完后`ThreadLocal`后手动调用`remove()  `

  - `CountDownLatch`: 通过构造器`new CountDownLatch(int Num)`设定数值用于被减少，在数值减小为0前调用了`await()`的线程均会被堵塞，运行中的线程（如main）通过`countDown()`减小数值，调用一次减1；
  - `FutureTask`: 在`Executor`框架中表示异步或时间较长的运算。将`Callable`通过`new FutureTask<E>(Callable)`置入运行，`get()`方法将获得执行结果或错误，若`Callable`未执行完成则调用该方法的线程阻塞。
  - `Semaphore`：管理一组虚拟许可(premit)，许可的数量为设定的（通过构造器设定）固定值（运行期不变）。线程通过`acquire()`获得一个"许可"。当"许可"到达上限时，调用`acquire()`的线程将受到阻塞，直到有"许可"为止。线程通过`release()`释放"许可"。
    - 初始值设为1时为二值信号量，可做互斥体，并具备不可重入的加锁语义。
  - `CyclicBarrier`: 通过`new CyclicBarrier(int Count, Runnable run)`设定"栅栏"拦截的线程数以及拦截结束后运行的Runnable，被拦截的线程通过调用`await()`阻塞直到受相同情况阻塞的数量达到设定值而结束拦截。
- "Java并发实战Book" Chapter 5.6 对并发问题的处理
- 若无限制的创建线程数量，将可能使服务器崩溃等严重故障。
- `Executor`: 接口，提供`void execute(Runnable command)`，提供异步执行功能，将任务提交过程与执行过程解耦，用`Runnable`表示任务。看到`new Thread(runnable).start()`创建线程时，请考虑使用`Executor`代替`Thread`
  - `ExecutorService`: 拓展`Executor`, 并提供了生命周期管理方法-生命周期有三种：运行、关闭、已终止。
    - `shutdown()`：正常关闭，速度慢，但安全，等待线程池队列中所有任务执行完毕才关闭（期间不添加新值）, `shutdownNow()`：强制关闭，风险大，可能将执行到一半的线程关闭-停止
    - `submit(Callable<V>):Future<V>`, `submit(Runnable):Future`, `submit(Runnable, T):Future<T>`, 用`Future`表示任务。
- 任务取消：使用取消标志，在运行期间通过检查标志判断是否取消
- 中断: 然而单纯使用标志检测任务是否取消也会存在问题，如下：
  ```java
  while(!cancelled){
    queue.put(...)// synchronized，可能发生阻塞
  }
  ```
  若发生queue阻塞，则无法正常取消任务，因cancelled无法检测。

  - `Thread`提供中断方法：
    ```java
    pulic class Thread{
      public void interrupt(){...} // 对指定的线程发出请求中断的信息（指定：因本方法为成员方法，仅适用于对应的线程对象）
      public boolean isInterrupted(){...}// 当前线程是否受到中断的请求（不会清理中断状态）
      public static boolean interrupted(){...}// 当前线程是否受到中断的请求（会清理中断状态）
    }
    ```
    调用中断后中断的状态将会“有粘性”——如果不触发`InterruptedException`，中断状态将会一直保持，直到明确的清除中断状态(`interrupted()`)

  - 上述方法更进：
    ```java
    while(!Thread.currentThread.isInterrupted()){
      queue.put(...);
    }
    ```
    通过调用`Thread.currentThread().interrupt()`将当前线程中断，此时将有两种中断方式：1. 通过`!Thread.currentThread().isInterrupted()`判断；2. 通过`queue`中的检测判断并掷出`InterruptedException`。
  - 发出中断请求时，会从最底层的调用开始检测中断，故`InterruptedException`将会从底层抛出。
  - **生产者-消费者 停止基于线程的服务**
    - 应用程序可拥有服务，服务可拥有工作者线程，但应用程序不可拥有工作者线程（此处的工作者线程指的是线程池）
    - 停止"生产者-消费者"操作时，要同时取消两者。
- `Futuer<V>`: 异步功能的实现，提供以下接口：`cancel(boolean mayInterruptIfRunning):boolean`, `get():V`, `get(long timeout, TimeUnit unit):v`. 第三个`get(long timeout, TimeUnit unit)`可设置超时时间，超出掷出`TimeOutException`
  - `FutureTask`为`Future`的基本实现

- `Executor`框架
  - `CompletionService`(实现类：`ExecutorCompletionService`)：此接口提供了对已进行细粒化，分化为多个相互独立且同构的小任务的支持，在完成后将结果存储到集合(`BlockingQueue`)中。即对任务细粒化的管理及结果存储。
    - `CompletionService`提供了接口`submit(Runnable/Callable<T>):Future<T>`接收所要运行的实体，并将其用`FutureTask`封装，随后用内部类`QueueingFuture extends FutureTask<Void>`封装，并通过构造器设定的`ExecutorService`执行。
    - `CompletionService`内部实现的`QueueingFuture`覆盖了`done()`，在`Future`执行完成后触发`done()`时将`Future`置入`BlockingQueue`进行存储
    - `CompletionService`使用`BlockingQueue`对已完成的`Future`存储，要求`Future`代表的任务为同构（即最终获得的对象相同，做相同的事）。同时带来从queue获取值时不会因无有效值而掷出Exception，此时会阻塞。



