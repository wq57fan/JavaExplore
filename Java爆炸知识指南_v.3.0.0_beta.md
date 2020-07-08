# Java爆炸知识指南

[TOC]

## 一、Java 基础

### 1、== 和 equals 的区别是什么？

#### 1.1 == 解读

对于基本类型和引用类型 == 的作用效果是不同的，如下所示：

* 基本类型：比较的是值是否相同；
* 引用类型：比较的是引用是否相同；

代码示例：

```java
String x = "string";
String y = "string";
String z = new String("string");
System.out.println(x==y); // true
System.out.println(x==z); // false
System.out.println(x.equals(y)); // true
System.out.println(x.equals(z)); // true
```

代码解读：因为 x 和 y 指向的是同一个引用，所以 == 也是 true，而 new String()方法则重新开辟了内存空间，所以 == 结果为 false，而 equals 比较的一直是值，所以结果都为 true。

#### 1.2 equals 解读

equals 本质上就是 ==，只不过 String 和 Integer 等重写了 equals 方法，把它变成了**值比较**。

> 总结 :
>
> 1. == 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；
>
> 2. equals 默认情况下是引用比较，只是很多类重写了 equals 方法，比如 String(String 重写了 Object 的 equals 方法，把引用比较改成了值比较)、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

### 2、Java 中操作字符串都有哪些类？区别？

操作字符串的类有：String、StringBuffer、StringBuilder。

String 和 StringBuffer、StringBuilder 的区别在于 ：

* String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象；
*  StringBuffer、StringBuilder 可以在原有对象的基础上进行操作，所以在**经常改变字符串内容**的情况下最好不要使用 String。

StringBuffer 和 StringBuilder 最大的区别在于：

* StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 StringBuffer。所以在**单线程环境**下推荐使用 StringBuilder，多线程环境下推荐使用 StringBuffer。

### 3、String str="i"与 String str=new String("i")一样吗？

不一样，因为内存的分配方式不一样。

* String str="i"的方式，java 虚拟机会将其分配到**常量池**中；
*  String str=new String("i") 则会被分到**堆内存**中。

### 4、深拷贝和浅拷贝区别是什么？

浅拷贝：只是复制了对象的引用地址，两个对象指向同一个内存地址，所以修改其中任意的值，另一个值都会随之变化。例如：assign()。

深拷贝：是将对象及值复制过来，两个对象修改其中任意的值另一个值不会改变。例如：JSON.parse()和JSON.stringify()，但是此方法无法复制函数类型。

### 5、抽象类和接口的区别？

抽象类：是用来捕捉子类的通用特性的，只能被用作于子类的超类。通过Abstract关键字进行声明。他可以有构造器、不能被实例化，其他和普通Java类没区别。子类用extends来继承父类，如果子类不是抽象类则需要实现该抽象类中所有声明的方法。

接口：是抽象方法的集合，本身不能做任何事。接口通过Interface关键字进行声明。子类用implements来实现接口，要实现接口中所有声明的方法。

### 6、final关键字是什么？

final关键字可以用来修饰类、方法和变量：

1. 修饰类

    当用final修饰一个类时，表明这个类不能被继承。

2. 修饰方法

   当用于修饰方法时，此方法不能被重写。

3. 修饰变量

    final成员变量表示常量，只能被赋值一次，赋值后值不再改变。

### 7、泛型？

> 什么是泛型？

泛型是类和接口的一种扩展机制：**数据类型参数化**，也叫类型参数。泛型把类型明确的工作推迟到创建对象或调用方法的时候才去明确的特殊的类型。其中：<数据类型>只能是引用类型。

> 为什么要用泛型？

早期Java是使用Object来代表任意类型的，但是向下转型有强转的问题，这样程序就不安全。如果没有泛型，集合会怎么样：

* Collection、Map集合对元素的类型是没有任何限制的。**本来Collection集合装载的是全部的Dog对象，但是外边把Cat对象存储到集合中，是没有任何语法错误的。**
* 把对象扔进集合中，集合是不知道元素的类型是什么的，仅仅知道是Object。**因此在get()的时候，返回的是Object。外边获取该对象，还需要强制转换。**

但是Java的泛型是伪泛型，这是因为Java在编译期间，所有的泛型信息都会被擦掉。

> 什么是类型擦除？

泛型是**提供给javac编译器使用的**，它用于限定集合的输入类型，让编译器在源代码级别上，即挡住向集合中插入非法数据。但编译器编译完带有泛形的java程序后，**生成的class文件中将不再带有泛形信息**，以此使程序运行效率不受到影响，这个过程称之为“擦除”。

例子：

```java
// 指定泛型为String
List<String> list1 = new ArrayList<>();
// 指定泛型为Integer
List<Integer> list2 = new ArrayList<>();
System.out.println(list1.getClass() == list2.getClass()); // true
```

上面的判断结果是true。代表了两个传入了不同泛型的List最终都编译成了ArrayList，成为了同一种类型，原来的泛型参数String和Integer被擦除掉了。

### 8、lambda表达式？

从Java 8开始，Lambda (闭包) 允许把函数作为参数传递进方法中。可以取代大部分的匿名内部类，写出更优雅的 Java 代码，尤其在集合的遍历和其他集合操作中，可以极大地优化代码结构。

语法：

```java
// 1. 不需要参数,返回值为 5  
() -> 5  
    
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```

## 二、容器

### 1、Java 容器都有哪些？

![image-20200526133709612](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200526133709612.png)

### 2、Collection 和 Collections 有什么区别？

* `java.util.Collection` 是一个集合**接口**（集合类的一个顶级接口）。**它提供了对集合对象进行基本操作的通用接口方法。**Collection接口在Java 类库中有很多具体的实现。
* `Collections` 则是集合类的一个**工具类**，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。

### 3、List、Set、Map 之间的区别是什么？

![image-20200526133836081](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200526133836081.png)

### 4、HashMap 、Hashtable、ConcurrentHashMap 有什么区别？

* hashMap去掉了hashTable的contains方法，但是加上了containsValue()和containsKey()方法。
* hashTable同步的，而hashMap是非同步的，效率上比hashTable要高。
* hashMap允许空键值，而hashTable不允许。
* hashMap线程不安全，hashTable线程安全。
* hashTable低效主要是因为所有访问hashTable的线程都争夺一把锁。如果容器有很多把锁，每一把锁控制容器中的一部分数据，那么当多个线程访问容器里的不同部分的数据时，线程之前就不会存在锁的竞争，这样就可以有效的提高并发的访问效率。这也正是ConcurrentHashMap使用的**分段锁技术**。将ConcurrentHashMap容器的数据分段存储，每一段数据分配一个Segment（锁），当线程占用其中一个Segment时，其他线程可正常访问其他段数据。具有了hashTable的安全性，也有hashMap的高速度。

### 5、HashMap 的实现原理？

HashMap概述： HashMap是基于哈希表的Map接口的**非同步**实现。此类不保证映射的顺序，允许使用null值和null键。HashMap是一个集合，键值对的集合，源码中每个节点用Node<K,V>表示：

```java
static class Node<K,V> implements Map.Entry<K,V> {
   final int hash;
   final K key;
   V value;
   Node<K,V> next;
```

HashMap的数据结构： 为一个“**链表散列**”的数据结构，即**数组+链表/红黑树**。当我们往Hashmap中put元素时，首先根据key的hashcode重新计算hash值，根绝hash值得到这个元素在数组中的位置(下标)，如果该数组在该位置上已经存放了其他元素，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放入链尾。如果数组中该位置没有元素，就直接将该元素放到数组的该位置上。这种数据结构的查询、插入、删除效率都很高。

![image-20200603163523676](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200603163523676.png)

需要注意：JDK 1.8中对HashMap的实现做了优化，当链表中的节点数据超过八个之后，该链表会转为**红黑树**来提高查询效率，从原来的O(n)到O(logn)。

### 6、HashSet 的实现原理？

* HashSet底层由HashMap实现

* HashSet的值存放于HashMap的key上

* HashMap的value统一为PRESENT

### 7、ArrayList 和 LinkedList 的区别是什么？

* ArrrayList底层的数据结构是数组，支持随机访问，而 LinkedList 的底层数据结构是双向循环链表，不支持随机访问。

* 使用下标访问一个元素，ArrayList 的时间复杂度是 O(1)，而 LinkedList 是 O(n)。

### 8、ArrayList 和 Vector 的区别是什么？

* Vector是**同步**的，而ArrayList不是。然而，如果你寻求在迭代的时候对列表进行改变，你应该使用CopyOnWriteArrayList。 

* ArrayList比Vector快，因为Vector有同步，不会过载。 

### 9、Array 和 ArrayList 有何区别？

* Array可以容纳基本类型和对象，而ArrayList只能容纳**对象**。
* Array是指定大小的，而ArrayList**大小是不固定的**。 
* Array没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。

### 10、在ArrayList的循环中删除元素，会不会出现问题？

会出现问题。如下：

1. for循环

   在删除重复但不连续的元素时是正常的，但在删除重复且连续的元素时，会出现删除不完全的问题。

2. ArrayList的remove()方法

   产生并发修改异常ConcurrentModificationException。

3. 迭代器的remove()方法

   可以删除重复的元素，但不推荐。

测试代码如下：

```java
public class ConcurrentModificationException_Test {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<String>();
        list.add("aa");
        list.add("bb");
        list.add("bb");
        list.add("aa");
        list.add("cc");
        // 删除元素 bb
        remove(list, "bb");
        for (String str : list) {
            System.out.println(str);
        }
    }

    public static void remove(ArrayList<String> list, String elem) {
        // 五种不同的循环及删除方法
        // 方法一：普通for循环正序删除，删除过程中元素向左移动，不能删除重复的元素
//        for (int i = 0; i < list.size(); i++) {
//            if (list.get(i).equals(elem)) {
//                list.remove(list.get(i));
//            }
//        }
        // 方法二：普通for循环倒序删除，删除过程中元素向左移动，可以删除重复的元素
//        for (int i = list.size() - 1; i >= 0; i--) {
//            if (list.get(i).equals(elem)) {
//                list.remove(list.get(i));
//            }
//        }
        // 方法三：增强for循环删除，使用ArrayList的remove()方法删除，产生并发修改异常 ConcurrentModificationException
//        for (String str : list) {
//            if (str.equals(elem)) {
//                list.remove(str);
//            }
//        }
        // 方法四：迭代器，使用ArrayList的remove()方法删除，产生并发修改异常 ConcurrentModificationException
//        Iterator iterator = list.iterator();
//        while (iterator.hasNext()) {
//            if(iterator.next().equals(elem)) {
//                list.remove(iterator.next());
//            }
//        }

        // 方法五：迭代器，使用迭代器的remove()方法删除，可以删除重复的元素，但不推荐
//        Iterator iterator = list.iterator();
//        while (iterator.hasNext()) {
//            if(iterator.next().equals(elem)) {
//                iterator.remove();
//            }
//        }
    }
}
```

## 三、多线程

### 1、线程和进程的区别？

进程是程序运行和资源分配的基本单位，一个程序至少有一个进程，一个进程至少有一个线程。

进程在执行过程中拥有独立的内存单元，而多个线程共享内存资源，减少切换次数，从而效率更高。

线程是进程的一个实体，是cpu调度和分派的基本单位，同一进程中的多个线程之间可以并发执行。

### 2、创建线程有哪几种方式？

1. **继承Thread类**创建线程类
   定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。因此把run()方法称为执行体。创建Thread子类的实例，即创建了线程对象。调用线程对象的start()方法来启动该线程。
2. **实现Runnable接口**创建线程类
   定义Runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。创建 Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象。调用线程对象的start()方法来启动该线程。
3. 通过**Callable和Future**创建线程
   创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程执行体，并且有返回值。创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值。使用FutureTask对象作为Thread对象的target创建并启动新线程。调用FutureTask对象的get()方法来获得子线程执行结束后的返回值。

### 3、线程有哪些状态？

线程通常都有五种状态，创建、就绪、运行、阻塞和死亡。

* 创建状态。在生成线程对象，但没有调用该对象的start方法时。
* 就绪状态。当调用了线程对象的start方法之后，该线程就进入了就绪状态，但是此时线程调度程序还没有把该线程设置为当前线程，此时处于就绪状态。在线程运行之后，从等待或者睡眠中回来之后，也会处于就绪状态。
* 运行状态。设置为当前线程后，进入该状态后线程开始运行run函数当中的代码。
* 阻塞状态。线程正在运行的时候被暂停，通常是为了等待某个时间的发生(比如说某项资源就绪)之后再继续运行。**sleep，suspend，wait**等方法都可以导致线程阻塞。
* 死亡状态。如果一个线程的run方法执行结束或者调用stop方法后，该线程就会死亡。

