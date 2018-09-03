## Java基础
### Java多态的实现
多态实现主要是靠方法的动态绑定，而动态绑定的实现主要依赖于方法表，通过继承和接口的多态实现。执行方法时，Java会在方法区找到对应的方法表，
### String类为什么是final的，什么是不可变性
final主要是拒绝继承。确保String的不可变性
不可变性即当给字符串赋值的时候，不是在原内存上修改数据而是重新指向一个新对象新地址。

不可变性的实现
	private final char value[];
	实际上还是可以通过设置里面的数组来修改元素
	只是String的方法实现里没有去动Array的元素
### 访问修饰符public、private、protected、default的区别
	public可以任意访问
	protected只有同包或继承他的子类可以访问
	default只有同包能访问
	private只有当前类可以访问
## Java中是否可以覆盖(override)一个private或者是static的方法？
不可以，方法覆盖是在运行时动态绑定的，而static方法是在编译时静态绑定的。
### float f = 3.4; 是否正确 ； short s1 = 1; s1 = s1+1;是否正确，s1+=1呢?
	float f=3.4;不正确，3.4是double类的数，赋值给浮点型属于downcast，会报错。应该使用float f=3.4F;或float f=(float)3.4f;
	 short s1 = 1; s1 = s1+1;不正确，因为1是int类型，所以s1 + 1的值被提升为int类型，不能直接赋给short类型，需要进行强转。而s1 += 1是正确的，因为它相当于s1 = (short)s1 + 1;其中有隐含的强制类型转换
## 什么是自动拆装箱？怎么实现的？谈谈Integer i = new Integer(xxx)和Integer i =xxx;这两种方式的区别。
Java为每种基本数据类型都提供了对应的包装器类型
装箱就是自动将基本数据类型转换为包装器类型，拆箱就是自动将包装器类型转换为基本数据类型
装箱通过调用包装器的valueOf方法实现，而拆箱通过调用包装器的xxxValue实现

Integer、Short、Byte、Character、Long这几个类都存储了[-128,127]的XXCache，调用valueOf时候如果值在之间直接使用那个对象，否则创建新的对象
Double和Float直接赋值创建新的对象。
Boolean只有两个对象 True & False

Integer i = new integer(xxx)不会触发装箱，而第二种触发
当xxx在[-128,127]内时候，前者会创建新的对象，而后者不会
### Java中有没有goto？
	goto是Java中的保留字，但是在目前版本的Java中没有使用。
	Java的break和continue部分实现了goto的功能，例如使用break+标签可以跳出多重嵌套循环。
### switch(expr)中，expr的类型可以是
	byte、short、char、int、enum类、String
### Java的参数传递
	Java的参数传递永远是值传递。只不过传递的值可以是引用类型或基本数据类型
### 重载Overload和重写Override的区别
	重载实现编译的多态性，重载发生在一个类中，同名的方法有不同的参数列表(参数类型不同或参数个数不同满足至少一点)
	重写实现运行时的多态，重写发生在子类中，要求被重写方法与父类的方法参数列表相同、有相同的返回类型，不能比父类被重写方法声明更多的异常，比父类被重写方法更好访问(即访问权限不能更低)。
### Java7、Java8的新特性
	Java7：
		Switch中可以使用字符串了
	Java8：
		接口现在可以提供方法的默认实现。只需使用default关键字
		default returntype interfacename { ... }	
### 反射相关
**Object的getClass方法**
	一个本地的final方法(也就是说所有类的getClass其实都是调用这个方法)
**反射中，Class.forName和classloader的区别**
	Class.forName将类的.class文件中加载到JVM中，且会执行雷州的static块。
	而classloader将类的.class集加载到JVM中，只在newInstance时才会去执行代码块
**如何获取一个类的类对象**
	Class.forName("xxx");
	对象.getClass();
	类型.class;
**如何创建对象**
	通过类对象调用newInstance()方法
	通过类对象的getConstrutor()获取构造器对象并调用其newInstance()方法创建对象。
### string、stringbuilder、stringbuffer区别
	String 字符串常量
	StringBuffer 线程安全的字符串变量
	StringBuilder 非线程安全的字符串变量
### 接口和抽象类的区别
接口中所有的方法都是抽象的，抽象类的方法有抽象的可能也有非抽象的(甚至可以没有)
接口只能被实现而不能被类继承，抽象类只能被继承
接口的成员变量均为public static final修饰的常量。抽象类则没有此限定
### 静态嵌套类和内部类
内部类一定需要通过创建外部类对象访问，再用这个对象去创建内部类的实例对象而静态嵌套类不需要。
内部可以自由的访问外部类的成员和方法无论是否private，而静态嵌套类不能
### 匿名内部类
没有名字的内部类，仅能被使用一次
new fatherclassname() {
    // codes ...
}
匿名内部类只能且必须继承一个父类或者一个接口，直接使用new生成一个对象的隐式引用。
匿名内部类没有构造器，但是可以使用构造代码块达到初始化的效果。
如果定义了一个匿名内部类，希望它使用一个其外部定义的参数，那么编译器会要求该参数是final的。但是事实上内部类使用参数的时候，利用自身的构造器对传入的参数进行备份，还是调用的是自己的属性。
### 如何实现对象克隆
	1. 实现Cloneable接口重写Object类中的clone()方法
	2. 实现Serializable接口通过对象的序列化和反序列化实现克隆
		基于序列化和反序列化实现的克隆不仅仅是深度克隆，更重要的是通过泛型限定要克隆的对象是否支持可序列化。
