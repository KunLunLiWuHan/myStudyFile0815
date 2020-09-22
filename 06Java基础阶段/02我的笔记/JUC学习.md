# 1 介绍

1、JUC（java.util.concurrent）是在并发编程中使用的工具类。 （concurrent：并发的，一致的）。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085014.png" alt="image-20200810171906868" style="zoom:80%;" />

（1）进程：进程是一个**具有一定独立功能的程序**关于某个数据集合的一次运行活动。它是操作系统动态执行的基本单元，在传统的操作系统中，进程既是基本的分配单元，也是基本的执行单元。

（2）线程：通常在一个进程中可以包含若干个线程，当然一个进程中至少有一个线程，不然没有存在的意义。线程可以利用进程所拥有的资源，在引入线程的操作系统中，通常都是**把进程作为分配资源的基本单位，而把线程作为独立运行和独立调度的基本单位**，由于线程比进程更小，基本上不拥有系统资源，故对它的调度所付出的开销就会小得多，能更高效的提高系统多个程序间并发执行的程度。

（3）进程和线程例子

​		使用QQ，查看进程一定有一个QQ.exe的进程，我可以用qq和A文字聊天，和B视频聊天，给C传文件，给D发一段语言，QQ支持录入信息的搜索。

​		大四的时候写论文，用word写论文，同时用QQ音乐放音乐，同时用QQ聊天，多个进程。

​		word如没有保存，停电关机，再通电后打开word可以恢复之前未保存的文档，word也会检查你的拼写，两个线程：容灾备份，语法检查。

2、并发和并行

并发：同一时刻多个线程在访问同一个资源，多个线程对一个点。
      例子：小米9今天上午10点，限量抢购
            春运抢票
            电商秒杀...
并行：多项工作一起执行，之后再汇总。
      例子：泡方便面，电水壶烧水，一边撕调料倒入桶中。

3、线程状态

```java
public enum State {
    /**
     * Thread state for a thread which has not yet started.
     */
    NEW,(新建)

    /**
     * Thread state for a runnable thread.  A thread in the runnable
     * state is executing in the Java virtual machine but it may
     * be waiting for other resources from the operating system
     * such as processor.
     */
    RUNNABLE,（准备就绪）

    /**
     * Thread state for a thread blocked waiting for a monitor lock.
     * A thread in the blocked state is waiting for a monitor lock
     * to enter a synchronized block/method or
     * reenter a synchronized block/method after calling
     * {@link Object#wait() Object.wait}.
     */
    BLOCKED,（阻塞）

    /**
     * Thread state for a waiting thread.
     * A thread is in the waiting state due to calling one of the
     * following methods:
     * <ul>
     *   <li>{@link Object#wait() Object.wait} with no timeout</li>
     *   <li>{@link #join() Thread.join} with no timeout</li>
     *   <li>{@link LockSupport#park() LockSupport.park}</li>
     * </ul>
     *
     * <p>A thread in the waiting state is waiting for another thread to
     * perform a particular action.
     *
     * For example, a thread that has called <tt>Object.wait()</tt>
     * on an object is waiting for another thread to call
     * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
     * that object. A thread that has called <tt>Thread.join()</tt>
     * is waiting for a specified thread to terminate.
     */
    WAITING,（不见不散）

    /**
     * Thread state for a waiting thread with a specified waiting time.
     * A thread is in the timed waiting state due to calling one of
     * the following methods with a specified positive waiting time:
     * <ul>
     *   <li>{@link #sleep Thread.sleep}</li>
     *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
     *   <li>{@link #join(long) Thread.join} with timeout</li>
     *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
     *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
     * </ul>
     */
    TIMED_WAITING,（过时不候）

    /**
     * Thread state for a terminated thread.
     * The thread has completed execution.
     */
    TERMINATED;(终结)
}
```

4、wait/sleep的区别

​		功能都是当前线程暂停，wait放开手去睡，放开手里的锁，sleep握紧手去睡，醒了手里还有锁。

# 2 Lock接口

## 1、创建线程的三种方式

（1）继承Thread

```java
public class SaleTicket extends Thread
```

java是单继承，资源宝贵，推荐要用接口方式。

（2）new Thread()

```java
Thread t1 = new Thread();
   t1.start();
```

（3）Thread(Runnable target, String name) 

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085015.png" alt="image-20200810222813439" style="zoom:80%;" />

## 2、SaleTicket回顾

1、实现代码

