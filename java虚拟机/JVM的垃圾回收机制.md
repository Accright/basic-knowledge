# JVM垃圾回收机制
## 简介
JVM的垃圾回收机制简称GC，JAVA中不需要关心内存动态分配和垃圾回收的问题。垃圾回收机制主要解决一下几个问题：
1. 如何定义一个对象是否为垃圾？（引用计数和可达性分析）
2. 使用什么方法回收垃圾
3. 典型的垃圾收集器机制
## 如何定义一个对象是否为垃圾
1. 引用计数法：给对象提供一个引用的数量，如果引用数量为0说明对象可以被回收了。在JAVA中未使用该方法，因为存在循环引用的问题，如下方代码所示：
```java
public class Main {
    public static void main(String[] args) {
        MyObject object1 = new MyObject();
        MyObject object2 = new MyObject();
         
        object1.object = object2;
        object2.object = object1;
         
        object1 = null;
        object2 = null;
    }
}
class MyObject{
    public Object object = null;
}
```
虽然两个对象的引用都为null，但是内部还有对象的互相引用，所以会导致垃圾收集器一直不会回收该对象。
2. 可达性分析法：类似于一个引用链，从 GC Roots开始进行搜索，如果Roots和对象之间不可达，可说明该对象可以回收。
3. 其他：JAVA中使用软引用和弱引用声明和创建的对象，在内存不足时会被GC回收，例如如下代码：
```java
String str = new String("hello");
SoftReference<String> sr = new SoftReference<String>(new String("java"));
WeakReference<String> wr = new WeakReference<String>(new String("world"));
```
## 垃圾回收算法
### 标记清除算法
标记清除算法分为标记和清除两个阶段，先标记出所有需要回收的对象，清除阶段就是回收被标记对象的空间，这种算法容易出现内存碎片，而且对象如果过多会导致标记时间很长。
示例图如下：
![标记清除算法](https://springboot-blog-1256194683.cos.ap-beijing.myqcloud.com/%E6%A0%87%E8%AE%B0%E6%B8%85%E9%99%A4%E7%AE%97%E6%B3%95.jpg)
### 标记整理算法
标记整理算法分为标记和整理两个阶段，先标记出所有需要回收的对象，整理阶段就是将这些对象统一向内存一段移动，然后清除掉边界之外的空间，可以保证被释放的空间是连续的。
示例图如下：
![标记整理算法](https://springboot-blog-1256194683.cos.ap-beijing.myqcloud.com/%E6%A0%87%E8%AE%B0%E6%95%B4%E7%90%86%E7%AE%97%E6%B3%95.jpg)
### 复制算法
为了解决内存碎片的问题，复制算法将内存分为相等的两部分（或几部分），每次对象只在一个内存块上分配，进行内存回收时，将对象复制到另一块的内存区域，然后清除掉第一块的内存，继续在第二块内存上分配，这种方法高效且不易产生内存碎片，但是对内存空间的使用比较苛刻。
示例图如下：
![复制算法](https://springboot-blog-1256194683.cos.ap-beijing.myqcloud.com/%E5%A4%8D%E5%88%B6%E7%AE%97%E6%B3%95.jpg)
### 分代收集算法
这是大部分垃圾收集器采用的GC算法，将内存按照对象的生命周期分为不同的块，然后将不同的对象分配在不同的内存块上，不同的内存块采用不同的垃圾回收算法。一般将堆划分为新生代和老年代，老年代发生垃圾回收一般只回收少量对象，所以一般采用标记整理算法，新生代需要回收大量的对象，所以一般采用的是复制算法。一般新生代使用复制算法时，会将内存分为三个区域，一块Eden区域和两块Survivor区域，一般只是用其中一块Survivor区域和Eden区域分配内存，清理时将可达对象复制到另一块Survivor区域，然后清空Eden和初始的Survivor区域，再分配内存时将在清空的Eden和存在对象的Survivor空间上。在堆外还有一块永久代内存，用来存储class类，常量，方法描述等，对永久代的回收主要回收废弃的常量和无用的类。
## 典型的垃圾收集器
1. Serial/Serial Old：单线程收集器，用它收集时必须暂停所有的用户线程。Serial针对新生代，采用的复制算法，Serial Old针对老年代，使用的标记整理算法。
2. ParNew：多线程收集器，针对新生代，是Serial的多线程版本。
3. Parallel Scavenge/Parallel Old: 多线程收集器，Parallel Scavenge针对新生代，使用复制算法，Parallel Old针对老年代，使用的标记整理算法。该收集器实现了可控的吞吐量。
4. CMS(Current Mark Sweep)收集器，以获取最短收集停顿时间为主，并发收集器，使用标记清除算法。
5. G1收集器，并发收集器，针对服务器的多核环境，能建立可预测的停顿时间模型。
## 其他
新生代与老年代内存分配：
![新生代的内存分配](https://springboot-blog-1256194683.cos.ap-beijing.myqcloud.com/%E6%96%B0%E7%94%9F%E4%BB%A3%E5%86%85%E5%AD%98%E5%88%86%E9%85%8D.jpg)
内存分配一般分配在堆的新生代上，主要分配在Eden Space和From Space，对于大对象则直接分配在老年代上，或在GC过程中，To Space无法存储该对象时，对象也会被移到老年代，或者对象的GC次数到达15次等一定次数，也会被移到老年代的内存空间。当Eden区域和From Space没有足够的地址用来做内存分配时，就会发生GC操作。当然分配规则不是百分百固定的，取决于使用的垃圾收集器组合和JVM的相关参数。
## Full GC和Minor GC
全部GC和局部GC的触发条件:一般GC都是执行的MinorGC，即Eden区满时，但是一下情况会触发FullGC：
1. 调用System.gc时，系统建议执行Full GC，但是不必然执行
2. 老年代空间不足
3. 方法区空间不足
4. 通过Minor GC后进入老年代的平均大小大于老年代的可用内存
5. 由Eden区、From Space区向To Space区复制时，对象大小大于To Space可用内存，则把该对象转存到老年代，且老年代的可用内存小于该对象大小
总结就是老年代的空间不足时，会出发全局GC，FullGC