# Java面试题
## 什么是虚拟机？为什么Java被称作是“平台无关的编程语言”
虚拟机是能够识别Java字节码的虚拟机进程。
虚拟机知晓底层硬件平台的指令长度和其他特性，所以我们只需要关心Java源文件的编写而不需要关心平台。

## JDK && JRE 区别
JRE是执行Java程序的Java虚拟机。
而JDK是完整的软件开发包，包含JRE、编译器和其他的工具(如JavaDoc、调试器等等)

## Java中是否可以覆盖(override)一个private或者是static的方法？
不可以，方法覆盖是在运行时动态绑定的，而static方法是在编译时静态绑定的。
## 是否可以在static环境中访问非static变量
不行，static变量和类一起加载。此时非static变量还没有被创建出来，且没有跟任何实例关联上

## 什么是自动拆装箱？怎么实现的？谈谈Integer i = new Integer(xxx)和Integer i =xxx;这两种方式的区别。
Java为每种基本数据类型都提供了对应的包装器类型
装箱就是自动将基本数据类型转换为包装器类型，拆箱就是自动将包装器类型转换为基本数据类型
装箱通过调用包装器的valueOf方法实现，而拆箱通过调用包装器的xxxValue实现

Integer、Short、Byte、Character、Long这几个类都存储了[-128,127]的XXCache，调用valueOf时候如果值在之间直接使用那个对象，否则创建新的对象
Double和Float直接赋值创建新的对象。
Boolean只有两个对象 True & False

Integer i = new integer(xxx)不会触发装箱，而第二种触发
当xxx在[-128,127]内时候，前者会创建新的对象，而后者不会

## equals方法和==
Long、Integer等的equals方法首先判断传入的参数类型是不是和他们一样，不一样直接false，否则比较他和它的XXValue()
==在有一个操作数是表达式时，看的是值，而都不是表达式时看指向的是不是同一个对象

## Java支持多继承吗？
Java的类支持单继承，但是接口可以多继承其他接口

## 接口和抽象类的区别
接口中所有的方法都是抽象的，抽象类的方法有抽象的可能也有非抽象的
接口只能被实现而不能被类继承，抽象类只能被继承
接口的成员变量均为public static final修饰的常量。抽象类则没有此限定

## 线程的创建方式 & 状态
继承Thread类、实现Runnable接口、使用线程池
状态：
新建new->可运行runnable->运行running->阻塞block->死亡dead

阻塞状态可能由于
1.调用wait 2调用了带锁的方法 3调用了其他阻塞

## 乐观锁和悲观锁
悲观锁假定会发生冲突，屏蔽一切可能违反数据完整性的操作
乐观锁假定不会冲突，只在提交操作的时候检查是否违反数据完整性

## Synchronize和Lock的区别与用法
在需要同步的对象中加入synchronize控制，synchronized可以加在方法上，也可以加在代码块中，括号需要锁的对象。
lock需要指定起始位置和终止位置，一般使用ReentrantLock类作为锁，多个线程必须要使用一个ReentrantLock类作为对象才能保证锁生效。且在加锁和解锁处需要通过lock()和unlock()显示指出
所以一般会在finally块中写unlock以防死锁

Synchronize使用的是悲观锁。而Lock使用的是乐观锁，即假设没有冲突而去完成某项操作，假设冲突失败就重试直到成功为止
ReentrantLock还提供了两种竞争锁的机制：公平锁和非公平锁，公平锁依赖线程进来的顺序，后来的线程后获得锁，而非公平锁即为后进来的锁也可以和前边等待锁的线程同时竞争锁资源

用途：
一般没什么区别，但在非常复杂的同步应用中使用ReentrantLock，特别是有下面两种需求的时候：
	1 某个线程在等待一个锁的控制权的时间内需要中断
		ReentrantLock可以使用lockInterruptibly方法使线程中断并不在等待锁的到来
	2 需要分开处理一些wait-notify，而ReentranLock里面的Condition可以控制notify哪个线程
		Condition processReady = lock.newCondition();
		processReady.await(); // 释放锁，进入阻塞等待其他线程的signal信号唤醒