```java
/**
 * 1、资源类：实例变量 + 实例方法（实例方法指空调身上的制冷和制热功能，这个功能是其
 * 自己带，即高内聚。空调设计人员没有咨询你空调的设计，即低耦合）
 * 内聚是指：内聚变量和方法
 */
class Ticket {
   /**
    * 1、number 可以将其看作空调出场的时候，
    * 默认的温度值。
    * 2、A,B抢空调进行设置制冷和制热（其制冷和制热功能对应
    * sale()方法），一个将其加一度，一个将其减一度。
    * 3、现在 number和sale()都封装到 Ticket 中，这就叫做
    * 高内聚。
    * 4、说白了，高内聚（自己动手，丰衣足食）：你要操作什么东西，这个东西就叫做资源类，你要对它
    * 进行什么操作，资源类要自身携带这些方法，而不是外部提供。
    */
   private int number = 30;

   // List list =new ArrayList();
   Lock lock = new ReentrantLock(); //可重入锁

   /**
    * 1、public synchronized void sale()是将代码块中的代码全部锁了
    * 2、冤有头，债有主。
    */
   public void sale() {
      lock.lock();
      try {
         if (number > 0) {
            System.out.println(Thread.currentThread().getName() + "\t 卖出第：" + (number--) + "\t 票，还剩下：" + (number) + "票");
         }
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         lock.unlock();
      }
   }
}


/**
 * 1、题目： 三个售货员 卖出 30张票
 * 2、企业级的多线程代码
 * （1）、在高内聚低耦合的前提下，线程 操作 资源类
 * （a）一言不合，先创建一个资源类（小明 使用遥控器操作 空调）
 */
public class SaleTickDemo01 {
   public static void main(String[] args) {//主线程，一切程序的入口

      Ticket ticket = new Ticket();

//    Thread t1 = new Thread();
      new Thread(new Runnable() {
         @Override
         public void run() {
            for (int i = 0; i < 40; i++) {
               //A线程要操作资源类
               ticket.sale();
            }
         }
      }, "A").start();

      new Thread(new Runnable() {
         @Override
         public void run() {
            for (int i = 0; i < 40; i++) {
               //A线程要操作资源类
               ticket.sale();
            }
         }
      }, "B").start();

      new Thread(new Runnable() {
         @Override
         public void run() {
            for (int i = 0; i < 40; i++) {
               //A线程要操作资源类
               ticket.sale();
            }
         }
      }, "C").start();
   }
}
```

2、Thread代码重构

```java
new Thread(() -> {for (int i = 0; i < 40; i++) ticket.sale();},"A").start();
new Thread(() -> {for (int i = 0; i < 40; i++) ticket.sale();},"B").start();
new Thread(() -> {for (int i = 0; i < 40; i++) ticket.sale();},"C").start();
```

## 3、Lambda表达式

​		Lambda 是一个匿名函数，我们可以把 Lambda表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。可以写出更简洁、更灵活的代码。作为一种更紧凑的代码风格，使Java的语言表达能力得到了提升。

​		Lambda 表达式在Java 语言中引入了一个新的语法元素和操作符。这个操作符为 “->” ， 该操作符被称为 Lambda 操作符或剪头操作符。它将 Lambda 分为两个部分：
左侧：指定了 Lambda 表达式需要的所有参数
右侧：指定了 Lambda 体，即 Lambda 表达式要执行的功能

1、实现代码

```java
/**
 * 1、Java不但重视函数式编程还重视下面的接口是编程
 * 2、下面只有唯一一个方法，可以使用匿名内部类
 * 3、使用Lambda表达式解决了匿名内部类代码冗余的问题
 * Lambda口诀 ： 拷贝小括号，落地大括号,写死右箭头。
 */
interface Foo{
// public void sayHello();
   //含有参数和返回值
   public int add(int x,int y);
}

/**
 * 1、Lambda表达式
 * 2、函数式编程
 *        int age = 23;
 *        f(x) = y = kx + 1;
 */
public class LambdaExpressDemo2 {
   public static void main(String[] args) {
      //接口也能new出来
//    Foo foo = new Foo(){
//       @Override
//       public void sayHello() {
//          System.out.println("nihao --> " + "0810");
//       }
//
//       @Override
//       public int add(int x, int y) {
//          return 0;
//       }
//    };
//    foo.sayHello();
//    Foo foo = () -> {
//       System.out.println("你好 --> " + "0810");
//    };
//    foo.sayHello();

      Foo foo = (int x,int y) -> {
         System.out.println("come in add method " );
         return (x + y);
      };
      System.out.println(foo.add(1, 2));
   }
}
```

2、函数式接口

```java
@FunctionalInterface
interface Foo{
   public int add(int x,int y);
}
```

​		lambda表达式，必须是函数式接口，必须只有一个方法如果接口只有一个方法java默认它为函数式接口。为了正确使用Lambda表达式，需要给接口加个注解：@FunctionalInterface如有两个方法，立刻报错

3、接口里的实现方法

（1）default方法

接口里在java8后容许有接口的实现，default方法默认实现

```java
default int div(int x,int y) {
  return x/y;
 }

//不需要实现，直接对象调用即可
foo.div(4, 2)
```

接口里default方法可以有多个。

（2）静态方法实现

```java
// 静态方法实现：接口新增
public static int sub(int x,int y){
  return x-y;
}

//注意：静态的叫类方法，不能能用foo去调，要改成Foo。
Foo.sub(2, 2)
```

接口里的静态方法可以有多个。

# 3 集合类不安全

## 1、举例线程不安全

```java
private static void listNotSafe() {
      List<String> list = new ArrayList<>();
      for (int i = 1; i <= 10; i++) {
         new Thread(() -> {
            list.add(UUID.randomUUID().toString().substring(0, 8));
            System.out.println(list);
         }, String.valueOf(i)).start();
      }
   }

//main主程序中运行
listNotSafe();
```

此时会抛出java.util.ConcurrentModificationException异常。

（1）原因

​		ArrayList在迭代的时候如果同时对其进行修改，就会抛出并发修改异常。所有的线程都在争操作该List的那一把锁。

（2）原理

```java
//看ArrayList的源码。没有synchronized线程不安全。
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}
```

## 2、解决方案

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085016.png" alt="List" style="zoom:80%;" />

（1）Vector

```java
List<String> list = new Vector<>();
```

通过查看Vector的源码，有synchronized，所以线程是安全的：

```java
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}
```

（2） Collections.synchronizedList

```java
List<String> list = Collections.synchronizedList(new ArrayList<>());
```

​		Collections提供了方法synchronizedList保证list是同步线程安全的。

​		那HashMap，HashSet也不是线程安全的，所以有同样的线程安全方法。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085017.jpg" alt="image-20200811001207498" style="zoom:80%;" />

