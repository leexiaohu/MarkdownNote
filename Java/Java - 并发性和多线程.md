# Java 并发编程

[TOC]

### 多线程的实现方法

> Java 中实现多线程有两种方法：继承 Thread 类、实现 Runnable 接口，在程序开发中只要是多线程，肯定永远以实现 Runnable 接口为主，因为实现 Runnable 接口相比继承 Thread 类有如下优势：

- 可以避免由于 Java 的单继承特性而带来的局限；
- 增强程序的健壮性，代码能够被多个线程共享，代码与数据是独立的；
- 适合多个相同程序代码的线程区处理同一资源的情况。

### 线程中断
#### 使用 interrupt()中断线程

当一个线程运行时，另一个线程可以调用对应的 Thread 对象的 `interrupt`()方法来中断它，该方法只是在目标线程中设置一个标志，表示它已经被中断，并立即返回。这里需要注意的是，如果只是单纯的调用 `interrupt`()方法，线程并没有实际被中断，会继续往下执行。

#### 待决中断

在上面的例子中，sleep()方法的实现检查到休眠线程被中断，它会相当友好地终止线程，并抛出 `InterruptedException` 异常。另外一种情况，如果线程在调用 sleep()方法前被中断，那么该中断称为待决中断，它会在刚调用 sleep()方法时，立即抛出 `InterruptedException` 异常。

#### 使用 isInterrupted()方法判断中断状态

可以在 Thread 对象上调用 `isInterrupted`()方法来检查任何线程的中断状态。这里需要注意：线程一旦被中断，`isInterrupted`()方法便会返回 true，而一旦 sleep()方法抛出异常，它将清空中断标志，此时`isInterrupted`()方法将返回 false。

[Header] 使用 Thread.interrupted()方法判断中断状态

可以使用 `Thread.interrupted`()方法来检查当前线程的中断状态（并隐式重置为 false）。又由于它是静态方法，因此**不能在特定的线程上使用，而只能报告调用它的线程的中断状态**，如果线程被中断，而且中断状态尚不清楚，那么，这个方法返回true。与 `isInterrupted`()不同，它将自动重置中断状态为 false，第二次调用 `Thread.interrupted`()方法，总是返回 false，除非中断了线程。


#### 补充 yield 和 join 方法的使用

join 方法用线程对象调用，如果在一个线程 A 中调用另一个线程 B 的 join 方法，线程 A 将会等待线程 B 执行完毕后再执行。
yield 可以直接用 Thread 类调用，yield 让出 CPU 执行权给同等级的线程，如果没有相同级别的线程在等待 CPU 的执行权，则该线程继续执行。

### 线程挂起、恢复与终止

不要使用线程提供的方法，应该使用`标志位`的方式

### 守护线程与线程阻塞

#### 守护线程

Java 中有两类线程：`User Thread(用户线程)、Daemon Thread(守护线程)`

用户线程即运行在前台的线程，而守护线程是运行在后台的线程。 守护线程作用是为其他前台线程的运行提供便利服务，而且仅在普通、非守护线程仍然运行时才需要，比如垃圾回收线程就是一个守护线程。
另外有几点需要注意：

- setDaemon(true)必须在调用线程的 start()方法之前设置，否则会跑出 IllegalThreadStateException 异常。
- 在守护线程中产生的新线程也是守护线程。
- 不要认为所有的应用都可以分配给守护线程来进行服务，比如读写操作或者计算逻辑。

####  线程阻塞

线程可以阻塞于==四种==状态：

- 当线程执行 Thread.sleep()时，它一直阻塞到指定的毫秒时间之后，或者阻塞被另一个线程打断；
- 当线程碰到一条 wait()语句时，它会一直阻塞到接到通知（notify()）、被中断或经过了指定毫秒时间为止（若制定了超时值的话）
- 线程阻塞与不同 I/O 的方式有多种。常见的一种方式是 InputStream的read()方法，该方法一直阻塞到从流中读取一个字节的数据为止，它可以无限阻塞，因此不能指定超时时间；
- 线程也可以阻塞等待获取某个对象锁的排他性访问权限（即等待获得 synchronized 语句必须的锁时阻塞）。

### Volatile关键字

> Java 语言规范中指出：为了获得最佳速度，允许线程保存共享成员变量的私有拷贝，而且只当线程进入或者离开同步代码块时才将私有拷贝与共享内存中的原始值进行比较。
> volatile 是一种稍弱的同步机制，在访问 volatile 变量时不会执行加锁操作，也就不会执行线程阻塞，因此 volatilei 变量是一种比 synchronized 关键字更轻量级的同步机制。

