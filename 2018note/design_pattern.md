# 设计模式
## 简单工厂模式
建立一个工厂类，对实现了同一个接口的类进行实例的创建
``` java
public interface sender {...}
public class XXXsender implements sender { ... }
public class YYYsender implements sender { ... }
public class SenderFactory {
    public sender produce(String type){
        if ("xxx".equals(type)){
            return new XXXsender();
        }
        else if ("yyy".equals(type)){
            return new YYYsender();
        } else {
          	System.out.println("please input right type!");
          	return null;
        }
    }
}
```
## 工厂方法模式
简单工厂模式的类的创建依赖工厂类，也就是要如果想要扩展程序需要对工厂类进行修改，这样破坏了闭包性。而工厂方法模式创建了一个工厂接口和多个实现类，如果需要增加的新功能，直接增加新的工厂类就可以了
``` java 
/// sender定义同上
public interface Provider { public Sender produce();}
public class XXXsenderFactory implements Provider {public Sender produce() {return new XXXsender();} }
public class YYYsenderFactory implements Provider {public Sender produce() {return new YYYsender();} }
```
## 抽象工厂模式
抽象工厂模式类似工厂模式，在产品只有一个就是工厂模式。
``` java
public interface PcFactory{ Mouse createMouse(); Keybo createKybo();}
public interface HpFactory implements PcFactory {...}
```
新增产品的时候需要修改所有的接口添加产品
## 单例模式
``` java
public class XX {
    private static XX instance = null;
    private XX(){ }
    public static synchronized void syncInit(){
        if (instance == null){
            instance = new Singleton();
        }
    }
    public static SingleTest getInstance() {
        if (instance == null){
            syncInit();
        }
        return instance;
    }
}
```
## 建造者模式
将各种产品集中起来就进行管理，用来创建复合对象。
通常包含下面几个角色：
``` java
	// product: 要创建的复杂对象
	public class XXX {
    	private xx a;
    	private yy b;
    	... getter and setter
	}
	// Builder: 给出一个抽象接口，以规范产品对象的各个组成成分的建造
	public interface XXBuilder {
        void builda(); void buildb(); 
        XXX buildXX();
	}
	// ConcreteBuilder 具体的实现接口
	public XXBuilderImpl implements XXBuilder{
	...}
	// Director调用具体创建者来创建复杂对象的各个部分，指导者中不包括具体的产品信息，只负责流程
	public class XXXDirector{
        public XXX constructXXX(XXXBuilder xb){
            xb.builda(); xb.buildb();
            return xb.buildXX();
        }
	}
```
## 原型模式
将一个对象作为原型对其进行复制、克隆，产生类似的新对象
``` java
public class Prototype implements Cloneable,Serializable{
	private static final long serialVersionUDI = 1L;
	private String string;
	private SerializableObject obj;
	public Object clone() throws CloneNotSupportedException {
        Prototype proto = (Prototype) super.clone();
        return proto;
	}
	public Object deepClone() throws IOException, ClassNotFoundException{
    	ByteArrayOutputStream bos = new ByteArrayOutputStream();
    	ObjectOutputStream oos = new ObjectOutputSream(bos);
    	oos.writeObject(this);
    	ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
    	ObjectInputStream ois = new ObjectInputStream(bis);
    	return ois.readObject();
	}    
}
```
## 适配器模式
类的适配器
Source类拥有偶一个方法，待适配，目标接口是Targetable，通过适配器将Source的功能扩展
``` java
public class Source {
    public void method1() {
        ...
    }
}
public interface Targetable {
    public void method1();
    public void method2();
}
public class Adapter extends Source implements Targetable{
    @Override
    public void method2(){
    	public interface
    }
}
```
对象的适配器模式
	Adapter改为持有Source的实例，然后Override method1并调用其方法。
接口的适配器模式
	接口中有太多的方法，而我们只需要其中的几个，实现一个类空实现所有的方法，然后继承他就可以了
## 装饰模式
给对象动态增加一些新的功能。扩展一个类的功能。
``` java
public interface Sourceable {  
    public void method();  
}  
public class Source implements Sourceable {  
    @Override  
    public void method() {  
        System.out.println("the original method!");  
    }  
}
public class Decorator implements Sourceable {  
  
    private Sourceable source;  
      
    public Decorator(Sourceable source){  
        super();  
        this.source = source;  
    }  
    @Override  
    public void method() {  
        System.out.println("before decorator!");  
        source.method();  
        System.out.println("after decorator!");  
    }  
}  
Sourceable obj = new Decorator(source); 
```
## 代理模式Proxy
给一个对象提供一个代理对象，并由代理对象控制原对象的引用。
## 外观模式Facade
用于解决类与类之间的依赖关系，像Spring一样，可以将类和类之间的关系配置到配置文件中。
而外观模式就是将他们的关系放在一个Facade类中，降低了类类之间的耦合度。
如现在有CPU、Memory、Disk三个类，如果没有一个总的Computer类来保持他们。则类与类之间会持有大量实例产生关系，造成严重的依赖。
## 桥接模式Bridge
把事务和具体实现分开，将抽象化与实现化解耦，使得两者可以独立变化。
如JDBC的DriverManager，JDBC提供统一接口，每个数据库提供各自的驱动实现即可
## 组合模式
组合模式类似树结构，包含以下角色：
Component抽象构件: 
	它可以是接口或抽象类