（3）CopyOnWriteArrayList

```java
List<String> list = new CopyOnWriteArrayList<>();
```

​		使用CopyOnWriteArrayList可以很好的可以保证数据一致性，又可以保证并发性。

（a）对于CopyOnWrite的理解：

​		CopyOnWriteArrayList是arraylist的一种线程安全变体，其中所有可变操作（add、set等）都是通过生成底层数组的新副本来实现的。

​		CopyOnWrite容器即**写时复制**的容器。往一个容器添加元素的时候，不直接往当前容器Object[]添加，而是先将当前容器Object[]进行Copy，复制出一个新的容器Object[] newElements，然后向新的容器Object[] newElements里添加元素。添加元素后，再将原容器的引用指向新的容器setArray(newElements)。这样做的好处是可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。

（b）举例-名单那签到

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085018.png" alt="image-20200811001727232" style="zoom:80%;" />

（c）CopyOnWrite理论代码

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

## 3、扩展类比

（1）set

```java
Set<String> set = new HashSet<>();//线程不安全
Set<String> set = new CopyOnWriteArraySet<>();//线程安全
```

HashSet底层数据结构是什么？但HashSet的add是放一个值，而HashMap是放K、V键值对。

（2）map

```java
Map<String,String> map = new HashMap<>();//线程不安全
Map<String,String> map = new ConcurrentHashMap<>();//线程安全
```

# 4 多线程锁

## 1、代码

```java
class Phone {
   public static synchronized void sendEmail() throws Exception {
      TimeUnit.SECONDS.sleep(4); //推荐时间设置
      System.out.println("---------sendEmail------------");
   }

   public static synchronized void sendSMS() throws Exception {
      System.out.println("---------sendSMS------------");
   }

   public static void sayHello() throws Exception{
      System.out.println("---------sayHello------------");
   }
}

/**
 * 8锁
 * 1、标准访问 是先打印邮件还是短信 
 	先发邮件
 * 2、暂停4秒钟在邮件方法，先打印邮件还是短信
 * 但是短信没有停止（模拟的是两个线程同时进来）先发邮件
 * 3、新增普通sayHello方法，请问先打印邮件还是sayHello
 	sayHello也是资源类的一部分，
 * 4、两部手机，是先打印邮件还是短信
 	phone2先打印短信,此时不对应同一个资源（现在一狼一肉），即二	 者不是同一把锁
 * 5、两个静态同步方法，同一部手机，是先打印邮件还是短信
 * 6、两个静态同步方法，两部手机，是先打印邮件还是短信
 	5和6这两种情况，都是先打印邮件。
 * 7、一个静态同步方法，一个普通同步方法，同一部手机，是先打印邮件还是短信
 		先打印短信
 * 8、一个静态同步方法，一个普通同步方法，两部手机，是先打印邮件还是短信
 		先打印短信
 */
public class Lock8Demo01 {
   public static void main(String[] args) {
      Phone phone = new Phone();
      Phone phone2 = new Phone();

      new Thread(() -> {
         try {
            phone.sendEmail();
         } catch (Exception e) {
            e.printStackTrace();
         }
      }, "A").start();

      new Thread(() -> {
         try {
//          phone.sendSMS();
//          phone.sayHello();
//          phone2.sendSMS();
            phone2.sayHello();
         } catch (Exception e) {
            e.printStackTrace();
         }
      }, "B").start();
   }
}
```

## 2、分析

​		A 一个对象里面如果有多个synchronized方法，某一个时刻内，只要一个线程去调用其中的一个synchronized方法了，其它的线程都只能等待，换句话说，某一个时刻内，只能有唯一一个线程去访问这些synchronized方法，**锁的是当前对象this**，被锁定后，其它的线程都不能进入到当前对象的其它的synchronized方法（对应1锁和2锁的）。

​		加个普通方法（sayHello()）后发现和同步锁无关，换成两个对象后，**不是同一把锁了**，情况立刻变化。我们可以这样理解：手机虽然在班长手里，但是手机壳（sayHello()）可以取下来，班长照样可以打电话，没有和班长竞争同一个资源类（对应3锁）。

​		synchronized实现同步的基础：Java中的每一个对象都可以作为锁。具体表现为以下3种形式：
（1）对于普通同步方法，锁是当前实例对象。锁的是对象(new Phone())锁。

（2）对于同步方法块，锁是Synchonized括号里配置的对象。synchronized(this){}

（3）对于**静态同步方法，锁是当前类的Class对象**，是一种全局的概念。锁的是全局Class锁。Phone都来自同一个型号，同一个模板。

​		当一个线程试图访问同步代码块时，它首先必须得到锁，退出或抛出异常时必须释放锁。

​		也就是说如果一个实例对象的非静态同步方法获取锁后，该实例对象的其他非静态同步方法必须等待获取锁的方法释放锁后才能获取锁，
可是别的实例对象的非静态同步方法因为跟该实例对象的非静态同步方法用的是不同的锁，所以毋须等待该实例对象已获取锁的非静态同步方法释放锁就可以获取他们自己的锁。

​		所有的静态同步方法用的也是同一把锁——类对象本身，这两把锁是两个不同的对象，所以**静态同步方法与非静态同步方法之间是不会有竞态条件的。**
​		但是一旦一个静态同步方法获取锁后，其他的静态同步方法都必须等待该方法释放锁后才能获取锁，而不管是同一个实例对象的静态同步方法之间，还是不同的实例对象的静态同步方法之间，只要它们同一个类的实例对象！

#  5 线程间通信