### 4、sleep() 和 wait() 有什么区别？

`sleep()`：sleep()是线程类（Thread）的静态方法，让调用线程进入睡眠状态，让出执行机会给其他线程，等到休眠时间结束后，线程进入就绪状态和其他线程一起竞争cpu的执行时间。当一个synchronized块中调用了sleep() 方法，线程虽然进入休眠，但是对象的**锁没有被释放**，其他线程依然无法访问这个对象。

`wait()`：wait()是Object类的方法，当一个线程执行到wait方法时，它就进入到一个和该对象相关的等待池，同时**释放对象的锁**，使得其他线程能够访问，可以通过notify，notifyAll来唤醒等待的线程。

### 5、线程的 run()和 start()有什么区别？

`run()`：每个线程都是通过Thread对象所对应的方法run()来完成其操作的，方法run()称为**线程体**。通过调用Thread类的start()方法来启动一个线程。

`start()`：用启动一个线程，真正实现了多线程运行。这时无需等待run方法体代码执行完毕，可以直接继续执行下面的代码；这时此线程是处于就绪状态，并没有运行。然后通过此Thread类调用方法run()来完成其运行状态，run方法运行结束则 此线程终止。然后CPU再调度其它线程。

### 6、synchronized 和 volatile 的区别是什么？

* volatile本质是在告诉jvm当前变量在寄存器（工作内存）中的值是不确定的，需要从主存中读取；synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。

* volatile仅能使用在变量级别；synchronized则可以使用在变量、方法、和类级别的。

* volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized则可以保证变量的修改可见性和原子性。

* volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞。

* volatile标记的变量不会被编译器优化；synchronized标记的变量可以被编译器优化。

### 7、synchronized 和 Lock、ReentrantLock 区别是什么？

* synchronized是Java内置关键字，Lock、ReentrantLock是类；

* synchronized无法判断是否获取锁的状态，Lock可以判断是否获取到锁；ReentrantLock可以获取各种锁的信息；

* synchronized会自动释放锁。a. 线程执行完同步代码会释放锁；b. 线程执行过程中发生异常会释放锁)，Lock需在finally中手工释放锁（unlock()方法释放锁），否则容易造成线程死锁；

* 用synchronized的两个线程1和2，如果线程1获得锁且阻塞，线程2则会一直等待下去，而Lock锁就不一定会等待下去，如果尝试获取不到锁，线程可以不用一直等待就结束了；ReentrantLock可以对获取锁的等待时间进行设置，这样就避免了死锁。

### 8、线程池？

在Java编码的过程中，我们经常会创建一个线程来提高程序的执行效率，虽然这样实现起来很方便，但是会有一个问题：如果并发的线程数多，并且每个线程都是执行一个时间很短的任务就结束了，这样会造成频繁的创建和销毁线程从而导致降低系统的效率。

> 那么问题来了，有没有办法可用复用创建好的线程呢，也就是线程执行完一个任务后，不被销毁，继续执行其他的任务？

在Java可以通过**线程池**来实现这样的效果。

#### 8.1 Java中的ThreadPoolExecutor类

##### 8.1.1 ThreadPoolExecutor的重要参数

1. `corePoolSize`

   核心线程数核心线程会一直存活，即使没有任务需要执行。**当线程数小于核心线程数时，即使有线程空闲，线程池也会优先创建新线程处理。**设置allowCoreThreadTimeout=true（默认false）时，核心线程会超时关闭。

2. `queueCapacity`

   任务队列容量（阻塞队列）当核心线程数达到最大时，新任务会放在队列中排队等待执行。

3. `maxPoolSize`

   最大线程数当线程数>=corePoolSize，且任务队列已满时，线程池会创建新线程来处理任务，该字段决定了池中的最大线程数量：**maxPoolSize=corePoolSize+queueCapacity+新线程数**。当线程数=maxPoolSize，且任务队列已满时，线程池会拒绝处理任务而抛出异常。

4. `keepAliveTime`

   线程空闲时间当**线程空闲时间达到keepAliveTime时，线程会退出，直到线程数量=corePoolSize。**如果allowCoreThreadTimeout=true，则会直到线程数量=0。

5. `allowCoreThreadTimeout`

   允许核心线程超时。

6. `rejectedExecutionHandler`

   任务拒绝处理器在两种情况会拒绝处理任务：

   (1)当线程数已经达到maxPoolSize，切队列已满，会拒绝新任务。

   (2)当线程池被调用shutdown()后，会等待线程池里的任务执行完毕，再shutdown。如果在调用shutdown()和线程池真正shutdown之间提交任务，会拒绝新任务。线程池会调用rejectedExecutionHandler来处理这个任务。如果没有设置默认是AbortPolicy，会抛出异常。ThreadPoolExecutor类有几个内部实现类来处理这类情况：

   (a)AbortPolicy 丢弃任务，抛运行时异常。

   (b)CallerRunsPolicy 执行任务。

   (c)DiscardPolicy 忽视，什么都不会发生。

   (d)DiscardOldestPolicy 从队列中踢出最先进入队列（最后一个执行）的任务。实现RejectedExecutionHandler接口，也可自定义处理器。

##### 8.1.2 ThreadPoolExecutor执行过程

1. 当线程数小于核心线程数时，创建线程。

2. 当线程数大于等于核心线程数，且任务队列未满时，将任务放入任务队列。

3. 当线程数大于等于核心线程数，且任务队列已满时：

   (1)若线程数小于最大线程数，创建线程。

   (2)若线程数等于最大线程数，抛出异常，拒绝任务。

#### 8.2 Java中4种线程池

Java通过Executors提供四种线程池，分别为：

##### 8.2.1 `newCachedThreadPool`

创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。

##### 8.2.2 `newFixedThreadPool` 

创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。

##### 8.2.3 `newScheduledThreadPool` 

创建一个定长线程池，支持定时及周期性任务执行。

##### 8.2.4 `newSingleThreadExecutor` 

创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

### 9、锁？

> 为什么要锁？—为了解决多线程情况下，资源冲突和数据准确性的问题（线程安全）。

#### 9.1 锁的类型

> 线程要不要锁住资源？
>
> * 要 	 -> 悲观锁
> * 不要 -> 乐观锁

##### 9.1.1 乐观锁

乐观锁是一种乐观思想，即认为读多写少，遇到并发写的可能性低，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，采取在写时先读出当前版本号，然后加锁操作（比较跟上一次的版本号，如果一样则更新），如果失败则要重复**读-比较-写**的操作。

###### (1) 版本号机制

在数据表中加一个数据库版本号 version 字段，当数据被修改时，它的version 值会加1。

如：当线程A需要更新数据值时，在读取数据的同时也会读取 version 值，在提交更新时再读取一次，若刚才读取到的 version 值为当前数据库中的 version 值相等时才更新，否则重试更新操作，直到更新成功。

###### (2) CAS 机制

什么是CAS呢？Compare-and-Swap，即比较并替换，也有叫做Compare-and-Set的，比较并设置。

1. 比较：读取到了一个值A，在将其更新为B之前，检查原值是否仍为A（未被其他线程改动）。

2. 设置：如果是，将A更新为B，结束。如果不是，则什么都不做。

上面的两步操作是原子性的，可以简单地理解为瞬间完成，在CPU看来就是一步操作。

##### 9.1.2 悲观锁

悲观锁是就是悲观思想，即认为写多，遇到并发写的可能性高，每次去拿数据的时候都认为别人会修改，所以每次在读写数据的时候都会上锁，这样别人想读写这个数据就会 block 直到拿到锁。

> 锁住同步资源失败后，要不要阻塞？
>
> * 要阻塞 	 -> 悲观锁
> * 不要阻塞 -> 自旋锁，适应性自旋锁

###### (1) 自旋锁 

基于CAS实现自旋锁。在许多场景中，同步资源的锁定时间很短，为了这一小段时间去切换线程，线程挂起和恢复现场的花费可能会让系统得不偿失。如果物理机器有多个处理器，能够让两个或以上的线程同时并行执行，我们就可以让后面那个请求锁的线程不放弃CPU的执行时间，看看持有锁的线程是否很快就会释放锁。

而为了让当前线程“稍等一下”，我们需让当前线程进行自旋，如果在自旋完成后前面锁定同步资源的线程已经释放了锁，那么当前线程就可以不必阻塞而是直接获取同步资源，从而避免切换线程的开销。这就是自旋锁。

自旋等待虽然避免了线程切换的开销，但它要占用处理器时间。如果锁被占用的时间很短，自旋等待的效果就会非常好。反之，如果锁被占用的时间很长，那么自旋的线程只会白浪费处理器资源。

###### (2) 适应性自旋锁 

在JDK1.6引入了适应性自旋锁，适应性自旋锁意味着自旋的时间不再是固定的了。

#### 9.2 锁的状态

###### (1) 无锁

`指锁的状态，并且是针对Synchronized`

无锁没有对资源进行锁定，所有的线程都能访问并修改同一个资源，但同时只有一个线程能修改成功。

###### (2) 偏向锁

`指锁的状态，并且是针对Synchronized`

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁。降低获取锁的代价。

###### (3) 轻量级锁

`指锁的状态，并且是针对Synchronized`

轻量级锁是指当锁是偏向锁的时候，被另一个线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能。

###### (4) 重量级锁 

`指锁的状态，并且是针对Synchronized`

重量级锁是指当锁为轻量级锁的时候，另一个线程虽然是自旋，但自旋不会一直持续下去，当自旋一定次数的时候，还没有获取到锁，就会进入阻塞，该锁膨胀为重量级锁。重量级锁会让他申请的线程进入阻塞，性能降低。

#### 9.3 应用

##### 9.3.1 Synchronized

最典型的悲观锁。Synchronized 底层也是一个基于 CAS 操作的等待队列，会导致争用不到锁的线程进入阻塞状态，所以说它是 Java 语言中一个重量级的同步操纵，被称为重量级锁。当有很多线程竞争锁的时候，会引起 CPU 频繁的上下文切换导致效率很低。为了缓解上述性能问题，JVM 从1.5开始，引入了轻量锁与偏向锁，默认启用了自旋锁。

##### 9.3.2 Lock/ReentrantLock

Lock 是个接口，一个典型的实现类是 ReentrantLock，大量使用了 CAS+自旋。

ReenTrantLock 先尝试 CAS 乐观锁去获取锁，获取不到，才会转换为悲观锁。他的实现是一种自旋锁，通过循环调用 CAS 操作来实现加锁。它的性能比较好也是因为避免了使线程进入内核态的阻塞状态。

> 多个线程竞争时要不要排队？
>
> 要  ->  公平锁
>
> 先尝试插队，失败了再排队 -> 非公平锁

ReenTrantLock 默认使用非公平锁，也可以通过构造器来显示的指定使用公平锁。

### 10、JMM？

#### 10.1 什么是JMM？

Java Memory Model，Java内存模型。

> 他是做什么的？—解决共享对象的可见性问题：volatile。

作用：缓存一致性协议，用于定义数据读写的规则。

JMM定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存（Main Memory）中，每个线程都有一个私有的本地内存（Local Memory）—是从主内存中拷贝过来的。

如下图，CPU中运行的线程从主存中拷贝共享对象obj到它的CPU缓存，把对象obj的count变量改为2。但这个变更对运行在右边CPU中的线程不可见，因为这个更改还没有flush到主存中：要解决共享对象可见性这个问题，我们可以使用Java volatile关键字或者是加锁。

![image-20200526214133601](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200526214133601.png)

#### 10.2 JMM三大特性

1. 原子性

   * AtomicInteger；

   * **使用 synchronized(使用了同步锁) 保证操作的原子性。**

2. 可见性

   * **volatile，会强制将该变量自己和当时其他变量的状态都刷出缓存，同步回主内存；**
   * **synchronized，对一个变量执行 unlock 操作之前，必须把变量值同步回主内存；**
   * final，被 final 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸（其它线程通过 this 引用访问到初始化了一半的对象），那么其它线程就能看见 final 字段的值。

3. 有序性

   > 源代码 -> 编译器优化的重排 -> 指令并行的重排 -> 内存系统的重排 ->最终执行的命令

   重排序过程不会影响到`单线程程序的执行`，却会影响到`多线程并发执行的正确性`。

### 11、多线程安全问题

> 多线程的线程安全有哪两个方面的问题？— **执行控制**和**内存可见**。

