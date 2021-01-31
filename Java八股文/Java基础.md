# Java基础

## I/O

###  BIO

- java.io包下File类、各种输入输出流、字节字符流等；
- BIO是同步阻塞的IO，一个请求占用一个进程处理，先等待数据准备好，然后从内核向进程复制数据，最后处理完数据后返回；

### NIO

- java.nio下的Channel、Buffer、Selector类等类实现的IO；
- NIO是同步非阻塞的IO，进程先将一个套接字在内核中设置成非阻塞再等待数据准备好，在这个过程中反复轮询内核数据是否准备好，准备好之后最后处理数据返回；

#### IO多路复用

多路复用阻塞在select、epoll这样的系统调用之上，而没有阻塞在真正的IO系统调用上，轮询机制被优化成通知机制，多个连接公用一个阻塞对象，进程只需要在一个阻塞对象上等待，无需再轮询所有连接；

#### Java NIO如何实现多路复用

- Java提供了Selector类、Channel类和Buffer类，Channel需要注册在Selector上，Selector可以检测注册的Channel是否处于就绪状态，就绪的Channel可以被进行后续的IO操作。所以Java通过Selector类实现了单线程监听多个数据通道；
- 在linux系统，Selector的底层实现是epoll的IO多路复用模型；

#### NIO与BIO的区别

- 通过Buffer而非Stream的方式进行数据交互，Buffer提供了灵活的数据处理方式；
- NIO是非阻塞的，每个Socket连接可以让底层操作系统帮我们完成而不需要每次开个线程去保持连接，只需通过Selector监听所有Channel的状态；
- NIO提供了从直接内存（堆外内存）分配内存的方式（DirectByteBuffer），消除了JVM与操作系统之间读写内存的损耗；

### AIO

Java7引入的一些Asynchronous开头的类，可用于构建异步非阻塞的IO；

#### AIO得到结果的方式

- 基于回调：实现CompleteHandler接口，调用时触发回调函数；
- 返回Future：通过isDone()查看是否准备好，通过get()等待返回数据；

## 线程

### 线程的生命周期