### String s = new String("xyz");创建了几个字符串对象？ 
	首先如果xyz不存在字符串池的话，Java会创建一个xyz
	然后Java会使用new创建一个堆上的对象，并将xyz的值赋给它
	所以答案是1或2个
### String a= “abc” String b = “abc” String c = new String(“abc”) String d = “ab” + “c” .他们之间用 == 比较的结果
	a = b = d 不等于 c
	因为new String会在堆中创建新的字符串，而不去管字符串内存池
	而字符串常量或字符串常量之间的拼接则会先去字符串常量池找。
	可以使用字符串的intern方法。该方法检查常量池中是否有该字符串的值的引用，如果没有就加入常量池
### 序列化接口
	实现序列化接口后就可以通过输出流writeObject写出，输入流readObject读入
### Java的final与const
	Java的const是个保留字，但是并没有效果和作用。
	final修饰类表示类不能被继承，修饰方法表示方法不能被重写，修饰变量表示变量值不能被修改(修改类对象的时候类似const xx*，不能改变指向的对象但是可以改变里面的属性)	
##  Java 的引用类型有哪几种
强引用、软引用、弱引用、虚引用
强引用：只要有强引用，JVM宁愿抛出OutOfMemory也不会去回收它
软引用：有用但不是必须的对象，只有在内存不足的时候JVM才会回收对象。适合实现缓存。
弱引用：在垃圾回收期线程扫描到只有弱引用的对象时候，不论当前内存足够与否都会回收它的内存。只不过由于GC线程优先级不高，不一定会很快发现
虚引用：必须和引用队列ReferenceQueue联合使用，当垃圾回收期准备回收一个对象的时候，如果发现它还有虚引用，就会把虚引用加入与之关联的引用队列，程序通过该队列是否有虚引用可以判断对象是否已被垃圾回收
### hashCode() 与 equals() 生成算法、方法怎么重写
hashCode的实现是根据本地机器相关的本地方法。
你也可以重写这个算法。要点是相同的对象有相同的hash值，如果两个对象的hashCode相同，它们并不一定相同。
String的hashCode实现
s[0]*31^(n-1) + s[1]*31^(n-2) + … + s[n-1]
**equals方法和==**
Long、Integer等的equals方法首先判断传入的参数类型是不是和他们一样，不一样直接false，否则比较他和它的XXValue()
==在有一个操作数是表达式时，看的是值，而都不是表达式时看指向的是不是同一个对象
**if (username.equals("xxx")){...}有什么不妥之处**
username可能为空，应该使用"xxxx".equals(username)
**两个对象值相同(x.equals(y) == true)，但却可有不同的hash code，这句话对不对?**
对。实现的时候可以通过重写实现
### Java异常机制
	Java把异常当做对象处理，定义了它的基类java.lang.Throwable作为所有异常的超类，Java中的异常分为两大类:Error & Exception。
	Error一般指JVM相关的问题，如系统崩溃、虚拟机出错、动态链接失败等。应用程序通常无法处理这些错误，无需捕获
	Exception包括运行时异常和非运行时异常。
	运行时异常是RuntimeException类及其子类异常，如NullPointerException、IndexOutOfBoundsException、ArithmeticException等
	非运行时异常是RuntimeException以外的异常
	遇到RuntimeException的时候通常可以选择不处理。比如NullPointerException，就一般不处理。
**finally**
	finally肯定会被执行吗?
		不一定。在JVM突然退出或者当前执行try-catch的线程被kill，finally就不一定会执行。
	finally + return
		进入finally语句块总有一个原因(reason)。原因可能是try代码执行完毕或是他执行了一个形如return的控制流语句或者是异常处理代码执行完毕。这个原因将在finally语句执行被记忆(也就是说如果是return i这类的会记忆i当前的值)。但是如果finally块自己创建了一个离开的原因例如执行return，则原先的语句会被遗忘。