#### 11.1 synchronized

执行控制的目的是控制代码执行顺序及是否可以并发执行。

synchronized关键字解决的是执行控制的问题，它会阻止其它线程获取当前对象的锁，这样就使得被synchronized关键字保护的代码块无法被其它线程访问，也就无法并发执行。更重要的是，synchronized还会创建一个内存屏障，内存屏障指令保证了所有CPU操作结果都会直接刷到主存中，从而保证了操作的内存可见性。

#### 11.2 volatile

内存可见控制的是线程执行结果在内存中对其它线程的可见性。根据 JMM 的实现，线程在具体执行时，会先拷贝主存数据到线程本地（CPU缓存），操作完成后再把结果从线程本地刷到主存。

volatile 关键字解决的是内存可见性的问题，会使得所有对 volatile 变量的读写都会直接刷到主存，即保证了变量的可见性。这样就能满足一些对变量可见性有要求而对读取顺序没有要求的需求。

#### 11.3 区别

* 原理：volatile本质是在告诉JVM当前变量在工作内存中的值是不确定的，要从主存中读取；synchronized 则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。
* 阻塞：volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞。
* 原子性：volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized则可以保证变量的修改可见性和原子性
* 有序性：volatile标记的变量不会被编译器优化；synchronized标记的变量可以被编译器优化。

### 12、同步异步

同步和异步都是最终目的。

> 为什么要用异步？

为了充分利用CPU的资源。`多线程`就是实现异步的一个手段。

> 为什么要用同步？

为了保证多线程情况下的数据准确性。多个线程同时操作一个可共享的资源变量时（如增删改查），将会导致数据不准确，相互之间产生冲突，因此加入同步锁以避免在该线程没有完成操作之前，被其他线程的调用，从而保证了该变量的唯一性和准确性。

#### 12.1 同步

> 什么是同步？

可以理解为在执行完一个函数或方法之后，一直等待系统返回值或消息，这时程序是出于阻塞的，只有接收到返回的值或消息后才往下执行其他的命令。  

比如：打电话，通信双方不能断（我们是同时进行，同步），你一句我一句，这样的好处是，对方想表达的信息我马上能收到，但是，我在打着电话，我无法做别的事情。

> 怎么实现同步？

如上9、11，使用锁和volatile。

#### 12.2 异步

执行完函数或方法后，不必阻塞性地等待返回值或消息，只需要向系统委托一个异步过程，那么当系统接收到返回值或消息时，系统会自动触发委托的异步过程，从而完成一个完整的流程。

比如：收发短信，对方不用保证此刻我一定在手机旁，同时，我也不用时刻留意手机有没有来短信。这样我看着视频，来了短信我就处理短信（也可以不处理），接着再看视频。

> 怎么实现异步？

如上所述，使用多线程。

## 四、反射

### 1、反射详解

> 反射是什么东西？

反射就是把 Java 类中的各种成分映射成一个个的 Java 对象。一个类有：成员变量、方法、构造方法、包等等信息，利用反射技术可以对一个类进行解剖，把各个组成部分映射成一个个对象。

也主要是指程序可以访问、检测和修改它本身状态或行为的一种能力。

> 它是用来做什么的？应用场景有哪些？

反射可以赋予 JVM 动态编译的能力。

对象有**编译类型**和**运行类型**：

```java
Object obj = new Date();
// 编译类型（声明类型）为：Object,此处为静态编译
// 运行类型（对象真实类型）为：Date
```

其中Java中编译类型有两种：

* 静态编译：一次性编译。在编译的时候把你所有的模块都编译进去。

* 动态编译：按需编译。程序在运行的时候，用到那个模块就编译哪个模块。

通俗理解：假如我们有两个程序员，一个程序员在写程序的时候，需要使用第二个程序员所写的类，但第二个程序员并没完成他所写的类。那么第一个程序员的代码能否通过编译呢？这是不能通过编译的。利用Java反射的机制，就可以让第一个程序员在没有得到第二个程序员所写的类的时候，来完成自身代码的编译。

常用场景：

1. 场景一：在日常的第三方应用开发过程中，经常会遇到某个类的某个成员变量、方法或是属性是私有的或是只对系统应用开放，这时候就可以利用Java的反射机制通过反射来获取所需的私有成员或是方法。

2. 场景二：当我们在使用IDE(如Eclipse，IDEA)时，当我们输入一个对象或类并想调用它的属性或方法时，一按点号，编译器就会自动列出它的属性或方法，这里就会用到反射。

3. 场景三：反射**最重要的用途**就是开发各种通用框架。比如在Spring中，我么将所有类的Bean交给Spring容器管理，无论是XML配置还是注解配置，当我们从容器中获取Bean时容器会读取配置，而配置中给的就是类的信息。Spring会根据这些信息，需要创建哪些Bean，Spring就动态地创建这些类。

> 为啥要用它？它有什么优缺点？

Java的反射机制就是增加程序的灵活性，解耦。反射就是一种机制，可以让你仅知道类的名字的情况下，可以了解整个类的内部的结构，并且访问内部的成员和方法等。

优势：

* 增加程序的灵活性，避免将固有的逻辑程序写死到代码里。

* 代码简洁，可读性强，可提高代码的复用率。

缺点：

* 相较直接调用在量大的情景下反射性能下降。

* 存在一些内部暴露和安全隐患。

> 它的工作原理是什么？

不管是 Java 语言默认的类还是我们自定义创建的类，都是为了创建具有相同行为属性的对象的模板。那么我们在定义每一个类的时候，是不是也可以抽取共性的东西，比如，每一个类都有包名，属性定义，行为(方法)，构造器等等。那么这些类对象的实例，应该也可以抽取成一个公有的模板，用于创建类对象实例的模板。所以在 Java 中，这个类定义的创建模板就是我们 Java 语言中的 java.lang.Class 类。

Class类：用于描述一切类/接口。为了明确区分Class实例表示的是谁的字节码，Class类提供了泛型。Class内部如下图：

![image-20200531113214864](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200531113214864.png)

> 怎么使用反射？

首先要获取到需要获得的`类的字节码`，即 `Class类对象`。如何得到 Class 实例？

1. 如果你知道一个类型，那么你可以使用“.class”的方法获得：

   ```java
   Class<User> clazz1 = User.class;
   ```

2. 如果你知道一个实例，那么你可以通过实例的“getClass()”方法获得：

   ```java
   User u = new User();
   Class clazz2 = u.getClass();
   ```

3. 如果你知道一类的全限定名，那么你可以通过他的全限定名来构建Class对象：

   ```java
   Class<?> clazz3 = Class.forName("cn.itcast.cd.User");
   ```

以上例子中，`clazz1 == clazz2 == clazz3`，**因为表示都是JVM中同一份字节码**（User.class）。

当得到了字节码，则可以通过各种方法获取所需的该类的包名，属性定义，行为(方法)，构造器等等内容：

* 反射-基本信息操作

  ```java
  int modifier = clazz.getModifiers(); //获取类的修饰符
  Package package = clazz.getPackage();//获取类的包名
  String fullClassName = clazz.getName();//获取类的全路径名称
  String simpleClassName = clazz.getSimpleName();//获取类的简单名称
  ClassLoader classLoader = clazz.getClassLoader();//获取类的类加载器
  Class[] interfacesClasses = clazz.getInterfaces();//获取类实现的接口列表
  Class fc = clazz.getSuperclass();//获取类的父类
  Annotation[] annotations = clazz.getAnnotations(); //获取类的注解列表
  ```

* 反射-字段操作

  ```java
  Field[] fields = clazz.getFields();//获取类中所有的公有字段包含继承
  Field[] declaredFields = clazz.getDeclaredFields();//获取类中定义的字段内部
  Field nameField = clazz.getField("name");//获取指定名称的公有字段
  Field likeDescField = clazz.getDeclaredField("likeDesc");//获取指定名称类中定义的字段
  int modifersFiled = likeDescField.getModifiers();//获取字段的修饰
  nameField.setAccessible(true);//指定字段强制访问
  nameField.set(person,"小皮皮");//成员字段赋值(需指定对象)
  descriptionField.set(null,"没有结婚的都是男孩!");//静态字段赋值
  ```

* 反射-方法操作

  ```java
  Method[] methods = clazz.getMethods();//获取类中所有的公有方法继承
  Method[] declaredMethods = clazz.getDeclaredMethods();//获取类中定义的方法
  Method talkMethod = clazz.getMethod("talk", String.class);//获取类中指定名称和参数的公有方法
  Method pugMethod = clazz.getDeclaredMethod("pickUpGirls") //获取类中定义指定名称和参数的方法
  int modifers = pugMethod.getModifiers();//获取方法的修饰符
  talkMethod.invoke(boy,"ILOVE SEVEN");//指定对象进行成员方法的调用
  pugMethod.setAccessible(true);//指定方法的强制访问
  pickUpGirlsMethod.invoke(null);//静态方法的调用
  ```

* 反射-构造器操作

  ```java
  Constructor[] cons = clazz.getConstructors();//获取类中所有的公有构造器
  Constructor[] cons = clazz.getDeclaredConstructors();//获取类中所有的构造器
  Constructor conNoParam = clazz.getDeclaredConstructor();//获取类中无参的构造器
  Constructor con = clazz.getDeclaredConstructor(String.class,String.class); //获取类中有参构造
  int modifers = con.getModifiers();//获取构造器的修饰符
  conNoParam.newInstance();//构造器实例对象
  con.setAccessible(true);//指定方法的强制访问
  con.newInstance('abc','def');//有参构造调用
  class.newInstacne();//class直接调用默认无参构造
  ```

### 2、什么是注解？

注解，也被称为元数据，为我们在代码中添加信息提供了一种形式化的方法，使我们可以在稍后某个时刻非常方便地使用这些数据。注解类型定义指定了一种新的类型，一种特殊的接口类型。 在关键词 interface 前加 @ 符号也就是用 @interface 来区分注解的定义和普通的接口声明。

作用：

* 提供信息给编译器： 编译器可以利用注解来探测错误和警告信息，如 @Override、@Deprecated。

* 编译阶段时的处理： 软件工具可以用来利用注解信息来生成代码、Html 文档或者做其它相应处理，如 @Param、@Return、@See、@Author 用于生成 Javadoc 文档。

* 运行时的处理： 某些注解可以在程序运行的时候接受代码的提取，值得注意的是，注解不是代码本身的一部分。

怎么读取注解？**通过反射机制可以读取这些元数据**。

#### 2.1 内置注解

Java中有三种内置注解，这些注解用来为编译器提供指令。它们是：

1. @Deprecated：已过期，表示该方法是不被建议使用的；
2. @Override：重写，将覆盖该方法的父类的方法；
3. @SuppressWarnings：抑制警告。

#### 2.2 元注解

元注解的作用是负责注解其他的注解。Java定义了4个元注解：

1. **@Target**：用于描述注解的适用范围；
2. **@Retention**：表示需要在什么级别保存该注释信息，用于描述注解的生命周期（SOURCE < CLASS < **RUNTIME**）；
3. @Document：说明该注解将被包含在javadoc中；
4. @Inherited：说明子类可以继承父类中的该注解。

#### 2.3 通过反射获取注解

通过反射操作注解的例子：

```java
/**
 * @Author: wq57fan
 * @Date: 2020/5/31 15:09
 * @Description: 通过反射读取注解
 */
public class test01 {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class c1 = Class.forName("main.java.Student2");

        // 获取类注解
        Annotation[] annotations =  c1.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }
        // 输出：@main.java.Table(value=db_student)

        // 获取类注解的内容
        Table table = (Table) c1.getAnnotation(Table.class);
        String value = table.value();
        System.out.println(value);
        // 输出：db_student

        // 获取属性字段的注解内容
        java.lang.reflect.Field f = c1.getDeclaredField("name");
        Field field = f.getAnnotation(Field.class);
        System.out.println(field.columnName()); // 输出db_name
        System.out.println(field.type());		// 输出varchar
        System.out.println(field.length());		// 输出3
    }
}

@Table("db_student")
class Student2{
    @Field(columnName = "db_id",type = "int",length = 10)
    private int id;
    
    @Field(columnName = "db_age",type = "int",length = 10)
    private int age;

    @Field(columnName = "db_name",type = "varchar",length = 3)
    private String name;
}

// 自定义类的注解
@Target(ElementType.TYPE)
@Retention(value = RetentionPolicy.RUNTIME)
@interface Table{
    String value();
}

// 自定义属性的注解
@Target(ElementType.FIELD)
@Retention(value = RetentionPolicy.RUNTIME)
@interface Field{
    String columnName();
    String type();
    int length();
}
```

