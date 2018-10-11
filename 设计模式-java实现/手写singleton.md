# 单例模式

## 简介
单例模式主要是为了避免因为创建了多个实例造成资源的浪费，且多个实例由于多次调用容易导致结果出现错误，而使用单例模式能够保证整个应用中有且只有一个实例。
## 注意
在JAVA中单例模式可以有多种实现，包括常见的懒汉模式，即用时加载然后常驻内存，需synchronize修饰，还有内部类方式。设计单例模式时应着重考虑其在多线程情况下不会创建多个实例。
## 对比
在知道了什么是单例模式后，我想你一定会想到静态类，“既然只使用一个对象，为何不干脆使用静态类？”，这里我会将单例模式和静态类进行一个比较。
1. 单例可以继承和被继承，方法可以被override，而静态方法不可以。
2. 静态方法中产生的对象会在执行后被释放，进而被GC清理，不会一直存在于内存中。
3. 静态类会在第一次运行时初始化，单例模式可以有其他的选择，即可以延迟加载。
4. 基于2， 3条，由于单例对象往往存在于DAO层（例如sessionFactory），如果反复的初始化和释放，则会占用很多资源，而使用单例模式将其常驻于内存可以更加节约资源。
5. 静态方法有更高的访问效率。
6. 单例模式很容易被测试。
## 实现
### 双重锁模式
```java
public class Singleton {
    public static volatile Singleton singleton = null;
    public static Singleton getInstance(){
        if(singleton == null){
            synchronized (Singleton.class){
                if (singleton == null){//双重锁 保证两个线程同时会串行执行
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```
### 内部类方式
```java
public class SingletonInnerClass {
    //通过构建内部类实现延迟加载
    public static class SingletonHodler{
        private static SingletonInnerClass singleton = new SingletonInnerClass();
    }
    public static SingletonInnerClass getInstance(){
        return SingletonHodler.singleton;
    }
}
```
### 饿汉模式
```java
//饿汉模式
public class SingletonHungry {
    private static SingletonHungry singletonHungry = new SingletonHungry();
    public SingletonHungry getInstance(){
        return singletonHungry;
    }
}
```
### 枚举方法
```java
public enum SingletonEnum {
    INSTANCE;
    public void doSomething(){
        System.out.println(">>>>>>do Something!");
    }

    public static void main(String[] args){
        SingletonEnum.INSTANCE.doSomething();
    }
}
```
### 方法锁模式
```java
public class SingletonLock {
    private static volatile SingletonLock singletonLock = null;

    //锁定类的静态方法
    public static synchronized SingletonLock getInstance(){
        return singletonLock;
    }
}
```