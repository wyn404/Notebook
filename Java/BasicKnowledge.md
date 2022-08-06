## [==和equals()的区别](#jump1)
## [hashCode()与equals()](#jump2)
## [HashMap和Hashtable的区别](#jump3)
## [HashMap的底层实现](#jump4)
## [Java异常](#jump5)
## [synchronized关键字](#jump6)
## [synchronized和ReentrantLock的区别](#jump7)
## [volatile关键字](#jump8)
## [ThreadLocal](#jump9)
## [线程池](#jump10)
## [AQS(AbstractQueuedSynchronizer)](#jump11)

### ==和equals()的区别
<span id="jump1">

* 对于基本数据类型来说，==比较的是值。

* 对于引用数据类型来说，==比较的是对象的内存地址。

> 因为Java只有值传递，所以，对于==来说，不管是比较基本类型还是引用类型，本质都是比较值，只是引用类型变量存的值是对象的地址。

equals()不能用于判断基本数据类型的变量，只能用来判断两个对象是否相等。equals()方法存在与Object类中，所有的类都有equals()方法。

equals()方法存在两种使用情况:
* __类没有重写equals()方法__：通过equals()比较该类的两个对象时，等价于通过“==”比较两个对象，使用的默认是Object类equals()方法。

* __类重写了equals()方法__：一般重写equals()方法来比较两个对象中的属性是否相等；若它们的属性相等，则返回true。

String中的equals方法是被重写过的，因为Object的equals方法比较的对象的内存地址，而String的equals方法比较的是对象的值。

</span>

```java
String a = new String("ab");
String b = new String("ab");
String aa = "ab";
String bb = "ab";
System.out.println(aa == bb); //true
System.out.println(a == b); // false
System.out.println(a.equals(b)); //true
System.out.println(42.0 == 42); //true
```

### hashCode()与equals()
<span id="jump2">
比较两个对象是否相等。

以HashSet为例：
> 当你把对象加入HashSet时，HashSet会先计算对象的hashCode值来判断对象加入的位置，同时也会与其他已经加入的对象的hashCode值作比较，如果没有相符的hashCode，HashSet会假设对象没有重复出现。但是如果发现有相同 hashCode 值的对象，这时会调用 equals() 方法来检查hashCode相等的对象是否真的相同。如果两者相同，HashSet就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。减少了 equals 的次数，提高了执行速度。

* 两个对象hashCode值相等并不代表两个对象就相等(哈希碰撞)。
* 两个对象hashCode值不相等，这两个对象就不相等。
* 两个对象的hashCode值相等并且equals()方法也返回true，我们才认为这两个对象相等。

重写equals()时没有重写hashCode方法的话就会导致equals方法判断是相等的两个对象，hashCode值却不相等。
equals方法判断两个对象是相等，那么这两个对象的hashCode值也要相等。

</span>

### HashMap和Hashtable的区别
<span id="jump3">

1. HashMap是非线程安全的，Hashtable是线程安全的，因为Hashtable内部的方法都经过synchronized修饰
2. 因为线程安全的问题，HashMap要比Hashtable效率高一点。Hashtable基本被淘汰。
3. HashMap可以存储null的key和value，但null作为键只能有一个，null作为值可以有多个；Hashtable不允许存在null键和null值，否则会抛出NullPointerException。
4. 创建时如果不指定容量初始值，Hashtable默认的初始大小为11，之后每次扩充容量变为2n+1。HashMap默认的初始化大小为16。之后每次扩充，容量变为原来的2倍。如果给定了容量初始值，Hashtable直接使用给定的大小，HashMap会将其扩充为2的幂次方大小。
5. JDK1.8以后的HashMap在解决哈希冲突时有了较大的变化，当链表长度大于阈值(默认为8)(将链表转换成红黑树前会判断，如果当前数组的长度小于64，会先对数组进行扩容)时，将链表转化成红黑树，以减少搜索时间。

</span>