## Java Collection类基本接口，为什么Collection没有实现Cloneable和Serializable
	map
	set
	list：有顺序的Collection，如arrayList、linkedList
	实现Serializable意味着该类需要通过字节流传输，而实现clonable接口意味着该类需要重载clone方法进行复制。不是所有的Collection都需要上面两种功能，所以没有实现

## HashMap和HashTable的区别
	HashMap允许键和值为null，而HashTable不允许
	Hashtable适合于多线程环境，HashMap更适合于单线程的环境
	HashMap提供Fail-Fast的迭代器，而HashTable提供的是Enumeration的类

## 快速失败(fail-fast)和安全失败(fail-safe)的区别
Fail-Fast：多个线程对Collection进行操作时，如果其中一个线程通过iterator去遍历集合时，集合的内容被其他线程所改变则会抛出ConcurrentModificationException
原理：当我们调用迭代器遍历集合时，会设置一个modCount如果线程发现modCount发生变化，则会抛出异常
ArrayList、Vector、HashSet等的迭代器是Fail-Fast的

Fail-Safe:
	Fail-safe的迭代器不会抛出任何异常，因为他们是在集合的复制上进行操作而非原本的集合
CopyOnWriteArrayList，ConcurrentHashMap等的迭代器是Fail-Safe的

一、Java基础

## String类为什么是final的，什么是不可变性
final主要是拒绝继承。确保String的不可变性
不可变性即当给字符串赋值的时候，不是在原内存上修改数据而是重新指向一个新对象新地址。

不可变性的实现
	private final char value[];
	实际上还是可以通过设置里面的数组来修改元素
	只是String的方法实现里没有去动Array的元素

## HashMap的源码，实现原理，底层结构。
实现原理：
	有一个内部类Entry，key、value、next，键值对的一个基础实现bean
	hash(Object)得到哈希码
	HashMap的hash表使用链表处理冲突
	get的时候通过key.equals得到正确的节点
	另外本身也有优化，当map size变大时达到threshold（loadfactor*size,负载因子默认是0.75）的时候。Entry也会变成2倍长度。
	确定数组index：hashcode % table.length取模
## 说说你知道的几个Java集合类：list、set、queue、map实现类咯。。。
	set：hashset、linkedhashset、treeset
	list: arraylist、llinkedlist、vector（与arraylist区别是线程安全的）、stack（继承vector）
	queue: PriorityQueue
	map: hashmap、linkedhashmap、HashTable、sortedmap
## Vector如何实现的线程安全
	所有方法都有synchronize关键字
## Java中的队列都有哪些，有什么区别。



## 反射机制的用途、应用


## 反射中，Class.forName和classloader的区别
	Class.forName将类的.class文件中加载到JVM中，且会执行雷州的static块。
	而classloader将类的.class集加载到JVM中，只在newInstance时才会去执行代码块
## Java7、Java8的新特性
	Java7：
		Switch中可以使用字符串了
	Java8：
		接口现在可以提供方法的默认实现。只需使用default关键字
		default returntype interfacename { ... }		
## Java内存泄露的问题调查定位：jmap，jstack的使用等等

## string、stringbuilder、stringbuffer区别
	String 字符串常量
	StringBuffer 线程安全的字符串变量
	StringBuilder 非线程安全的字符串变量	
## 异常的结构，运行时异常和非运行时异常，各举个例子

14. String a= “abc” String b = “abc” String c = new String(“abc”) String d = “ab” + “c” .他们之间用 == 比较的结果
	a = b = d 不等于 c
    因为new String会在堆中创建新的字符串，而不去管字符串内存池
    而字符串常量或字符串常量之间的拼接则会先去字符串常量池找。
    可以使用字符串的intern方法。该方法检查常量池中是否有该字符串的值的引用，如果没有就加入常量池
    
16. Java 的引用类型有哪几种