Leaf叶子构件:
	一般为空节点，不应该被访问到，需要提供异常或错误提示
Composite:
	它在组合结构中表示容器节点对象
组合模式的关键是定义了一个抽象构件类，它既可以代表叶子，又可以代表容器，而客户端针对该抽象构件类进行编程，无须知道它到底表示的是叶子还是容器，可以对其进行统一处理。同时容器对象与抽象构件类之间还建立一个聚合关联关系，在容器对象中既可以包含叶子，也可以包含容器，以此实现递归组合，形成一个树形结构
e.g
``` java
abstract class Component {  
    public abstract void add(Component c); //增加成员  
    public abstract void remove(Component c); //删除成员  
    public abstract Component getChild(int i); //获取成员  
    public abstract void operation();  //业务方法  
}  
class Leaf extends Component {  
    public void add(Component c) {   
        //异常处理或错误提示   
    }     
          
    public void remove(Component c) {   
        //异常处理或错误提示   
    }  
      
    public Component getChild(int i) {   
        //异常处理或错误提示  
        return null;   
    }  
      
    public void operation() {  
        //叶子构件具体业务方法的实现  
    }   
}  
class Composite extends Component {  
    private ArrayList<Component> list = new ArrayList<Component>();  
      
    public void add(Component c) {  
        list.add(c);  
    }  
      
    public void remove(Component c) {  
        list.remove(c);  
    }  
      
    public Component getChild(int i) {  
        return (Component)list.get(i);  
    }  
      
    public void operation() {  
        //容器构件具体业务方法的实现  
        //递归调用成员构件的业务方法  
        for(Object obj:list) {  
            ((Component)obj).operation();  
        }  
    }     
}  
```
## 享元模式 FlyWeight
主要目的是实现对象的共享，即共享池。
一提到共享池，我们很容易联想到Java里面的JDBC连接池，想想每个连接的特点，我们不难总结出：适用于作共享的一些个对象，他们有一些共有的属性，就拿数据库连接池来说，url、driverClassName、username、password及dbname，这些属性对于每个连接来说都是一样的，所以就适合用享元模式来处理，建一个工厂类，将上述类似属性作为内部数据，其它的作为外部数据，在方法调用时，当做参数传进来，这样就节省了空间，减少了实例的数量。
享元模式节省了对象重新创建的开销，提高了性能
## 策略模式
提供一系列策略，然后继承同一个接口，系统本身提供不同策略的实现，新增或者删除策略，对各种策略做封装。策略模式多用在算法决策系统中。
## 模板方法模式
一个抽象类有一个主方法其中实现对其他方法的调用，再定义1-n个方法，定义一个类，继承该抽象类，重写抽象方法，通过调用抽象类的主方法，实现对子类的调用。
## 观察者模式 Observer
类似RSS、当一个对象变化的时候其他观察者都会收到通知
对象需要添加和删除观察者，每次更新的时候notifyObserver
## 迭代子模式 Iterator
类似Collection的迭代器
## 责任链模式 Chain of Responsiblity
有多个对象，每个对象持有对下一个对象的引用，这样就会形成一条链，请求在这条链上传递，直到某个对象决定处理这个请求。
链接上的请求可以是一条链，可以是一个树，还可以是一个环，模式本身不约束这个，需要我们自己去实现，同时，在一个时刻，命令只允许由一个对象传给另一个对象，而不允许传给多个对象
## 命令模式 Command
请求和执行分开
Invoker - Command - Receiver
Invoker是调用者，里面封装Command对象
Command是命令接口，命令被调用者执行策略
Receiver是被调用者
## 备忘录模式 Memento
主要目的是会保存一个对象的某个状态，以便在适当的时候恢复对象。
A有各种属性，而B是备忘录类持有部分A需要保存的，C是存储类，里面存着B的实例
其实就是备份啦，只不过是部分备份
## 状态模式
当对象状态改变时改变其行为
比如QQ有多个状态。
Context在不同状态的时候调用State的不同方法
## 访问者模型
## 中介者模型 Mediator
	类似Facade，Mediator持有多个类的实例，这些类只需维持和Mediator的关系，剩下的全由Mediator处理
	与Facade的区别主要是Mediator对用户是隐藏的，它的规约是既成事实而不是一项约定事务。
## 解释器模型
	主要用于OOP编译器的开发中，