**TWR try with resource**
通过使用Java7的TWR语法可以精简代码。省去finally块中关闭资源的麻烦
try (XXXX xx = new XXXX(....)){
    ...
}
这样xx在try结束后会自动释放，该语法要求XXXX是实现Closeable接口的类
## Java集合类、流
### 传统IO(BIO) & AIO & NIO
同步并阻塞: 传统IO
同步非阻塞: NIO，面向缓冲区的，基于选择器的，用一个线程来轮询传输通道，哪个通道准备好了就处理哪个通道。
异步非阻塞: AIO，一个有效请求启动一个线程，操作系统完成后会告知启动线程进行处理，所以每个线程不必亲自处理IO而是委派操作系统处理且不需要等待IO完成。该模式采用了Linux的epoll模型
NIO三大部分：Channel、Buffer、Selector
NIO是基于块的，以块为基本单位处理数据，Buffer是一段连续的内存块。用于向缓冲读取或写入数据，是访问缓冲的接口。Channel是一个双向通道，即可读，也可写，但是应用程序不能直接对Channel进行操作，需要通过Buffer。
使用Bufffer读取数据一般遵循以下四个步骤:
写入数据到Buffer、调用flip()方法、从Buffer中读取数据、调用clear或者compact方法
当向Buffer中写入数据时，Buffer会记录下写了多少数据，一旦要读取数据，需要通过flip将Buffer从写模式切换到读模式。在读模式下，可以读取之前写入到Buffer的所有数据。
一旦读完了所有的数据，需要清空缓冲区，让它可以再次被写入，两种方式能清空缓冲区：调用clear或compact方法。clear会清空整个缓冲区，compact只会清除已经读过的数据，任何未读的数据都移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。
Buffer写数据一般有两种情况：
从Channel写到Buffer或者直接调用put往里面写数据
Buffer读取数据一般有两种方式：
从Buffer读取数据到Channel或者使用get()方法从Buffer中读取数据
Buffer的参数
Buffer有三个重要的参数:position、capacity、limit
limit指Buffer处于写模式还能写入的数据或者处于读模式时还能读的数据
一般使用过程就是: getChannel->channl.write(buffer);
由于NIO不是很好使，很多公司都有封装好的NIO框架如Netty
AIO

### 快速失败(fail-fast)和安全失败(fail-safe)的区别
Fail-Fast：多个线程对Collection进行操作时，如果其中一个线程通过iterator去遍历集合时，集合的内容被其他线程所改变则会抛出ConcurrentModificationException
原理：当我们调用迭代器遍历集合时，会设置一个modCount如果线程发现modCount发生变化，则会抛出异常
ArrayList、Vector、HashSet等的迭代器是Fail-Fast的
Fail-Safe:Fail-safe的迭代器不会抛出任何异常，因为他们是在集合的复制上进行操作而非原本的集合
CopyOnWriteArrayList，ConcurrentHashMap等的迭代器是Fail-Safe的
### ArrayList、Vector、LinkedList的存储性能和特性
ArrayList和Vector都是使用数组方式存储数据。Vector可以参考C++ STL的Vector，它有个属性叫做capacityIncrement的属性，可以设置，默认为0，当它小于等于0时每次Vector空间不够就开原来的两倍。而ArrayList则会是原来的2/3 + 1
它们两个本质上都是通过一个object数组来存储元素的。Vector是线程安全的而ArrayList不是
LinkedList则是通过双向链表实现
### TreeMap
	TreeMap是一个有序的键值对集合通过红黑树实现
### Java Collection类基本接口，为什么Collection没有实现Cloneable和Serializable
	实现Serializable意味着该类需要通过字节流传输，而实现clonable接口意味着该类需要重载clone方法进行复制。不是所有的Collection都需要上面两种功能，所以没有实现
### HashMap和HashTable的区别
	HashMap允许键和值为null，而HashTable不允许
	Hashtable适合于多线程环境，HashMap更适合于单线程的环境
	HashMap提供Fail-Fast的迭代器，而HashTable提供的是Enumeration的类
### HashMap的源码，实现原理，底层结构。
实现原理：
	有一个内部类Entry，key、value、next，键值对的一个基础实现bean
	hash(Object)得到哈希码
	HashMap的hash表使用链表处理冲突
	get的时候通过key.equals得到正确的节点
	另外本身也有优化，当map size变大时达到threshold（loadfactor*size,负载因子默认是0.75）的时候。Entry也会变成2倍长度。
	确定数组index：hashcode % table.length取模