## java的基础类型和字节大小。
double、long 8
int 、 float 4 
char 、 short 2
byte 、boolean 1

19. Hashtable,HashMap,ConcurrentHashMap 底层实现原理与线程安全问题（建议熟悉 jdk 源码，才能从容应答）

20. 如果不让你用Java Jdk提供的工具，你自己实现一个Map，你怎么做。说了好久，说了HashMap源代码，如果我做，就会借鉴HashMap的原理，说了一通HashMap实现

22. HashMap冲突很厉害，最差性能，你会怎么解决?从O（n）提升到log（n）咯，用二叉排序树的思路说了一通

23. rehash

24. hashCode() 与 equals() 生成算法、方法怎么重写
hashCode的实现是根据本地机器相关的本地方法。
你也可以重写这个算法。要点是相同的对象有相同的hash值，如果两个对象的hashCode相同，它们并不一定相同。
String的hashCode实现
s[0]*31^(n-1) + s[1]*31^(n-2) + … + s[n-1]

二、Java IO

1. 讲讲IO里面的常见类，字节流、字符流、接口、实现类、方法阻塞。

2. 讲讲NIO。

## String 编码UTF-8 和GBK的区别?
GBK是使用双字节来表示所有字符，而UTF-8是对英文使用8位，中文使用24位编码。

4. 什么时候使用字节流、什么时候使用字符流?

5. 递归读取文件夹下的文件，代码怎么实现

三、Java Web

1. session和cookie的区别和联系，session的生命周期，多个服务部署时session管理。

2. servlet的一些相关问题

3. webservice相关问题

4. jdbc连接，forname方式的步骤，怎么声明使用一个事务。举例并具体代码

5. 无框架下配置web.xml的主要配置内容

6. jsp和servlet的区别

四、JVM
## Java内存管理
Java一般分为方法区、虚拟机栈、本地方法栈、堆、程序计数器
程序计数器 Program Counter Register
	用于指示当前线程所执行的字节码执行到了第几行
	易知它是线程私有的。
	如果程序执行的是Java方法，则计数器记录正在执行的虚拟机字节码地址，如果正在执行的是一个本地方法，则计数器的值为Undefined，由于程序计数器只是记录当前指令地址，所以不存在内存溢出的情况。
虚拟机栈 JVM Stack
	每个线程的每个方法在执行的同时都会创建一个栈帧。其中存储着局部变量表、操作站、动态链接、方法出口等。在方法被调用时栈帧入虚拟机栈，执行完毕后出栈。
	每个线程对应着一个虚拟机栈，所以同样线程私有
本地方法栈 Native Method
	本地方法栈在作用，运行机制，异常类型都与虚拟机栈相同。唯一区别是本地方法栈是执行本地方法的。
	同样线程私有

堆区 Heap
	堆区由所有线程共享，在JVM启动时创建。
	堆区用于存储对象实例。
方法区
	各个线程共享的区域。用于存储被虚拟机加载的类信息，final常量、静态常量、即时编译的代码等。还有运行时常量池也放在这，用于存储编译器就生成的字面常量、符号引用、直接引用

## Java内存分配 & GC
Java内存分配和回收概括地说就是分代分配、分代回收。
对象根据存活的时间分为：
年轻代 Young Generation、年老代 Old Generation、永久代 Permanet Generation

1.年轻代
对象创建时内存分配首先发生在年轻代(大对象可以直接被创建在年老代)
大部分的对象在创建后很快就消亡被年轻代的GC机制清理掉
该GC机制为Minor GC

年轻代的内存分配
年轻代分为3个区域 Eden区(伊甸园)，Suvivor0和Survivor1两个存活区
Survivor0和Survivor1总有一个是空白的
内存分配过程为：
绝大多数刚创建的对象分配在Eden区。Eden区是一个连续的内存空间，所以分配内存极快。
每当Eden区满的时候，执行Minor GC，将消亡的对象清理掉。并将剩余的对象复制到一个存活区Survivor0
当Survivor0也满的时候，将仍然活着的对象复制到Survivor1，以后Eden区执行Minor GC将剩余对象添加到Sunrvivor1，此时Survivor0是空的
当Survivor1也满的时候，将仍然活着的对象复制到Survivor0，以后Eden区执行Minor GC将剩余对象添加到Survivor0，此时Survivor1是空的
当这么一直切换了好多次以后，仍然存活的对象将被复制到年老代。

