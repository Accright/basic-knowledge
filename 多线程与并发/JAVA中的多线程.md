# Thread类详解
## 线程基础
线程基础其实在别的模块也介绍过了，这里再次做一个介绍。
### 线程状态转换
线程可以简单的分为几个状态，创建，就绪，运行，阻塞，销毁。其中阻塞又分为time waiting(多是sleep了该线程)，waiting(调用了wait方法，等待notify),blocked(IO等阻塞)。其原理图如下所示：

![线程的状态转化](https://images0.cnblogs.com/blog/288799/201409/061045374695226.jpg)

### 上下文切换
CPU只能同时运行一个线程，因此对于多线程就涉及轮询时间片时的上下文切换，其主要是存储当前线程的程序计数器和寄存器等CPU的值，再次运行时恢复CPU状态，使该线程能从中断点处继续执行。

## Thread类详解
### Thread类的常用方法
1. start()方法
start()方法对应线程中的创建和就绪状态，该方法会开启新的线程来执行子任务，同时会为该线程分配相应的资源。
2. run()方法
run()方法对应线程中的运行态，不需要直接调用，在获得CPU时间片之后，run方法会被自动调用，从而执行其中的具体的任务。
3. sleep()方法
sleep()方法对应其中的time waiting状态，sleep会让线程睡眠一段时间，然后继续执行。
4. yield()方法
yield()方法可以让当前线程交出CPU，然后等待下次的CPU轮询时间片继续执行。
5. join()方法
join()方法会将新的线程插入到主线程的执行过程中，通过不同的重载方法，可以指定主线程在子线程执行完之后继续执行或在子线程执行一段时间之后再执行，同时其会释放对象的锁。
6. interrupt()方法
interrupt()是中断阻塞状态的线程的方法，如果线程处于运行状态，该线程会在释放CPU之后才能被中断。
7. stop()方法
stop()方法主要是停止线程运行，已被废弃。
8. destroy()方法
停止线程的方法，已被废弃。

### 属性相关方法
1. getId： 获取线程的ID
2. getName&&setName：获取和设置线程名称
3. getPriority&&getPriority：获取和设置线程优先级
4. isDaemon&&setDaemon：是否为守护线程和设置为守护线程，守护线程会随主线程的销毁而销毁，无论任务是否执行完毕。例如垃圾收集器的线程便是守护线程。
5. currentThread:该方法为静态方法，用来获取当前的线程。

更详细的线程状态转换图如下所示：

![线程状态转换](https://images0.cnblogs.com/blog/288799/201409/061046391107893.jpg)

# synchronized关键字
## 简介
在访问临界资源时，会导致程序结果不一致或不正确的问题，因此在访问临界资源时，需要使用多线程加锁机制来实现同步的互斥访问。
## synchronized同步
synchronized方法是通过获取对象锁从而实现了多线程的互斥访问。在JAVA中，每一个对象都有一个锁标记，线程只有获取了synchronized标记的对象锁之后才能继续访问。synchronized关键字通过获取互斥锁，执行完之后再释放，从而使其他访问该临界对象的线程阻塞从而达到互斥访问的目的。在一个线程访问synchronized方法时，其他线程仍然可以访问其非synchronized方法，因为其他方法不需要使用临界资源，所以不需要获取对象锁也可以进行操作。synchronized方法会自动释放对象的锁。
## 几种synchronized方法的修饰
1. 修饰方法：表示方法是互斥的，只有一个线程可以操作该对象的互斥方法。
2. 修饰代码块：可以修饰一个具体的对象或对象的具体属性，可以是this，表示获取当前对象的锁。只有在执行完修饰的代码块之后，才会释放对对象或属性加的锁。
3. 修饰类：用来控制对static方法或对象的互斥访问，类和对象的synchronized是不互斥的，因为对象创建在堆上，而类的静态变量和静态方法创建在方法区的运行时常量池中。

# Lock类的锁
## 简介
synchronized虽然会实现线程互斥访问，但是存在一定的效率问题，比如读写互斥，读读不互斥中，使用synchronized会使读读的线程互斥，影响程序效率，因此便有了Lock类实现的锁，synchronized是JAVA的关键字，而Lock是java的一个类，Lock需要主动释放锁，而synchronized会自动释放锁。
## 相关概念
1. 可重入锁：synchronized和ReentrantLock锁都是可重入的，就是说锁是基于线程分配的，而不是基于方法分配的，在一个synchronized方法中，调用该对象的另一个synchronized方法，不需要再次申请该对象的锁，因为该线程已经持有该对象的锁了。
2. 可中断锁：顾名思义，就是在等待过程中可以中断的锁，synchronized就是不可中断锁，而Lock由于有lockInterruptibly方法，所以是可中断锁。
3. 公平锁：在一个线程释放锁之后，对象锁如果是由等待时间最久的线程获得锁，便是公平锁。synchronized是公平锁，而ReentrantLock默认为非公平锁。
## Lock类详解
Lock是一个接口，主要有以下几个方法：lock,lockInterruptibly,trylock,trylock(long time, TimeUnit unit),unlock等，主要的实现类有:ReentrantLock,ReadWriteLock,ReentrantReadWriteLock。
### 方法详解
1. lock:Lock的方法执行必须在try{}catch{}finally{}中处理，在最后释放锁，lock表示直接加锁
2. trylock: 判断是否可以获取锁，不等待直接返回
3. trylock(long time,TimeUnit unit):判断是否可以获取锁，会在传入的参数期间内不断重试
4. lockInterruptibly:加可中断锁，在等待过程中可以调用interrupt方法终端锁
### 实现类详解
1. ReentrantLock：可重入锁，示例代码如下：
```java
//lock使用
public class Test {
    private ArrayList<Integer> arrayList = new ArrayList<Integer>();
    private Lock lock = new ReentrantLock();    //注意这个地方
    public static void main(String[] args)  {
        final Test test = new Test();
         
        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();
         
        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();
    }  
     
    public void insert(Thread thread) {
        lock.lock();
        try {
            System.out.println(thread.getName()+"得到了锁");
            for(int i=0;i<5;i++) {
                arrayList.add(i);
            }
        } catch (Exception e) {
            // TODO: handle exception
        }finally {
            System.out.println(thread.getName()+"释放了锁");
            lock.unlock();
        }
    }
}
//trylock使用
public class Test {
    private ArrayList<Integer> arrayList = new ArrayList<Integer>();
    private Lock lock = new ReentrantLock();    //注意这个地方
    public static void main(String[] args)  {
        final Test test = new Test();
         
        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();
         
        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();
    }  
     
    public void insert(Thread thread) {
        if(lock.tryLock()) {
            try {
                System.out.println(thread.getName()+"得到了锁");
                for(int i=0;i<5;i++) {
                    arrayList.add(i);
                }
            } catch (Exception e) {
                // TODO: handle exception
            }finally {
                System.out.println(thread.getName()+"释放了锁");
                lock.unlock();
            }
        } else {
            System.out.println(thread.getName()+"获取锁失败");
        }
    }
}
//lockInterruptibly使用
public class Test {
    private Lock lock = new ReentrantLock();   
    public static void main(String[] args)  {
        Test test = new Test();
        MyThread thread1 = new MyThread(test);
        MyThread thread2 = new MyThread(test);
        thread1.start();
        thread2.start();
         
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        thread2.interrupt();
    }  
     
    public void insert(Thread thread) throws InterruptedException{
        lock.lockInterruptibly();   //注意，如果需要正确中断等待锁的线程，必须将获取锁放在外面，然后将InterruptedException抛出
        try {  
            System.out.println(thread.getName()+"得到了锁");
            long startTime = System.currentTimeMillis();
            for(    ;     ;) {
                if(System.currentTimeMillis() - startTime >= Integer.MAX_VALUE)
                    break;
                //插入数据
            }
        }
        finally {
            System.out.println(Thread.currentThread().getName()+"执行finally");
            lock.unlock();
            System.out.println(thread.getName()+"释放了锁");
        }  
    }
}
 
class MyThread extends Thread {
    private Test test = null;
    public MyThread(Test test) {
        this.test = test;
    }
    @Override
    public void run() {
         
        try {
            test.insert(Thread.currentThread());
        } catch (InterruptedException e) {
            System.out.println(Thread.currentThread().getName()+"被中断");
        }
    }
}
```
2. ReadWriteLock：
ReadWriteLock也是一个接口，主要有两个方法，readlock和writelock，分别用来获取读锁和写锁。
3. ReentrantReadWriteLock：
ReadWriteLock的实现类。

## Lock和synchronized
1. Lock是一个接口，synchronized是JAVA的内置关键字
2. Lock锁需要主动释放，synchronized锁会自动释放
3. Lock加的锁可以中断，synchronized不可以中断
4. Lock可以知道是否成功获得了锁
5. Lock可以提高多个线程的读操作效率

# volatile关键字
## volatile的作用 简单来说主要有两个作用，一是修改某一个临界变量时使CPU中缓存的变量失效，其他线程必须从内存中重新读取；二是禁止指令的重排序，保证指令执行的有序性。注意，volatile是不能保证操作的原子性的，对于临界资源的互斥访问还是要使用synchronized关键字或lock锁。
## volatile实现详解
对于并发性操作，可能会出现缓存不一致问题，目前的解决方案主要有：1. 通过总线加Lock的方式 2. 通过缓存一致性协议 保证CPU中的缓存一致。
保证多线程并发中，主要要解决以下问题：1. 原子性 2. 有序性 3.可见性
volatile关键字可以解决有序性和可见性，但是对于原子性却无法保证。java中32位的赋值(或64位赋值)是原子性的，即指令一次性执行完毕，而其他操作不是，volatile关键字不能实现原子性，保证原子性还是需要synchronized或Lock锁。

# ThreadLocal详解
## ThreadLocal简介
ThreadLocal可以简单理解为每个线程创建的内存工作副本，用于多线程并发时的数据库连接等，每个线程ThreadLocal内存区域都是互不影响的，其主要是通过获取当前线程，然后根据当前线程获取当前线程中的ThreadLocalMap类型的成员变量threadlocals,该类型是一个Map，其中的key就是一个ThreadLocal对象，value便是存入的值，获取时会根据当前的ThreadLocal对象去获取value，ThreadLocal和Thread是没有直接关系的，真正保存数据的是Thread的ThreadLocalMap成员变量。
## 方法详解
1. set(T value):使用currentThread()获取到当前线程，然后使用当前线程的threadlocals变量，如果不为空，初始化创建这个变量，源码如下：
```java
public void set(T value) {
	Thread t = Thread.currentThread();
	ThreadLocalMap map = getMap(t);
	if (map != null)
		map.set(this, value);
	else
		createMap(t, value);
}
```
2. get():使用currentThread获取到当前线程，然后用ThreadLocal作为key，获取线程中threadlocals中变量的value，如果value为空，初始化value，该初始化可以由initialValue()重写。
```java
public T get() {
	Thread t = Thread.currentThread();
	ThreadLocalMap map = getMap(t);
	if (map != null) {
		ThreadLocalMap.Entry e = map.getEntry(this);
		if (e != null)
			return (T)e.value;
	}
	return setInitialValue();
}
```
3. remove():移除当前值，相当于从Map中删除这个key对应的值。
```java
public void remove() {
	ThreadLocalMap m = getMap(Thread.currentThread());
	if (m != null)
		m.remove(this);
}
```
4. initialValue():初始化当前值，重写该方法，可以避免未set值时不返回null。