### 修改HashMap实现get、set、setAll都是O(1)复杂度
``` java
class Value
{
public:
	int money;
	unsigned long long timeslot;//增加一个时间戳
	Value(){}
	Value(int m, unsigned long long t) :money(m), timeslot(t){}
};

class HashMap//键是string(员工姓名)，值是int(员工工资)
{
	public:
		hash_map<string, Value> hashmap;
		unsigned long long time;//插入的时间，初始化为1，每次插入记录或setAll时都会加1
		unsigned long long curtime;//为setAll函数服务，记录最后一次setAll的时间
		int curmoney;//记录最后一次setAll的工资

		HashMap() :curmoney(-1), curtime(0),time(1){}

		void put(const string &s, int i)
		{
			Value tmp(i, time);
			hashmap[s] = tmp;
			time++;//时间加1
		}

		int get(const string &key)
		{
			if (hashmap.find(key) == hashmap.end())
				return -1;//找不到记录
			else
			{
				unsigned long long time2= hashmap[key].timeslot;
				if (time2 > curtime)
					return hashmap[key].money;
				else
					return curmoney;//setAll操作的结果
			}
		}

		bool containsKey(const string &key)
		{
			if (hashmap.find(key) == hashmap.end())
				return false;
			else
				return true;
		}
		
		void setAll(int money)
		{
			curtime = time;
			curmoney = money;
			time++;
		}

};
```
### 书写Java代码列出目录下所有文件、文件拷贝、统计某字符串出现在文件中的次数
## JVM
### 什么是虚拟机？为什么Java被称作是“平台无关的编程语言”
虚拟机是能够识别Java字节码的虚拟机进程。
虚拟机知晓底层硬件平台的指令长度和其他特性，所以我们只需要关心Java源文件的编写而不需要关心平台。
### JDK && JRE 区别
JRE是执行Java程序的Java虚拟机。
而JDK是完整的软件开发包，包含JRE、编译器和其他的工具(如JavaDoc、调试器等等)
### JVM加载class文件的原理机制
JVM中类的装载通过类加载器ClassLoader和它的子类实现的。
由于Java的可移植性，经过编译的Java源程序并不是一个可执行程序而是一个或多个类文件，当Java程序需要使用某个类的时候，JVM会确保该类已经被加载、链接、初始化。
类的加载是指把.class文件中的数据读入到内存中，通常是创建一个字节数组读取.class文件。然后产生与加载类对应的Class对象。
加载完毕后进入链接阶段，这一阶段包括验证、准备(为静态变量分配内存和默认的初始值)和解析(将符号引用替换为直接引用)三个步骤。链接完毕后进入初始化阶段
初始化阶段包括:如果累存在直接的父类未初始化，就先初始化父类，如果类中存在初始化语句就依次执行这些初始化语句。
类的加载是由类加载器，类加载器包括：根加载器BootStrap、扩展加载器Extension、系统加载器System和用户自定义类加载器java.Lang.ClassLoader的子类。Java的加载采取了父类委托机制，Boostrap是根机制，Java首先让父加载器加载，父加载器无能为力的时候才由其子加载器自行加载。
	BootStrap: 一般用本地代码实现，负责加载JVM基础核心类
	Extension: 从java.ext.dirs系统属性指定的目录加载类，父类是BootStrap
	System: 又叫应用类加载器，父类是extension。是用户自定义类加载器的父加载器。
### Java的内存管理
Java一般分为方法区、虚拟机栈、本地方法栈、堆、程序计数器、直接内存
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
直接内存
	受本机内存限制，简单说就是计算机其他地方的内存
简单的来说，函数调用的线程保存、对象的引用、基本数据类型的变量都放在JVM的栈区。而通过new关键字和构造器构造的对象都放在堆空间，堆是GC管理的主要区域。
## JVM中7个区域可能造成内存溢出的情况说明
程序计数器：不会出现内存溢出情况
虚拟机栈、本地方法栈：
	如果线程请求栈的深度大于虚拟机所允许的深度 StackOverError
	如果栈的扩展时无法申请到足够的内存 OutOfMemoryError
Java堆：
	堆中内存不够了且无法扩展时 OutOfMemoryError
方法区：
	方法区无法满足内存的分配需求时 OutOfMemoryError
运行时常量池：
	当常量池无法再申请到内存时 OutOfMemoryError
直接内存：
	系统内存不足时 OutOfMemoryError
### Java中的基本数据类型一定存储在栈中吗？
不一定。栈内存用来存储局部变量和方法调用。
如果局部变量是一个对象(如int[])，那么栈只存储引用，而堆存储对象
### TLAB
堆内的对象数据是各个线程所共享的，所以当在堆内new一个对象的时候就要进行锁操作，锁操作比较耗费性能，因此针对每个线程，在堆上的Eden区分配了一块TLAB(Thread Local Allocation Buffer)，每个线程在创建新的对象的时候首先尝试在TLAB里进行分配，如果成功就返回，失败再到Eden其他区域申请空间。
###  jvm new 对象如何不分配在堆而是栈上，常量池解析
**逃逸分析**
逃逸分析Escape Amalysis是一种可以有效减少Java同步负载和内存堆分配压力的跨函数全局数据流分析算法。通过它，Hotspot编译器能够分析出一个新的对象的使用范围从而决定是否要将这个对象分配在堆上。如果一个方法中的对象，对象的引用没有发生逃逸，那么这个方法可能被分配在栈内存上而非常见的堆内存上。
逃逸状态分为：
	全局逃逸：一个对象的引用复制给了类变量或者存储在一个已逃逸的对象中或者引用作为返回返回给了调用方法
	参数级逃逸：在方法调用过程传递对象的引用给一个方法。
	没有逃逸