## 1、生产者消费者

### 1、实现代码

```java
/**
 * 1、资源类-空调类
 * 2、先生产才能消费
 */
class AirCondition {
   private int number = 0;

   public synchronized void increment() throws Exception { //制热
      //1、判断（这个活该不该我干）
      if (number != 0) {
         this.wait();
      }
      //2、干活
      number++;
      System.out.println(Thread.currentThread().getName() + "\t" + number);
      //2、通知（通知老板我干完了）
      this.notifyAll();
   }

   public synchronized void decrement() throws Exception { //制冷
      //1、判断
      if (number == 0) {
         this.wait();
      }
      //2、干活
      number--;
      System.out.println(Thread.currentThread().getName() + "\t" + number);
      //3、通知
      this.notifyAll();
   }
}

/**
 * 题目：现在两个线程，可以操作初始值为零的一个变量，
 * 实现一个线程对该变量加1，一个线程对该变量减1，
 * 实现交替，来10轮，变量初始值为0。
 * 1、高内聚低耦合的前提下：线程操作资源类
 * 2、判断/干活/通知
 * 3、防止虚假唤醒。
 */
public class ProdConsumerDemo01 {
   public static void main(String[] args) {
      AirCondition airCondition = new AirCondition();
      new Thread(() -> {
         for (int i = 0; i < 10; i++) {
            try {
               airCondition.increment();
            } catch (Exception e) {
               e.printStackTrace();
            }
         }
      }, "A").start();
      new Thread(() -> {
         for (int i = 0; i < 10; i++) {
            try {
               airCondition.decrement();
            } catch (Exception e) {
               e.printStackTrace();
            }
         }
      }, "B").start();
   }
}
```

输出结果：

```ini
A	1
B	0
A	1
B	0
...
```

​		当换成4个线程的时候，生产和消费会出现错误，比如会出现一个数字2的情况。会出现虚假唤醒的情况，解释如下：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085019.png" alt="image-20200811101059300" style="zoom:80%;" />

​		在java多线程判断时，不能用if，程序出事出在了判断上面，突然有一添加的线程进到if了，突然中断了交出控制权，没有进行验证，而是直接走下去了，加了两次，甚至多次。

​		即：中断和虚假唤醒是可能产生的，所以要用loop循环，if只判断一次，while是只要唤醒就要拉回来再判断一次。if换成while。用到下面的代码重构。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085020.png" alt="image-20200811101426580" style="zoom:80%;" />

### 2、代码重构

```java
class AirCondition {
   private int number = 0;

   public synchronized void increment() throws Exception { //制热
      //1、判断（这个活该不该我干）
      //用while来解决虚假唤醒
      while (number != 0) {
         this.wait();
      }
      //2、干活
      number++;
      System.out.println(Thread.currentThread().getName() + "\t" + number);
      //2、通知（通知老板我干完了）
      this.notifyAll();
   }

   public synchronized void decrement() throws Exception { //制冷
      //1、判断
      while (number == 0) {
         this.wait();
      }
      //2、干活
      number--;
      System.out.println(Thread.currentThread().getName() + "\t" + number);
      //3、通知
      this.notifyAll();
   }
}
```

### 3、使用Lock代码重写

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085021.png" alt="image-20200811114312519" style="zoom:80%;" />

```java
class AirCondition2 {
   private int number = 0;
   private Lock lock = new ReentrantLock();
   private Condition condition = lock.newCondition();

   public void increment() throws Exception { //制热
      lock.lock();
      try {
         //1、判断
         while (number != 0) {
//          this.wait();
            condition.await();
         }
         //2、干活
         number++;
         System.out.println(Thread.currentThread().getName() + "\t" + number);
         //3、通知
//       this.notifyAll();
         condition.signalAll();
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         lock.unlock();
      }
   }

   public void decrement() throws Exception { //制热
      lock.lock();
      try {
         //1、判断
         while (number == 0) {
            condition.await();
         }
         //2、干活
         number--;
         System.out.println(Thread.currentThread().getName() + "\t" + number);
         //3、通知
         condition.signalAll();
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         lock.unlock();
      }
   }
}

public class ProdConsumerDemo02 {
   public static void main(String[] args) {
      AirCondition2 airCondition = new AirCondition2();

      new Thread(() -> {
         for (int i = 0; i < 10; i++) {
            try {
               airCondition.increment();
            } catch (Exception e) {
               e.printStackTrace();
            }
         }
      }, "A").start();
      new Thread(() -> {
         for (int i = 0; i < 10; i++) {
            try {
               airCondition.decrement();
            } catch (Exception e) {
               e.printStackTrace();
            }
         }
      }, "B").start();
   }
}
```

## 2、Condition