### synchronized 关键字

> 采用 synchronized 修饰符实现的同步机制叫做互斥锁机制，它所获得的锁叫做互斥锁。每个对象都有一个 monitor (锁标记)，当线程拥有这个锁标记时才能访问这个资源，没有锁标记便进入锁池。任何一个对象系统都会为其创建一个互斥锁，这个锁是为了分配给线程的，防止打断原子操作。每个对象的锁只能分配给一个线程，因此叫做互斥锁。

synchronized 特性：

1. ==类的每个实例==都有自己的==对象级别锁==。当一个线程访问实例对象中的 synchronized 同步代码块或同步方法时，该线程便获取了该实例的对象级别锁，其他线程这时如果要访问 synchronized 同步代码块或同步方法，便需要阻塞等待，直到前面的线程从同步代码块或方法中退出，释放掉了该对象级别锁。

2. 访问同一个类的不同实例对象中的同步代码块，不存在阻塞等待获取对象锁的问题，因为它们获取的是各自实例的对象级别锁，相互之间没有影响。

3. 持有一个对象级别锁不会阻止该线程被交换出来，也不会阻塞其他线程访问同一示例对象中的非 synchronized 代码。当一个线程 A 持有一个对象级别锁（即进入了 synchronized 修饰的代码块或方法中）时，线程也有可能被交换出去，此时线程 B 有可能获取执行该对象中代码的时间，但它只能执行非同步代码（没有用 synchronized 修饰），当执行到同步代码时，便会被阻塞，此时可能线程规划器又让 A 线程运行，A 线程继续持有对象级别锁，当 A 线程退出同步代码时（即释放了对象级别锁），如果 B 线程此时再运行，便会获得该对象级别锁，从而执行 synchronized 中的代码。

4. 持有对象级别锁的线程会让其他线程阻塞在所有的 synchronized 代码外。例如，在一个类中有三个synchronized 方法 a，b，c，当线程 A 正在执行一个实例对象 M 中的方法 a 时，它便获得了该对象级别锁，那么其他的线程在执行同一实例对象（即对象 M）中的代码时，便会在所有的 synchronized 方法处阻塞，即在方法 a，b，c 处都要被阻塞，等线程 A 释放掉对象级别锁时，其他的线程才可以去执行方法 a，b 或者 c 中的代码，从而获得该对象级别锁。

5. 使用 synchronized（obj）同步语句块，可以获取指定对象上的对象级别锁。obj 为对象的引用，如果获取了 obj 对象上的对象级别锁，在并发访问 obj 对象时时，便会在其 synchronized 代码处阻塞等待，直到获取到该 obj对象的对象级别锁。当 obj 为 this 时，便是获取当前对象的对象级别锁。

6. == 类级别锁==被特定类的所有示例共享，它用于控制对 static 成员变量以及 static 方法的并发访问。具体用法与对象级别锁相似。

7. 互斥是实现同步的一种手段，临界区、互斥量和信号量都是主要的互斥实现方式。synchronized 关键字经过编译后，会在同步块的前后分别形成 monitorenter 和 monitorexit 这两个字节码指令。根据虚拟机规范的要求，在执行 monitorenter 指令时，首先要尝试获取对象的锁，如果获得了锁，把锁的计数器加 1，相应地，在执行 monitorexit 指令时会将锁计数器减 1，当计数器为 0 时，锁便被释放了。由于 synchronized 同步块对同一个线程是可重入的，因此一个线程可以多次获得同一个对象的互斥锁，同样，要释放相应次数的该互斥锁，才能最终释放掉该锁。

#### synchronized 的个一重要作用：内存可见性

加锁（synchronized 同步）的功能不仅仅局限于互斥行为，同时还存在另外一个重要的方面：内存可见性。我们不仅希望防止某个线程正在使用对象状态而另一个线程在同时修改该状态，而且还希望确保当一个线程修改了对象状态后，其他线程能够看到该变化。它是根据synchronized 第**4**个特性来达到目的。

####  sychronized 语句块

用sychronized修饰方法是有一定弊端的，比如方法内有一部分是耗时的操作，这部分不需要同步，而只有一小部分代码需要同步，则若把sychronized 修饰在方法外面，则必须等方法中所有操作执行完别的进程才能进入，这样效率明显很低，若只把sychronized修饰每个特殊的代码块，则别的线程会执行同步代码块外的内容，而只是阻塞同步代码块的内容。