### 3、什么是 Java 序列化？什么情况下需要序列化？

简单说就是为了保存在内存中的各种对象的状态（也就是实例变量，不是方法），并且可以把保存的对象状态再读出来。虽然你可以用你自己的各种各样的方法来保存object states，但是Java给你提供一种应该比你自己好的保存对象状态的机制，那就是序列化。

什么情况下需要序列化：

1. 当你想把的内存中的对象状态保存到一个文件中或者数据库中时候；
2. 当你想用套接字在网络上传送对象的时候；
3. 当你想通过RMI传输对象的时候。

### 4、什么是动态代理？

> 什么是动态代理？有哪些应用？

动态代理：当想要给实现了某个接口的类中的方法，加一些额外的处理。比如说加日志，加事务等。可以给这个类创建一个代理，故名思议就是创建一个新的类，这个类不仅包含原来类方法的功能，而且还在原来的基础上添加了额外处理的新类。这个代理类并不是定义好的，是动态生成的。具有解耦意义，灵活，扩展性强。

动态代理的应用：Spring的AOP、加事务、加权限、加日志。

> 怎么实现动态代理？

需要两个类：`Proxy`代理类；`InvocationHandler`接口(调用处理程序)。

动态代理的代理类是动态生成的，不是直接写好的。可分为两大类：

1. 基于接口的动态代理，如JDK动态代理；
2. 基于类的动态代理，如cglib动态代理、javassist动态代理。

JDK的动态代理实现AOP例子：

```java
public interface Rent {
    public void rent();
}
```

```java
public class Host implements Rent {
    @Override
    public void rent() {
        System.out.println("房东要出租房子！");
    }
}
```

```java
public class ProxyInvocationHandler implements InvocationHandler {

    // 被代理的接口
    private Rent rent;

    public void setRent(Rent rent) {
        this.rent = rent;
    }

    // 生产代理类
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), rent.getClass().getInterfaces(), this);
    }

    // 处理代理实例，并返回结果
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        before();
        // 动态代理的本质是使用反射机制
        Object result = method.invoke(rent, args);
        after();
        return result;
    }

    public void before() {
        System.out.println("before-AOP:租房之前先看一下");
    }

    public void after() {
        System.out.println("after-AOP:租房之后签合同");
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        // 真实角色
        Host host = new Host();
        // 代理角色
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        pih.setRent(host);
        Rent proxy = (Rent) pih.getProxy();
        proxy.rent();
        /*  输出：
        	before-AOP:租房之前先看一下
        	房东要出租房子！
        	after-AOP:租房之后签合同
        */
    }
}
```

## 五、Java Web

### 1、Jsp 和 Servlet 有什么区别？

区别如下：

1. Jsp经编译后就变成了Servlet.（JSP的本质就是Servlet，JVM只能识别Java的类，不能识别JSP的代码，Web容器将JSP的代码编译成JVM能够识别的Java类） 
2. Jsp更擅长表现于页面显示，Servlet更擅长于逻辑控制。
3. Servlet中没有内置对象，Jsp中的内置对象都是必须通过HttpServletRequest对象，HttpServletResponse对象和HttpServlet对象得到。
4. Jsp是Servlet的一种简化，使用Jsp只需要完成程序员需要输出到客户端的内容，Jsp中的Java脚本如何镶嵌到一个类中，由Jsp容器完成。而Servlet则是个完整的Java类，这个类的Service方法用于生成对客户端的响应。

### 2、session 和 cookie 有什么区别？

> 思考一下服务端如何记录每个客户的操作？-`Session`

由于HTTP协议是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识具体的用户，这个机制就是 Session。典型的场景比如购物车，当你点击下单按钮时，由于HTTP协议无状态，所以并不知道是哪个用户操作的，所以服务端要为特定的用户创建了特定的 Session，用用于标识这个用户，并且跟踪用户，这样才知道购物车里面有几本书。这个 Session是保存在服务端的，有一个**唯一标识**。在服务端保存 Session的方法很多，**内存、数据库、文件**都有。集群的时候也要考虑 Session的转移，在大型的网站，一般会有专门的 Session服务器集群，用来保存用户会话。这个时候 Session 信息都是放在内存的，使用一些缓存服务比如 Redis等来放Session。

> 思考一下服务端如何识别特定的客户？-`Cookie`

这个时候Cookie就登场了。每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端。实际上大多数的应用都是**用 Cookie 来实现 Session 跟踪**的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在 Cookie 里面记录一个**Session ID**，以后每次请求把这个会话ID发送到服务器，我就知道你是谁了。

> 有人问，如果客户端的浏览器禁用了 Cookie 怎么办？

一般这种情况下，会使用一种叫做**URL重写**的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。

> Cookie其实还可以用在一些方便用户的场景下，设想你某次登陆过一个网站，下次登录的时候不想再次输入账号了，怎么办？

这个信息可以写到Cookie里面，访问网站的时候，网站页面的脚本可以读取这个信息，就自动帮你把用户名给填了，能够方便一下用户。这也是Cookie名称的由来，给用户的一点甜头。

总结：Session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。

### 3、session 的工作原理？

session是一个存在服务器上的类似于一个**散列表格**的文件。里面存有我们需要的信息，在我们需要用的时候可以从里面取出来。类似于一个大号的 map吧，里面的键存储的是用户的 sessionid，用户向服务器发送请求的时候会带上这个 sessionid。这时就可以从中取出对应的值了。

### 4、如果客户端禁止 cookie 能实现 session 还能用吗？

Cookie 与 Session，一般认为是两个独立的东西，Session采用的是在服务器端保持状态的方案，而Cookie采用的是在客户端保持状态的方案。

> 为什么禁用Cookie就不能得到Session呢？

因为Session是用Session ID来确定当前对话所对应的服务器Session，而Session ID是通过Cookie来传递的，禁用Cookie相当于失去了Session ID，也就得不到Session了。

假定用户关闭Cookie的情况下使用Session，可以**用文件、数据库等形式保存Session ID**，在跨页过程中手动调用。也可以用**URL重写**。

### 5、什么是 XSS 攻击，如何避免？

XSS 攻击又称 CSS，全称 Cross Site Script （跨站脚本攻击），其原理是攻击者向有XSS漏洞的网站中输入恶意的HTML代码，当用户浏览该网站时，这段HTML代码会自动执行，从而达到攻击的目的。XSS攻击类似于SQL注入攻击，SQL注入攻击中以SQL语句作为用户输入，从而达到查询/修改/删除数据的目的。而在XSS攻击中，通过插入恶意脚本，实现对用户游览器的控制，获取用户的一些信息(cookie)。 XSS是 Web 程序中常见的漏洞，XSS 属于被动式且用于客户端的攻击方式。

XSS防范的总体思路是：对输入(和URL参数)进行**过滤**，对输出进行**编码**。

### 6、什么是 CSRF 攻击，如何避免？

CSRF（Cross-site request forgery），中文全称是叫跨站请求伪造。一般来说，攻击者通过伪造用户的浏览器的请求，向访问一个**用户自己曾经认证访问过的网站**发送出去，使目标网站接收并误以为是用户的真实操作而去执行命令。常用于盗取账号、转账、发送虚假消息等。攻击者利用网站对请求的验证漏洞而实现这样的攻击行为，网站能够确认请求来源于用户的浏览器，却不能验证请求是否源于用户的真实意愿下的操作行为。

如何避免：

1. 验证 HTTP Referer 字段
   HTTP头中的Referer字段记录了该 HTTP 请求的来源地址。在通常情况下，访问一个安全受限页面的请求来自于同一个网站，而如果黑客要对其实施 CSRF 攻击，他一般只能在他自己的网站构造请求。因此，可以通过验证Referer值来防御CSRF 攻击。

2. 使用验证码
   关键操作页面加上验证码，后台收到请求后通过判断验证码可以防御CSRF。但这种方法对用户不太友好。

3. 在请求地址中添加token并验证
   CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于cookie中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的 cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。**可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token**，如果请求中没有token或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。这种方法要比检查 Referer 要安全一些，**token 可以在用户登陆后产生并放于session之中，然后在每次请求时把token 从 session 中拿出，与请求中的 token 进行比对**，但这种方法的难点在于如何把 token 以参数的形式加入请求。

   > 1、对于 GET 请求，token 将附在请求地址之后，这样 URL 就变成 http://url?csrftoken=tokenvalue。
   >
   > 2、对于 POST 请求来说，要在 form 的最后加上 
   >
   > <input type="hidden" name="csrftoken" value="tokenvalue"/>
   >
   > 这样就把token以参数的形式加入请求了。

### 7、WebService接口？

WebService是指一个应用程序向外界暴露了一个能通过Web调用的API接口。

目的：

1. 异构系统（不同语言）的整合；
2. 不同客户端的整合（PC端、浏览器、移动端包括安卓、IOS等）。

实现：远程调用。WebService服务端通过一个文件（WSDL）来说明自己有啥服务可以对外调用，服务是什么（服务中有哪些方法，方法接受 的参数是什么，返回值是什么），服务的网络地址用哪个url地址表示，服务通过什么方式来调用。WSDL文件保存在Web服务器上，通过一个url地址就可以访问到它。客户端要调用一个WebService服务之前，要知道该服务的WSDL文件的地址。

![image-20200607122417613](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200607122417613.png)

### 8、中间件

> 使用消息队列的好处？消息队列的主要用途是削峰、异步、解耦、可恢复、缓冲。

**峰值处理能力**：不会因为突发的超负荷的请求而完全崩溃，消息队列能够使关键组件顶住突发的访问压力，消费者可以慢慢处理消息。

**异步通信**：消息队列允许用户把消息放入队列但不立即处理它。

**解耦**：允许我们独立的扩展或修改队列两边的处理过程。

可恢复性：即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。

缓冲：有助于解决生产消息和消费消息的处理速度不一致的情况。

现实生活中比如蜂巢、菜鸟驿站就是采用了消息队列的思想。

#### 8.1 ActiveMQ

优点：老牌的消息队列，使用Java语言编写。对JMS支持最好，采用多线程并发，资源消耗比较大。

缺点：版本更新很缓慢，集群模式需要依赖Zookeeper实现。最新架构的产品被命名为Apollo，号称下一代ActiveMQ，目前案例较少。 

ActiveMQ工作模型比较简单。只有两种模式  queue 和 topics：

* `queue`就是一对多，producer往queue里发送消息，消费者从queue里取，消费一条，就从queue里移除一条。如果一个消费者消费速度不够快怎么办呢？在ActiveMQ里，提供messageGroup的概念，一个queue可以有多个消费者，但是他们得标记自己是一个messageGroup里的。这样，Queue支持存在多个消费者，**但是对一个消息而言，只会有一个消费者可以消费、其它的则不能消费此消息了**。

* `topics`就是广播。producer往broker发消息，每个消息包含topic。消费者订阅感兴趣的topic，所有订阅了topic的消费者都会收到消息。当然订阅分持久和不持久。持久订阅：当消费者断开一会，再连上来，仍然会把没收到的消费发过来。不持久的订阅：断开这段时间的消息就收不到了。

#### 8.2 RabbitMQ

优点：生态丰富，AMQP协议的领导实现，支持多种场景。淘宝的MySQL集群内部有使用它进行通讯，OpenStack开源云平台的通信组件，最先在金融行业得到运用。

缺点：虽然Erlang是天然集群化的，但RabbitMQ在高可用方面做起来还不是特别得心应手。

从机制上来讲，RabbitMQ也有queue和topic的概念，发消息的时候还要指定消息的key，这个key之后会做路由键用。但是，多了一个概念叫做交换器exchange。exchange有四种，direct、fanout、topic、header。也就是说，发消息给RabbitMQ时，消息要有一个key，并告诉他发给哪个exchange。exchange收到之后，根据key分发或者广播给queue。消费者是从queue里拿消息的，并接触不到交换机。

在RabbitMQ里，有各种默认行为，如果我们不指定exchange，会有个默认的direct类型的exchange，如果不指定队列和交换器的绑定关系，默认就按消息的key绑定对应的queue。此时发一个消息，消息的key是什么，就会被默认交换器送给对应的queue。

#### 8.3 Kafka

Kafka是最初由Linkedin公司开发，是一个分布式、支持分区的、多副本的，基于ZooKeeper协调的分布式消息系统，它的最大的特性就是**可以实时的处理大量数据**以满足各种需求场景：比如基于Hadoop的批处理系统、低延迟的实时系统、Storm/Spark流式处理引擎，Web/Nginx日志、访问日志，消息服务等等。