### HashMap的底层实现
<span id="jump4">
JDK1.8之前HashMap底层是数组和链表结合在一起使用也就是链表散列。HashMap通过key的hashCode经过扰动函数处理过后得到hash值，然后通过(n-1)&hash判断当前元素存放的位置(这里n指的是数组的长度)，如果当前位置存在元素的话，就判断元素与要存入的元素的hash值及key是否相同，如果相同直接覆盖，不同就通过拉链发解决冲突。
扰动函数指的是HashMap的hash方法。使用hash方法就是扰动函数为了防止一些实现比较差的hashCode()方法，使用扰动函数之后可以减少碰撞。
JDK1.8以后的HashMap在解决哈希冲突时有了较大的变化，当链表长度大于阈值(默认为8)(将链表转换成红黑树前会判断，如果当前数组的长度小于64，会先对数组进行扩容)时，将链表转化成红黑树，以减少搜索时间。
</span>

### Java异常
<span id="jump5">

* __Exception__：程序本身可以处理的异常，可以通过catch来捕获。Exception又可以分为Checked Exception(受检查异常，必须处理)和Unchecked Exception(不受检查异常，可以不处理).
* __Error__：Error属于程序无法处理的错误，不建议通过catch捕获。例如Java虚拟机运行错误(Virtual MachineError)、虚拟机内存不够错误(OutOfMemoryError)、类定义错误(NoClassDefFoundError)等。这些异常发生时，Java虚拟机一般会选择线程终止。

__Checked Exception__ 包括RuntimeException及其子类都统称为非受检查异常，例如:NumberFormatException,ArithmaticException,NullPointerException,ArrayIndexOutOfBoundsException等). SQLException。
</span>

### synchronized关键字
<span id="jump6">

synchronized关键字解决的是多个线程之间访问资源的同步性，synchronized关键字可以保证被它修饰的方法或者代码在任意时刻只能有一个线程执行。

* synchronized关键字加到static静态方法和synchronized(class)代码块上都是给Class类上锁。
* synchronized关键字加到实例方法上是给对象实例上锁。
* 尽量不要使用synchronized(String a),因为JVM中，字符串常量具有缓存功能。

构造方法不能使用synchronized关键字修饰。

synchronized同步语句块的实现使用的是monitorenter和monitorexit指令，其中monitorenter指令指向同步代码块的开始位置，monitorexit指令则指明同步代码块的结束位置。
synchronized修饰的方法并没有monitorenter指令和monitorexit指令，取得代之的确实是ACC_SYNCHRONIZED标识，该标识指明了该方法是一个同步方法。

JDK1.6对锁的实现引入了大量的优化，如偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁清除等技术来减少锁操作的开销。
锁主要存在四种状态，依次是：无所状态、偏向锁状态、轻量级锁状态、重量级锁状态，他们会随着竞争的激烈而剧烈升级。锁可以升级不可降级，这种策略为了提高获得锁和释放锁的效率。

</span>

### synchronized和ReentrantLock的区别
<span id="jump7">

__相同__：
* 两者都是可重入锁：可重入锁指的是可以自己再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果是不可重入锁的话，就会造成死锁。
* synchronized依赖于JVM而ReentrantLock依赖于API：synchronized是依赖于JVM实现的，JDK1.6为synchronized关键字进行了很多优化，但是这些优化是在虚拟机层面实现的，并没有直接暴露给我们。ReentrantLock是JDK层面实现的(也就是API层面，需要lock()和unlock()方法配合try/finally语句块来完成),所以我们可以通过查看它的源代码，来看他是如何实现的。

__不同点__：
* __等待可中断__：ReentrantLock提供了一种能够终端等待锁的线程的机制，通过lock.lockInterruptibly()来实现这个机制。也就是说正在等待的线程可以选择放弃等待，改为处理其他事情。
* __可实现公平锁__：ReentrantLock可以指定公平锁还是非公平锁。而synchronized只能是非公平锁。所谓的公平锁就是先等待的线程先获得锁。ReentrantLock默认情况是非公平的，可以通过ReentrantLock类的ReentrantLock(boolean fair)构造方法来制定是否是公平的。
* __可实现选择性通知(锁可以绑定多个条件)__：synchronized关键字与wait()和notify()/notifyAll()方法结合可以实现等待/通知机制。ReentrantLock类当然也可以实现，但是需要借助于Condition接口与newCondition()方法。
</span>

