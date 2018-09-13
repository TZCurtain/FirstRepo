## 左值、右值
  **左值、右值概念**
  左值是 拥有一个确定的地址(指向对象)的标示符或表达式
  否则就是右值
  换句话说，左值指代了一块具体的内存可以用&操作符取地址而右值不行。
  右值可以出现在赋值表达式的左边但是不能作为赋值的对象。  
  **左值**
  左值引用：常规引用、变量是左值
  **右值引用**
  右值引用：必须绑定到右值的引用，通过&&而非&获得
  e.g int&& i = 1; 
  右值引用只能绑定到将要销毁的对象即临时对象上，所以不能直接绑定到变量上。
  临时对象一般作为右值处理，但是如果作为参数传入一个接受右值的函数，然后再传递给另一个函数时就会变成左值
  **转移语义**
  这是右值引用最主要的用途。
  转移语义即将资源从一个对象转移到另一个对象，可以减少不必要的临时对象的创建和销毁，节省开支。转移和拷贝类似Win的剪切和拷贝。速度剪切快很多
  比如String a = new String("world"); 此处world是临时对象，但是如果没有转移语义，C++还是会调用拷贝构造函数去构建a
  **转移构造函数和赋值函数的定义**
``` C++
  String(String&& str){
     _len = str._len; _data = str._data;
     str._len = 0; str._data = NULL; // 注意右值的资源链接和标记修改，否则右值的析构函数释放资源后，转移到新对象的资源也无效了
  }
  String& operator=(String&& str){
      if (this != &str) { 
     	_len = str._len; 
     	_data = str._data; 
     	str._len = 0; 
     	str._data = NULL; 
   	  } 
   	  return *this; 
  }
```
  **std::move**
  	如果已知有命名对象不再使用而想对它调用构造函数和转移赋值函数，该怎么办呢？
  	使用std::move将左值引用转换为右值引用
  	e.g 使用move优化swap函数
  	template <class T>
  	swap(T &a,T& b){
        T tmp(std::move(a));
        a = std::move(b);
        b = std::move(tmp);
  	}
  	避免了3次不必要的拷贝函数
  **通用引用 & 精确传递 Perfect Forwarding**
  通用引用：
  	满足T&&形式
  	类型T需通过推断得到(auto、函数模板而非类模板里的函数、typedef、decltype)
  对于通用引用。传进来的时候，右值引用为右值引用，左值实参仍然为左值引用，即不改变参数的属性。
 精确传递：
 	即传参数的时候不改变左值/右值,const/非const
  使用std::forward(args)传参，该函数会保留参数的所有属性
  这样就不用再加上const T&和T&的分开定义了
  **i++与++i与左值**
  i++一定是一个非左值
  而++i在C中被认定为非左值，而在C++中被认定为左值，所以++(++a)C++中不报错而C报错
## 引用的实质
  从汇编层次来看，引用其实是封装了一个指针，指针指向被引用的对象，每次使用这个引用就是解引用这个被封装的指针。之所以我们使用的时候不需要解引用是因为编译器的优化。
  而右值引用的话，底层是将原来的对象进行了一份内存拷贝，然后封装了对这个拷贝的指针。因为是拷贝，所以实际上右值引用其实也是左值。STL里面有一个forkward函数，它的作用就是用来进行右值引用的类型恢复...
## C的内存布局
静态区域(全局区域)
静态变量和全局变量的存储在一块区域中
文本段(Text)
	代码段(Code)
	由程序中执行的机器代码组成。
只读数据段(RO data，即常量区)
	存储初始化好的常量和字符串常量本身(非指针)
	只读数据段是程序使用的一些不会被更改的数据。
	因为这些不需要更改，所以放在只读存储器中
数据段(RW data -- Initialized Data Segment)
	存储初始化的全局变量和静态变量
	已初始化数据是在程序中声明，并且具有初值的变量
	这些变量需要位于可读写的内存区域内，并且具有初值。