```java
class ShareDate {
   // 标志位
   private int number = 1; //A:1；B:2；C:3；
   private Lock lock = new ReentrantLock();
   //一把锁配多把备用钥匙
   private Condition c1 = lock.newCondition();
   private Condition c2 = lock.newCondition();
   private Condition c3 = lock.newCondition();

   public void print5() {
      lock.lock();
      try {
         //1、判断
         while (number != 1) {
            //wait...
            c1.await();
         }
         //2、干活
         for (int i = 1; i <= 5; i++) {
            System.out.println(Thread.currentThread().getName() + "\t" + i);
         }
         //3、通知（永远要先改变标志位）
         number = 2;
         c2.signal(); //通知第二个
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         lock.unlock();
      }
   }

   public void print10() {
      lock.lock();
      try {
         //1、判断
         while (number != 2) {
            //wait...
            c2.await();
         }
         //2、干活
         for (int i = 1; i <= 10; i++) {
            System.out.println(Thread.currentThread().getName() + "\t" + i);
         }
         //3、通知（永远要先改变标志位）
         number = 3;
         c3.signal();
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         lock.unlock();
      }
   }

   public void print15() {
      lock.lock();
      try {
         //1、判断
         while (number != 3) {
            //wait...
            c3.await();
         }
         //2、干活
         for (int i = 1; i <= 15; i++) {
            System.out.println(Thread.currentThread().getName() + "\t" + i);
         }
         //3、通知（永远要先改变标志位）
         number = 1;
         c1.signal();
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         lock.unlock();
      }
   }
}

/**
 * @Description: 多线程之间按顺序调用，实现A->B->C
 * 三个线程启动，要求如下：
 * AA打印5次，BB打印10次，CC打印15次
 * 接着AA打印5次，BB打印10次，CC打印15次
 * 来10轮
 */
public class ConditionDemo01 {
   public static void main(String[] args) {
      ShareDate shareDate = new ShareDate();
      new Thread(() -> {
         for (int i = 1; i <= 10; i++) {
            shareDate.print5();
         }
      }, "A").start();
      new Thread(() -> {
         for (int i = 1; i <= 10; i++) {
            shareDate.print10();
         }
      }, "B").start();
      new Thread(() -> {
         for (int i = 1; i <= 10; i++) {
            shareDate.print15();
         }
      }, "C").start();
   }
}
```

# 5 Callable接口

​		这是一个函数式接口，因此可以用作lambda表达式或方法引用的赋值对象。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085022.png" alt="image-20200811135429526" style="zoom:80%;" />

## 1、callable接口与runnable接口的区别

```java
 //创建新类MyThread实现runnable接口
class MyThread implements Runnable{
 @Override
 public void run() {
 
 }
}

//新类MyThread2实现callable接口
class MyThread2 implements Callable<Integer>{
 @Override
 public Integer call() throws Exception {
  return 200;
 } 
}
```

（1）是否有返回值。

（2）是否抛异常。

（3）落地方法不一样，一个是run，一个是call。

## 2、核心代码

```java
//Callable接口
class MyThread implements Callable<Integer> {
   @Override
   public Integer call() throws Exception {
      System.out.println("-----------come into MyThread-------------");
      TimeUnit.SECONDS.sleep(4); //模拟执行任务
      return 1024;
   }
}

/**
 * 1、get方法放到最后一行
 */
public class CallableDemo01 {
   public static void main(String[] args) throws ExecutionException, InterruptedException {
      //FutureTask 中间人;Integer 返回值类型
      FutureTask<Integer> futureTask = new FutureTask(new MyThread());

      //futureTask实现了Runnable接口
      new Thread(futureTask, "A").start();
      //
      /**
       * MyThread()中的System.out.println("-----------come into MyThread-------------")方法只会调用一次，
       * 即在控制台打印一次。因为线程 B 使用正确结果复用的方法。将A计算的结果拿到而返回。
       */
//    new Thread(futureTask, "B").start();
      /**
       * 将futureTask.get()放到这里，此时耗时任务还没有完成，线程会阻塞到这里。导致一直等待。
       * 高考一进去做难题
       */
//    System.out.println("result --> " + futureTask.get());
      //mian线程 （将容易的做完，将耗时的计算完）
      System.out.println(Thread.currentThread().getName() + "----------计算完成----------");
      //获取返回值
      System.out.println("result --> " + futureTask.get());
   }
}
```

（1）理解		

​		在主线程中需要执行比较耗时的操作时，但又不想阻塞主线程时，可以把这些作业交给Future对象在后台完成，当主线程将来需要时，就可以通过Future对象获得后台作业的计算结果或者执行状态。

​		一般FutureTask多用于耗时的计算，主线程可以在完成自己的任务后，再去获取结果。

​		仅在计算完成时才能检索结果；如果计算尚未完成，则阻塞 get 方法。一旦计算完成，就不能再重新开始或取消计算。get方法而获取结果只有在计算完成时获取，否则会一直阻塞直到任务转入完成状态，
然后会返回结果或者抛出异常。 

​		只计算一次：get方法放到最后。

（2）FutureTask中间人

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085023.png" alt="FutureTask" style="zoom:80%;" />

## 3、讲解

​		未来的任务，用它（Callable）就干一件事，即异步调用。main方法就像一个冰糖葫芦，一个个方法由main串起来。但解决不了一个问题：正常调用挂起堵塞问题

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085024.png" alt="image-20200811143852089" style="zoom:80%;" />

举例：

（1）老师上着课，口渴了，去买水不合适，讲课线程继续，我可以单起个线程找班长帮忙买水，水买回来了放桌上，我需要的时候再去get。
（2）4个同学，A算1+20,B算21+30,C算31*到40,D算41+50，是不是C的计算量有点大啊，FutureTask单起个线程给C计算，我先汇总ABD，最后等C计算完了再汇总C，拿到最终结果。
（3）高考：会做的先做，不会的放在后面做。

# 6 JUC辅助类

## 1、减少计数-CountDownLatch

1、实现代码

```java
/**
 * @Description: 让一些线程阻塞直到另一些线程完成一系列操作后才被唤醒。
 * 控制线程顺序
 * 题目：6个同学陆续离开教室后,最后班长才可以关门。
 * main主线程必须要等前面6个线程完成全部工作后，自己才能开干。
 */
public class CountDownLatchDemo01 {
   public static void main(String[] args) throws InterruptedException {
      //6个计数
      CountDownLatch countDownLatch = new CountDownLatch(6);

      //开启六个线程，模拟6个同学
      for (int i = 1; i <= 6; i++) {
         new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t 离开教室");
            //离开一个，计算减少一个
            countDownLatch.countDown();
         }, String.valueOf(i)).start();
      }
      countDownLatch.await(); //主线程阻塞，等到 countDownLatch.countDown();为0
      System.out.println(Thread.currentThread().getName() + "\t班长关门走人");
   }
}
```