### 多线程环境下安全使用集合 API

在集合 API 中，最初设计的 Vector 和 Hashtable 是多线程安全的。例如：对于 Vector 来说，用来添加和删除元素的方法是同步的。如果只有一个线程与 Vector 的实例交互，那么，要求获取和释放对象锁便是一种浪费，另外在不必要的时候如果滥用同步化，也有可能会带来死锁。因此，对于更改集合内容的方法，没有一个是同步化的。集合本质上是非多线程安全的，当多个线程与集合交互时，为了使它多线程安全，必须采取额外的措施。

在 Collections 类中有多个静态方法，它们可以获取通过同步方法封装非同步集合而得到的集合：

- public static Collection synchronizedCollention(Collection c)

- public static List synchronizedList(list l)

- public static Map synchronizedMap(Map m)

- public static Set synchronizedSet(Set s)

- public static SortedMap synchronizedSortedMap(SortedMap sm)

- public static SortedSet synchronizedSortedSet(SortedSet ss)

注意，ArrayList 实例马上封装起来，不存在对未同步化 ArrayList 的直接引用（即直接封装匿名实例）。这是一种最安全的途径。

下面给出一段多线程中安全遍历集合元素的示例。我们使用 Iterator 逐个扫描 List 中的元素，在多线程环境中，当遍历当前集合中的元素时，一般希望阻止其他线程添加或删除元素。安全遍历的实现方法如下：

```java
import java.util.*;  

public class SafeCollectionIteration extends Object {  
    public static void main(String[] args) {  
        //为了安全起见，仅使用同步列表的一个引用，这样可以确保控制了所有访问  
        //集合必须同步化，这里是一个List  
        List wordList = Collections.synchronizedList(new ArrayList());  

        //wordList中的add方法是同步方法，会获取wordList实例的对象锁  
        wordList.add("Iterators");  
        wordList.add("require");  
        wordList.add("special");  
        wordList.add("handling");  

        //获取wordList实例的对象锁，  
        //迭代时，阻塞其他线程调用add或remove等方法修改元素  
        synchronized ( wordList ) {  
            Iterator iter = wordList.iterator();  
            while ( iter.hasNext() ) {  
                String s = (String) iter.next();  
                System.out.println("found string: " + s + ", length=" + s.length());  
            }  
        }  
    }  
}  
```
### 可重入内置锁

每个 Java 对象都可以用做一个实现同步的锁，这些锁被称为内置锁或监视器锁。线程在进入同步代码块之前会自动获取锁，并且在退出同步代码块时会自动释放锁。获得内置锁的唯一途径就是进入由这个锁保护的同步代码块或方法。

当某个线程请求一个由其他线程持有的锁时，发出请求的线程就会阻塞。然而，由于内置锁是可重入的，因此如果摸个线程试图获得一个已经由它自己持有的锁，那么这个请求就会成功。“重入”意味着获取锁的操作的粒度是“线程”，而不是调用。重入的一种实现方法是，为每个锁关联一个获取计数值和一个所有者线程。当计数值为 0 时，这个锁就被认为是没有被任何线程所持有，当线程请求一个未被持有的锁时，JVM 将记下锁的持有者，并且将获取计数值置为 1，如果同一个线程再次获取这个锁，计数值将递增，而当线程退出同步代码块时，计数器会相应地递减。当计数值为 0 时，这个锁将被释放。

同一个线程在调用本类中其他 synchronized 方法/块或父类中的 synchronized 方法/块时，都不会阻碍该线程地执行，因为互斥锁时可重入的。

### 线程间的协作

##### wait、notify、notifyAll

###### public final void wait() throws InterruptedException,IllegalMonitorStateException

该方法用来将当前线程置入休眠状态，直到接到通知或被中断为止。在调用 wait()之前，线程必须要获得该对象的对象级别锁，即只能在同步方法或同步块中调用 wait()方法。进入 wait()方法后，当前线程释放锁。在从 wait()返回前，线程与其他线程竞争重新获得锁。如果调用 wait()时，没有持有适当的锁，则抛出 IllegalMonitorStateException，它是 RuntimeException 的一个子类，因此，不需要 try-catch 结构。

###### public final native void notify() throws IllegalMonitorStateException

该方法也要在同步方法或同步块中调用，即在调用前，线程也必须要获得该对象的对象级别锁，的如果调用 notify()时没有持有适当的锁，也会抛出 IllegalMonitorStateException。

