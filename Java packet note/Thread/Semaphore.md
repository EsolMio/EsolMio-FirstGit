# Semaphore 信号量
- 构造`Semaphore` : `new Semaphore(int  permits)`
  - `int permits` : 指定通过一个信号量可获得许可的数量，未超过数量时可连续获取（无论哪个线程），若超过此数量则均block。
  - 还有一个异构造器`Semaphore(int permits, boolean fair)`
    - Doucment explain: "true if this semaphore will guarantee first-in first-out granting of permits under contention, else false"
- `acquire()` & `acquire(int permits)` 
  - `acquire()`，获得单个许可permit，若调用此方法的线程数达到构造器设定的获取许可上限，此时将会block。
  - `acquire(int permits)`，一次调用，获得指定数量的许可。参数值可大于构造器设定的数值，此时将会阻塞直到其他线程调用`realse()`/`realse(int permit)`，再继续获得剩下为获取的permits。