2、理解

（1）CountDownLatch主要有两个方法，当一个或多个线程调用await方法时，这些线程会阻塞。

（2）其它线程调用countDown方法会将计数器减1(调用countDown方法的线程不会阻塞)，当计数器的值变为0时，因await方法阻塞的线程会被唤醒，继续执行。

（3）有一种感觉倒着计数的功能（倒数到0才开始）。

## 2、循环栅栏-CyclicBarrier

1、实现代码

```java
/**
 * 做加法，数到多少才开始
 * 集齐7颗龙珠就可以召唤神龙
 * (人到齐才能开会)
 */
public class CyclicBarrierDemo01 {
   private static final int NUMBER = 7;

   public static void main(String[] args) {
      CyclicBarrier cyclicBarrier = new CyclicBarrier(NUMBER, () -> {
         //数字到7（NUMBER）,龙就出来了
         System.out.println("*****集齐7颗龙珠就可以召唤神龙");
      });

      for (int i = 1; i <= 7; i++) {
         new Thread(() -> {
            try {
               System.out.println(Thread.currentThread().getName() + "\t 星龙珠被收集 ");
               cyclicBarrier.await();//将线程在这里等待
            } catch (InterruptedException | BrokenBarrierException e) {
               e.printStackTrace();
            }

         }, String.valueOf(i)).start();
      }
   }
}
```

2、理解

​		CyclicBarrier的字面意思是可循环（Cyclic）使用的屏障（Barrier）。

​		它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。

​		线程通过CyclicBarrier的await()方法来进入CyclicBarrier屏障。

## 3、信号灯-Semaphore

1、实现代码

```java
//多对多的情况 6 -》 3
public class SemaphoreDemo01 {
   public static void main(String[] args) {
      Semaphore semaphore = new Semaphore(3);//模拟3个停车位（资源类）

      for (int i = 1; i <= 6; i++) //模拟6部汽车
      {
         new Thread(() -> {
            try {
               semaphore.acquire(); //获取一个车位
               System.out.println(Thread.currentThread().getName() + "\t 抢到了车位");
               TimeUnit.SECONDS.sleep(3); //停留时间
               System.out.println(Thread.currentThread().getName() + "\t------- 离开");
            } catch (InterruptedException e) {
               e.printStackTrace();
            } finally {
               semaphore.release(); //释放一个车位
            }
         }, String.valueOf(i)).start();
      }
   }
}
```

```java
//等价于synchronized加在一个资源类上，只有一个资源加一个锁。
Semaphore semaphore = new Semaphore(1);//模拟1个停车位
```

2、理解

 在信号量上我们定义两种操作：

​		acquire（获取） 当一个线程调用acquire操作时，它要么通过成功获取信号量（信号量减1），要么一直等下去，直到有线程释放信号量，或超时。

​		release（释放）实际上会将信号量的值加1，然后唤醒等待的线程。

​		信号量主要用于两个目的，一个是用于多个共享资源的互斥使用，另一个用于并发线程数的控制。

# 7 读写锁

```java
/**
 * 1、缓存
 */
class MyCache {
   private volatile Map<String, Object> map = new HashMap();
   private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

   public void put(String key, Object value) {
      readWriteLock.writeLock().lock();
      try {
         TimeUnit.SECONDS.sleep(1);
         System.out.println(Thread.currentThread().getName() + "\t写入数据" + key);
         map.put(key, value);
         System.out.println(Thread.currentThread().getName() + "\t写入完成");
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         readWriteLock.writeLock().unlock();
      }
   }

   public void get(String key) {
      readWriteLock.readLock().lock();
      try {
         TimeUnit.SECONDS.sleep(1);
         System.out.println(Thread.currentThread().getName() + "\t读取数据" );
         Object result = map.get(key);
         System.out.println(Thread.currentThread().getName() + "\t读取完成" + result);
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         readWriteLock.readLock().unlock();
      }
   }
}

/**
 * 1、多个线程同时读一个资源类，没有问题，所以，为了满足
 * 并发量，读取共享资源应该可以同步进行
 * 但是，如果有一个线程想要去写共享资源，就不应该再有其他线程
 * 对其资源进行读或写。
 * 2、总结
 * 读-读能共存
 * 读-写不能共存
 * 写-写不能共存
 */
public class ReadWriteLockDemo01 {
   public static void main(String[] args) {
      MyCache myCache = new MyCache();
      for (int i = 1; i <= 5; i++) {
         final int tempInt = i;
         new Thread(() -> {
            myCache.put(tempInt + "", tempInt + "");
         }, String.valueOf(i)).start();
      }
      for (int i = 1; i <= 5; i++) {
         final int tempInt = i;
         new Thread(() -> {
            myCache.get(tempInt + "");
         }, String.valueOf(i)).start();
      }
   }
}
```

和Lock不同的地方在于，Lock读写都会锁上资源，造成性能的降低。

# 8 阻塞队列-BlockingQueue

## 1、阻塞队列

1、介绍

（1）阻塞：必须要阻塞/不得不阻塞 
阻塞队列是一个队列，在数据结构中起的作用如下图：

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085025.png" alt="image-20200811164403698" style="zoom:80%;" />

 		线程1往阻塞队列里添加元素，线程2从阻塞队列里移除元素。