### 9、Web容器

#### 9.1 Jetty

Jetty 是一个开源的**Servlet**容器，它为基于Java的Web容器，例如JSP和Servlet提供运行环境。Jetty是使用Java语言编写的，它的API以一组JAR包的形式发布。开发人员可以将Jetty容器实例化成一个对象，可以迅速为一些独立运行的Java应用提供网络和Web连接。

相比于Tomcat，Jetty更加轻量级和灵活，更满足公有云的分布式环境的需求，而Tomcat更符合企业级环境。

#### 9.2 Tomcat

Tomcat 服务器是一个免费的开放源代码的Web应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP 程序的首选。Tomcat本质上就是一个**Servlet**容器，所以Catalina就是其核心。

> Tomcat工作原理？

Tomcat服务器的工作原理可以概括为以下几点：

1. Tomcat是运行在JVM中的一个du进程。它定义为“中间件”，顾名思义，是一个在Java项目与JVM之间的中间容器。

2. Web项目的本质，是一大堆的资源文件和方法。Web项目没有入口方法（即main方法），这意味着Web项目中的方法不会自动运行起来。

3. Web项目部署进Tomcat的webapp中的目的是很明确的，那就是希望Tomcat去调用写好的方法去为客户端返回需要的资源和数据。

4. Tomcat可以运行起来，并调用写好的方法。那么，Tomcat一定有一个main方法。对于Tomcat而言，它并不知道用户会有什么样的方法，这些都只是在项目被部署进webapp下后才确定的。

由此，可知Tomcat用到了Java的反射来实现类的动态加载、实例化、获取方法、调用方法。但是部署到Tomcat的中的Web项目必须是按照规定好的接口来进行编写，以便进行调用。

#### 9.3 JBoos

JBoss是一个管理**EJB**的容器和服务器，但JBoss核心服务不包括支持Servlet/JSP的WEB容器，一般与Tomcat或Jetty绑定使用。

* EJB：即 Enterprise Java Bean。他就是将那些"类"放到一个服务器上，用C/S 形式的软件客户端对服务器上的"类"进行调用。EJB 是运行在独立服务器上的组件，客户端是通过网络对EJB 对象进行调用的。在Java中，能够实现远程对象调用的技术是**RMI**。通过RMI 技术，J2EE将EJB 组件创建为远程对象，客户端就可以通过网络调用EJB 对象了。
* RMI ：即 Remote Method Invocation。是一种计算机之间对象互相调用对方函数，启动对方进程的一种机制。利用这个方法来调用远程的类的时候，就不需要编写Socket 程序了，也不需要把对象进行序列化操作，直接调用就行了，很方便。

> 什么是Netty？

Netty是一个JBoss提供的基于NIO类库封装的客户端、服务端的通讯编程框架，具有异步非阻塞，事件驱动，高性能，高可用等特点。应用场景包括：Dubbo、ES、MQ的底层RPC实现，某些游戏和服务器的通讯。

为什么使用Netty？

1. 使用的人多，稳定；
2. 基于NIO自己开发过于复杂。

## 六、异常

### 1、throw 和 throws 的区别？

`throws` 是用来声明一个方法可能抛出的所有异常信息，throws是将异常声明但是**不处理**，而是将异常往上传，谁调用我就交给谁处理。

`throw` 则是指抛出的一个具体的异常类型，一般catch后自行处理。

### 2、final、finally、finalize 有什么区别？

`final` 可以修饰类、变量、方法，修饰类表示该类不能被继承、修饰方法表示该方法不能被重写、修饰变量表示该变量是一个常量不能被重新赋值。

`finally` 一般作用在try-catch代码块中，在处理异常的时候，通常我们将一定要执行的代码方法。finally代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。

`finalize` 是一个方法，属于Object类的一个方法，而Object类是所有类的父类，该方法一般由垃圾回收器来调用，当我们调用 System 的 gc() 方法的时候，由JVM垃圾回收器自动调用 finalize() 回收垃圾，回收一个不被其他对象引用的对象。

## 七、网络

### 1、forward 和 redirect 的区别？

`直接转发方式（Forward）`，客户端向浏览器只发一次请求，Servlet、HTML、JSP或其它信息资源由被转发的资源响应该请求，在请求对象request中，保存的对象对于每个信息资源是共享的。

`间接转发方式（Redirect）`，实际是两次HTTP请求，服务器端在响应第一次请求的时候，让浏览器再向另外一个URL发出请求，从而达到转发的目的。

### 2、tcp 为什么要三次握手，两次不行吗？为什么？

为了实现可靠数据传输， TCP 协议的通信双方， 都必须维护一个序列号， 以标识发送出去的数据包中， 哪些是已经被对方收到的。 三次握手的过程即是通信双方相互告知序列号起始值， 并确认对方已经收到了序列号起始值的必经步骤。

如果只是两次握手， 至多只有连接发起方的起始序列号能被确认， 另一方选择的序列号则得不到确认。

### 3、get 和 post 请求有哪些区别？

* GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。GET参数通过URL传递，POST放在Request body中。

* GET请求会被浏览器主动缓存（求参数会被完整保留在浏览器历史记录里），而POST不会，除非手动设置。

* GET请求只能进行URL编码，而POST支持多种编码方式。

* GET在浏览器回退时是无害的，而POST会再次提交请求。

### 4、Ajax跨域？

现在前后端分离，前后端开发是独立的，前端要调用后端接口，常产生跨域问题。

#### 4.1 产生跨域问题的原因

以下三点同时满足，才会产生跨域问题：

1. 浏览器限制；浏览器会对请求进行校验，不通过则报错。
2. 跨域；即协议、ip、端口有不一样的，则产生跨域问题。
3. 发送的是XHR（XmlHttpRequest）请求，其他类型的不会发生跨域安全问题。

#### 4.2 解决思路与方案

1. **浏览器解除限制**。即指定参数设置，不让浏览器做校验。需要每个客户端做改动，价值不大。

   方案：前台启动浏览器时新加一个参数：

   ```properties
   chrome --disable--web-security --user-data-dir=g:\temp3
   ```

2. **让发送类型不是XHR**。JSONP通过动态创建script，在<script>中发送请求，弊端较多，用的越来越少了。

   方案：**JSONP**，使用JSONP后台需要改动。否则后台传回前台的还是json对象的话，前台会报错。

   缺点：(1)服务器需要改动代码；(2)只支持GET方法；(3)发送的不是XHR请求（异步等特性无法使用）。

3. (1)被调用方修改代码，让他**支持跨域**。A域名调用B域名时，B域名在参数里增加一些字段告诉浏览器允许被A域名调用，跨过浏览器的校验即可。

   方案：应用服务器端-filter/nginx/Spring框架(在Controller类上新增一个@CrossOrigin)

   (2)调用方修改代码，**隐藏跨域**。通过代理，从浏览器发出的请求都是A域名的请求，在代理里把指定的url转到B域名，浏览器看来都是B域名。

   方案：修改调用方的http服务器vhost配置文件

### 5、说一下 JSONP 实现原理？

JSONP 即 json+padding，动态创建script标签，利用script标签的 **src 属性**可以获取任何域下的js脚本，通过这个特性(也可以说漏洞)，服务器端不再返回json格式，而是返回一段调用某个函数的js代码，在src中进行了调用，实现了跨域。

## 八、Spring/Spring MVC

### 1、什么是 IOC？

控制反转：对象A获得依赖对象B的过程由主动行为变为了被动行为，控制权颠倒过来。

比如：service层中加了setter了后可以由用户来进行dao的对象的创建，发生了控制反转，就不需要再改动service层了。而在此之前需要由service层进行指定对象的创建。

> <I> dao
>
> <C> mysqlDao
>
> <C> oracleDao
>
> <I> service
>
> // IOC前固定写死了，高耦合。加入setter方法后，对象的创建可以交给test类去完成
>
> <C> serviceImpl	->	dao dao = new mysqlDao();  
>
> // IOC后，调用service层
>
> <C> test                 ->   service.setDao(mysqlDao);

### 2、什么是 AOP？

面向切面编程：将那些影响了多个类的公共行为封装到一个可重用模块，将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。使用了动态代理技术实现。

### 3、Spring 事务实现方式有哪些？

1. 代码中调用beginTransaction()、commit()、rollback()的编程式事务管理；
2. 基于 TransactionProxyFactoryBean 的声明式事务管理；
3. 基于 @Transactional 的声明式事务管理；
4. 基于 Aspectj 框架的 AOP 配置事务；

### 4、Spring MVC 运行流程？

`DispatcherServlet` 是核心，三个小弟：`HandlerMapping`、`HandlerAdapter`、`ViewResolver`。

![image-20200526134123340](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200526134123340.png)

* Spring运行流程描述：

1. 用户向服务器发送请求，请求被 Spring 前端控制 DispatcherServlet 捕获；   

2. DispatcherServlet 对请求URL进行解析，得到请求资源标识符（URI）。然后根据该URI，调用 HandlerMapping 获得该 Handler 配置的所有相关的对象（包括Handler对象以及Handler对象对应的拦截器），最后以HandlerExecutionChain对象的形式返回；   

3. DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter；（附注：如果成功获得HandlerAdapter后，此时将开始执行拦截器的preHandler(...)方法）    

4. 提取 Request 中的模型数据，填充 Handler 入参，开始执行 Handler（Controller)。 在填充 Handler 的入参过程中，根据你的配置，Spring 将帮你做一些额外的工作：

   (1) HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息

   (2) 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等

   (3) 数据根式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等

   (4) 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中   

5. Handler 执行完成后，向 DispatcherServlet 返回一个 ModelAndView 对象；

6. 根据返回的 ModelAndView，选择一个适合的 ViewResolver（必须是已经注册到Spring容器中的ViewResolver)返回给 DispatcherServlet；

7. ViewResolver 结合Model和View，来渲染视图；

8. 将渲染结果返回给客户端。

## 九、Spring Boot/Spring Cloud

### 1、什么是 Spring Boot？

SpringBoot 是一个服务于框架的框架，服务范围是简化配置文件。他的产生简化了 Spring众多框架中所需的大量且繁琐的配置文件。

> SpringBoot和Spring区别？

我们知道，SpringApplication类就是SpringBoot的总入口，进入SpringApplication这个类的源码里，首先看到的就是几个ApplicationContext所使用的类：

```java
// 基于注解的，非web应用使用的类，它是spring-context里面的类，现在也用于SpringBoot中
public static final String DEFAULT_CONTEXT_CLASS = "org.springframework.context.annotation.AnnotationConfigApplicationContext";
// 基于注解的，web应用使用的类，他是基于Servlet实现的。注意，这个类是SpringBoot里的类。
public static final String DEFAULT_SERVLET_WEB_CONTEXT_CLASS = "org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext";
// 基于注解的，web应用使用的类，他是基于Reactive实现的。同上，这个类是SpringBoot里的类。
public static final String DEFAULT_REACTIVE_WEB_CONTEXT_CLASS = "org.springframework.boot.web.reactive.context.AnnotationConfigReactiveWebServerApplicationContext";
```

结论：

1. 对于**非web应用**来说，采用传统的Spring构建，或是采用现在的SpringBoot构建，核心部分没有本质区别，因为类都是用的同一个。**【没区别】**
2. 对于**web应用**来说，基于传统Spring构建web应用时使用的是AnnotationConfigWebApplicationContext这个类，这个类位于spring-web中，显然它是Spring里面的类。**【有区别】**
3. 传统Spring和SpringBoot在对待**bean定义注册**这一块，完全相同，没有任何区别。**【没区别】**

> 为什么会有区别呢？

传统Spring构建的web应用，会打成一个war包，放入tomcat下面。先启动tomcat，然后tomcat再去加载它下面的web应用（即war包）。

SpringBoot构建的web应用，会打成一个jar包，采用内嵌的tomcat。先启动jar包，会进入SpringBoot中，然后再去启动tomcat。

因为现在SpringBoot要来负责启动和停止web server，这和传统Spring完全不同，所以它要自己实现一个web application context所使用的类。由此我们可以推断出，这个类里一定有关于web server启动和停止的相关内容。现在SpringBoot翻身成了主人，它不仅可以启停web服务器，还可以选择web服务器，是用tomcat、jetty还是netty，都是可以配置的。

### 2、Spring Boot自动装配原理（源码）？

从@SpringBootApplication启动注解入手。@SpringBootApplication有很多的注解组成，其实归纳后重要的只有三个Annotation，也就是说我们在开发的时候，加上以下注解会等同于加上@SpringBootApplication注解：