未初始化数据段(BSS -- Uninitilized Data Segment)
	存储未初始化(此处指显式初始化)的全局变量，全局常量(自动初始化为0，但仍放在BSS)，局部静态变量
	未初始化数据是在程序中声明，但没有初始化的变量。
	这些变量在程序运行之前不需要占用存储器的空间。
动态区域
堆heap
	只在程序运行时出现，一般由程序员分配和释放。
	在具有操作系统的情况下，如果程序没有释放，会在进程结束后释放
栈stack
	局部常量存储在栈中
	只在程序运行时出现，由编译器自动分配和释放
	函数内部的变量、参数及返回值使用栈空间。
栈中的变量内存地址从高到低。
而堆中的变量内存地址从低到高。
## 归并排序 时间复杂度分析
  总共要分成logn层，每层都总共排序n个元素，所以复杂度是O(nlogn)
## ELF是什么？其大小与程序中全局变量的是否初始化有什么关系（注意.bss段）
ELF是Linux系统上的一种可执行可链接文件的格式。
ELF分为代码段、数据段、bss段
其中的bss段中未链接前只是一个占位符，没有里面的变量
## 动态链接和静态链接的区别
静态链接：在程序执行前完成所有的组装工作
动态链接：在程序已经执行后完成链接单元，而且一般在内存中只保留该编译单元的一份拷贝
DLL的优点：节省内存，dll文件与exe文件独立，只要输出接口不变，更换dll不会对exe造成影响。
## 32位系统一个进程最多多少堆内存
2^32 b=2^22 kb = 2^12 mb = 2^2 G =  4G，当然实际空间受硬件(地址总线)制约
## 多线程之间共享和私有资源
共享：地址空间、全局变量、子进程、闹铃
不共享：程序计数器、寄存器、栈、状态字
## 写一个c程序辨别系统是64位 or 32位
sizeof(char * ); 8就是64位，4就是32位。
## 写一个c程序辨别系统是大端or小端字节序
union{
    short s;
   	char c[2];
} u;
u.s = 0x0102;
如果u.c[0] == 1 && [1] == 2则大端 低位放在高位 
u.c[0] = 2 c[1] = 1则小端 低位放在内存地址低位
大端看起来就像字符串的存储一样:
0x12345678
0x12| 0x34 | 0x56 | 0x78
低地址->高地址
## i++是否原子操作？并解释为什么？
不是，i++三步操作，内存到寄存器，寄存器自增，写回内存。
## 列举说明linux系统的各类异步机制
就知道个信号是处理异步的
## 标准库函数和系统调用的区别？
标准库函数时语言本身的一部分，系统调用是内核提供给应用程序的一部分。
库函数调用一段程序，而系统调用调用系统内核的服务
库函数在用户地址空间执行，系统地址空间在内核地址空间执行。
库函数属于过程调用，开销小，而系统调用需要在用户态和内核态切换，开销大。
## 宏定义和展开（必须精通）
函数宏定义如果有多句语句一般会
``` C
#define doit(a,b) \ 
	do { \
        sentence a; \
        sentence b; \
	} while (0)
```
这样使得宏展开后正常
另外宏允许重复定义，但是要求定义必须一模一样，否则需要#undef取消原来的定义

#运算符，用于创建字符串
‘#’后面跟一个形参中间可以有空格或tab
‘##’ 用于连接两个预处理Token
gcc -e xxx.c 或者cpp xxx.c 可以查看展开宏
## sizeof必考
sizeof(基础类型) 返回类型大小，如char1int4long8等
sizeof(指针)，指针是用于存储地址的，所以等于计算机地址总线的长度
sizeof(数组名)，数组占用的内存
sizeof(类) 
	空类/只有构造和析构函数的时候为1
	有虚函数的时候虚函数指针会提供4/8，虚继承同理
	有成员变量时类似结构体内存对齐，注意静态数据成员不算