​		当队列是空的，从队列中获取元素的操作将会被阻塞。

​		当队列是满的，从队列中添加元素的操作将会被阻塞。

​		试图从空的队列中获取元素的线程将会被阻塞，直到其他线程往空的队列插入新的元素。试图向已满的队列中添加新元素的线程将会被阻塞，直到其他线程从队列中移除一个或多个元素或者完全清空，使队列变得空闲起来并后续新增。

（2）栈和队列

栈：先进后出；队列：先进先出。

## 2、阻塞队列的好处

​		在多线程领域：所谓阻塞，在某些情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动被唤起。

​		为什么需要BlockingQueue，好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程，因为这一切BlockingQueue都给你一手包办了。

​		在concurrent包发布以前，在多线程环境下，我们每个程序员都必须去自己控制这些细节，尤其还要兼顾效率和线程安全，而这会给我们的程序带来不小的复杂度。

## 3、架构介绍

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085026.png" alt="Collection" style="zoom:80%;" />

## 4、种类分析

ArrayBlockingQueue：由数组结构组成的有界阻塞队列。

LinkedBlockingQueue：由链表结构组成的有界（但大小默认值为integer.MAX_VALUE）阻塞队列。

PriorityBlockingQueue：支持优先级排序的无界阻塞队列。

DelayQueue：使用优先级队列实现的延迟无界阻塞队列。

SynchronousQueue：不存储元素的阻塞队列，也即单个元素的队列。

LinkedTransferQueue：由链表组成的无界阻塞队列。

LinkedBlockingDeque：由链表组成的双向阻塞队列。

5、BlockingQueue核心方法

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085027.png" alt="image-20200811165111335" style="zoom:80%;" />

|          | 描述                                                         |
| -------- | :----------------------------------------------------------- |
| 抛出异常 | 当阻塞队列满时，再往队列里add插入元素会抛IllegalStateException:Queue full<br/>当阻塞队列空时，再往队列里remove移除元素会抛NoSuchElementException |
| 特殊值   | 插入方法，成功ture失败false<br/>移除方法，成功返回出队列的元素，队列里没有就返回null |
| 一直阻塞 | 当阻塞队列满时，生产者线程继续往队列里put元素，队列会一直阻塞生产者线程直到put数据or响应中断退出<br/>当阻塞队列空时，消费者线程试图从队列里take元素，队列会一直阻塞消费者线程直到队列可用 |
| 超时退出 | 当阻塞队列满时，队列会阻塞生产者线程一定时间，超过限时后生产者线程会退出 |

# 9 线程池

## 1、线程池的优势

1、例子	
		10年前单核CPU电脑，假的多线程，像马戏团小丑玩多个球，CPU需要来回切换。
		现在是多核电脑，多个线程各自跑在独立的CPU上，不用切换效率高。

2、线程池的优势
		线程池做的工作主要是控制运行的线程数量，处理过程中将任务放入队列，然后在线程创建后启动这些任务，如果线程数量超过了最大数量，超出数量的线程排队等候，等其他线程执行完毕，再从队列中取出任务来执行。

3、主要特点为：线程复用;控制最大并发数;管理线程。

（1）降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的销耗。

（2）提高响应速度。当任务到达时，任务可以不需要等待线程创建就能立即执行。

（3）提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

## 2、架构说明

Java中的线程池是通过Executor框架实现的，该框架中用到了Executor，Executors，ExecutorService，ThreadPoolExecutor这几个类。

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085028.png" alt="image-20200811173424832" style="zoom:80%;" />

## 3、线程池的3大方法

 1、创建线程池类的讲解

（1）Executors.newFixedThreadPool(int)

执行长期任务性能好，创建一个线程池，一池有N个固定的线程，有固定线程数的线程。

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```

newFixedThreadPool创建的线程池corePoolSize和maximumPoolSize值是相等的，它使用的是LinkedBlockingQueue

（2）Executors.newSingleThreadExecutor()

​		一个任务一个任务的执行，一池一线程。

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

newSingleThreadExecutor 创建的线程池corePoolSize和maximumPoolSize值都是1，它使用的是LinkedBlockingQueue。

（3）Executors.newCachedThreadPool()

执行很多短期异步任务，线程池根据需要创建新线程，但在先前构建的线程可用时将重用它们。可扩容，遇强则强。

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

newCachedThreadPool创建的线程池将corePoolSize设置为0，将maximumPoolSize设置为Integer.MAX_VALUE，它使用的是SynchronousQueue，也就是说来了任务就创建线程运行，当线程空闲超过60秒，就销毁线程。

2、代码

```java
/**
 * 线程池
 * Arrays
 * Collections
 * Executors
 */