```java
@SpringBootConfiguration //实质就是一个@Configuration
@EnableAutoConfiguration
@ComponentScan
```

1. @Configuration

   代表了一个配置类，相当于一个beans.xml文件。

2. @ComponentScan

   功能其实就是自动扫描并加载符合条件的组件或bean定义，最终将这些bean定义加载到容器中。

3. @EnableAutoConfiguration

   代表开启springboot的自动装配。

   在spring中有关于@Enablexxx的注解是开启某一项功能的注解，比如@EnableScheduling表示开启spring的定时任务。其原理是借助@Import的帮助，将所有符合自动配置条件的bean定义加载到Ioc容器。

### 3、Spring Boot启动流程？

![image-20200615185823337](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200615185823337.png)

SpringBoot启动流程主要分为两个步骤：

1. 创建SpringApplication对象。
2. 调用SpringApplication对象的run()方法实现启动，并且返回当前容器的上下文。

### 4、什么是微服务？

微服务是一种架构模式，提倡将单一的应用程序划分成一组小的服务，每个服务运行在自己独立的进程内，服务之间互相协调、互相配置，为用户提供服务。

> 微服务的分布式架构的四大核心问题是什么？

1. 这么多服务，客户该如何去访问？（服务路由问题）
2. 这么多服务，服务之间如何通信？（异步调用问题）
3. 这么多服务，如何治理？（高可用问题）
4. 服务挂了，怎么办？（服务降级问题）

为什么要解决这些问题？因为**网络是不可靠**的。

解决方案：Spring Cloud，他是一套生态，就是用来解决以上四个问题的。而Spring Cloud是基于Spring Boot的。

#### 1.1 Spring Cloud NetFlix

针对以上问题，NetFlix推出的Spring Cloud NetFlix(集成各种NetFlix OSS)一站式解决方案如下：

1. 客户通过API网关访问，使用`Zuul`组件；
2. 服务间通过`Feign`通信，基于HttpClient。是HTTP的通信方式，同步并阻塞；
3. 通过服务注册与发现组件`Eureka`治理；
4. 使用熔断机制，`Hystrix`。

#### 1.2 Apache

2018年年底，NetFlix停止对该方案进行维护。于是Apache推出了分布式架构的第二套半自动解决方案：

1. API网关：没有！要么找第三方组件，要么自己实现；
2. 基于Java的高性能RPC通信框架 `Dubbo`；
3. 通过服务注册与发现组件 `ZooKeeper`治理，可管理Hadoop、Hive等组件；
4. 熔断机制：没有！借助了NetFlix的 `Hystrix`。

#### 1.3 Spring Cloud Alibaba

阿里2019年年底推出的一站式解决方案。

#### 1.4 Service Mesh

下一代微服务标准。代表解决方案：`Istio`。

#### 1.5 微服务技术栈

| 微服务条目          | 落地技术                            |
| ------------------- | ----------------------------------- |
| 服务开发            | SpringBoot、Spring、SpringMVC       |
| 服务配置与管理      | Archaius(NetFlix)、Diamond(Alibaba) |
| 服务注册与发现      | **Eureka**、Consul、**ZooKeeper**   |
| 服务调用            | **Rest**、**RPC**、gRPC             |
| 服务熔断器          | **Hystrix**、Envoy                  |
| 负载均衡            | **Ribbon**、**Nginx**               |
| 服务接口调用        | **Feign**                           |
| 消协队列            | Kafka、RabbitMQ、ActiveMQ           |
| 服务配置中心管理    | **SpringCloud Config**、Chef        |
| 服务器路由(API网关) | **Zuul**                            |
| 服务监控            | Zabbix、Nagios、Metrics、Specatator |
| 全链路追踪          | Zipkin、Brave、Dapper               |
| 服务部署            | Docker、OpenStack、Kubernetes       |
| 数据流操作开发包    | SpringCloud Stream                  |
| 事件消息总线        | SpringCloud Bus                     |

### 5、什么是 Spring Cloud？

SpringCloud 是基于 SpringBoot 的一整套微服务的解决方案，微服务协调治理框架。它提供了微服务开发所需的配置管理、服务发现、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话和集群状态管理等组件。最重要的是，基于SpringBoot 会让开发微服务架构非常方便。

以前没有使用 SpringCloud 前，基本上都是使用 Dubbo来拆分服务，进行服务间的调用。SpringCloud 和 Dubbo最大的区别是：SpringCloud抛弃了Dubbo的RPC通信 ，采用了基于HTTP的RESTful方式。

* 参考网站：

1. 《Spring Cloud Netflix》https://www.springcloud.cc/spring-cloud-netflix.html
2. 《Spring Cloud 中文API文档》https://www.springcloud.cc/spring-cloud-dalston.html
3. 《Spring Cloud 中文网站》https://www.springcloud.cc/

基本的微服务框架搭建：

![image-20200707151838724](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200707151838724.png)

### 6、什么是服务熔断？什么是服务降级？

> 服务熔断？

服务熔断的作用类似于保险丝，当某服务出现不可用或响应超时的情况时，为了防止整个系统出现雪崩，暂时停止对该服务的调用。在互联网系统中，当下游服务因访问压力过大而响应变慢或失败，上游服务为了保护系统整体的可用性，可以暂时切断对下游服务的调用。

<img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200616152432656.png" alt="image-20200616152432656" style="zoom:80%;" />

1. 开启熔断

   在固定时间窗口内，接口调用超时比率达到一个阈值，会开启熔断。进入熔断状态后，后续对该服务接口的调用不再经过网络，直接执行本地的默认方法，达到服务降级的效果。

2. 熔断恢复

   熔断不可能是永久的。当经过了规定时间之后，服务将从熔断状态回复过来，再次接受调用方的远程调用。

> 服务降级？

服务降级是从整个系统的负荷情况出发和考虑的，对某些负荷会比较高的情况，为了预防某些功能（业务场景）出现负荷过载或者响应慢的情况，在其内部暂时舍弃对一些非核心的接口和数据的请求，而直接返回一个提前准备好的 fallback（退路）错误处理信息。这样，虽然提供的是一个有损的服务，但却保证了整个系统的稳定性和可用性。

### 7、Eureka 和 ZooKeeper 的区别？

著名的CAP理论指出，一个分布式系统不可能同时满足C(Consistency 一致性)、A(Availability 可用性)和P(Partition tolerance 分区容错性)。由于分区容错性在是分布式系统中必须要保证的，因此我们只能在A和C之间进行权衡。在此Zookeeper保证的是CP，而Eureka则是AP。

谁更好呢？Eureka。

> Zookeeper

![image-20200615221059772](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200615221059772.png)

当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的注册信息，但不能接受服务直接down掉不可用。也就是说，服务注册功能对可用性的要求要高于一致性。但是zk会出现这样一种情况，当master节点因为网络故障与其他节点失去联系时，剩余节点会重新进行leader选举。问题在于，选举leader的时间太长，30 ~ 120s, 且选举期间整个zk集群都是不可用的，这就导致在选举期间注册服务瘫痪。在云部署的环境下，因网络问题使得zk集群失去master节点是较大概率会发生的事，虽然服务能够最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。

> Eureka

![image-20200615220331105](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200615220331105.png)

Eureka看明白了这一点，因此在设计时就优先保证可用性。Eureka各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而Eureka的客户端在向某个Eureka注册或时如果发现连接失败，则会自动切换至其它节点，只要有一台Eureka还在，就能保证注册服务可用(保证可用性)，只不过查到的信息可能不是最新的(不保证强一致性)。除此之外，Eureka还有一种自我保护机制，如果在15分钟内超过85%的节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了网络故障，此时会出现以下几种情况：
1. Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务；
2. Eureka仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上(即保证当前节点依然可用)；
3. 当网络稳定时，当前实例新的注册信息会被同步到其它节点中。

因此， Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像zookeeper那样使整个注册服务瘫痪。

> 什么是Eureka自我保护机制？

即：是一种`应对网络异常的安全保护措施`。某一时刻某个微服务不能用了，Eureka不会立刻清理，依旧会对该微服务的信息进行保存。**宁可同时保留所有微服务，也不盲目注销任何健康的微服务。**

* 背景：默认情况下，如果EurekaServer在一定时间内没有接收到某个微服务实例的心跳，则他会注销该实例(默认90秒)。但是网络故障也会导致该情况，但是以上行为会变得非常危险，因为微服务本身是健康的，不应该注销这个服务。
* Eureka自我保护机制：能解决上面的问题。当EurekaServer节点短时间内丢失过多客户端时，该节点就会进入自我保护模式。该模式中，EurekaServer会保护服务注册表中的信息，不再注销任何服务实例。当他收到的心跳数重新恢复到阈值以上时，该EurekaServer节点就会自动退出自我保护模式。

### 8、什么是Dubbo？

Apache Dubbo™ 是一款高性能 Java **RPC** 框架。它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。

Dubbo 架构图如下：

![image-20200602092611812](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200602092611812.png)

节点角色说明：

| 节点        | 角色说明                               |
| ----------- | -------------------------------------- |
| `Provider`  | 暴露服务的服务提供方                   |
| `Consumer`  | 调用远程服务的服务消费方               |
| `Registry`  | 服务注册与发现的注册中心               |
| `Monitor`   | 统计服务的调用次数和调用时间的监控中心 |
| `Container` | 服务运行容器                           |

调用关系说明：

1. 服务容器负责启动，加载，运行服务提供者。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

### 9、什么是Ribbon\Feign？

* Ribbon

Spring Cloud Ribbon是基于 Netflix Ribbon实现的一套基于HTTP和TCP的客户端负载均衡工具。他可以把来自客户端的请求平均地分摊到服务端，从而实现系统的高可用。类似的软件有LVS、Nginx、Apache。

Spring Cloud Ribbon虽然只是一个工具类框架，它不像服务注册中心、配置中心、API网关那样需要独立部署，但是它几乎存在于每一个Spring Cloud构建的微服务和基础设施中。因为微服务间的调用，API网关的请求转发等内容，实际上都是通过Ribbon来实现的，包括后续将要学习的Feign，它也是基于Ribbon实现的工具。

* Feign

他是声明式负载均衡的 webservice 客户端（http），他让微服务之间的调用变得更简单，类似controller调用service。

前面在使用Ribbon+RestTemplate时，利用RestTemplate对Http的请求封装处理，形成了一套模板化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义，**在Feign的实现下，我们只需要创建一个接口并使用注解的方式来配置它（类似于以前Dao接口上标注Mapper注解，现在是一个微服务接口上面标注一个Feign注解即可）**即可完成对服务提供方的接口绑定，简化了使用Spring Cloud Ribbon时，自动封装服务调用客户端的开发量。

> Nginx、Ribbon、Feign区别？

1. 服务器端负载均衡Nginx
    Nginx是客户端所有请求统一交给 Nginx，由 Nginx进行实现负载均衡请求转发，属于服务器端负载均衡。即请求由 Nginx服务器端进行转发。

2. Feign

    调用微服务访问两种方式：

    1. 微服务名字【Ribbon】
    2. 接口和注解【Feign】

3. 客户端负载均衡Ribbon
    Ribbon是从 Eureka注册中心服务器端上获取服务注册信息列表，缓存到本地，让后在本地实现**轮询**负载均衡策略。即在客户端实现负载均衡。

 应用场景的区别：

1. Nginx适合于**服务器端**实现负载均衡，比如Tomcat；
2. Ribbon适合与在**微服务中RPC远程调用**实现本地服务负载均衡，比如Dubbo、SpringCloud中都是采用本地负载均衡。

### 10、什么是Hystrix？

Hystrix是一个用于处理分布式系统的延迟和容错的开源库。在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等。Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败避免级联故障，以提高分布式系统的弹性。

“断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个服务预期的，可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方法无法处理的异常，这样就可以保证了服务调用方的线程不会被长时间、不必要的占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

熔断机制是对应雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。当检测到该节点微服务调用正常后恢复调用链路。在SpringCloud框架里熔断机制通过Hystrix实现。他会监控微服务间的调用状况，当失败的调用到达阈值时（缺省是5秒内20次调用失败）就会启动熔断机制。注解是@HystrixCommand。

### 11、什么是Zuul？

Zuul是Spring Cloud全家桶中的微服务API网关。所有从设备或网站来的请求都会经过Zuul到达后端的Netflix应用程序。主要作用：对请求的**路由**和**过滤**。

* 路由：负责将外部请求转发到具体的微服务实例上，是实现外部访问统一入口的基础；
* 过滤：负责对请求的处理过程进行干预，是实现请求校验、服务聚合等功能的基础。