- 通常认为的线程的生命周期有5种状态，即新建、就绪、运行、阻塞、死亡。线程对象被创建时状态为新建，调用start()方法后进入就绪状态，得到CPU资源后进入运行状态，运行状态中如果调用了sleep()或wait()方法，则进入阻塞状态，运行结束后线程死亡；
- 实际上JDK中定义的线程的状态一共有6种：New（新建）、Runnable（可运行/运行中）、Blocked（获取不到锁而阻塞）、Waiting（无限期等待，一般是调用wait()方法需要等待被notify()，或者是调用了别的线程的join()方法，需要等待别的线程执行完）、Timed_Waiting（有限期等待，一般是调用了sleep()方法或输入了超时时间的join()和wait(0方法）、Terminated（执行完毕、终止状态）；

### 线程的通信方式

- 等待通知机制：wait()、notify()、notifyAll()、join()、interrupt()；
- 并发工具：synchronized、lock、countDownLatch、Semaphore等；

### 交替打印奇偶数

```java
private static int num = 1;

public static void main(String[] args) throws InterruptedException {
    SwitchPrint switchPrint = new SwitchPrint();

    Thread thread1 = new Thread(switchPrint::print);
    Thread thread2 = new Thread(switchPrint::print);
    thread1.start();
    thread2.start();

    thread1.join();
    thread2.join();
}

private synchronized void print() {
    while (num <= 100) {
        System.out.println(Thread.currentThread().getName() + ": " + num++);

        notify();
        try {
            wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

### ThreadLocal

- 每个线程内部都有一个ThreadLocalMap，每次操作ThreadLocal时，都会获取到当前线程对应的ThreadLocalMap进行操作，所以不同线程之间ThreadLocal的数据是隔离的；
- 应用场景：数据库连接，每个线程都需要有一个连接。也可以设置一些上下文的数据，避免多层传递参数；

### 线程池

#### 为什么使用线程池

- 如果每次都创建新线程，有较大的创建回收对象的开销，性能差，使用线程池可以复用已存在的线程；
- 线程缺乏统一管理，可以无限制创建线程，有OOM的风险。线程池可以控制可以创建、执行的最大并发线程数；

#### 线程池核心参数

- corePoolSize：核心线程数量，线程池中常驻的线程数量；
- maximumPoolSize：线程池允许的最大线程数，只有在阻塞队列满了以后才会尝试将线程数量增加至maximumPoolSize；
- workQueue：阻塞队列，存储等待执行的任务；
- keepAliveTime：线程没有任务执行时可以保持的时间；
- rejectHandler：拒绝任务提交时的策略；

#### 创建线程的逻辑

如果运行时的线程数小于corePoolSize，则创建新线程。如果运行的线程数大于等于corePoolSize，新任务会被插入到阻塞队列。如果阻塞队列已满，且线程数小于maximumPoolSize，则增加线程数。如果阻塞队列已满且线程数等于最大maximumPoolSize，则执行拒绝策略；

#### 阻塞队列的策略

- 直接提交：使用SynchronousQueue，生产者线程对其的插入操作必须等待消费者的移除操作，将任务直接提交给线程而不保持它们；
- 无界队列：使用无限的maximumPoolSize时，corePoolSize线程都忙碌时，新任务会在队列中等待，有OOM的风险；
- 有界队列：使用有限的maximumPoolSize，有助于防止资源耗尽，但是较难调整和控制；

### 并发包工具类

#### CountDownLatch

请其他线程满足特定条件时主线程再继续执行的线程同步工具；

#### Semaphore

- 能控制并发量并阻塞超出并发量的线程的工具；
- 使用场景是数据库连接并发数，如果超过并发数，则等待或抛出异常；

#### CyclicBarrier

可以让一组线程互相等待，当每个线程都准备好之后，所有线程才能继续执行的工具类；

## 锁

### 锁是什么

锁是在多个线程竞争资源的情况下来分配不同线程执行方式的同步控制工具，只有线程获取到锁之后才能访问同步代码，否则等待其他其他线程使用结束后释放锁；

### lock与synchronized的区别

- 性能：资源竞争激烈的情况下，lock性能比synchronized好，如果竞争资源不激烈，两者的性能差不多；
- 用法：synchronized用在代码块、方法上。lock可以有更精确的线程语义，但是需要手动释放，还提供了公平锁、有时间限制的同步、可以被中断的同步等多样化同步；
- 原理：synchronized在JVM级别，会在生成的字节码中，在被修饰的代码块前后分别加上monitorenter和monitorexit指令，任何对象都有一个monitor与之关联。在执行monitorenter指令后，monitor的计数器加1，monitor处于锁定状态，执行monitorexit指令后，计数器减1，计数器为0时锁被释放。synchronized还通过内存指令屏障来保证共享变量的可见性。lock使用AbstractQueuedSynchronizer在代码级别实现，通过Unsafe.park调用操作系统内核进行阻塞；
- 功能：Lock更强大，因为提供了公平锁、读锁、写锁。Lock提供了一个Condition类，可用于实现分组唤醒线程，而不是像synchronized要么随机唤醒一个线程要么唤醒全部线程。Lock还提供了lockinterruptibly()方法，可以中断等待锁的线程。

### 锁的种类

#### 公平锁与非公平锁

- 公平锁：多个线程按照申请锁的顺序来获取锁；
- 非公平锁：获得锁的线程与申请顺序无关；
- synchronized是非公平锁，ReentrantLock默认是非公平锁，也可以实现公平锁，但是公平锁性能差很多；

#### 可重入锁

- 同一线程在获得某个锁之后，可以再次获得该锁，而不会出现死锁；
- synchronized和ReentrantLock都是可重入锁；

#### 独享锁和共享锁（写锁和读锁）

独享锁同一时间只能被一个线程持有，共享锁同一时间可以被多个线程持有；

#### 乐观锁和悲观锁

- 乐观锁：认为线程在获取数据之后不会对数据进行修改，所以在获取数据时不加锁，在修改数据时用CAS（Compare And Swap）或版本号的方式在确定数据是否被其他线程修改过，如果没被修改，则当前线程成功修改数据；
- 悲观锁：认为线程在获取数据之后一定会修改数据，因此在一个线程获得锁之后，会阻塞其他尝试获得锁的线程；

#### 偏向锁/轻量级锁/重量级锁

Java SE1.6为了减少获得锁和释放锁带来的性能消耗，引入了偏向锁和轻量级锁，在Java SE1.6中一共有4种状态，级别从低到高依次为：无锁状态、偏向锁状态、轻量级锁状态和重量级锁状态；

- 偏向锁：当一个线程访问同步块并获取锁时，会在对象头中存储锁偏向的线程id，以后该线程在进出同步块不需要进行CAS操作来加锁和解锁，只需检测对象头的Mark Word里是否存储着指向当前线程的偏向锁；
- 轻量级锁：当锁是偏向锁时，如果同步块被另一个线程所访问，则偏向锁会升级为轻量级锁，此时其他想获得该锁的线程会尝试使用自旋来获取锁；
- 重量级锁：当锁为轻量级锁时，获取不到锁的线程在自旋了一定次数后，如果还获取不到，则这个锁会升级为重量级锁，获取不到锁的线程会进入阻塞状态；

#### 自旋锁/自适应自旋锁

- 自旋锁：尝试获取锁的线程，如果获取不到时，不会立即阻塞，而是先循环尝试获取锁。这样的好处是减少线程上下文切换的消耗，缺点是循环会消耗CPU；
- 自适应自旋锁：自旋锁的自旋次数不再固定，而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态决定，是虚拟机对锁状况的一个预测；