sizeof(A) sizeof(B) 如果B继承于A，二者大小关系如何
## 各类库函数必须非常熟练的实现 memcpy、strcpy
``` C++
void* memcpy(void* pvTo,const void* pvFrom,size_t size){
    if (pvTo == NULL || pvFrom == NULL) return NULL;
	byte* pbTo = (byte*)pvTo;
    by
}
```
## 无符号数和符号数相加都会转成符号数
## 一个String类的完整实现必须很快速写出来（注意：赋值构造，operator=是关键）
``` c++
class String{
public:
	String(const char* str = NULL);
	String(const String& str);
	~String();
	
	String opeartor+(const String& str) const;
	String& opeartor=(const String& str){
        if (this == &str) return *this; // 如果一样就不复制
        delete []data; // 释放原来的
        length = str.length;
        data = new char[length+1];
        strcpy(data,str.c_str());
        return this;
	}
	String& operator+=(const String& str);
	bool operator==(const String& str) const;
	char& opearator[](int n)const;
	friend istream& operator>>(istream& is,String& str);
	friend ostream& operator<<(ostream& is,String& str);
	const char* c_str() const{
        return this->data;
	}
private:
	char* data;
	size_t length;
};
```
## stl个各容器的实现原理（必考）
**Vector**
底部是数组，初始分配大小为10
当已经分配的空间不够的时候，会分配一个双倍于(如果加上新增元素大于双倍容量，则是该值)当前容量的存储区，将当前的值拷贝过去并释放原来的内存区域
注意vec.clear()并不能真正的释放内存，实际内存在vector析构之后释放
如果要即时释放
`vector<int>().swap(ivec); or ivec.swap(vector<int>())；
修整空间
比如reserve了10000，但实际只用了100.
vector<int> ivec;
ivec.swap(vector<int>(ivec)); //使用当前ivec构建新的vector。这样的vector并没有预留空间，以前的空间也得以释放
vector插入平均性能O(n)
**list**
对于任何位置的元素安插或元素移除，list永远是常数时间
本质是一个双向链表
以结点为单位存放数据，结点的地址在内存中不一定连续，每次插入或删除一个元素，就配置或释放一个元素空间
**Deque**
双向开口的连续线性空间
相比vector，deque的最大区别是：
	1.允许在常数时间内操作头部元素
	2.deque没有所谓容量概念，它由多个连续的空间组合而成，所以随时可以增加一段新的空间并链接起来
deque虽然提供随机访问迭代器，但是迭代器访问复杂度较高。
对deque进行排序时，为了高效率，可以讲deque复制到一个vector里去，然后排序，然后再复制回deque
实现机制：
	deque采用所谓map(注意不是map容器)作为主控。该map是一小块连续空间，其中每个元素都是指标，指向较大的连续线性空间，称为缓冲区。该缓冲区才是deque的存储空间。
deque的迭代器包含四个内容，cur:当前所指元素，first:此迭代器所指的缓冲区的头，last:缓冲区尾，node：指向管控中心
**Map、Set**
底层均为红黑树
为什么map和set的插入效率比其他序列容器高
因为不需要内存拷贝和移动
为什么map和set插入之后，之前保存的迭代器不会失效。
因为节点内存没有改变。
set和map中的元素/键值不能被改变，只能删除后再添加。
为什么set不使用hash实现
set有大量数据操作、比较的部分，这部分相对来说比较适合红黑树
**hashset、hashmap**
底层均为hashtable
查找速度快，属于常数级别。
**hashtable**
处理冲突用的是链表
表格内的每个单元都不只是一个节点(node)，可能是一桶节点(bucket list)
hashtable内部维护一个bucket互相连接的vector，而bucket自己维护了类似linklist的node链表
hashtable的迭代器没有后退操作或所谓逆向迭代器
hashtable<Value、Key、HashFunc、ExtractKey、EqualKey、Alloc>
HashFunc计算元素的位置，hashtable调用bkt_num()来调用这个函数取得一个值
**为什么一般hashtable的桶数会取一个素数？如何有效避免hash结果值的碰撞**
为了减少冲突。
取合数的时候，设有一个哈希函数是H(c)=c%N;如果N取一个函数，最简单的例子是取2^n，无论c的第4位取什么值都不会影响H(c)的值，这样无法充分反应c的特性，冲突可能加大了
改进Hash算法，尺寸达到一定程度的时候rehash
## STL线程安全
STL保证：
1 多个读取者是安全的，多个线程可以读取同容器中的内容
2 对不同容器的多个写入者是安全的。即多个线程对不同的容器同时写入是合法的
但是在同一个容器中有线程写、有线程读时需要程序员自己来控制线程安全
## extern c 是干啥的，（必须将编译器的函数名修饰的机制解答的很透彻）
因为C++支持函数重载，所以编译器编译函数的时候会将函数的参数类型也加到编译后的代码中，而C不支持，所以编译C语言的函数时并不会带上函数的参数类型，一般包括函数名
extern "C"能让C++正确的调用其他C语言代码
**__cplusplus**
是C++定义的一个宏，常用于头文件中判断当前编译语言是C还是C++
e.g
``` C
#ifdef __cplusplus
extern "C"{
    
}
#endif
```
## volatile是干啥用的，（必须将cpu的寄存器缓存机制回答的很透彻）
C对变量有寄存器缓存，方便读取，但是
而被声明为volatile的变量，编译器将会直接去读取。
主要用于
	一些状态寄存器
	中断服务程序中修改的供其他程序检测的变量
## 快排
**思路**
划分，每做一次划分
**非递归实现**
用栈保存每一个待排序的首尾元素下标,下一次while循环时取出这个范围，对这段子序列进行partition操作
``` C++
void quicksort2(vector<Comparable> &vec,int low,int high){
    stack<int> st;
    if(low<high){
        int mid=partition(vec,low,high);
        if(low<mid-1){
            st.push(low);
            st.push(mid-1);
        }
        if(mid+1<high){
            st.push(mid+1);
            st.push(high);
        }
        while(!st.empty()){
            int q=st.top();
            st.pop();
            int p=st.top();
            st.pop();
            mid=partition(vec,p,q);
            if(p<mid-1){
                st.push(p);
                st.push(mid-1);
            }
            if(mid+1<q){
                st.push(mid+1);
                st.push(q);
            }       
        }
    }
}
```
**最坏情况分析、如何避免**
每次划分只得到比上一次划分少一个记录的子序列。
**稳定性**
不稳定
## STL 内存管理
	分为两阶段： 
		内存配置alloc::allocate() + 对象构造::construct()
		内存释放alloc::deallocate() + 对象析构::destroy()
	<stl_construct.h> 定义有全域函数 construt()和destroy()
	<stl_alloc.h> 定义有一、二级配置器，名为alloc
	<stl_unintialized.h> 用于填充或复制大块内存内容
**construct & destroy**
construct使用placement new分配内存
destroy：
根据参数不同有多重情况
``` C++
destroy(T * pointer) {
	pointer->~T();
}
destroy(ForwardIterator first,last){
	__destroy(first,last,value_type(first));
}
// 如果元素有non-trivial destructor
__destroy (ForwardIterator first, ForwardIterator last, T * )
{
typedef typename __type_traits<T>::has_trivial_destructor trivial_destructor;
__destroy_aux(first, last, trivial_destructor());
}
__destroy_aux(ForwardIterator,ForwardIterator,__false_type) {
	for (;first < last; ++first) destroy(&first);
}
```
**两级配置器**
	SGI设计了两层级配置器，第一次配置器直接使用malloc和free，第二级配置器则视情况采用不同的策略（又称为次层配置）：
	如果配置区块超过128Bytes则呼叫第一级配置器
	如果小于128Bytes则采用内存池整理方式。
	第一级配置器
		allocate和realloc首先尝试使用malloc分配，如果分配不成功，就改呼叫oom_malloc和oom_realloc。后两者都有内循环，不断调用内存不足处理例程，但如果没有这种例程，则调用__THROW_BAD_ALLOC，抛出bad_alloc异常讯息或利用exit(1)终止程序
	第二级配置器
		第二级配置器维护16个free_lists,各自管理大小分别为8,16,24,32,40，48,56,64,72,80,88，96,104。。
		free-list节点
```C++		
		union _Obj {
			union _Obj* _M_free_list_link;   //指向下一块空白内存
			char _M_client_data[1];    /* The client sees this. 用户使用的*/
		};