Zuul服务最终会注册进Eureka，同时从Eureka中获得其他微服务的消息，以后的访问微服务都是通过Zuul跳转后获得。

### 12、什么是Config？

<img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200706221937332.png" alt="image-20200706221937332" style="zoom:80%;" />

在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。在Spring Cloud中，有分布式配置中心组件Config ，它支持配置服务放在配置服务的内存中（即本地），也支持放在远程Git仓库中。Spring Cloud Config为分布式系统中的外部配置提供服务器和客户端支持。

* 服务端：也称分布式配置中心，是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口。
* 客户端：通过指定配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息。默认采用 git，并且可以通过 git 客户端工具来方便管理和访问配置内容。

## 十、Mybatis

### 1、Mybatis 分页？

主要是以下几种分页方式：

1. 数组分页；
2. sql分页(`limit(startIndex,pageSize)`)；
3. 拦截器分页；
4. RowBounds分页。

### 2、Mybatis 和 Hibernate 的区别？

Mybatis 需要直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果；

Hibernate 对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。 

### 3、一级缓存和二级缓存？

一级缓存：基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 cache 就将清空。**默认打开一级缓存**；

二级缓存：与一级缓存其机制相同，默认也是采用 PerpetualCache 的 HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。**默认不打开二级缓存**。要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置 cache。

> 什么情况使用一级缓存？Session级别是什么意思？

使用 Mybatis 的过程中，每次用Mybatis开启一次和数据库的会话操作的时候，Mybatis就会创建出一个 `SqlSession` 对象来表示一次数据库会话。

在对数据库的一次会话中，我们有可能会反复地执行完全相同的查询语句，如果不采取一些措施的话，每一次查询都会查询一次数据库，而我们在极短时间内做了完全相同的查询，那么它们的结果可能完全相同，由于查询一次数据库的代价很大，这有可能造成很大的资源浪费。

为了解决这一问题，减少资源的浪费，**Mybatis会在表示会话的 SqlSession 对象中建立一个简单的缓存**，将每次查询到的结果缓存起来，当下次查询的时候，如果判断先前有一个完全一样的查询，会直接从缓存汇总直接将结果取出来，返回给用户，不需要再次进行一次数据库的查询操作了。

## 十一、Mysql

### 1、数据库的三范式是什么？

* 第一范式：强调的是列的原子性，即数据库表的每一列都是不可分割的原子数据项。

* 第二范式：二范式就是要有主键，要求其他字段都仅依赖于主键。2NF的要求是不允许有部分函数依赖，即要求实体的属性完全依赖于主关键字。

* 第三范式：三范式就是要消除传递依赖，方便理解，可以看做是“消除冗余”。

  <img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200525204142404.png" alt="image-20200525204142404" style="zoom:80%;" />

### 2、事务的4种隔离级别？

#### 2.1 `Read uncommitted`

**读未提交**，顾名思义，就是**一个事务可以读取另一个未提交事务的数据**。

事例：老板要给程序员发工资，程序员的工资是3.6万/月。但是发工资时老板不小心按错了数字，按成3.9万/月，该钱已经打到程序员的户口，但是事务还没有提交，就在这时，程序员去查看自己这个月的工资，发现比往常多了3千元，以为涨工资了非常高兴。但是老板及时发现了不对，马上回滚差点就提交了的事务，将数字改成3.6万再提交。

分析：实际程序员这个月的工资还是3.6万，但是程序员看到的是3.9万。他看到的是老板还没提交事务时的数据。这就是脏读。

> 那怎么解决脏读呢？Read committed 能解决脏读问题！

#### 2.2 `Read committed`

**读提交**，顾名思义，就是**一个事务要等另一个事务提交后才能读取数据**。

事例：程序员拿着信用卡去享受生活（卡里当然是只有3.6万），当他买单时（程序员事务开启），收费系统事先检测到他的卡里有3.6万，就在这个时候！！程序员的妻子要把钱全部转出充当家用，并提交。当收费系统准备扣款时，再检测卡里的金额，发现已经没钱了（第二次检测金额当然要等待妻子转出金额事务提交完）。程序员就会很郁闷，明明卡里是有钱的…

分析：这就是读提交，若有事务对数据进行更新（UPDATE）操作时，读操作事务要等待这个更新操作事务提交后才能读取数据，可以解决脏读问题。但在这个事例中，出现了一个事务范围内两个相同的查询却返回了不同数据，这就是不可重复读。

> 那怎么解决可能的不可重复读问题？Repeatable read ！

#### 2.3 `Repeatable read`

**重复读**，就是**在开始读取数据（事务开启）时，不再允许修改操作**。

事例：程序员拿着信用卡去享受生活（卡里当然是只有3.6万），当他埋单时（事务开启，不允许其他事务的UPDATE修改操作），收费系统事先检测到他的卡里有3.6万。这个时候他的妻子不能转出金额了。接下来收费系统就可以扣款了。

分析：重复读可以解决不可重复读问题。写到这里，应该明白的一点就是，不可重复读对应的是修改，即UPDATE操作。但是可能还会有幻读问题。因为幻读问题对应的是插入INSERT操作，而不是UPDATE操作。

> 什么时候会出现幻读？

事例：程序员某一天去消费，花了2千元，然后他的妻子去查看他今天的消费记录（全表扫描FTS，妻子事务开启），看到确实是花了2千元，就在这个时候，程序员花了1万买了一部电脑，即新增INSERT了一条消费记录，并提交。当妻子打印程序员的消费记录清单时（妻子事务提交），发现花了1.2万元，似乎出现了幻觉，这就是幻读。

> 那怎么解决幻读问题？Serializable！

#### 2.4 `Serializable`

**序列化** 是最高的事务隔离级别，**在该级别下，事务串行化顺序执行**，可以避免脏读、不可重复读与幻读。但是这种事务隔离级别效率低下，比较耗数据库性能，**一般不使用**。

PS：大多数数据库默认的事务隔离级别是 Read committed，比如 Sql Server , Oracle。Mysql 的默认隔离级别是 Repeatable read。

| 隔离级别                     |   脏读（未提交）   | 不可重复读（update/delete后） |  幻读（insert后）  |
| :--------------------------- | :----------------: | :---------------------------: | :----------------: |
| 读未提交（read uncommitted） | :heavy_check_mark: |      :heavy_check_mark:       | :heavy_check_mark: |
| 读已提交（read committed）   |        :x:         |      :heavy_check_mark:       | :heavy_check_mark: |
| 可重复读（repeatable read）  |        :x:         |              :x:              | :heavy_check_mark: |
| 可串行化（serializable）     |        :x:         |              :x:              |        :x:         |

### 3、如何做 Mysql的性能优化？

1. 创建索引：创建合适的索引，我们就可以先在索引中查询，查询到以后直接找对应的记录；
2. 分表：当一张表的数据比较多或者一张表的某些字段的值比较多并且很少使用时，采用水平分表`同样的表结构，一个表内容拆分为多个`和垂直分表`有的字段内容很大，单独把字段抽离出来建表`来优化；
3. 读写分离：当一台服务器不能满足需求时，采用读写分离的方式进行集群；
4. 缓存：使用 redis 来进行缓存。

### 4、ElasticSearch 与 传统数据库的区别？

1. ES

   * ElasticSearch 是一款分布式全文检索框架，底层基于Lucene实现。Mysql引擎有InnoDB、Myisam等；

   * ElasticSearch 使用 JSON 格式存储数据，属于文档存储；

   * ES没有事务的概念，不支持回滚，无法恢复删除数据。

2. 遍历方式

   * ES有分片的概念，一个大的索引会被分成多个分片来进行存储数据，使用分布式的架构对分片进行并行搜索(基于倒排)；

   * 传统数据库的遍历，属于正向全表扫描。

3. 数据结构

   * ES采用倒排索引，对文本进行分词处理，记录单词、词频、文本 id 等信息，搜索时基于内容来找文本 id；
   * 传统数据库采用 B+ 树索引，存储时会根据内容生成一个节点，搜索时时跟据节点id来查找内容。

4. 选型

   * 在面对大数据量简单计算的时候es的效率远高于Mysql等传统数据库，但是在定位某个唯一值(如用会员id找会员)时并不需要es；
   * 但在大数据的相似计算与查找或简单计算时，es的分布式并行计算有绝对的优势。

## 十二、Redis

### 1、如何保持Mysql和Redis中数据的一致性？

一致性：假如一个数据访问者同时读取 Redis和 DB，他能在一段时间里发现二者不一样。

> 比如：一个事务执行失败回滚了，但是采取了先写 Redis的方式，就会造成 Redis和 MySQL数据库的不一致。

> 比如：一个事务写入了 MySQL，但是此时还未写入 Redis，如果这时候有用户访问 Redis，则此时就会出现数据不一致。

1. 分别处理       

   针对某些对数据一致性要求不高的情况下，可以将这些数据放入Redis，请求来了直接查询Redis，例如近期回复、历史排名这种实时性不强的业务。

   针对那些强实时性的业务，例如虚拟货币、物品购买件数等等，则直接穿透Redis写到MySQL上，等到MySQL上写入成功，再同步更新到Redis上去。

2. 高并发情况下    

   如果写入请求较多，则直接写入Redis中去，然后间隔一段时间批量将所有的写入请求刷新到MySQL中去。

   如果写入请求不多，则可以在每次写入Redis，都立刻将该命令同步至MySQL中去。

3. 基于订阅binlog的同步机制     

   阿里巴巴的开源框架 canal 提供了一种发布/ 订阅模式的同步机制，通过该框架我们可以对MySQL的 binlog 进行订阅，这样一旦MySQL中产生了新的写入、更新、删除等操作，就可以把 binlog 相关的消息推送至Redis，Redis再根据 binlog 中的记录，对Redis进行更新。

### 2、什么是缓存穿透？怎么解决？

缓存穿透：指**去缓存中查询一个不存在的数据**。由于缓存不命中时需要再从数据库查询，查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到数据库去查询，造成缓存穿透。

解决方案：最简单粗暴的方法如果一个查询返回的数据为空（不管是数据不存在，还是系统故障），**我们就把这个空结果进行缓存**，但它的过期时间会很短，最长不超过五分钟。

### 3、Redis持久化有几种方式？

Redis 持久化方案，在 Redis 的 `redis.windows.config` 文件中配置：

1. 默认采用 **RDB（Redis Database）** 持久化方案，每隔一段时间以**快照**的方式持久化到`dump.rdb`，不能实时保存数据，用于一般数据；

   ```properties
   save 900 1				#900秒内有1条记录改变
   save 300 10				#300秒内有10条记录改变
   save 60 10000			#60秒内有1W条记录改变
   ```

2. **AOF（Append Only File）**模式会把数据实时保存到**硬盘文件**里，持久化到`appendonly.aof`，用于重要数据，比如秒杀数据等。

   ```properties
   appendonly no			#开启AOF
   appendfsync always		#缓存变化立即持久化
   ```

### 4、Redis中的5种数据结构

> 在 redis 中每种数据结构的使用场景都是什么呢？

* String——字符串

  使用 Strings 类型可以完全实现目前 Memcached 的功能，并且效率更高。还可以享受 Redis 的定时持久化（可以选择 RDB 模式或者 AOF 模式），操作日志及 Replication 等功能。

* Hash——字典

  Redis 的 Hash 结构可以使你像在数据库中 Update 一个属性一样只修改某一项属性值。用于存储、读取、修改用户属性


* List——列表

  使用 List 结构可以实现：1、最新消息排行等功能（比如新浪微博的 TimeLine ）；2、消息队列，可以利用 List 的 PUSH 操作，将任务存在 List 中，然后工作线程再用 POP 操作将任务取出进行执行。

* Set——集合

  使用Set 结构，可以存储一些集合性的数据。比如在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。因为 Redis 非常人性化的为集合提供了求交集、并集、差集等操作，那么就可以非常方便的实现如共同关注、共同喜好、二度好友等功能。

* Sorted Set——有序集合

  Sorted Sets是将 Set 中的元素增加了一个权重参数 score，使得集合中的元素能够按 score 进行有序排列。比如一个存储全班同学成绩的 Sorted Sets，其集合 value 可以是同学的学号，而 score 就可以是其考试得分，这样在数据插入集合的时候，就已经进行了天然的排序。

## 十三、JVM

### 1、 JVM 的主要组成部分？及其作用？

* **类加载器**（ClassLoader）
* **运行时数据区**（Runtime Data Area）
* 执行引擎（Execution Engine）
* 本地库接口（Native Interface） 

