# 几种常用的JVM内存调试工具
## 简介
jvm调试工具是oracle自带的调试工具，也有部分外部的界面调试工具，在OpenJdk中是没有这些命令和工具的，其简介如下：
1. jps 查看java线程id和虚拟机列表
2. jinfo 查看虚拟机的信息
3. jstat 查看gc情况和内存分布信息
4. jmap 可以保存对象分析文件到dump，然后使用Mat等进行分析
5. jhat 分析jmap保存的文件
6. jstack 栈分析工具，可以查看线程锁

其他外部工具如下：
jconsole、jvisualvm、mat： 查看JVM内存，CPU信息，线程，参数，类信息等
## 内部工具简介
### jps
jps查看java虚拟机的列表。和ps -ef类似 
使用：
jps -l 

![jps](https://img-blog.csdn.net/20180913172423227?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
### jinfo
jinfo查看java虚拟机的参数配置。 
使用：

> jinfo pid

![jinfo](https://img-blog.csdn.net/20180913172615598?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
### jstat
jstat查看gc情况和内存中类的信息 
使用：

> jstat -gc pid 5000 1000 

![jstat](https://img-blog.csdn.net/20180913172921619?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

C表示总数，U表示used 
其中S0表示survivor 0，S1表示survivor 1，E表示eden，O表示old老年代，P表示Perm，YGC表示younggc的次数，YGCT表示younggc的总耗时，FGC表示fullgc的次数，FGCT表示fullgc总耗时，GCT表示gc的总耗时。

> jstat -gcutil pid 5000 1000 

![jstat](https://img-blog.csdn.net/2018091317344633?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

gcutil和gc差不多，只不过不是具体的内存数值，而是占比，内涵和gc类似

> jstack -class pid 5000 1000 

![jstat](https://img-blog.csdn.net/20180913173601912?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
### jmap
jmap是一个重要的工具，查看内存详细信息，可以dump到文件中 
使用：

> jmap [-F] dump:live,format=b,file=/tmp/a pid 

![jmap](https://img-blog.csdn.net/20180913173852330?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

-F 表示强制执行，live表示收集存活的对象，file存储到某个文件。dump文件下来后，可以使用工具来分析堆内存，包括jmap -histo、mat等

> jmap [-F] histo pid 

![jmap](https://img-blog.csdn.net/20180913174128525?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

查看堆内存的情况，按照对象数和对象占用的内存排队，可以初步定位到是哪个对象占用过多内存，内存泄漏的地方。后面使用mat工具能分析到哪个来存储的这个对象，也就是GCroot在哪

> jmap [-F] heap pid 

![jmap](https://img-blog.csdn.net/2018091317444960?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

heap能查看当前使用的垃圾收集器是什么和一些参数策略，还有具体的内存分布，一般用不到。参数可以用jinfo，内存分布可以用jstat
### jhat
jhat会分析一个dump文件，然后把结果发布到一个html服务器上，有一定的用途，html也是主要看histogram。和我们的jmap -histo功能类似，所以楼主觉得用处不大。 
使用：

> jhat file 

![jhat](https://img-blog.csdn.net/20180913174838275?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

返回访问127.0.0.1:7000 查看histogram 

![jhat](https://img-blog.csdn.net/20180913174947102?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### jstack
jstack是比较有用的一个命令，查看线程的情况，包含锁，俗称javacore 
使用：

> jstack [-F] [-l] pid 
> -l包含锁信息

![jstack](https://img-blog.csdn.net/20180913175336280?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
## 外部工具简介
### jconsole
查看jvm的内存，cpu信息，线程，参数，类信息
![jconsole](https://img-blog.csdn.net/20180913175705590?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
### jvisualvm
是一个比较好用的工具，界面功能更强大，界面更友好。 
也是监控内存，cpu，线程，类信息和参数的。还可以执行dump和javacore的生成。
![jvisualvm](https://img-blog.csdn.net/20180913175925137?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![jvisualvm](https://img-blog.csdn.net/20180913180004194?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
如果产线允许的话，可以直接连接到产线发现解决问题。设置远程ip和jmx端口。当然产线的java启动需要设置jmx配置：

> -Dcom.sun.management.jmxremote=true 
> -Dcom.sun.management.jmxremote.port=18080

### MAT
mat是一个比较强大的分析堆溢出的工具。把之前dump文件导入到工具中。 
首先看overview。
![MAT](https://img-blog.csdn.net/20180913180923481?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
有几个比较重要的信息。 
1、图中列出的大对象 
左击list object->with outgoing references，查看次大对象是哪个GCroot。 
![MAT](https://img-blog.csdn.net/20180913181556416?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
shallow heap为本对象大小，retained heap为实际包含的对象的总大小。主要看retained heap，一层一层展开，能够看到哪个类中存在存储大对象。 
2、histogram 
和jmap -histo和jhat中的类似。 
3、dominator tree 
查看各个对象的GCroot，和第一个点类似 
4、leak suspects 
内存泄漏疑点报告，会把可能的内存泄漏点展示，再查看detail，就能看到具体信息。 
![MAT](https://img-blog.csdn.net/20180913182146897?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
![MAT](https://img-blog.csdn.net/20180913182132395?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdhbm1pbmcxMjE0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



可参考链接：https://blog.csdn.net/denganming1214/article/details/82692635