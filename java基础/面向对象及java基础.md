# JAVA中的对象及对象引用
## 何为对象？
在java中，有一句比较流行的话，一切皆对象。引用《JAVA编程思想》中的一句话是：
> 按照通俗的说法，每个对象都是某个类（class）的一个实例（instance），这里，‘类’就是‘类型’的同义词。
## 何为对象引用？
尽管一切皆对象，但实际操纵的标识符为对象的引用。例如：
```java
Person person =  new Person("张三");
```
左侧是声明的一个对象引用，而右侧则是真正在堆上创建对象的操作。例如：
```java
Person person;
person = new Person("小明");
person = new Person("小红");
```
person可以指向任何Person类的实例。同样，一个对象也可以被多个引用所引用：
```java
Person person1 = new Person("小明");
Person person2 = person1;
```
# java中的权限控制
## 简介
java中的权限控制有一下几种：默认，public，protected，private。其中默认和public可以用来修饰类（非内部类），全部都可以修饰变量和方法。
如果类是默认修饰的话，只有同一包的类才能引用，其他包的类是不能引用的。使用public修饰则全部可以引入。
对于修饰字段和方法的关键字：
默认：只有同一包的类才能直接使用;private：只有同一类内才能使用；protected：只有同一包内或继承该类才能使用；public：都可以使用。
## 其他
如果在一个java文件里声明了一个public类，那么该文件名必须用该类进行命名，并且该文件内不允许有其他的public类。
# 类与继承
## 基础
面向对象的几个特性：抽象，封装，继承，多态。
java的基础数据类型：byte，short，int，long，float，double，boolean，char。这些基础数据类型在初始化时会被初始化为0或者false，对于对象的引用则会被初始化为null。
构造器：必须和类为同一个名称，不能有返回值，且必须为public的。
对象的初始化顺序：初始化对象时，先要加载该对象对应的类，加载类时，会按照顺序执行static代码块。之后类中的变量会在任何方法（包括构造器方法）执行前就开始初始化。
加载完类之后才开始初始化对象。
## 继承
java中所有的类如果未声明继承，都隐式继承与Object。使用extends可以继承一个类，然后可以调用基类的方法和变量。java只允许单继承。java的继承有如下规则：
1. 不能继承private的方法和变量
2. 如果不在同一个包内，protected和默认权限的方法和变量也不能继承
3. 同名的变量会被隐藏，同名的方法会被覆盖。
4. 构造器方法不能够被继承，且如果没有无参构造器，必须显示调用基类的构造器。
5. super代表基类，可以使用super.XXX调用方法和成员变量和super(XXX)调用构造器。
# 接口和抽象类
## 抽象类
### 简介
在了解抽象类之前，先来了解一下抽象方法。抽象方法是一种特殊的方法：它只有声明，而没有具体的实现。抽象方法的声明格式为：
```java
abstract void fun();
```
抽象方法必须用abstract关键字进行修饰。如果一个类含有抽象方法，则称这个类为抽象类，抽象类必须在类前用abstract关键字修饰。因为抽象类中含有无具体实现的方法，所以不能用抽象类创建对象。
包含抽象方法的类称为抽象类，但并不意味着抽象类中只能有抽象方法，它和普通类一样，同样可以拥有成员变量和普通的成员方法。注意，抽象类和普通类的主要有三点区别：
1. 抽象方法必须为public或者protected（因为如果为private，则不能被子类继承，子类便无法实现该方法），缺省情况下默认为public。
2. 抽象类不能用来创建对象；
3. 如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法。如果子类没有实现父类的抽象方法，则必须将子类也定义为为abstract类。
在其他方面，抽象类和普通的类并没有区别。
## 接口
### 简介
接口中可以含有 变量和方法。但是要注意，接口中的变量会被隐式地指定为public static final变量（并且只能是public static final变量，用private修饰会报编译错误），而方法会被隐式地指定为public abstract方法且只能是public abstract方法（用其他关键字，比如private、protected、static、 final等修饰会报编译错误），并且接口中所有的方法不能有具体的实现，也就是说，接口中的方法必须都是抽象方法。从这里可以隐约看出接口和抽象类的区别，接口是一种极度抽象的类型，它比抽象类更加“抽象”，并且一般情况下不在接口中定义变量。
## 不同
1. 抽象类可以提供成员方法的实现细节，而接口中只能存在public abstract 方法；
2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的；
3. 接口中不能含有静态代码块以及静态方法，而抽象类可以有静态代码块和静态方法；
4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。
# JAVA内部类
## 简介
内部类分为以下几种：成员内部类，局部内部类，匿名内部类和静态内部类。
可以简单做以下理解：成员内部类和静态内部类可以看做是类内的一个成员变量和静态变量，其权限修饰符等和成员变量保持一致。注意，如果成员内部类的变量和外部类的变量同名，外部类的变量挥别隐藏，因此需要用OutterClass.this.变量/方法来调用外部类的变量和方法。局部内部类是在方法内定义的类，其修饰符等和方法内部的变量一样是没有权限修饰符的，匿名内部类多用于接口的回调等。
成员内部类的用法：成员内部类的初始化方法：必须用外部类的对象调用内部类进行内部类对象的初始化，也就是说成员内部类是依赖于外部类的。匿名内部类是没有构造方法的，一般用来重写某个方法。静态内部类不依赖于外部类，和静态成员变量的用法是相似的。
## 代码
### 成员内部类：
```java
public class Test {
    public static void main(String[] args)  {
        //第一种方式：
        Outter outter = new Outter();
        Outter.Inner inner = outter.new Inner();  //必须通过Outter对象来创建
         
        //第二种方式：
        Outter.Inner inner1 = outter.getInnerInstance();
    }
}
 
class Outter {
    private Inner inner = null;
    public Outter() {
         
    }
     
    public Inner getInnerInstance() {
        if(inner == null)
            inner = new Inner();
        return inner;
    }
      
    class Inner {
        public Inner() {
             
        }
    }
}
```
### 局部内部类
```java
class People{
    public People() {
         
    }
}
 
class Man{
    public Man(){
         
    }
     
    public People getWoman(){
        class Woman extends People{   //局部内部类
            int age =0;
        }
        return new Woman();
    }
}
```
### 匿名内部类
```java
button.setOnClickListener(new OnClickListener() {
             
	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		 
	}
});
```
### 静态内部类
```java
public class Test {
    public static void main(String[] args)  {
        Outter.Inner inner = new Outter.Inner();
    }
}
 
class Outter {
    public Outter() {
         
    }
     
    static class Inner {
        public Inner() {
             
        }
    }
}
```
## 其他
1. 局部内部类和匿名内部类只能使用局部方法的final 变量（实际上是使用的该变量的复制，因此必须为final不可修改）。
2. 局部内部类创建依赖于外部类，默认会在编译时创建一个指向外部类的引用，然后在构造方法中将该外部类对象引入。
# JAVA中的装箱和拆箱
## 简介
装箱和拆箱是指基本数据类型与其包装类之间的相互转化。装箱时一般调用包装类的valueOf()方法,拆箱时一般调用xxxValue()方法。
### 其他
Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的。Double、Float的valueOf方法的实现是类似的。比较数值时，会触发自动拆箱的操作。
# JAVA中的异常及处理
## 简介
异常本质上是程序上的错误，包括程序逻辑错误和系统错误。比如使用空的引用、数组下标越界、内存溢出错误等，这些都是意外的情况，背离我们程序本身的意图。错误在我们编写程序的过程中会经常发生，包括编译期间和运行期间的错误，在编译期间出现的错误有编译器帮助我们一起修正，然而运行期间的错误便不是编译器力所能及了，并且运行期间的错误往往是难以预料的。假若程序在运行期间出现了错误，如果置之不理，程序便会终止或直接导致系统崩溃，显然这不是我们希望看到的结果。因此，如何对运行期间出现的错误进行处理和补救呢？Java提供了异常机制来进行处理，通过异常机制来处理程序运行期间出现的错误。通过异常机制，我们可以更好地提升程序的健壮性。
　　在Java中异常被当做对象来处理，根类是java.lang.Throwable类，在Java中定义了很多异常类（如OutOfMemoryError、NullPointerException、IndexOutOfBoundsException等），这些异常类分为两大类：Error和Exception。
　　Error是无法处理的异常，比如OutOfMemoryError，一般发生这种异常，JVM会选择终止程序。因此我们编写程序时不需要关心这类异常。
　　Exception，也就是我们经常见到的一些异常情况，比如NullPointerException、IndexOutOfBoundsException，这些异常是我们可以处理的异常。
　　Exception类的异常包括checked exception和unchecked exception（unchecked exception也称运行时异常RuntimeException，当然这里的运行时异常并不是前面我所说的运行期间的异常，只是Java中用运行时异常这个术语来表示，Exception类的异常都是在运行期间发生的）。
　　unchecked exception（非检查异常），也称运行时异常（RuntimeException），比如常见的NullPointerException、IndexOutOfBoundsException。对于运行时异常，java编译器不要求必须进行异常捕获处理或者抛出声明，由程序员自行决定。
　　checked exception（检查异常），也称非运行时异常（运行时异常以外的异常就是非运行时异常），java编译器强制程序员必须进行捕获处理，比如常见的IOExeption和SQLException。对于非运行时异常如果不进行捕获或者抛出声明处理，编译都不会通过。
　　在Java中，异常类的结构层次图如下图所示：![异常类的结构层次](https://springboot-blog-1256194683.cos.ap-beijing.myqcloud.com/%E5%BC%82%E5%B8%B8%E7%B1%BB%E7%9A%84%E7%BB%93%E6%9E%84.jpg)
    在Java中，所有异常类的父类是Throwable类，Error类是error类型异常的父类，Exception类是exception类型异常的父类，RuntimeException类是所有运行时异常的父类，RuntimeException以外的并且继承Exception的类是非运行时异常。
　　典型的RuntimeException包括NullPointerException、IndexOutOfBoundsException、IllegalArgumentException等。
　　典型的非RuntimeException包括IOException、SQLException等。
## 异常的详解及注意
参考：[异常的详解及使用](https://www.cnblogs.com/dolphin0520/p/3769804.html).
# JAVA中的拷贝
## 简介
java中的拷贝就是将目标对象在堆上复制一份，分为深拷贝和浅拷贝两种。直接使用改变引用的方式，例如：
```java
Person person1 = new Person("小明");
Person person2 = person1;
```
这样是无法拷贝对象的，因为只是两个引用（类似于指针）指向了同一个对象而已。
浅拷贝：只拷贝对象，不拷贝对象的成员变量为另一个对象的情况，步骤如下： 
1. 被复制的类需要实现Clonenable接口（不实现的话在调用clone方法会抛出CloneNotSupportedException异常) 该接口为标记接口(不含任何方法)
2. 覆盖clone()方法，访问修饰符设为public。方法中调用super.clone()方法得到需要的复制对象，（native为本地方法)
深拷贝：同时拷贝属性为另一个对象的情况。可以使用实现序列化和反序列化的方式实现，类似于创建了两个对象。
```java
package com.lin.test;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class ObjCloner {
    
    @SuppressWarnings("unchecked")
    public static  <T>T cloneObj(T obj){
        
        T retVal = null; 
        
        try{
            
            // 将对象写入流中
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            ObjectOutputStream oos = new ObjectOutputStream(baos);
            oos.writeObject(obj);
            
            
            // 从流中读出对象
            ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());
            ObjectInputStream  ois = new ObjectInputStream(bais);
            
            retVal = (T)ois.readObject();
            
        }catch(Exception e){
            e.printStackTrace();
        }
        
        return retVal;
    }

}
```