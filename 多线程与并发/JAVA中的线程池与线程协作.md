# 阻塞队列
## 简介
普通的队列在取不到元素或元素满时，线程不会阻塞，因此需要使用Condition类进行同步（具体的使用在线程同步与协作中介绍），而使用阻塞队列时，不需要考虑线程的阻塞问题，阻塞队列提供了取不到元素或元素满时的队列的阻塞方法，不需要单独使用Condition类进行同步。
## 几种阻塞队列
1. ArrayBlockingQueue：基于数组实现的阻塞队列，必须指定其大小，在其满时会阻塞，可以指定其公平性访问或非公平性访问,先进先出队列；

2. LinkedBlockingQueue：基于链表实现的阻塞队列，必须指定其大小，其默认大小为Integer.MAX_VALUE在其满时会阻塞，可以指定其公平性访问或非公平性访问，先进先出队列；

3. PriorityBlockingQueue:基于优先级出队的队列，会按照优先级排序，没有大小限制，因此不存在放入元素时阻塞的问题；

4. DelayQueue：延时队列，只有当其指定的延时完成时，才能从队列中取出元素。

## 队列方法详解
### 普通队列方法
add(E e):插入到队列末尾，成功返回true，如果队列已满，则会抛出一个异常；

offer(E e):将元素加入到队列末尾，如果插入成功，返回true，如果队列已满，则会返回false；

remove():移除队首元素，如果队列为空，则会抛出异常；

poll():移除并返回队首元素，若成功，返回队首元素，否则返回null；

peek():返回队首元素，若成功，返回队首元素，否则返回null；

一般推荐使用offer、poll、peek方法，可以判断操作是否成功，而add和remove会抛出异常
### 阻塞队列方法
阻塞队列在以上方法的基础上，提供了操作不成功便阻塞的方法，因此可以免去使用Condition对普通队列的控制：

put(E e):向队尾插入元素，如果队列已满，则会阻塞当前线程；

take():从队首取出一个元素，如果队列为空，则会阻塞当前线程；

offer(E e,long timeout, TimeUnit unit)：向队尾插入元素，如果队列已满，阻塞一定的时间；

poll(long timeout, TimeUnit unit):从队首取出元素，如果队列为空，阻塞一定的时间
### 使用场景
适用于生产者消费者的场景，阻塞队列的部分原理也会结合生产者消费者模式介绍。

# JAVA线程池
## 简介
线程池是为了线程更好的复用线程执行任务，直接创建线程会降低系统效率，浪费系统资源。
## ThreadPoolExcutor类详解
线程池的类主要是ThreadPoolExcutor类，通过它的构造方法，可以创建一个线程池（实际上不推荐直接这样创建，当然阿里的代码检查里是推荐直接这样创建线程池的，因为使用Executors创建的线程池，有可能会导致OOM（创建无限制的线程或队列占用过大的内存））， 构造器中的主要参数如下：
1. corePoolSize：线程池的核心线程大小，也是默认的执行任务的线程的数量大小，当线程数达到corePoolSize时，再来的任务便会进入任务阻塞队列
2. maximumPoolSize: 线程池的最大线程数，当线程数达到corePoolSize，而且任务的队列已满时，便会创建新的线程直接执行新的任务，如果线程达到maximumPoolSize，便会拒绝新的任务
3. keepAliveTime：超过corePoolSize创建的非核心线程，在keepAliveTime之后便会销毁。调用allowCoreThreadTimeOut方法之后，对于核心线程也会应用该参数，直到线程池中无线程。
4. workQueue：任务的阻塞队列，主要有ArrayBlockingList、LinkedBlockingList、SynchronousList几种，用来存储待执行的任务
5. threadFactory:线程的工厂类，主要用于创建线程
6. handler:任务的拒绝策略，主要有1：ThreadPoolExecutor.AbortPolicy，丢弃策略，丢弃任务并抛出RejectedExecutionException异常；2.ThreadPoolExecutor.DiscardPolicy，丢弃任务但不抛出异常；3.ThreadPoolExecutor.DiscardOldestPolicy，丢弃队列中的最前面的任务，然后执行该任务；4.ThreadPoolExecutor.CallerRunsPolicy，由调用线程处理该任务

ThreadPoolExcutor继承自AbstractExecutorService，而AbstractExecutorService是一个实现了ExecutorService接口的抽象类，而ExecutorService继承自Executor接口，Executor接口的主要方法就是execute(Runable command)，即执行传递过来的任务。在ThreadPoolExecutor中，有几个比较重要的方法：
1. excute:向线程池提交一个任务，然后去执行
2. submit：向线程池提交一个任务，然后利用Future来获取执行结果
3. shutdown&shutdownNow：关闭线程池

## ThreadPoolExecutor原理