动态对象年龄判断：
虚拟机并不总是要求对象的年龄达到阈值才能达到老年代
当Survivor空间的相同年龄的所有对象大小总和大于Survivor空间的一半，则年龄大于等于该年龄的对象可以直接进入老年代。

年老代
年老代的空间一般比年轻代大，发生的GC次数也比年轻代少，当内存不足时会执行Major GC，也叫Full GC
对象比较大，空间不足，则大对象会直接分配到老年代上。可以使用PretenureSizeThreshold调节大对象的对象大小阈值，大于此值的都会被直接分配在老年代。
有时候存在年老代引用新生代对象的情况，如果需要执行Young GC，则会去查一个512 byte的块，所有老年代对象引用新生代对象的记录在这里(实现机制在下面)，Young GC查询此处以确定是否可以清理回收而非查全部老年代。

块的实现机制：
将老年代内存分片
如果老年代的对象发生了修改或老年代对象指向了新生代对象，就把老年代的对象所在的片标记为脏。
--XX：UseCondCardMark参数和该项有关，因为高并发时，标记为脏也存在竞争，使用它避免被重复标记。

GC算法
年轻代： Minor GC上面讲了
	停止-复制算法
	清理时将Eden和Survivor中存活的对象拷贝到另一个Survivor中，然后清理这两个区
老年代： Full GC
	老年代存储的对象大、多。
	使用标记-整理算法或标记-清理算法
	标记：遍历GC Roots标记所有的未存活对象。
	清除：遍历堆清除所有的未标记对象
	缺点：会产生内存碎片
	
	标记：遍历GC Roots标记所有的存活对象。
	整理：移动所有存活的对象按照内存地址依次排列。然后将末端内存地址以外的内存全部回收。
	缺点是效率低
	
方法区(永久代)：
	永久区需要回收常量池的常量和无用类，
	常量回收只需要没有引用了就可以被回收。
	对于无用的类进行回收，则必须保证三点：
		回收了类的所有实例
		加载类的ClssLoader已经被回收。
		类对象的Class对象没有被引用。（即没有通过反射引用该类的地方）

GC机制
	第一个机制负责回收堆的Young区
	第二个线程在堆不足时，遍历堆将Young区省纪委Older区

由于老年代和新生代的GC算法不一样，所以垃圾收集器也相应地分为老年代和新生代

新生代收集器：Serial、ParNew、Parallel Scavenge
Serial收集器
作用于新生代，是一个单线程收集器，基于复制算法。
在进行垃圾回收的时候仅使用单条线程并且在回收的过程中会挂起所有的用户线程(Stop the world)。Serial收集器是JVM client的默认收集器
ParNew收集器
是一个多线程收集器，基于复制算法。在进行回收的时候同时使用多条线程进行回收，在回收过程也会挂起所有的用户进程
Parallel Scavenge收集器
与ParNew类似多线程基于复制算法的收集器。不过此收集器主要关注的是吞吐量，而其他收集器主要关注的是缩短垃圾收集时用户线程的停顿时间。

停顿时间短意味着良好的响应速度，适合需要与用户交互的程序。
而高吞吐量则可以最高效率地利用CPU时间，尽快完成程序的运算任务，适合在后台运算而不需要太多交互的任务。

