# synchronized锁

## 1、修饰代码块，访问大括号{}中的代码块的时候必须先查看该对象是否被锁定（互斥锁）
修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围是大括号{}括起来的代码，作用的对象是**调用这个代码块的对象**；

>(1)当两个并发线程访问同一个对象中的synchronized(this){}同步代码块时，同一时间内只能有一个线程得到执行。另一个线程必须等待当前线程执行完这个代码块以后才能执行该代码块;


>(2)当一个线程访问对象中的一个synchronized(this){}同步代码块时，另一个线程仍然可以访问该对象中的非synchronized(this){}同步代码块；


>(3)当一个线程访问对象中的一个synchronized(this){}同步代码块时，其他线程对对象中所有其它synchronized(this){}同步代码块的访问将被阻塞。

```
public void run() {
 synchronized (this) {
     for (int i = 0; i < 5; i++) {
         System.out.println(Thread.currentThread().getName()+" "+i);
     }
 }
```
## 2、修饰方法
修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法，作用的对象是**调用这个方法的对象**；  
要访问该方法先获取该对象是否被锁定

>synchronized修饰方法时，其作用和修饰代码块类似，此处不再赘述
```
 public synchronized void fun0() { //等同给方法外部加上ynchronized(this){}
      for (int i = 0; i < 5; i++) {
          System.out.println(Thread.currentThread().getName()+" "+i);
      }
}
```
 
## 3、修饰静态方法  
修饰一个静态的方法，其作用的范围是整个静态方法，作用的对象是**这个类的所有对象**；
```
public synchronized static void function() {
      for (int i = 0; i < 5; i++) {
          System.out.println(Thread.currentThread().getName()+" "+i);
     }
}
```
>因为静态方法（或变量）是属于其所属类的，而不是属于该类的对象的，所以synchronized关键字修饰的静态方法锁定的是这个类的所有对象，即所有对象都是同一把锁。

## 4、修饰一个类  
修饰一个类，其作用的范围是synchronized后面括号括起来的部分，作用主的对象是**这个类的所有对象**。

>synchronized关键字还可以用来修饰类，其作用与修饰静态方法类似，即所有类用的是同一把锁

```
  public class MyThread {
      public  static void function() {
          synchronized(MyThread.class){
              for (int i = 0; i < 5; i++) {
                  System.out.println(Thread.currentThread().getName()+" "+i);
              }
          }
 }
```