### Java GC 区域、算法
Java内存分配和回收概括地说就是分代分配、分代回收。
对象根据存活的时间分为：
	年轻代 Young Generation、
	年老代 Old Generation、
	永久代 Permanent Generation
**年轻代**
对象创建时内存分配首先发生在年轻代(大对象可以直接被创建在年老代)
大部分的对象在创建后很快就消亡被年轻代的GC机制清理掉，该GC机制为Minor GC
年轻代的内存分配
年轻代分为3个区域 Eden区(伊甸园)，Suvivor0和Survivor1两个存活区
Survivor0和Survivor1总有一个是空白的
内存分配过程为：
1. 绝大多数刚创建的对象分配在Eden区。Eden区是一个连续的内存空间，所以分配内存极快。
2. 每当Eden区满的时候，执行Minor GC，将消亡的对象清理掉。并将剩余的对象复制到一个存活区Survivor0
3. 当Survivor0也满的时候，将仍然活着的对象复制到Survivor1，以后Eden区执行Minor GC将剩余对象添加到Sunrvivor1，此时Survivor0是空的
4. 当Survivor1也满的时候，将仍然活着的对象复制到Survivor0，以后Eden区执行Minor GC将剩余对象添加到Survivor0，此时Survivor1是空的
当这么一直切换了好多次以后，仍然存活的对象将被复制到年老代。

动态对象年龄判断：
虚拟机并不总是要求对象的年龄达到阈值才能达到老年代
当Survivor空间的相同年龄的所有对象大小总和大于Survivor空间的一半，则年龄大于等于该年龄的对象可以直接进入老年代。

**年老代**
年老代的空间一般比年轻代大，发生的GC次数也比年轻代少，当内存不足时会执行Major GC，也叫Full GC
对象比较大，空间不足，则大对象会直接分配到老年代上。可以使用PretenureSizeThreshold调节大对象的对象大小阈值，大于此值的都会被直接分配在老年代。
有时候存在年老代引用新生代对象的情况，如果需要执行Young GC，则会去查一个512 byte的块，所有老年代对象引用新生代对象的记录在这里(实现机制在下面)，Young GC查询此处以确定是否可以清理回收而非查全部老年代。

块的实现机制：
将老年代内存分片
如果老年代的对象发生了修改或老年代对象指向了新生代对象，就把老年代的对象所在的片标记为脏。
--XX：UseCondCardMark参数和该项有关，因为高并发时，标记为脏也存在竞争，使用它避免被重复标记。

**GC算法**
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
	第二个线程在堆不足时，遍历堆将Young区升级为Older区

由于老年代和新生代的GC算法不一样，所以垃圾收集器也相应地分为老年代和新生代

### Java GC 收集器
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
初始化标记、并发标记、并发预清除、重新标记、并发清除、并发重置

会抛出两个异常
Concurrent mode failure
执行CMS GC的过程中有对象要放入老年代，而此时老年代空间不足
Promotion Failed
进行Minor GC的时候，新生代的Survivor区放不下，放入老年代也放不下

### Java的内存泄漏
	如果长生命周期的对象持有短生命周期的引用，就很可能会出现内存泄露
	可能出现情况:
		集合里面的对象被修改后，调用remove方法无法remove，因为改变对象的属性后，hashCode值可能发生变化
		各类连接(数据库连接等等)，不显式调用close是不会被回收的
	解决方法:
		及时将无用的对象标记为可被清理的对象null


5. 自己从classload 加载方式，加载机制说开去，从程序运行时数据区，讲到内存分配，讲到String常量池，讲到JVM垃圾回收机制，算法，hotspot。反正就是各种扩展



## 数组多大放在 JVM 老年代（不只是设置 PretenureSizeThreshold ，问通常多大，没做过一问便知）
首先由PretenureSizeThreshold决定：没有默认值，设置参数后，大于参数的对象将直接到老年代
还有就是长期存活的对象(大于MaxTenuringThreshold，默认为15)会移入老年代
还有一个机制是动态对象年龄判断：
虚拟机并不总是要求对象的年龄达到阈值才能达到老年代
当Survivor空间的相同年龄的所有对象大小总和大于Survivor空间的一半，则年龄大于等于该年龄的对象可以直接进入老年代。
8. 老年代中数组的访问方式
## Systen.gc()调用的是full gc
## GC 算法，永久代对象何时GC
调用了System.gc()或永久代空间已满
## GC有环怎么处理
使用可达性判断算法即可
## 如果想不被 GC 怎么办

## 如果想在 GC 中生存 1 次怎么办
	即使在GC中被判断为不可达对象，也不一定死亡，此时它们处于“缓刑”状态，要真正被GC回收，还需要两次标记过程：
	如果对象可达性分析后没法发现引用链，则它被第一次标记并进行一次筛选，筛选的条件是此对象是否有必要执行finallize，若对象重写了finalize方法且未执行过finalize方法，则将其放入F-QUEUE队列。会有一个低优先级虚拟机自动建立的线程去执行finalize方法。稍后GC会对F-QUEUE中的对象再来一次可达性判断，如果可达则会"复活"。
	所以通过重写fianlize方法来引用引用链上的对象就可重新复活
