# juc简介


```
在 Java 5.0 提供了 java.util.concurrent （简称JUC ）包，在此包中增加了在并发编程中很常用的实用工具类， 用于定义类似于线程的自定义子系统，包括线程池、异步 IO 和轻量级任务框架。提供可调的、灵活的线程池。还提供了设计用于多线程上下文中的 Collection 实现等。

1-volatile 关键字
内存可见性（Memory Visibility）是指当某个线程正在使用对象状态而另一个线程在同时修改该状态，需要确保当一个线程修改了对象状态后，其他线程能够看到发生的状态变化。
 可见性错误是指当读操作与写操作在不同的线程中执行时，我们无法确保执行读操作的线程能适时地看到其他线程写入的值，有时甚至是根本不可能的事情。
 我们可以通过同步来保证对象被安全地发布。除此之外我们也可以使用一种更加轻量级的 volatile 变量
Java 提供了一种稍弱的同步机制，即 volatile 变量，用来确保将变量的更新操作通知到其他线程。可以将 volatile 看做一个轻量级的锁，但是又与锁有些不同：
 对于多线程，不是一种互斥关系不能保证变量状态的“原子性操作
例如：
package juchello;
class Tdemo implements Runnable{
    private volatile boolean flag;
    
	public boolean isFlag() {
		return flag;
	}

	public void setFlag(boolean flag) {
		this.flag = flag;
	}

	@Override
	public void run() {
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		setFlag(true);
		System.out.println("over...");
	}
	
}
public class Demo1 {
  public static void main(String[] args) throws InterruptedException {
	  Tdemo td = new Tdemo();
	  
	Thread t=new Thread(td);
	t.start();
	while(true){
		if(td.isFlag())
			System.out.println("main over...");
	}
}
}


2 CAS 算法（原子类型）
 CAS (Compare-And-Swap) 是一种硬件对并发的支持，针对多处理器
操作而设计的处理器中的一种特殊指令，用于管理对共享数据的并
发访问。
 CAS 是一种无锁的非阻塞算法的实现。
 CAS 包含了 3 个操作数：
 需要读写的内存值 V
 进行比较的值 A
 拟写入的新值 B
 当且仅当 V 的值等于 A 时， CAS 通过原子方式用新值 B 来更新 V 的
值，否则不会执行任何操作。
类的小工具包，支持在单个变量上解除锁的线程安全编程。事实上，此包中的类可
将 volatile 值、字段和数组元素的概念扩展到那些也提供原子条件更新操作的类。
 类 AtomicBoolean、 AtomicInteger、 AtomicLong 和 AtomicReference 的实例各自提供对
相应类型单个变量的访问和更新。每个类也为该类型提供适当的实用工具方法。
 AtomicIntegerArray、 AtomicLongArray 和 AtomicReferenceArray 类进一步扩展了原子操
作，对这些类型的数组提供了支持。这些类在为其数组元素提供 volatile 访问语义方
面也引人注目，这对于普通数组来说是不受支持的。
 核心方法： boolean compareAndSet(expectedValue, updateValue)
 java.util.concurrent.atomic 包下提供了一些原子操作的常用类:
 AtomicBoolean 、 AtomicInteger 、 AtomicLong 、 AtomicReference
 AtomicIntegerArray 、 AtomicLongArray
 AtomicMarkableReference
 AtomicReferenceArray
 AtomicStampedReference

例如：
public class AtomicTestCh02 {
	public static void main(String[] args) {
		// 创建线程操作对象
		ThreadDemo2 td = new ThreadDemo2();
		for (int i = 0; i < 10; i++) {
			Thread t = new Thread(td);
			t.start();
		}
	}

}
class ThreadDemo2 implements Runnable{
   private AtomicInteger count=new AtomicInteger();
	@Override
	public void run() {
		//获取当前值get方法
		while(count.get()<20){
			//增加1并返回增加的值
			int x = count.getAndIncrement();
		    try {
				Thread.sleep(200);
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		    System.out.println(Thread.currentThread().getName()+",count:"+x);
		}
	}	
}

3 集合锁分段
Java 5.0 在 java.util.concurrent 包中提供了多种并发容器类来改进同步容器
的性能。
 ConcurrentHashMap 同步容器类是Java 5 增加的一个线程安全的哈希表。对
与多线程的操作，介于 HashMap 与 Hashtable 之间。内部采用“锁分段”
机制替代 Hashtable 的独占锁。进而提高性能。
 此包还提供了设计用于多线程上下文中的 Collection 实现：
ConcurrentHashMap、 ConcurrentSkipListMap、 ConcurrentSkipListSet、
CopyOnWriteArrayList 和 CopyOnWriteArraySet。当期望许多线程访问一个给
定 collection 时， ConcurrentHashMap 通常优于同步的 HashMap，
ConcurrentSkipListMap 通常优于同步的 TreeMap。当期望的读数和遍历远远
大于列表的更新数时， CopyOnWriteArrayList 优于同步的 ArrayList。

/*
 * ConcurrentHashMap 同步容器类是Java 5 增加的一个线程安全的哈希表。对
与多线程的操作，介于 HashMap 与 Hashtable 之间。内部采用“锁分段”
机制替代 Hashtable 的独占锁。进而提高性能。
 此包还提供了设计用于多线程上下文中的 Collection 实现：
ConcurrentHashMap、 ConcurrentSkipListMap、 ConcurrentSkipListSet、
CopyOnWriteArrayList 和 CopyOnWriteArraySet。当期望许多线程访问一个给
定 collection 时， ConcurrentHashMap 通常优于同步的 HashMap，
ConcurrentSkipListMap 通常优于同步的 TreeMap。当期望的读数和遍历远远
大于列表的更新数时， CopyOnWriteArrayList 优于同步的 ArrayList。
 * */
public class ConcurhashmapDemoCh03 {
	public static void main(String[] args) {
		ThreadDemo3 d = new ThreadDemo3();
		for (int i = 0; i < 20; i++) {
			Thread t = new Thread(d);
			t.start();
		}
	}
}

class ThreadDemo3 implements Runnable {
//	private static List<String> slist = Collections.synchronizedList(new ArrayList<>());报错
	private static CopyOnWriteArrayList<String> slist=new CopyOnWriteArrayList<>();
	static {
		slist.add("刘备");
		slist.add("关羽");
		slist.add("张飞");
	}
	@Override
	public void run() {
		for (String s : slist) {
			String cname = Thread.currentThread().getName();
			System.out.println(cname + ":" + s);
			slist.add(cname + ",添加信息");
			
		}
	}
}

4 闭锁
Java 5.0 在 java.util.concurrent 包中提供了多种并发容器类来改进同步容器
的性能。
 CountDownLatch 一个同步辅助类，在完成一组正在其他线程中执行的操作
之前，它允许一个或多个线程一直等待。
 闭锁可以延迟线程的进度直到其到达终止状态，闭锁可以用来确保某些活
动直到其他活动都完成才继续执行：
 确保某个计算在其需要的所有资源都被初始化之后才继续执行;
 确保某个服务在其依赖的所有其他服务都已经启动之后才启动;
 等待直到某个操作所有参与者都准备就绪再继续执行。
/*
 * 统计多个线程计算时间
 * CountDownLatch--闭锁
 * Java 5.0 在 java.util.concurrent 包中提供了多种并发容器类来改进同步容器
的性能。
 CountDownLatch 一个同步辅助类，在完成一组正在其他线程中执行的操作
之前，它允许一个或多个线程一直等待。
 闭锁可以延迟线程的进度直到其到达终止状态，闭锁可以用来确保某些活
动直到其他活动都完成才继续执行：
 确保某个计算在其需要的所有资源都被初始化之后才继续执行;
 确保某个服务在其依赖的所有其他服务都已经启动之后才启动;
 等待直到某个操作所有参与者都准备就绪再继续执行。
 */
public class CountDownLatchDemoCh04 {
	public static void main(String[] args) {
		//初始化锁定6个线程对象
		CountDownLatch cd=new CountDownLatch(3);
		
		ThreadDemo4 td5 = new ThreadDemo4(cd);
		long start = System.currentTimeMillis();
		for (int i = 0; i < 3; i++) {
          Thread t=new Thread(td5);
          t.start();
		}
		
			try {//主线程，锁等待
				cd.await();
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				//e.printStackTrace();
			}
		long end=System.currentTimeMillis();
		//主线程中统计时间
				
			long x=end-start;
			System.out.println("共使用："+x+"毫秒");
		
	}
}

class ThreadDemo4 implements Runnable {
	// 统计5000因的偶数
	//private AtomicInteger count = new AtomicInteger(5000);
   
	//闭锁对象
	private final CountDownLatch cd;
	public ThreadDemo4(CountDownLatch d) {
		cd=d;
	}
	@Override
	public void run() {
		synchronized (this) {
			try{
				int count=5000;
				while (count>= 0) {
					if (count % 2 == 0) {
						String tname = Thread.currentThread().getName();
						System.out.println(tname + ",count:" + count);
					}
					count--;
				}
			}finally {
				//统计减一
				cd.countDown();
			}
		}
	}
}

5 锁（同步）
在 Java 5.0 之前，协调共享对象的访问时可以使用的机制只有 synchronized 和 volatile 。 Java 5.0 后增加了一些新的机制，但并不是一种替代内置锁的方法，而是当内置锁不适用时，作为一种可选择的高级功能。
 ReentrantLock 实现了 Lock 接口，并提供了与synchronized 相同的互斥性和内存可见性。但相较于synchronized 提供了更高的处理锁的灵活性。

/*
 * jdk1.5以前版本，处理线程安全方式
 * 使用同步块和同步方法
 * 
 * jdk1.5以后版本可以使用 lock方法
 * 
 * 在 Java 5.0 之前，协调共享对象的访问时可以使用的机
制只有 synchronized 和 volatile 。 Java 5.0 后增加了一些
新的机制，但并不是一种替代内置锁的方法，而是当内
置锁不适用时，作为一种可选择的高级功能。
 ReentrantLock 实现了 Lock 接口，并提供了与
synchronized 相同的互斥性和内存可见性。但相较于
synchronized 提供了更高的处理锁的灵活性。
 * */
public class LockDemoCh06 {
	public static void main(String[] args) {
		/*
		 * OldTicket ot=new OldTicket(); for (int i = 0; i < 4; i++) { Thread
		 * t=new Thread(ot); t.start(); }
		 */
		ThreadDemo6 td6 = new ThreadDemo6();
		for (int i = 0; i < 4; i++) {
			FutureTask<Integer> ft = new FutureTask<>(td6);
			new Thread(ft).start();
		}
	}
}

class ThreadDemo6 implements Callable<Integer> {
	private Integer ticket = 100;
   //创建锁
	private Lock lock=new ReentrantLock();
	@Override
	public Integer call() throws Exception {
		while (ticket > 0) {
			//显示锁定
			lock.lock();
			try {
				Thread.sleep(200);
				System.out.println(Thread.currentThread().getName() + "窗口剩余:" + --ticket + "张票");
			} finally {
				//一定在finally模块释放
				lock.unlock();
			}
		}
		return ticket;
	}
}

6 
Condition 接口描述了可能会与锁有关联的条件变量。这些变量在用法上与使用 Object.wait 访问的隐式监视器类似，但提供了更强大的功能。需要特别指出的是，单个 Lock 可能与多个 Condition 对象关联。为了避免兼容性问题， Condition 方法的名称与对应的 Object 版
本中的不同。
 在 Condition 对象中，与 wait、 notify 和 notifyAll 方法对应的分别是
await、 signal 和 signalAll。
 Condition 实例实质上被绑定到一个锁上。要为特定 Lock 实例获得
Condition 实例，请使用其 newCondition() 方法。

7 面试题
编写一个程序，开启 3 个线程，这三个线程的 ID 分别为A、 B、 C，每个线程将自己的 ID 在屏幕上打印 10 遍，要求输出的结果必须按顺序显示。
如： ABCABCABC…… 依次递归

8 聊天室

```