组件的作用： 首先通过通过 Javac 编译器将 Java 代码编译为字节码文件，类加载器（ClassLoader）会加载 .class 文件，运行时数据区（Runtime Data Area）把字节码文件加载到内存中，而字节码文件只是 JVM 的一套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器—执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来实现整个程序的功能。

### 2、类加载器介绍？

类加载器会根据指定全限定名称将 class 文件加载到 JVM 内存，然后再转化为 class 对象。类通常是按需加载，即第一次使用该类时才加载。

分类如下：

* 启动类加载器（Bootstrap ClassLoader），是虚拟机自身的一部分，用来加载`Java_HOME/lib/`目录中，或者被 -Xbootclasspath 参数所指定的路径中并且被虚拟机识别的类库；

* 扩展类加载器（Extension ClassLoader）：负责加载`Java_HOME\lib\ext`目录或`Java. ext. dirs`系统变量指定的路径中的所有类库；

  `对象.getClass().getClassLoader().getParent() -> ExtClassLoader`

* 应用程序类加载器（Application ClassLoader）。负责加载用户类路径（classpath）上的指定类库，我们可以直接使用这个类加载器。一般情况，如果我们没有自定义类加载器**默认就是用这个加载器**。

  `对象.getClass().getClassLoader() -> AppClassLoader`

### 3、类加载的执行过程？

类加载分为以下 5 个步骤：

1. 加载：根据查找路径找到相应的 class 文件然后导入；
2. 检查：检查加载的 class 文件的正确性；
3. 准备：给类中的静态变量分配内存空间；
4. 解析：虚拟机将常量池中的符号引用替换成直接引用的过程。符号引用就理解为一个标示，而在直接引用直接指向内存中的地址；
5. 初始化：对静态变量和静态代码块执行初始化工作。

### 4、什么是双亲委派模型？

双亲委派模型：如果一个类加载器收到了类加载的请求，它首先不会自己去加载这个类，而是把这个请求委派给父类加载器去完成，每一层的类加载器都是如此，这样所有的加载请求都会被传送到顶层的启动类加载器中，只有当父加载无法完成加载请求（它的搜索范围中没找到所需的类）时，子加载器才会尝试去加载类。

> 比如：自己写个 String 类的 toString() 方法，但是执行时不会执行他的，JVM 会去执行根加载器 Bootstrap ClassLoader 中的 String 类中的 toString() 方法。

### 5、JVM 运行时数据区？

* 堆

* 方法区

* 虚拟机栈

* 本地方法栈(调用JNI接口)

* 程序计数器

![image-20200525200409716](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200525200409716.png)

> 什么是堆？

1. 存储的全部是对象，每个对象都包含一个与之对应的class的信息；
2. JVM只有一个堆区(heap)被所有线程共享，堆中不存放基本类型和对象引用，只存放对象本身。

> 什么是(虚拟机)栈？

1. 每个线程包含一个栈区，栈中只保存基础数据类型的对象和自定义对象的引用(不是对象)；
2. 每个栈中的数据(原始类型和对象引用)都是私有的，其他栈不能访问；
3. 栈分为3个部分：基本类型变量区、执行环境上下文、操作指令区(存放操作指令)。

> 什么是方法区？

1. 又叫静态区，跟堆一样，被所有的线程共享。方法区包含所有的class和static变量。
2. 方法区中包含的都是在整个程序中永远唯一的元素，如class，static变量。

### 6、堆栈的区别？

1. 栈内存存储的是局部变量。堆内存存储的是实体；
2. 栈内存的更新速度要快于堆内存，因为局部变量的生命周期很短；
3. 栈内存存放的变量生命周期一旦结束就会被释放。堆内存存放的实体会被垃圾回收机制不定时的回收。

### 7、怎么判断对象是否可以被回收？

`引用计数器`：为每个对象创建一个引用计数，当有对象引用时计数器 +1，引用被释放时计数 -1，当计数器为 0 时就被回收。它有一个缺点：不能解决循环引用的问题（互相引用的对象无法回收）；

`可达性分析`：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是可以被回收的。

### 8、JVM 有哪些垃圾回收算法？

* 标记-清除算法

  先扫描 GC Roots，对活着的对象进行标记。之后再扫描一次 GC Roots，清除没有标记的对象。无需额外空间，但是浪费时间，会产生内存碎片。

* 标记-整理算法(老年代垃圾回收器)

  在标记-清除的基础上，移动所有存活的对象，且按照内存地址次序依次排列，然后将末端内存地址以后的内存全部回收。没有了内存碎片，但还是浪费时间。

* 复制算法(新生代垃圾回收器)

  复制算法为了保证幸存区中有一个区是空的，空的区为To区。复制算法将内存划分为两个区间，在任意时间点，所有动态分配的对象都只能分配在其中一个区间（From），而另外一个区间（To）则是空闲的。

### 9、分代垃圾回收器是怎么工作的？

分代回收器有两个分区：老生代、新生代，新生代默认的空间占比总空间的 1/3，老生代的默认占比是 2/3。

新生代使用的是复制算法，新生代里有 3 个分区：Eden、To Survivor、From Survivor，它们的默认占比是 8:1:1，它的执行流程如下：

1. Eden 满了-- minor gc -- 把 Eden 存活的对象放入 From 区；
2. 清空 Eden 分区；
3. From 区满了-- minor gc -- 把 From 区存活的对象放入 To 区；
4. 清空 From 分区；
5. From 和 To 分区交换，From 变 To ，To 变 From（谁空谁是 To 区）。

每次在 From 到 To 移动时都存活的对象，年龄就 +1，当年龄到达 15（默认配置是 15）时，升级为老年代。当老年代空间占用到达某个值之后就会触发 full gc，一般使用标记-整理算法回收。以上这些循环往复就构成了整个分代垃圾回收的整体执行流程。

### 10、Static关键字

被static关键字修饰的不需要创建对象去调用，直接根据类名就可以去访问。

* 修饰类：Java里面static一般用来修饰成员变量或函数。但有一种特殊用法是用static修饰内部类，普通类是不允许声明为静态的，只有内部类才可以。
* 修饰方法： 修饰方法的时候，其实跟类一样，可以直接通过类名来进行调用
* 修饰变量： 被static修饰的成员变量叫做静态变量，也叫做类变量，说明这个变量是属于这个类的，而不是属于是对象，没有被static修饰的成员变量叫做实例变量，说明这个变量是属于某个具体的对象的。 static修饰的成员被所有的对象共享的。

static关键字修饰代码块时，类初始化的顺序：父类静态变量->父类静态代码块->子类静态变量->子类静态代码块->父类普通变量->父类普通代码块->父类构造函数->子类普通变量->子类普通代码块->子类构造函数

> 是什么原理？

静态变量存放在JVM的方法区中，并且是被所有线程所共享的。而Java堆存放的就是我们创建的一个个实例变量(非static变量)。详见JVM方法区的内容。

### 11、内存溢出

在JVM申请内存的过程中，会遇到无法申请到足够内存，从而导致内存溢出的情况。一般有以下几种情况：

1. 虚拟机栈/本地方法栈溢出
   * StackOverflowError：线程请求的栈深度大于虚拟机所允许的最大深度(循环递归)；
   * OutOfMemoryError：虚拟机在扩展栈时无法申请到足够内存空间，不停地创建线程会引起此种情况；
2. Java堆溢出：当创建大量对象并且对象生命周期都很长的情况下，会引发OutOfMemoryError；
3. 运行时常量区溢出：OutOfMemoryError:PermGen space；
4. 方法区溢出：方法区存放Class等元数据信息，若产生大量的类(使用cglib)，那么就会引发此内存溢出：OutOfMemoryError:PermGen space，在使用Hibernate等框架时就会容易引起此种情况。

## 十四、Linux

### 1、Linux与内核？

> Linux系统包含什么？

Linux系统一般有4个主要部分：**内核**、**shell**、文件系统和应用程序。其中Linux内核主要由五个子系统组成：进程调度、内存管理、虚拟文件系统、网络接口、进程通信。

> 什么是内核？

Linux内核是一种低级系统软件，其主要作用是为用户管理硬件资源。它还用于为用户级交互提供界面。真正能够控制计算机硬件（CPU、内存、显示器等）的只有操作系统内核（Kernel），图形界面和命令行只是架设在用户和内核之间的一座桥梁。

> 怎么去操作内核呢？

由于安全、复杂、繁琐等原因，用户不能直接接触内核（也没有必要），需要另外再开发一个程序，让用户直接使用这个程序；该程序的作用就是接收用户的操作（点击图标、输入命令），并进行简单的处理，然后再传递给内核，这样用户就能间接地使用操作系统内核了。在用户和内核之间增加一层“代理”，既能简化用户的操作，又能保障内核的安全。

Shell 是一个应用程序，它连接了用户和 Linux 内核，让用户能够更加高效、安全、低成本地使用 Linux 内核，这就是 Shell 的本质。

Shell 本身并不是内核的一部分，它只是站在内核的基础上编写的一个应用程序，它和 QQ、迅雷、Firefox 等其它软件没有什么区别。然而 Shell 也有着它的特殊性，就是开机立马启动，并呈现在用户面前。

<img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200609141133198.png" alt="image-20200609141133198" style="zoom:80%;" />

### 2、日常命令

* `mkdir 目录名称`：新增目录

* `pwd`：获取当前路径
  * 上层目录：../
  * 主目录：~
  * 切换目录：cd
  
* `ll -a`：查看当前目录夹所有文件和目录

* `rm -rf 目录`：递归删除指定目录下的所有文件

* `vi 文件`：修改文件

* `mv 源文件夹名 新文件夹名`：移动文件夹到指定目录

* `grep`：查找想要的信息

* `cp -r 源目录名 新目录名`：拷贝指定目录中的文件到新目录中

* `touch 文件名`：创建文件

#### 2.1 grep

grep（global search regular expression (RE) and print out the line）作为一款非常方便且强大的**文本搜索**工具，其能使用正则表达式搜索文本，并把匹配的行打印出来。

#### 2.2 sed

Sed 全名为 Stream EDitor，顾名思义是**对数据流进行编辑**操作的一个命令，它能够遍历文件或文件流，对读入的输入流可以将其先存储在模式空间中，并将行号记录在内存中，利用模式空间中的一系列指定命令对其进行操作，待操作完成后从模式空间输出到 stdout，类似于在一个管道在其中对数据进行加工，完成后从另一头输出，接着读取下一行，重复往返，直至将所有标准输入读取处理完成。

其主要功能为对文件进行修改处理，可以对文件或标准输入数据流进行增删改查等操作，尤其适用于大文件或有规律的文件。

#### 2.3 awk

在文本处理功能非常强大，是一款Linux服务器文本报告生成器和格式化文本输出工具。

### 3、查看活动进程的命令

* ps：用于显示系统内的所有进程

  `ps -elf`：查看所有进程
  ps -ef | grep java：查看和java有关的所有进程

* top：用于查看活动进程的CPU和内存信息，能够实时显示系统中各个进程的资源占用情况，
  可以按照CPU、内存的使用情况和执行时间对进程进行排序

### 4、查看日志常用命令

* history：查看所有的历史记录
  history | grep xxx：查看历史记录中包含某些指令的记录
  history | more：分页查看记录
  history -c：清空所有的历史记录

* cat：查看内容

  cat -n test.log | grep "run"：在test.log中查询包含run的行
  cat test.log：显示test.log中的所有内容`

### 5、压缩文件命令

* tar：压缩指令
  
  `tar -xvf file.tar`：解压 tar 包
 `tar -xzvf file.tar.gz`： 解压 tar.gz 格式的包
  tar -zcvf abc.gz a.txt b.txt c.txt：将这三个文件压缩成 abc.gz 压缩包
  unrar e file.rar //解压rar
  unzip file.zip //解压zip

### 6、文件传输命令

* scp
  scp -p ./files/yum.log 192.168.214.187:/tmp/demo/：表示将当前系统的files目录下的yum日志文件复制到IP为 192.168.214.18 的机器的/tmp/demo目录下。
* sz
  sz test.log：下载test.log日志文件到windows下。
* rz
  文件上传，出现弹出框，选中要上传的文件，确定之后即可上传文件。

### 7、网络配置相关命令

* `ifconfig`：查看当前系统的网卡信息，和`ip addr`命令类似

* ping：查看与某台机器的连接情况

* netstat：用于显示各种网络相关信息，如网络连接，路由表，接口状态，masquerade 连接，多播成员等
  查看当前系统的端口使用：netstat -an
  列出所有端口： netstat -a