## 普遍认为尽量不要使用finalize()进行资源释放,为什么？
	finalize的执行时间是没有保障的，它完全由GC线程决定，极端情况下，若不发生GC，则finalize没有机会执行，有可能造成资源无法得到释放。
	一个糟糕的finalize会严重影响GC性能
	但是，在某些场合，使用finalize可以起到双保险作用：在MySQL的JDBC驱动中，com.mysql.jdbc.ConnectionImpl就实现了finalize
## GC的两种判定方法(垃圾检测算法)：引用计数与引用链
	引用计数：每有一个地方引用对象，引用计数+1，引用失效后-1，引用计数为0的对象可以被回收。
	缺点是两个对象相互引用的时候就无法回收
	引用链：通过可达性分析（Tracing GC）,通过一系列名为"GC Roots"的对象作为起始点，从节点往下搜索，走过的路径称为引用链。当一个对象到GC Roots没有任何引用链相连时则证明此对象是不用的，判定为可回收对象。
	GC Roots一般选用的是确定存货的引用类型对象，如虚拟机栈局部变量、方法区中常量、静态属性，本地方法栈JNI引用的对象。
## 谁会被 GC ，什么时候 GC
	谁会被GC：不可达对象
	什么时候GC: 程序调用System.gc可以触发；系统自身来决定GC触发的时机(如内存大小空间不足的时候)
## GC停顿原因，如何降低停顿?
CMS启动过程中，新生代提升速度过快、老年代收集速度赶不上提升速度
http://www.itdadao.com/2016/02/20/402713/
## Java多线程
### 线程安全概念
如果代码所在的进程有多个线程在同时运行，这些线程可能会同时运行这段代码，如果每次运行结果和单线程运行的结果是一样的，而且其他的变量值也和预期的是一样的，就是线程安全的。
### atomicinteger和Volatile等线程安全操作的关键字的理解和使用
**AtomicInteger**
atomicinteger是一个提供原子操作的Integer类，通过线程安全的方式操作加减，适合高并发情况的使用。
像i++这样的操作在多线程下存在问题。这时候就要使用这个类
该类使用的是非阻塞方式，避免了Synchronize和锁，响应快，效率高
它对++操作的实现是开一个死循环，不停地获取值和计算+1的值，然后调用compareAndSet(current,next)直到其返回true，该方法实质上是调用unsafe类的本地方法compareandswapint，如果当前值等于传入的参数current则它会将当前值设置为next
**volatile**
volatile变量具有synchronize的可见性，但是不具备原子特性。
要使用volatile的变量的情况：
对变量的写操作不依赖于当前值
变量没有包含在具有其他变量的不等式中
所以大部分情况都不符合上面两个条件，所以一般使用情况只有：
1.作为布尔型的状态标示符
2.一次性安全发布，将对象引用定义为volatile
3.传感器啥的
4.volatile bean，所有变量+volatile，getter、settter不变
5.读写操作，写多，则使用volatile读，使用synchronize的写



### sleep方法和wait方法区别
	sleep方法是Thread类的静态方法，调用此方法会让当前线程暂停执行一段时间，将执行机会给其他时间，但是线程的锁仍然保持，休眠时间结束后回到就绪状态
	wait方法是Object类的方法。调用wait方法导致当前线程放弃对象的锁，进入等待池，只要调用对象的notify方法才能被唤醒，如果线程被重新唤醒且重新获得对象是锁则可以进入就绪状态。
### sleep方法和yield方法区别
	sleep()方法给其他进程运行机会的时候不会考虑线程的优先级，而yield只会给比自己等级高的线程让步
	线程执行sleep转入阻塞状态，而执行yield后进入就绪态。
	sleep抛异常、yield不抛

### 线程池
	创建和销毁对象是需要很费时间的。所以创建一个线程池用于减少这样的花费。
	Executors是一个工具类提供了一些方便的操作
