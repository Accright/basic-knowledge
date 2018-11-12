# String及StringBuffer、StringBuilder等
## 简介
String 是一个final修饰的类，其主要是利用char[]数组保存数据，对String对象的任何改变操作（concat,replace）等都不影响原来的对象，创建的对象初始化在运行时常量池。
StringBuffer和StringBuilder创建的对象初始化在堆上，其中StringBuilder是synchronized修饰的。
## 参考
具体可以参考一下链接： [String,StringBuffer和StringBuilder](https://www.cnblogs.com/dolphin0520/p/3778589.html)
# 字符串的比较
## ==和equals()的区别
引用Java编程思想中的一句话： 
> 关系操作符生成的是一个boolean结果，它们计算的是操作数的值之间的关系
也就是说，==是用来比较值是否相等的，对于基础的数据类型，是可以直接比较的，但对于对象的比较，实际是比较的对象的引入，如下代码：
```java
String a = new String("hello");
String b = new String("hello");
String c = new String("hello");
System.out.println(b==c);
b = a;
c = a;
System.out.println(b==c);
```
第一个输出为false，第二个为true，因为第二个比较的对象引用(类似于指针)是同一个了。
## 总结
1. 作用于基础数据类型，==比较的是值是否相等，equals不能作用于基础数据类型；
2. 作用于对象，==比较的是对象的引用地址是否相等，equals如果不重写比较的是对象引用指向的对象地址是否相同（即是否为同一个对象）。
# hashCode方法
## 简介
hashCode是一个native方法，返回的是一个int型整数，hashCode方法主要是用于容器类（HashSet，HashMap,HashTable）等的对象是否存在的判断，在向容器中插入的时候，先对key生成一个hashCode，作为key(实际上会多hash几次，避免出现重复)，然后以此作为数组下标（类似的或者链接地址）保存value，下次进行再放入值的时候，会先判断这个key的hashCode是否已经存在，如果已经存在，判断对象是否相同，如果相同进行覆盖(或者不保存)，否则继续链接在该hashCode下一个指向的地方，或者重新hashCode一次，可以参见具体的HashMap底层原理以及Hash冲突的解决办法。
## 其他
重写对象的equals方法时，注意也要重写hashCode()方法（适用于当该对象作为Key的情况），从而保证在向容器存取时取得的key的hashCode为同一个。同时，对象用来做hashCode的数据应该是不易变的，否则容易导致两次hashCode值不一致。