### volatile关键字
<span id="jump8">

__volatile关键字可以防止JVM指令重排(使变量到主存中进行读取)，保证变量的可见性。__
JDK1.2之前，Java的内存模型实现总是从主存(即共享内存)读取变量，是不需要进行特别的注意的。在当前的Java内存模型下，线程可以把变量保存在本地内存(比如机器的寄存器)中，不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成数据的不一致。
* __主内存__：所有线程创建的实例对象都放在主内存中，不管该实例对象是成员变量还是方法中的本地变量(也称局部变量)。
* __本地内存__：每个线程都有一个私有的本地内存来存储共享变量的副本，并且，每个线程只有访问自己的本地内存，无法访问其他线程的本地内存。本地内存是JMM抽象出来的一个概念，存储了主内存中共享变量副本。


__synchronized关键字和volatile关键字的区别__：
* volatile关键字使线程同步的轻量级实现，所以volatile性能肯定比synchronized关键字要好。但是volatile关键字只能用于变量而synchronized关键字可以修饰方法以及代码块。
* volatile关键字能保证数据的可见性，但不能保证数据的原子性，synchronized关键字两者都能保证。
* volatile关键字主要用于解决变量在多个线程之间的可见性，而synchronized关键字解决的是多个线程之间访问资源的同步性。

__并发编程的三个重要特性__：
1. __原子性__：一次操作或者多次操作，要么所有的操作全部都得到执行并且不会受到任何因素的干扰而中断，要么都不执行。synchronized可以保证代码片段的原子性。
2. __可见性__：当一个线程对共享变量进行了修改，那么另外的线程都是立即可以看到修改后的最新值。volatile关键字可以保证共享变量的可见性。
3. __有序性__：代码在执行的过程中的先后顺序，Java在编译器以及运行期间的优化，代码的执行顺序未必就是编写代码时侯的顺序。volatile关键字可以禁止指令进行重排序优化。

</span>

### ThreadLocal
<span id="jump9">

__ThreadLocal__ 类主要解决的是让每个线程绑定自己的值，可以将ThreadLocal类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。如果创建了一个ThreadLocal变量，那么访问这个变量的每个线程都会有这个变量的本地副本，这也是ThreadLocal变量名的由来。他们可以使用get()和set()方法来获取默认值或将其值更改为当前线程所存的副本的值，从而避免了线程安全问题。

ThreadLocal中的变量是放在了当前线程的ThreadLocalMap中，并不是存在ThreadLocal上，ThreadLocal可以理解为只是ThreadLocalMap的封装，传递了变量值。每个Thread中都具备一个ThreadLocalMap，而ThreadLocalMap可以存储以ThreadLocal为key，Object对象为value的键值对。

ThreadLocalMap中使用的key为ThreadLocal的弱引用，而value是强引用。所以，如果ThreadLocal没有被外部强引用的情况下， 在垃圾回收的时候，key会被清理掉，而value不会被清理掉。ThreadLocalMap中就会出现key为null的Entry。如果不做任何措施，value永远无法被回收，这个时候就会出现内存泄露。ThreadLocalMap在调用set()、get()、remove()方法的时候，会清理掉key为null的记录。使用完ThreadLocal方法后最好手动调用remove方法。
</span>

### 线程池
<span id="jump10">

__使用线程池的好处__：
* __降低资源消耗__：通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
* __提高响应速度__：当任务到达时，任务可以不需要等到线程创建就能立即执行。
* __提高线程的可管理性__：线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

__Runnable接口和Callable接口的区别__：
__Runnable接口__ 不会返回结果或抛出检查异常，但是 __Callable__ 接口可以。