public class MyThreadPoolDemo01 {
   public static void main(String[] args) {
       //List list = new ArrayList();
       //List list = Arrays.asList("a","b");

      /**
       * 1、创建线程池的方式，使用工具类Executors（Fixed：固定的）
       * 2、一个线程池中有5个受理（工作）线程（已经new好了），
       * 类似一个银行有5个受理窗口。
       * 3、主要是 ThreadPoolExecutor,我们拿到这个类就可以获得池子。
       */
//    ExecutorService threadPool = Executors.newFixedThreadPool(5);
      //一池1个工作线程，类似一个银行有1个受理窗口。 pool-1-thread-1  办理业务
//    ExecutorService threadPool = Executors.newSingleThreadExecutor();
      //一池N线程，可扩容，可伸缩。
      ExecutorService threadPool = Executors.newCachedThreadPool();

      try {
         /**
          * 1、模拟有10个顾客过来银行办理业务（此时会有10个请求），
          * 目前池子中有5个工作人员提供服务。
          * 2、控制台输出：pool-1-thread-1 办理业务
          * 线程可以被复用。（只要有线程用完了，被释放，可以再次使用）
          */
         for (int i = 1; i <= 10; i++) {
            threadPool.execute(() -> {
               System.out.println(Thread.currentThread().getName() + "\t办理业务");
            });
         }
      } catch (Exception e) {
         e.printStackTrace();
      } finally {
         threadPool.shutdown(); //线程池用完了之后要关闭
      }
   }
}
```

## 4、ThreadPoolExecutor底层原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085029.png" alt="image-20200811180709989" style="zoom:80%;" />

## 5、线程池的7大参数

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085030.png" alt="image-20200811181410410" style="zoom:80%;" />

1、corePoolSize：线程池中的常驻核心线程数（银行常驻值班人员）。

2、maximumPoolSize：线程池中能够容纳同时执行的最大线程数（有上限），此值必须大于等于1。

3、keepAliveTime：多余的空闲线程的存活时间当前池中线程数量超过`corePoolSize`时，当空闲时间达到`keepAliveTime`时，也没有新的请求，多余线程会被销毁直到只剩下`corePoolSize`个线程为止（即线程池不但可以扩容，而且也可以缩容线程数）。

​		`keepAliveTime`属性需要集合`unit`单位一起使用。

4、`unit：keepAliveTime`的单位。

5、workQueue：任务队列，被提交但尚未被执行的任务。线程池为啥存在阻塞队列？可以理解为银行的候客区。

6、threadFactory：表示生成线程池中工作线程的线程工厂，用于创建线程，一般默认的即可。可以理解为银行的统一胸卡。

7、handler：拒绝策略，表示当队列满了，并且工作线程大于等于线程池的最大线程数（maximumPoolSize）时如何来拒绝请求执行的runnable的策略。可以理解为此时银行的候客区也满了。

## 6、线程池底层工作原理

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085031.png" alt="image-20200811183037384" style="zoom:80%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085032.png" alt="image-20200811183050851" style="zoom:150%;" />

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085033.png" alt="image-20200811185007125" style="zoom:80%;" />

   以下很重要：

1、在创建了线程池后，开始等待请求。

2、当调用execute()方法添加一个请求任务时，线程池会做出如下判断：

（1）如果正在运行的线程数量小于`corePoolSize`，那么马上创建线程运行这个任务；

（2）如果正在运行的线程数量大于或等于`corePoolSize`，那么将这个任务放入队列；

  （3）如果这个时候队列满了且正在运行的线程数量还小于`maximumPoolSize`，那么还是要创建**非核心线程**立刻运行这个任务；

  （4）如果队列满了且正在运行的线程数量大于或等于`maximumPoolSize`，那么线程池会启动饱和拒绝策略来执行。

3、当一个线程完成任务时，它会从队列中取下一个任务来执行。

4、当一个线程无事可做超过一定的时间（`keepAliveTime`）时，线程会判断：
		如果当前运行的线程数大于`corePoolSize`，那么这个线程就被停掉。所以线程池的所有任务完成后，它最终会收缩到`corePoolSize`的大小。

##  7、生产中如何设置参数

1、在工作中单一的/固定数的/可变的三种创建线程池的方法哪个用的多？

​		答案是一个都不用，我们工作中只能使用自定义的。Executors中JDK已经给你提供了，为什么不用？

<img src="https://gitee.com/whlgdxlkl/my-picture-bed/raw/master/uploadPicture/20200901085034.png" alt="image-20200811185111165" style="zoom:150%;" />

2、自定义线程池

```java
public class DiyThreadPoolDemo01 {
	public static void main(String[] args) {
		//打印出12个cpu核数		System.out.println(Runtime.getRuntime().availableProcessors());
		int number = Runtime.getRuntime().availableProcessors();
		ExecutorService threadPool = new ThreadPoolExecutor(
				2,
//				5,
				number, //替换成集群核数
				2L,
				TimeUnit.SECONDS,
				new ArrayBlockingQueue<Runnable>(3),
				Executors.defaultThreadFactory(),
				//new ThreadPoolExecutor.AbortPolicy()
				//new ThreadPoolExecutor.CallerRunsPolicy()
				//new ThreadPoolExecutor.DiscardOldestPolicy()
				new ThreadPoolExecutor.DiscardOldestPolicy()
		);
		//10个顾客请求
		try {
			for (int i = 1; i <= 10; i++) {
				threadPool.execute(() -> {
					System.out.println(Thread.currentThread().getName() + "\t 办理业务");
				});
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			threadPool.shutdown();
		}
	}
}
```

3、线程池的拒绝策略

等待队列已经排满了，再也塞不下新任务了同时，线程池中的max线程也达到了，无法继续为新任务服务，这个是时候我们就需要拒绝策略机制合理的处理这个问题。我们可以使用JDK内置的拒绝策略（该内置策略均实现了`RejectedExecutionHandle`接口）：

（1）AbortPolicy(默认)：直接抛出`RejectedExecutionException`异常阻止系统正常运行。

（2）CallerRunsPolicy：“调用者运行”一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量。

（3）DiscardOldestPolicy：抛弃队列中等待最久的任务，然后把当前任务加入队列中尝试再次提交当前任务。

（4）DiscardPolicy：该策略默默地丢弃无法处理的任务，不予任何处理也不抛出异常。如果允许任务丢失，这是最好的一种策略。