1. 线程池状态：使用volatile关键字声明的变量，当其他线程修改该值时可以直接被各个线程发现（可见性），状态主要有：RUNNING=0：运行状态；SHUTDOWN=1：暂停接受任务状态；STOP=2：停止接受新任务并终止当前任务；TERMINATED=3：所有线程都已销毁且任务队列为空时，标记为TERMINATED状态。
2. 任务的执行：线程池中的重要成员变量，除了上述构造器中的变量之外，还有以下几个：

1.ReentrantLock mainLock：线程池的状态锁，涉及到线程池状态改变的操作需要用该锁加锁

2.HashSet<Worker> workers：工作任务的集合，Worker是一个内部类，封装了Runable任务

其中的核心方法便是execute()方法，其源代码如下：
```java
public void execute(Runnable command) {
    if (command == null)
        throw new NullPointerException();
    if (poolSize >= corePoolSize || !addIfUnderCorePoolSize(command)) {//如果当前的线程数量不大于核心线程池数量，执行增加核心线程
        if (runState == RUNNING && workQueue.offer(command)) {//如果核心线程池满了，将任务加入执行队列
            if (runState != RUNNING || poolSize == 0)//做运行状态的双重验证，保证线程池未关闭，继续执行任务
                ensureQueuedTaskHandled(command);
        }
        else if (!addIfUnderMaximumPoolSize(command))//如果任务队列满了并且非核心线程也满了，拒绝该任务
            reject(command); // is shutdown or saturated
    }
}
```
其总体过程如下：

1.如果当前线程池中的任务数量小于corePoolSize，创建一个新线程去执行任务

2.如果线程数量超过corePoolSize，则将后续任务将加入缓存队列中，轮询该队列等待有空闲线程继续执行

3.如果任务队列已满，创建新的线程直接执行，如果线程数量达到maximumPoolSize，则会按照拒绝策略拒绝任务执行，这些线程会在达到keepAliveTime之后终止

3. 线程池的线程初始化：初始化线程池之后，无任务的时候是不会创建线程的，可以使用以下方法直接创建线程:prestartCoreThread,初始化一个核心线程；prestartAllCoreThreads，初始化所有核心线程
4. 任务缓存队列及排序策略：ArrayBlockingQueue，LinkedBlockingQueue，synchronousQueue，这个队列比较特殊，不会保存提交的任务，直接新建一个线程来执行任务
5. 任务拒绝策略：如上的参数
6. 线程池关闭：shutdown&&shutdownNow
7. 线程池大小动态调整：动态更改corePoolSize和maximumPoolSize的值

## 线程池的使用

上面说了不推荐直接使用ThreadExecutorPool的构造函数去直接创建线程池（不是很绝对，阿里就不推荐这样使用，但这样使用足够简单而且一般场景够用），在Executors中提供了几个静态方法，分别初始化好了一些参数，可以直接调用用来创建线程池：

1. Executors.newCachedThreadPool()：创建一个容量为Integer.MAX_VALUE的线程池，任务存在SynchronousList中，会直接创建新线程运行
2. Executors.newSingleThreadExecutor()：创建一个容量为1的线程池
3. Executors.newFixedThreadPool(int)：创建一个固定长度的线程池

线程池的大小一般通过CPU多少确定，可以创建1+CPU个，如果是IO密集型的任务，可以创建2N个，具体的值可以根据系统运行情况等进行调整。

# Future、FutureTask、Callable
## 简介
上面说明了可以线程池可以使用submit方法获取返回值，事实上submit的返回值就是Future<T>泛型，参数可以是Callable或Runable任务，Callable是一个接口，里面主要是一个有返回值的方法：V call() throws Exception，不同于Runable接口，实现类在重写该方法时重写的是call方法，Future是一个接口，主要有以下几个方法：
cancel():取消当前任务

isCanceled：当前任务是否取消

isDone:当前任务是否完成

V get():获取任务的运行结果，这个方法会阻塞当前的线程，直到另一个线程执行完毕并返回结果

V get(long timeout, TimeUnit unit)：阻塞指定时间获取结果，否则返回null

也就是说，Futrue主要有这些功能：

1. 中断任务执行
2. 判断任务是否完成
3. 获取任务的执行结果

FutureTask是Future接口的一个唯一实现类，其主要实现了RunableFuture接口，而RunnableFuture又继承自Runable和Futrue。
## 使用
### 使用Callable和Futrue获取结果
```java
public class Test {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        Future<Integer> result = executor.submit(task);
        executor.shutdown();
         
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
         
        System.out.println("主线程在执行任务");
         
        try {
            System.out.println("task运行结果"+result.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
         
        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```
### 使用FutrueTask和Callable获取结果
```java
public class Test {
    public static void main(String[] args) {
        //第一种方式
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        executor.submit(futureTask);
        executor.shutdown();
         
        //第二种方式，注意这种方式和第一种方式效果是类似的，只不过一个使用的是ExecutorService，一个使用的是Thread
        /*Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        Thread thread = new Thread(futureTask);
        thread.start();*/
         
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }
         
        System.out.println("主线程在执行任务");
         
        try {
            System.out.println("task运行结果"+futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
         
        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```