老年代收集器：Serial Old、PraallelOld、CMS
Serial Old收集器
作用于老年代，采用单线程和标记-整理算法来实现垃圾回收。在回收垃圾的时候同样会挂起所有用户线程，造成应用的停顿。该收集器是JVM Client模式下的老年代收集器
还有一个重要的用途是作为CMS收集器的后备方案，在并发收集发生Concurrent Mode Failure使用，进行内存碎片的整理。
Parallel Old收集器
是Parallel Scavenge收集器的老年代版本，采用多线程和标记-整理方法来进行垃圾回收。
该收集器主要是为了配合Parallel Scavenge收集器的使用，即当新生代选择Parallel Scavenge的时候，老年代可以考虑这个收集器
CMS收集器
Concurrent Mark Sweep是一款真正实现了并发收集的老年代收集器。CMS收集器可以最大程度的减少因GC而造成应用停顿的时间。CMS只在初始化和重新标记阶段需要挂起用户进程。而其他阶段收集进程都可以与用户线程并发交替进行
初始化标记
并发标记
并发预清除
重新标记
并发清除
并发重置
Concurrent mode failure
执行CMS GC的过程中有对象要放入老年代，而此时老年代空间不足
Promotion Failed
进行Minor GC的时候，新生代的Survivor区放不下，放入老年代也放不下

## jvm性能调优都做了什么

3. 介绍JVM中7个区域，然后把每个区域可能造成内存的溢出的情况说明

4. 介绍GC 和GC Root不正常引用。

5. 自己从classload 加载方式，加载机制说开去，从程序运行时数据区，讲到内存分配，讲到String常量池，讲到JVM垃圾回收机制，算法，hotspot。反正就是各种扩展

6. jvm 如何分配直接内存， new 对象如何不分配在堆而是栈上，常量池解析

7. 数组多大放在 JVM 老年代（不只是设置 PretenureSizeThreshold ，问通常多大，没做过一问便知）

8. 老年代中数组的访问方式

9. GC 算法，永久代对象如何 GC ， GC 有环怎么处理

10. 谁会被 GC ，什么时候 GC

11. 如果想不被 GC 怎么办

12. 如果想在 GC 中生存 1 次怎么办
13、普遍认为尽量不要使用finalize()进行资源释放,为什么？

在finalize时可能会导致对象复活

finalize的执行时间是没有保障的，它完全由GC线程决定，极端情况下，若不发生GC，则finalize没有机会执行

一个糟糕的finalize会严重影响GC性能

回收对象-->>FinalizerThread的执行队列-->>

但是，在某些场合，使用finalize可以起到双保险作用：在MySQL的JDBC驱动中，com.mysql.jdbc.ConnectionImpl就实现

了finalize

 

14、GC的两种判定方法：引用计数与引用链

15、GC停顿原因，如何降低停顿?

http://www.itdadao.com/2016/02/20/402713/

16、内网中有一张图片，如何防止被泄漏？

五、开源框架

1. hibernate和mybatis的区别

2. 讲讲mybatis的连接池。

3. spring框架中需要引用哪些jar包，以及这些jar包的用途

4. springMVC的原理

5. springMVC注解的意思

6. spring中beanFactory和ApplicationContext的联系和区别

7. spring注入的几种方式（循环注入）

8. spring如何实现事物管理的

9. springIOC

10. spring AOP的原理

11. hibernate中的1级和2级缓存的使用方式以及区别原理（Lazy-Load的理解）

12. Hibernate的原理体系架构，五大核心接口，Hibernate对象的三种状态转换，事务管理。

六、多线程
2. 常用的线程池模式以及不同线程池的使用场景

3. newFixedThreadPool此种线程池如果线程数达到最大值后会怎么办，底层原理。

4. 多线程之间通信的同步问题，synchronized锁的是对象，衍伸出和synchronized相关很多的具体问题，例如同一个类不同方法都有synchronized锁，一个对象是否可以同时访问。或者一个类的static构造方法加上synchronized之后的锁的影响。

5. 了解可重入锁的含义，以及ReentrantLock 和synchronized的区别

6. 同步的数据结构，例如concurrentHashMap的源码理解以及内部实现原理，为什么他是同步的且效率高

7. atomicinteger和Volatile等线程安全操作的关键字的理解和使用