```
		考虑到内存对齐的因素，对于所有小额区块的内存请求，二级配置器会将其上调至8的倍数。二级配置器在配置区块超过128时调用第一级配置器，否则在16个freelist中适当选择。如果没有freelist就重新填充(refill)freelist。最后调整freelist
		同理回收区块时，大于128就调用第一级配置器，否则寻找对应的freelist调整回收区块。
	Refill
		调用chunk_alloc从memory pool中取空间给新的freelist使用，然后导引freelist指向新配置的空间并串联各节点
## bitmap的运用
## socket服务端的实现
## udp调用connect有什么作用？
1. 可以绑定新的ip和端口或断掉原来的ip和端口（将第二参数的sin_Family设置成AF_UNSPEC）
2. 使用connect的udp效率较高。
	普通的udp发送两个报文内核做了两次建立连接和断开连接
	而采用connect方式的udp做了建立连接、发送报文、发送报文即成功
## 大规模连接上来，并发模型怎么设计
负载均衡+增加机群+
tcp头多少字节？哪些字段?(必问)


## 红黑树有了解吗？说一下你对红黑树的理解。
根节点为黑色。
每个节点不是红色就是黑色，如果节点为红色，则子节点必须为黑
如果节点为红，其子节点必须为黑。
任意节点到NULL的任何路径，所含之黑节点数必须相等。

设插入的节点为X，父节点为P，父节点的兄弟节点为S，祖父节点为G，
**插入情况**
1. S为黑(null也视为黑色)且X为外侧插入，则对P、G做一次单旋转，改变P、G颜色
2. S为黑且X为内侧插入，则对P、X做一次单旋转并改变G、X颜色，再对G做一次单旋转
3. S为红且X为外侧插入，对P和G做一次单旋转，并改变X的颜色，如果GG也是红，继续往上做直到没有连续的红
插入实现
从根节点开始寻找适当的位置，插入。
然后调整RB-Tree，使红黑树满足上面的要求
``` C++
inline void __rb_tree_rebalance(__rb_tree_node_base* x,__rb_tree_node_base*& root){ // x为插入节点，root为根
	x->color = __rb_tree_red; // 新节点必为红
	while (x != root && x->parent->color == __rb_tree_red){ 
    	if (x->parent == x->parent->parent->left){ 
            __rb_tree_node_base* y = x->parent->parent->right;
            if (y && y->color == __rb_tree_red){
                x->parent->color = __rb_tree_black;
                y->color = __rb_tree_black;
                x->parent->parent->color = __rb_tree_red;
                x = x->parent->parent;
            }
            else {
                if (x == x->parent->right){
                    x = x->parent;
                    __rb_tree_rotate_left(x,root); // 将新插入的节点左旋上去
                }
                x->parent->color = __rb_tree_black;
                x->parent->parent->color = __rb_tree_red;
                __rb_tree_rotate_right(x->parent->parent,root); // 右旋G即可满足红黑树
            }
    	} 
    	else {
        	__rb_tree_node_base* y = x->parent->parent->left;
        	if (y && y->color == __rb_tree_red){
                x->parent->color = __rb_tree_black;
                y->color = __rb_tree_black;
                x->parent->parent->color = __rb_tree_red;
                x = x->parent->parent;
            }
            else {
                if (x == x->parent->left){
                    x = x->parent;
                    __rb_tree_rotate_right(x,root);
                }
                x->parent->color = __rb_tree_black;
                x->parent->parent->color = __rb_tree_red;
                __rb_tree_rotate_left(x->parent->parent,root);
            }
    	}
	}
	root->color = __rb_tree_black; // 根节点必为黑
}