该方法用来通知那些可能等待该对象的对象锁的其他线程。如果有多个线程等待，则线程规划器任意挑选出其中一个 wait()状态的线程来发出通知，并使它等待获取该对象的对象锁（notify 后，当前线程不会马上释放该对象锁，wait 所在的线程并不能马上获取该对象锁，要等到程序退出 synchronized 代码块后，当前线程才会释放锁，wait所在的线程也才可以获取该对象锁），但不惊动其他同样在等待被该对象notify的线程们。当第一个获得了该对象锁的 wait 线程运行完毕以后，它会释放掉该对象锁，此时如果该对象没有再次使用 notify 语句，则即便该对象已经空闲，其他 wait 状态等待的线程由于没有得到该对象的通知，会继续阻塞在 wait 状态，直到这个对象发出一个 notify 或 notifyAll。

###### public final native void notifyAll() throws IllegalMonitorStateException

notifyAll 使所有原来在该对象上 wait 的线程统统退出 wait 的状态，变成等待获取该对象上的锁，一旦该对象锁被释放（notifyAll 线程退出调用了 notifyAll 的 synchronized 代码块的时候），他们就会去竞争。如果其中一个线程获得了该对象锁，它就会继续往下执行，在它退出 synchronized 代码块，释放锁后，其他的已经被唤醒的线程将会继续竞争获取该锁，一直进行下去，直到所有被唤醒的线程都执行完毕。

### 生产者—消费者模型
生产者消费者问题是线程模型中的经典问题：生产者和消费者在同一时间段内共用同一存储空间，生产者向空间里生产数据，而消费者取走数据。

```java
class Info{ // 定义信息类  
    private String name = "name";//定义name属性，为了与下面set的name属性区别开  
    private String content = "content" ;// 定义content属性，为了与下面set的content属性区别开  
    private boolean flag = true ;   // 设置标志位,初始时先生产  
    public synchronized void set(String name,String content){  
        while(!flag){  
            try{  
                super.wait() ;  
            }catch(InterruptedException e){  
                e.printStackTrace() ;  
            }  
        }  
        this.setName(name) ;    // 设置名称  
        try{  
            Thread.sleep(300) ;  
        }catch(InterruptedException e){  
            e.printStackTrace() ;  
        }  
        this.setContent(content) ;  // 设置内容  
        flag  = false ; // 改变标志位，表示可以取走  
        super.notify();  
    }  
    public synchronized void get(){  
        while(flag){  
            try{  
                super.wait() ;  
            }catch(InterruptedException e){  
                e.printStackTrace() ;  
            }  
        }  
        try{  
            Thread.sleep(300) ;  
        }catch(InterruptedException e){  
            e.printStackTrace() ;  
        }  
        System.out.println(this.getName() +   
            " --> " + this.getContent()) ;  
        flag  = true ;  // 改变标志位，表示可以生产  
        super.notify();  
    }  
    public void setName(String name){  
        this.name = name ;  
    }  
    public void setContent(String content){  
        this.content = content ;  
    }  
    public String getName(){  
        return this.name ;  
    }  
    public String getContent(){  
        return this.content ;  
    }  
}  
class Producer implements Runnable{ // 通过Runnable实现多线程  
    private Info info = null ;      // 保存Info引用  
    public Producer(Info info){  
        this.info = info ;  
    }  
    public void run(){  
        boolean flag = true ;   // 定义标记位  
        for(int i=0;i<10;i++){  
            if(flag){  
                this.info.set("姓名--1","内容--1") ;    // 设置名称  
                flag = false ;  
            }else{  
                this.info.set("姓名--2","内容--2") ;    // 设置名称  
                flag = true ;  
            }  
        }  
    }  
}  
class Consumer implements Runnable{  
    private Info info = null ;  
    public Consumer(Info info){  
        this.info = info ;  
    }  
    public void run(){  
        for(int i=0;i<10;i++){  
            this.info.get() ;  
        }  
    }  
}  
public class ThreadCaseDemo03{  
    public static void main(String args[]){  
        Info info = new Info(); // 实例化Info对象  
        Producer pro = new Producer(info) ; // 生产者  
        Consumer con = new Consumer(info) ; // 消费者  
        new Thread(pro).start() ;  
        //启动了生产者线程后，再启动消费者线程  
        try{  
            Thread.sleep(500) ;  
        }catch(InterruptedException e){  
            e.printStackTrace() ;  
        }  

        new Thread(con).start() ;  
    }  
}  
```