8. 线程间通信，wait和notify

9. 定时线程的使用

10. 场景：在一个主线程中，要求有大量(很多很多)子线程执行完之后，主线程才执行完成。多种方式，考虑效率。

11. 进程和线程的区别

12. 什么叫线程安全？举例说明

13. 线程的几种状态

14. 并发、同步的接口或方法

15. HashMap 是否线程安全，为何不安全。 ConcurrentHashMap，线程安全，为何安全。底层实现是怎么样的。

16. J.U.C下的常见类的使用。 ThreadPool的深入考察； BlockingQueue的使用。（take，poll的区别，put，offer的区别）；原子类的实现。

17. 简单介绍下多线程的情况，从建立一个线程开始。然后怎么控制同步过程，多线程常用的方法和结构

18. volatile的理解

19. 实现多线程有几种方式，多线程同步怎么做，说说几个线程里常用的方法

七、网络通信

1. http是无状态通信，http的请求方式有哪些，可以自己定义新的请求方式么。

2. socket通信，以及长连接，分包，连接异常断开的处理。

3. socket通信模型的使用，AIO和NIO。

4. socket框架netty的使用，以及NIO的实现原理，为什么是异步非阻塞。

5. 同步和异步，阻塞和非阻塞。

6. OSI七层模型，包括TCP,IP的一些基本知识

7. http中，get post的区别

8. 说说http,tcp,udp之间关系和区别。

9. 说说浏览器访问http://www.taobao.com，经历了怎样的过程。

10. HTTP协议、 HTTPS协议，SSL协议及完整交互过程；

11. tcp的拥塞，快回传，ip的报文丢弃

12. https处理的一个过程，对称加密和非对称加密

13. head各个特点和区别

八、数据库MySql

1. MySQL的存储引擎的不同

2. 单个索引、联合索引、主键索引

3. mysql怎么分表，以及分表后如果想按条件分页查询怎么办(如果不是按分表字段来查询的话，几乎效率低下，无解)

4. 分表之后想让一个id多个表是自增的，效率实现

5. MySql的主从实时备份同步的配置，以及原理(从库读主库的binlog)，读写分离

7. 索引的数据结构，B+树

8. 事务的四个特性，以及各自的特点（原子、隔离）等等，项目怎么解决这些问题

9. 数据库的锁：行锁，表锁；

10. 数据库事务的几种粒度；

11. 关系型和非关系型数据库区别

九、设计模式

1. 单例模式：饱汉、饿汉。以及饿汉中的延迟加载,双重检查

2. 工厂模式、装饰者模式、观察者模式。

3. 工厂方法模式的优点（低耦合、高内聚，开放封闭原则）

十、算法

## 使用随机算法产生一个数，要求把1-1000W之间这些数全部生成。（考察高效率，解决产生冲突的问题） ？
1-1000W数组 shuffle足够次
13. 红黑树

十一、并发与性能调优

1. 有个每秒钟5k个请求，查询手机号所属地的笔试题(记得不完整，没列出)，如何设计算法?请求再多，比如5w，如何设计整个系统?

2. 高并发情况下，我们系统是如何支撑大量的请求的

3. 集群如何同步会话状态

4. 负载均衡的原理
DNS负载均衡：
	将不同的DNS请求解析到不同的服务器上
IP的负载均衡：
	LVS，根据特定的负载均衡算法将IP报文转发到不同服务器上
5 .如果有一个特别大的访问量，到数据库上，怎么做优化（DB设计，DBIO，SQL优化，Java优化）

6. 如果出现大面积并发，在不增加服务器的基础上，如何解决服务器响应不及时问题“。

7. 假如你的项目出现性能瓶颈了，你觉得可能会是哪些方面，怎么解决问题。

8. 如何查找 造成 性能瓶颈出现的位置，是哪个位置照成性能瓶颈。

9. 你的项目中使用过缓存机制吗？有没用用户非本地缓存

十二、其他

1.常用的Linux下的命令

祝大家面试顺利！