inline void
__rb_tree_rotate_left(__rb_tree_node_base* x,__rb_tree_node_base*& root){
    __rb_tree_node_base* y = x->right;
    x->right = y->left;
    if (y->left != 0){
        y->left->parent = x;
    }
    y->parent = x->parent;
    if (x == root) root = y;
    else if (x == x->parent->left) x->parent->left = y;
    else x->parent->right = y;
    y->left = x;
    x->parent = y;
}

inline void
__rb_tree_rotate_right(__rb_tree_node_base*x,__rb_tree_node_base*& root){
    __rb_tree_node_base* y = x->left;
    x->left = y->right;
    if (y->right != 0) y->right->parent = x;
    y->parent = x->parent;
    
    if (x == root) root = y;
    else if (x == x->parent->left) x->parent->left = y;
    else x->parent->right = y;
    y->right = x;
    x->parent = y;
}

```
**红黑树与avl树的区别**
红黑树插入效率更高
而avl树查找效率更高
## 一个每秒百万级访问量的互联网服务器，每个访问都有数据计算和I/O操作，如果让你设计，你怎么设计？
## 介绍C++11特性
auto、nullptr、for each、
## push_back会导致迭代器失效吗。
	会，vector插入元素后可能发生扩容移动元素
## 下面是其中一道题目，请写出运行结果
``` C++
#include <iostream>
using namespace std;
struct A{
    A(){
        local_var++;
    }
    virtual void func(){
        cout << "A" << endl;
    }
    static int local_var;
};
int A::local_var = 0;
struct B : A{
    B(){
        local_var+=2;
    }
    virtual void func(){
        cout << "B" << endl;
    }
};
int main(){
    A* a1 = new B[3];
    cout << a1[0].local_var << endl;
    a1->func();
    A a2 = a1[0];
    cout << a2.local_var << endl;
    a2.func();
    A a3;
    a3 = a1[0]; 
    cout << a3.local_var << endl;
    a3.func();
}
// 输出9 B 9 A 10 A
// 类的静态变量继承后，B中改变也会对A造成影响
// a3 = a1[0]触发了拷贝赋值函数导致local_var++
```
以下代码会出现什么问题。
``` C++
#include <iostream>
using namespace std;
struct A{
    virtual void func1(){
        cout << a << endl;
    }
    void func2(){
        cout << a << endl;
    }
    void func3(){
        cout << "a" << endl;
    }
    int a;
};
int main(){
    A * a = (A*)malloc(sizeof(A)); // 未使用new，malloc并不会初始化
    a->func1();
    a->func2();
    a->func3();
}
```
你都使用什么线程模型
介绍协程
## 构造函数为什么不能是虚函数
	构造函数被调用的时候首先要做的事是初始化VPTR。

打印在纸上的题目，考察：new [] 对象。static 成员。子类构造函数具体调用了啥。拷贝构造函数与赋值函数的区别。等号在拷贝构造函数出现的时机。什么时候需要赋值函数。深拷贝与浅拷贝。虚函数的调用时机。

考察虚指针的初始化时机。
随机出一道纸上的题目。给定前序遍历ABC后序遍历CBA，求中序遍历是什么，画出来两种情况。

## 系统调用是什么。你用过哪些系统调用。什么系统调用会耗时长。
## 函数指针和指针函数的区别。写个例子出来。
## 手写fork调用示例。你在哪些场合使用过fork调用。
## 索引是什么。多加索引一定会好吗？
索引是为了加速对表中数据的检索而创建一种分散的存储结构，索引是针对表而建立的。
太多的索引会影响更新数据的速度，应该在条件选择的语句上合理的放置索引。

  2.说一下c/c++源文件如何从代码变成可执行程序的（程序的编译链接）


    12. Linux下常用的命令有哪些

  ## 查看一个进程打开的文件怎么查看

  14.如何查看指定进程打开的端口号


  22.半同步半异步模型说一下。（因为我简历上写了）

  23.如果一个客户端请求异常是如何处理的？

  24.然后他又说这个客户端如果请求连接之后再无响应怎么办？

## 那从内存读数据一定比磁盘快吗？

  29.补充之前，编译链接一个库的时候，俩个库中都有同一个.o文件，如何决定取哪个

  4.什么是配置的热加载？


  2.为什么负载均衡用多线程？

  3.进程和线程的通信和互斥和同步控制。

  4.互斥锁和读写锁说一下吧？哪个效率高呢？（不是很确定，之前没有认真查过，答的不是很好）
  5.互斥锁的时间数量级是什么？毫秒级?微秒级？还是纳秒级？（还好之前看过）
## ptmalloc和tcmalloc的区别
**ptmalloc**
	Ptmalloc2有一个主分配区(main arena)， 有多个非主分配区。 非主分配区只能使用mmap向操作系统批发申请HEAP_MAX_SIZE（64位系统为64MB）大小的虚拟内存。 当某个线程调用malloc的时候，会先查看线程私有变量中是否已经存在一个分配区，如果存在则尝试加锁，如果加锁失败则遍历arena链表试图获取一个没加锁的arena， 如果依然获取不到则创建一个新的非主分配区。
	用户请求分配的内存在ptmalloc中使用chunk表示，每个chunk至少使用8个字节额外的开销
	用户free掉的内存不会马上归还操作系统，ptmalloc统一管理heap和mmap区的空闲chunk
ptmalloc将相似大小的chunk用双向链表连接起来，这样的一个链表被称为一个bin。ptmalloc共维护128个bin，使用一个数组来存储这些 bin。
	当free了一个chunk并放入bin的时候，ptmalloc还会检查它前后的chunk是否也是空闲的。如果是的话，会将他们合并成一个大的chunk，放入unstored bin里。另外ptmalloc为了提高分配的速度，会把一些小的chunk先放到一个叫做fast bins的容器里。
	在fast bins和bins都不能满足需求后，ptmalloc会设法在一个叫做top chunk的空间分配内存。 对于非主分配区会预先通过mmap分配一大块内存作为top chunk， 当bins和fast bins都不能满足分配需要的时候, ptmalloc会设法在top chunk中分出一块内存给用户, 如果top chunk本身不够大, 分配程序会重新mmap分配一块内存chunk, 并将 top chunk 迁移到新的chunk上，并用单链表链接起来。如果free()的chunk恰好 与 top chunk 相邻,那么这两个 chunk 就会合并成新的 top chunk，如果top chunk大小大于某个阈值才还给操作系统。主分配区类似，不过通过sbrk()分配和调整top chunk的大小，只有heap顶部连续内存空闲超过阈值的时候才能回收内存。
缺点是
	后分配的内存先释放,因为 ptmalloc 收缩内存是从 top chunk 开始,如果与 top chunk 相邻的 chunk 不能释放, top chunk 以下的 chunk 都无法释放。
	多线程锁开销大， 需要避免多线程频繁分配释放。
	内存从thread的areana中分配， 内存不能从一个arena移动到另一个arena， 就是说如果多线程使用内存不均衡，容易导致内存的浪费。 比如说线程1使用了300M内存，完成任务后glibc没有释放给操作系统，线程2开始创建了一个新的arena， 但是线程1的300M却不能用了。
	每个chunk至少8字节的开销很大
	不定期分配长生命周期的内存容易造成内存碎片，不利于回收。64位系统最好分配32M以上内存，这是使用mmap的阈值。
**tcmalloc**
Google开源的
tcmalloc为每个线程分配了一个线程本地ThreadCache，小内存从ThreadCache分配，此外还有个中央堆（CentralCache），ThreadCache不够用的时候，会从CentralCache中获取空间放到ThreadCache中。
小对象（<=32K）从ThreadCache分配，大对象从CentralCache分配。大对象分配的空间都是4k页面对齐的，多个pages也能切割成多个小对象划分到ThreadCache中。
他的改进
	ThreadCache会阶段性的回收内存到CentralCache里。 解决了ptmalloc2中arena之间不能迁移的问题。
	Tcmalloc占用更少的额外空间。例如，分配N个8字节对象可能要使用大约8N * 1.01字节的空间。即，多用百分之一的空间。Ptmalloc2使用最少8字节描述一个chunk。
	更快。小对象几乎无锁， >32KB的对象从CentralCache中分配使用自旋锁。 并且>32KB对象都是页面对齐分配，多线程的时候应尽量避免频繁分配，否则也会造成自旋锁的竞争和页面对齐造成的浪费。
  8.大内存申请时候选用哪种？

  10.变量的大小存在哪？

  11.符号表存在哪？
    1. 说一下什么是负载均衡

    2. 健康性检查是如何实现的

    5. ptmalloc的实现

    6. Linux下进程间通信

    9. 线程和进程区别，从linux内核角度说一下（do_fork）

    10. 什么是虚拟地址空间？
    13. 页缓存

    1. 讲一下负载均衡的设计

    2. 什么是一致性哈希

    3. 容灾如何处理

    4. 如何加权控制后台服务器负载的平衡性的？
    6. Send返回值为0的情况
    7. tcp的缓冲区
    4. http请求报文和应答报文的格式（三块）

  ## 说一下一致性哈希是如何实现的（单调性、平衡性）
  	计算memcached服务器节点的哈希值，并将其一一配置到0~2^32-1的圆上
  	然后用同样算法算出存储数据的键的哈希值，并映射到相同的圆上。
      然后从那个位置开始顺时针查找，找到的第一个服务器的位置就是数据保存的节点位置。如果超过2^32仍未找到，就会保存到第一台memcached服务器上。
**服务器挂/增加了**
只有此服务器到环空间的前面一台服务器之间的数据会受到影响
**性质**
平衡性：哈希的结果尽可能的分布到所有的缓冲中去
单调性：当有新的缓冲区加入到系统，应保证原有已分配的内容可以被映射到新的缓冲区中去

## IO模型有哪几种？

  10.异步IO你知道有什么应用的场景吗？

  11.还有异步IO有什么缺点呢？

  12.你对多线程和多进程应用场景说一下（说了内核对线程和进程的实现，最后分析了IO密集型和CPU密集型）
  15.平时是如何学习专业知识的？如何了解内核的？
    2. IO复用源码。
  3.问了我内核源码部分，内核内存管理的伙伴系统和SLAB机制。各自的优缺点。
TCP协议三次握手过程，传输报文中处理请求连接信息还有哪些信息？
如果让你设计微信视频，你会选择什么协议？如何实现
recv函数的返回值及其意义
## 不用临时变量实现strlen
只能递归了呀
```C++
int mystrlen(const char* str){
    if (str == NULL || *str == '\0') return 0;
    return 1 + mystrlen(++str);
}
```
## cdn
content distribute network，内容分发网络
是一个经策略性部署的整体系统，包括分布式存储、负载均衡、网络请求的重定向和内容管理4个要件。
**特点**
本地Cache加速 提高了企业站点（尤其含有大量图片和静态页面站点）的访问速度和稳定性
镜像服务 消除了运营商之间互联的瓶颈造成的影响，实现了跨运营商的网络加速。
远程加速 远程访问用户根据DNS负载均衡技术自动选择Cache服务器，选择最快的Cache服务器，加快远程访问的速度。
带宽优化 自动生成服务器的远程Mirror Cache服务器，远程用户访问时从Cache服务器读取数据，减少远程访问的带宽、分担网络流量等。
## 如果一个cpu有八个核，多进程比较好，还是多线程比较好，我回答了根据线程实现方式不一样有所区别，内核级线程适合用多线程，用户级线程适合用多进程
6、求一个数组的最长连续子序列

## B+树，跟B树有什么区别？
## cout和printf有什么区别？
	cout重载了<<运算符，所以不需要进行参数类型的指定
5、为什么模板类一般都是放在一个h文件中？
6、有两个链表，怎么求交点？
7、一副扑克牌，怎么实现随机打乱？