**常用的线程池场景以及不同线程池的使用场景**
newCachedThreadPool:适用于执行很多短期异步的小程序或者负载较轻的服务器
newFixedThreadPool:执行长期的任务，性能好很多
newSingleThreadPool: 一个任务一个任务执行的场景
newScheduledThreadPool：周期性地执行任务的场景
**newFixedThreadPool线程池达到最大值后会怎么办，底层原理**
会把新进入的线程加入到LinkedBlockingQueue这个阻塞队列中，等待池中进程执行完毕
底层原理：
在ThreadPoolExecutor.execute(commands)中调用workQueue.offer(command)入队
###  J.U.C下的常见类的使用。 ThreadPool的深入考察； BlockingQueue的使用。（take，poll的区别，put，offer的区别）；原子类的实现。
## 在一个主线程中，要求有大量(很多很多)子线程执行完之后，主线程才执行完成。多种方式，考虑效率
方法1:使用sleep让主线程沉睡一段时间，但是这个方法不好使，因为时间是主观给的
方法2:使用CountDownLatch类，子线程通过调用countDown方法表示子线程调用完毕，而主线程调用await方法等待子线程完成，注意await要在所有子线程start之后立即执行且初始化的时候线程计数器(new CountDownLatch(num))的num一定要是子线程countDown的次数
方法3:使用CyclicBarrier，该类允许一组线程互相等待直到到达某个公共屏障点。用法如其名即所有的线程到齐(调用barrier.await())后才能通过公共屏蔽点(障碍)。
## 乐观锁和悲观锁
悲观锁假定会发生冲突，屏蔽一切可能违反数据完整性的操作
乐观锁假定不会冲突，只在提交操作的时候检查是否违反数据完整性
## 可重入锁
在同一个线程中给同一把锁加锁而不会造成死锁
Synchronize和ReentrantLock都是可重入锁
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
**多线程之间通信的同步问题，synchronized锁的是对象，衍伸出和synchronized相关很多的具体问题，例如同一个类不同方法都有synchronized锁，一个对象是否可以同时访问。或者一个类的static构造方法加上synchronized之后的锁的影响。**

### JDBC
步骤
Class.forName("com.jdbc.driver.MySQLDriver");
Connection con = DriverManager.getConnection("url");
PreparedStatement ps = con.prepareStatement("sql语句，参数可用?代替");
ps.setInt(pos,val); // pos从1开始
ResultSet rs = ps.executeQuery();
while (rs.next()){
    
}
finally {
    if (con !- null){
        try { rs.close(); ps.close(); con.close();}
        catch (SQLException e) {e.printStackTrace();}
    }
}
**PreparedStatement和Statement哪个好**
PreparedStatement接口代表预编译的语句，可以增强安全性，且批量处理的时候有明显的性能上的优势。
**提升读取数据的性能**
通过指定结果集抓取的大小FetchSize，采用PreparedStatement来处理批处理SQL等
**连接池**
由于创建和释放链接有较大的开销所以可以实现创建爱你若干链接至于连接池中，需要时候可以直接从连接池获取，使用结束后归还连接池而不必关闭。
## JavaWeb
1. session和cookie的区别和联系，session的生命周期，多个服务部署时session管理。
2. servlet的一些相关问题
3. webservice相关问题
5. 无框架下配置web.xml的主要配置内容
6. jsp和servlet的区别
### 可以自己定义新的http请求方式么
	是可以的，但是例如Tomcat的Servlet只接受传统的那些HTTP请求(GET、HEAD、POST...)，虽然你可以通过实现你自己的DispatcherServlet，重写service方法来让它能够接受你自定义的请求方式。
### hibernate和mybatis的区别
hibernate的移植性比mybatis更好。hibernate通过强大的映射结构与hql语言，降低了对象与数据库之间的耦合性，而mybatis需要手写sql语句，所以如果sql不具有通用性而使用了许多数据库特性，移植性也会受到很大影响
hibernate拥有完整的日志系统，mybatis则欠缺一些，只有基本的sql记录功能
sql直接优化上，mybatis由于是手写的，比较方便优化。
### 讲讲mybatis的数据源和连接池
MyBatis将数据源分为三类：UNPOOLED(不使用连接池)、POOLED(使用连接池)、JNDI(使用JNDI实现的数据源)
java.sql.UnpooledDataSource、PooledDataSource
**数据源的创建**
在xml配置文件中
``` xml
<dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://127.0.0.1:3306/testdb" />
            <property name="username" value="root"/>
            <property name="password" value="newpass"/>
</dataSource>
```
MyBatis通过抽象工厂接口DataSourceFactory来创建数据源对象，分别有对应的Pooled/Unpooled/JndiDataSourceFactory
**连接(java.sql.Connection)对象的创建**
lazy load
直到我们需要执行sql语句的时候，才会创建java.sql.Connection对象
**dataSrouce.getConnection()**
对于Unpooled，它将user和password封装到props里后通过java.sql.DriverManager.getConnection()获取连接对象
对于连接池
getConnection会去调用popConnection方法
该方法首先查看是否有空闲的PooledConnection对象，如果有，就直接返回一个可用的PooledcConnection对象。
如果没有就再去查看池中的ActiveConnection是否已满，没有满就新创建一个PooledConnection对象，然后放到ActiveConnection中。
如果ActiveConnection也满了，就去看最先进入的PooledConnection对象是否已经过期，如果过期从ActiveConnection中移除，然后创建新的PooledConnection对象放到ActiveConnection中。如果没有过期的，则进入等待状态过一会再进行上述步骤
**连接对象的回收**
使用完了以后对于非连接池的连接对象，调用con.close();
对于连接池对象，也是调用close()，不过该close实际上是把连接对象还给池子里。
这是因为我们使用的PooledConnection内部持有一个真正的Connection的实例和代理。
当我们调用GetConnection的时候，实际上返回的是代理的连接
当我们调用close方法时，调用了PooledDataSource.pushConnection将连接放回连接池
### spring框架中需要引用哪些jar包，以及这些jar包的用途
spring-core.jar
	核心依赖包