__execute()__ 和 __submit()__ 方法的区别是什么
1. execute()方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否；
2. submit()方法用于提交需要返回值的任务。线程池会返回一个Future类型的对象，通过这个Future对象可以判断任务是否执行成功，并且可以通过Future的get()方法来获取返回值，get()方法会阻塞当前线程直到任务完成，使用get(long timeout, TimeUnit unit)方法则会阻塞当前线程一段时间后立即返回，这时候可能任务没有执行完。

__创建线程池的方法__：

* __通过构造方法实现__
* __通过Executor框架的工具类Executors来实现__
  1. __FixedThreadPool__：该方法返回一个固定线程数量的线程池。该线程池中的线程数量始终不变。当有一个新任务提交时，线程池中若有空闲线程，则立即执行。若没有，新的任务会被暂存在一个任务队列中，有线程空闲时，便处理在任务队列中的任务。
  2. __SingleThreadExecutor__：方法返回一个只有一个现成的线程池。若多余一个任务被提交到该线程池，任务会被保存在一个任务队列中，待线程空闲，按先入先出的顺序执行队列中的任务。
  3. __CachedThreadPool__：该方法返回一个可根据实际情况调整线程线程数量的线程池。线程池的线程数量不确定，但若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程处理任务。所有线程在当前任务执行完毕后，将返回线程池进行复用。

__TheadPoolExecutor3个最重要的参数：__
* __corePoolSize__：核心线程数定义了最小可以同时运行的线程数
量。
* __maximumPoolSize__：队列中存放的任务达到队列容量时，当前可以同时运行的线程数量变为最大线程数。
* __workQueue__：当新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放到队列中。

__其他常见参数：__
1. __keepAliveTime__：当线程池中的线程数量大于corePoolSize的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了keepAliveTime才会被回收销毁；
2. __unit__：keepAliveTime参数的时间单位；
3. __threadFactory__：executor创建新线程的时候会用到。
4. __handler__：饱和策略。下面单独介绍

__ThreadPoolExecutor饱和策略定义__:
如果当前同时运行的线程数量达到最大线程数量并且队列也已经放慢了任务时，ThreadPoolTaskExecutor定义了一些策略：
* __ThreadPoolExecutor.AbortPolicy__：抛出RejectedExecutionException来拒绝新任务的处理。
* __ThreadPoolExecutor.CallerRunsPolicy__：调用执行自己的线程运行任务，也就是直接在调用execute方法的线程中运行(run)被拒绝的任务，如果执行程序已关闭，则会丢弃该任务。因此这种策略会降低对于新任务的提交速度，影响程序的整体性能。如果应用程序可以承受此延迟并且你要求任何一个任务请求都要被执行的话，可以选择这个策略。
* __ThreadPoolExecutor.DiscardPolicy__：不处理新任务，直接丢弃掉。
* __ThreadPoolExecutor.DiscardOldestPolicy__：此策略将丢弃最早的未处理的任务请求。


</span>

### AQS(AbstractQueuedSynchronizer)

<span id="jump11">

__AQS__ 核心思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制AQS是用CLH队列锁实现的，即将暂时获取不到锁的线程加入到队列中。

__AQS定义两种资源共享方式__：
* __Exclusive(独占)__：只有一个线程能执行。如ReentrantLock。又分为公平锁和非公平锁：
  - 公平锁：按照线程在队列中的排队顺序，先到者先拿到锁。
  - 非公平锁：当线程要获取锁时，无视队列顺序直接去抢锁，谁抢到就是谁的。
* __Share(共享)__：多个线程同时执行。如CountDownLatch、Semaphore、CyclicBarrier。

__AQS组件总结__：
* __Semaphore(信号量)-允许多个线程同时访问：__ synchronized和ReentrantLock都是一次只允许一个线程访问某个资源，Semaphore可以指定多个线程同时访问某个资源。
* __CountDownLatch(倒计时器)：__ CountDownLatch是一个同步工具类，用来协调多个线程之间的同步。这个工具常用来控制线程等待，它可以让某一个线程直到倒计时结束，再开始执行。
* __CyclicBarrier(循环栅栏)：__ CyclicBarrier和CountDownLatch非常类似，它也可以实现线程间的技术等待，但是它的功能比CountDowunLatch更加复杂和强大。
</span>