spring-beans.jar
	访问配置文件、创建管理Bean、进行Ioc/DI操作的所有类
spring-aop.jar
	使用Spring AOP特性所需要的类和源码元数据支持。
spring-context.jar
	Spring核心的扩展，包括ApplicationContext等
spring-dao.jar
	Dao、Transaction及数据访问的所有类
spring-jdbc.jar
	这个jar文件包含Spring DAO以及事务进行数据访问的所有类。
spring-orm.jar
	DAO包的扩展
spring-remoting.jar
spring-support.jar
spring-web.jar
	使用Spring框架Web应用开发所需的核心类
spring-webmvc.jar
	Spring MVC所需的类
spring-mock.jar
	包含一整套mock类来辅助应用的测试。
### springMVC的原理
### spring中beanFactory和ApplicationContext的联系和区别
BeanFactory提供了最简单的容器的功能，只提供了实例化对象和拿对象的功能
ApplicationContext继承ListBeanFactory接口(该接口扩展了BeanFactory类)，它是Spring的一个更高级的容器，提供了更多的功能：国际化、访问资源、AOP
ApplicationContext在启动的时候就将所有Bean实例化。而BeanFactory是lazy-load，直到使用的时候才会去实例化
### spring注入的几种方式
	set方法/属性注入
	构造函数注入
	接口注入
	注解注入
### spring如何实现事务管理的
把笔记上的加进来就好
### spring IOC & DI
传统Java程序中，我们通过new来创建、使用对象，而在Spring中，你需要通过容器来获取、使用这些对象。
IoC是一种思想，指导我们写出低耦合的程序。传统程序在类中主动创建依赖对象，有了IoC容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是松散耦合，这样也方便测试，利于功能复用。
DI即依赖注入，组件之间的依赖关系由容器在运行期决定。通过DI，我们需要通过简单的配置，无需任何代码就可以指定目标需要的资源，只需要完成自身的业务逻辑而无需关注具体的资源来源或由谁实现。
**DI理解**
谁依赖于谁：应用程序依赖于IoC容器
为什么需要依赖：应用程序需要IoC容器来提供对象需要的外部资源。
谁注入谁：IoC容器注入应用程序某个对象，应用程序依赖的对象
注入了什么：就是注入某个对象所需要的外部资源
DI是IOC的另外一种说法
### spring AOP的原理
基于动态代理或者CGLib
对于每个CutPoint给出Advice
JdkProxyAopProxy和CgLibProxy生成代理对象，并且自身实现了InvocationHandler说明我们使用代理时对代理对象调用的方法最终都会转到这个类的invoke方法
匹配器MethodMatcher匹配所有需要加Advice的方法，拦截器Interceptor在适当的时候拦截方法进行Advice的执行
### hibernate中的1级和2级缓存的使用方式以及区别原理（Lazy-Load的理解）
1级缓冲：
	Session的缓存，当一个Session做了查询操作，他会把操作的结果先存起来，下次做同样操作的时候直接拿出来用而不是去数据库查
2级缓存：
	SessionFactory级别的缓存，就是查询的时候会把查询结果缓存到二级缓存中，如果同一个sessionFactory的某个session执行了同样的操作，就会去SessionFactory的二级缓存里拿而非去数据库查
一级缓存是事务范围内的，另一个是进程范围内的
一般只需要管理第二级缓存
当应用程序调用Session的save、update、saveOrUpdate、get、load以及调用查询接口的list、iterate、filter方法时候，如果在session缓存的中还不存在响相应的对象，Hibernate就会把该对象加入到第一级缓存中。当清理缓存时，Hibernate会根据缓存中的对象的状态变化来同步更新数据库
**启用二级缓存**
hibernate.cfg.xml
<property name="hibernate.cache.use_second_level_cache">  true  </property>
**lazy-load**
使用的是一级缓存，Hibernate通过Cglib代理完成延迟加载的功能的扩展
### Hibernate的原理体系架构，五大核心接口，Hibernate对象的三种状态转换，事务管理
https://blog.csdn.net/martinmateng/article/details/50879436
**五大核心接口**
Session、SessionFactory、Configuration、Transaction、Query和Criteria
SessionFactory负责初始化Hibernate，充当数据存储源的代理并负责创建Session对象
Session负责被持久化对象的CRUD操作，注意Session是非线程安全的
Configuration接口负责配置并启动Hibernate，创建SessionFactory对象。
Transaction接口负责事务相关的操作
Query和Criteria接口：负责执行各种数据库查询，使用HQL或SQL或Criteria
**三种状态转换**
**事务管理**
