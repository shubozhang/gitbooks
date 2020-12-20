#Interview Questions


1. Java中的原始数据类型都有哪些，它们的大小及对应的封装类是什么？
3. Java中的四种引用及其应用场景是什么？
强引用: 通常我们使用new操作符创建一个对象时所返回的引用即为强引用
软引用: 若一个对象只能通过软引用到达，那么这个对象在内存不足时会被回
收，可用于图片缓存中，内存不足时系统会自动回收不再使用的Bitmap
弱引用: 若一个对象只能通过弱引用到达，那么它就会被回收（即使内存充
足），同样可用于图片缓存中，这时候只要Bitmap不再使用就会被回收
虚引用: 虚引用是Java中最“弱”的引用，通过它甚至无法获取被引用的对象，它
存在的唯一作用就是当它指向的对象回收时，它本身会被加入到引用队列中，这
样我们可以知道它指向的对象何时被销毁。
4. object中定义了哪些方法？
clone(), equals(), hashCode(), toString(), notify(), notifyAll(), wait(), finalize(),
getClass()
6. ArrayList, LinkedList, Vector的区别是什么？
ArrayList: 内部采用数组存储元素，支持高效随机访问，支持动态调整大小
LinkedList: 内部采用链表来存储元素，支持快速插入/删除元素，但不支持高效
地随机访问
Vector: 可以看作线程安全版的ArrayList
7. String, StringBuilder, StringBuffer的区别是什么？
String: 不可变的字符序列，若要向其中添加新字符需要创建一个新的String对象
StringBuilder: 可变字符序列，支持向其中添加新字符（无需创建新对象）
StringBuffer: 可以看作线程安全版的StringBuilder
8. Map, Set, List, Queue、Stack的特点及用法。
Map<K, V>: Java中存储键值对的数据类型都实现了这个接口，表示“映射表”。
支持的两个核心操作是get(Object key)以及put(K key, V value)，分别用来获取键
对应的值以及向映射表中插入键值对。
Set<E>: 实现了这个接口的集合类型中不允许存在重复的元素，代表数学意义上
的“集合”。它所支持的核心操作有add(E e), remove(Object o), contains(Object
o)，分别用于添加元素，删除元素以及判断给定元素是否存在于集中。
List<E>: Java中集合框架中的列表类型都实现了这个接口，表示一种有序序列。
支持get(int index), add(E e)等操作。
Queue<E>: Java集合框架中的队列接口，代表了“先进先出”队列。支持add(E
element), remove()等操作。
Stack<E>: Java集合框架中表示堆栈的数据类型，堆栈是一种“后进先出”的数据
结构。支持push(E item), pop()等操作。
更详细的说明请参考官方文档，对相关数据结构不太熟悉的同学可以参考《算法导
论》或其他相关书籍。
9. HashMap和HashTable的区别。
HashTable是线程安全的，而HashMap不是
HashMap中允许存在null键和null值，而HashTable中不允许
更加详细的信息请点击“阅读原文”
10. HashMap的实现原理
简单的说，HashMap的底层实现是“基于拉链法的散列表”。更加详细的分析请点
击“阅读原文”。
11. ConcurrentHashMap的实现原理
ConcurrentHashMap是支持并发读写的HashMap，它的特点是读取数据时无需加
锁，写数据时可以保证加锁粒度尽可能的小。由于其内部采用“分段存储”，只需对要
进行写操作的数据所在的“段”进行加锁。关于ConcurrentHashMap底层实现的更加
详细分析请点击“阅读原文”。
12. TreeMap, LinkedHashMap, HashMap的区别是什么？
HashMap的底层实现是散列表，因此它内部存储的元素是无序的；
TreeMap的底层实现是红黑树，所以它内部的元素的有序的。排序的依据是自然
序或者是创建TreeMap时所提供的比较器（Comparator）对象。
LinkedHashMap能够记住插入元素的顺序。
13. Collection与Collections的区别是什么？
Collection<E>是Java集合框架中最基本的接口；
Collections是Java集合框架提供的一个工具类，包含了大量用于操作或返回集合的静态方法。
14. 对于“try-catch-finally”，若try语句块中包含“return”语句，finally语句块会执行
吗？
答案是会执行。只有两种情况finally块中的语句不会被执行：
调用了System.exit()方法；
JVM“崩溃”了。
15. Java中的异常层次结构
更加详细的说明请点击“阅读原文”。
16. Java面向对象的三个特征与含义
17. Override, Overload的含义与区别
Override表示“重写”，是子类对父类中同一方法的重新定义
Overload表示“重载”，也就是定义一个与已定义方法名称相同但签名不同的新方
法

19. 静态内部类与非静态内部类的区别
静态内部类不会持有外围类的引用，而非静态内部类会隐式持有外围类的一个引
用。关于内部类的详细介绍请点击“阅读原文”。

21. 简述Java中创建新线程的两种方法
继承Thread类（假设为MyThread），并重写run()方法，然后new一个MyThread
对象并对其调用start()即可启动新线程。
实现Runnable接口（假设实现类为MyRunnable），而后将MyRunnable对象作为
参数传入Thread构造器，在得到的Thread对象上调用start()方法即可。
22. 简述Java中进行线程同步的方法
volatile: Java Memory Model保证了对同一个volatile变量的写happens before对
它的读；
synchronized: 可以来对一个代码块或是对一个方法上锁，被“锁住”的地方称为
临界区，进入临界区的线程会获取对象的monitor，这样其他尝试进入临界区的
线程会因无法获取monitor而被阻塞。由于等待另一个线程释放monitor而被阻塞
的线程无法被中断。
ReentrantLock: 尝试获取锁的线程可以被中断并可以设置超时参数。
更加详细的介绍请点击“阅读原文”。
23. 简述Java中具有哪几种粒度的锁
Java中可以对类、对象、方法或是代码块上锁。
24. 给出“生产者-消费者”问题的一种解决方案
25. ThreadLocal的设计理念与作用
ThreadLocal的作用是提供线程内的局部变量，在多线程环境下访问时能保证各个线
程内的ThreadLocal变量各自独立。也就是说，每个线程的ThreadLocal变量是自己
专用的，其他线程是访问不到的。ThreadLocal最常用于以下这个场景：多线程环境
下存在对非线程安全对象的并发访问，而且该对象不需要在线程间共享，但是我们
不想加锁，这时候可以使用ThreadLocal来使得每个线程都持有一个该对象的副本。
26. concurrent包的整体架构
27. ArrayBlockingQueue, CountDownLatch类的作用
CountDownLatch: 允许线程集等待直到计数器为0。适用场景: 当一个或多个线
程需要等待指定数目的事件发生后再继续执行。
ArrayBlockingQueue: 一个基于数组实现的阻塞队列，它在构造时需要指定容
量。当试图向满队列中添加元素或者从空队列中移除元素时，当前线程会被阻
塞。通过阻塞队列，我们可以按以下模式来工作：工作者线程可以周期性的将中
间结果放入阻塞队列中，其它线程可以取出中间结果并进行进一步操作。若工作
者线程的执行比较慢（还没来得及向队列中插入元素），其他从队列中取元素的
线程会等待它（试图从空队列中取元素从而阻塞）；若工作者线程执行较快（试
图向满队列中插入元素），则它会等待其它线程取出元素再继续执行。
28. wait()，sleep()的区别
wait(): Object类中定义的实例方法。在指定对象上调用wait方法会让当前线程进
入等待状态（前提是当前线程持有该对象的monitor），此时当前线程会释放相
应对象的monitor，这样一来其它线程便有机会获取这个对象的monitor了。当其
它线程获取了这个对象的monitor并进行了所需操作时，便可以调用notify方法唤
醒之前进入等待状态的线程。
sleep(): Thread类中的静态方法，作用是让当前线程进入休眠状态，以便让其他
线程有机会执行。进入休眠状态的线程不会释放它所持有的锁。
29. 线程池的用法与优势
优势: 实现对线程的复用，避免了反复创建及销毁线程的开销；使用线程池统一
管理线程可以减少并发线程的数目，而线程数过多往往会在线程上下文切换上以
及线程同步上浪费过多时间。
用法: 我们可以调用ThreadPoolExecutor的某个构造方法来自己创建一个线程
池。但通常情况下我们可以使用Executors类提供给我们的静态工厂方法来更方
便的创建一个线程池对象。创建了线程池对象后，我们就可以调用submit方法提
交任务到线程池中去执行了；线程池使用完毕后我们要记得调用shutdown方法
来关闭它。
关于线程池的详细介绍以及实现原理分析请点击“阅读原文”。

31. 简述Java IO与NIO的区别
Java IO是面向流的，这意味着我们需要每次从流中读取一个或多个字节，直到
读取完所有字节；NIO是面向缓冲的，也就是说会把数据读取到一个缓冲区中，
然后对缓冲区中的数据进行相应处理。
Java IO是阻塞IO，而NIO是非阻塞IO。
Java NIO中存在一个称为选择器（selector）的东西，它允许你把多个通道
（channel）注册到一个选择器上，然后使用一个线程来监视这些通道：若这些
通道里有某个准备好可以开始进行读或写操作了，则开始对相应的通道进行读
写。而在等待某通道变为可读/写期间，请求对通道进行读写操作的线程可以去
干别的事情。
32. 反射的作用与原理
反射的作用概括地说是运行时获取类的各种定义信息，比如定义了哪些属性与方
法。原理是通过类的class对象来获取它的各种信息。

34. Java 1.7与1.8的新特性
35. 常见设计模式
所谓“设计模式”，不过是面向对象编程中一些常用的软件设计手法，并且经过实践
的检验，这些设计手法在各自的场景下能解决一些需求，因此它们就成为了如今广
为流传的”设计模式“。也就是说，正式因为在某些场景下产生了一些棘手的问题，才
催生了相应的设计模式。明确了这一点，我们在学习某种设计模式时要充分理解它
产生的背景以及它所解决的主要矛盾是什么。
常用的设计模式可以分为以下三大类：
创建型模式: 包括工厂模式（又可进一步分为简单工厂模式、工厂方法模式、抽
象工厂模式）、建造者模式、单例模式。
结构型模式: 包括适配器模式、桥接模式、装饰模式、外观模式、享元模式、代
理模式。
行为型模式: 包括命令模式、中介者模式、观察者模式、状态模式、策略模式。
关于每个模式具体的介绍请点击“阅读原文”。
36. JNI的基本用法
37. 动态代理的定义、应用场景及原理
38. 注解的基本概念与使用
注解可以看作是“增强版的注释”，它可以向编译器、虚拟机说明一些事情。
注解是描述Java代码的代码，它能够被编译器解析，注解处理工具在运行时也能
Read more
够解析注解。注解本身是“被动”的信息，只有主动解析它才有意义。
除了向编译器/虚拟机传递信息，我们也可以使用注解来生成一些“模板化”的代
码。













###################################################################################




4、float f=3.4;是否正确？
答:不正确。3.4是双精度数，将双精度型（double）赋值给浮点型（float）属于下转型（down-casting，也称为窄化）会造成精度损失，因此需要强制类型转换float f =(float)3.4; 或者写成float f =3.4F;。








8、&和&&的区别？
答：&运算符有两种用法：(1)按位与；(2)逻辑与。&&运算符是短路与运算。逻辑与跟短路与的差别是非常巨大的，虽然二者都要求运算符左右两端的布尔值都是true整个表达式的值才是true。&&之所以称为短路运算是因为，如果&&左边的表达式的值是false，右边的表达式会被直接短路掉，不会进行运算。很多时候我们可能都需要用&&而不是&，例如在验证用户登录时判定用户名不是null而且不是空字符串，应当写为：username != null &&!username.equals("")，二者的顺序不能交换，更不能用&运算符，因为第一个条件如果不成立，根本不能进行字符串的equals比较，否则会产生NullPointerException异常。注意：逻辑或运算符（|）和短路或运算符（||）的差别也是如此。

    补充：如果你熟悉JavaScript，那你可能更能感受到短路运算的强大，想成为JavaScript的高手就先从玩转短路运算开始吧。


9、解释内存中的栈(stack)、堆(heap)和方法区(method area)的用法。
答：通常我们定义一个基本数据类型的变量，一个对象的引用，还有就是函数调用的现场保存都使用JVM中的栈空间；而通过new关键字和构造器创建的对象则放在堆空间，堆是垃圾收集器管理的主要区域，由于现在的垃圾收集器都采用分代收集算法，所以堆空间还可以细分为新生代和老生代，再具体一点可以分为Eden、Survivor（又可分为From Survivor和To Survivor）、Tenured；方法区和堆都是各个线程共享的内存区域，用于存储已经被JVM加载的类信息、常量、静态变量、JIT编译器编译后的代码等数据；程序中的字面量（literal）如直接书写的100、"hello"和常量都是放在常量池中，常量池是方法区的一部分，。栈空间操作起来最快但是栈很小，通常大量的对象都是放在堆空间，栈和堆的大小都可以通过JVM的启动参数来进行调整，栈空间用光了会引发StackOverflowError，而堆和常量池空间不足则会引发OutOfMemoryError。

String str = new String("hello");

上面的语句中变量str放在栈上，用new创建出来的字符串对象放在堆上，而"hello"这个字面量是放在方法区的。

    补充1：较新版本的Java（从Java 6的某个更新开始）中，由于JIT编译器的发展和"逃逸分析"技术的逐渐成熟，栈上分配、标量替换等优化技术使得对象一定分配在堆上这件事情已经变得不那么绝对了。

    补充2：运行时常量池相当于Class文件常量池具有动态性，Java语言并不要求常量一定只有编译期间才能产生，运行期间也可以将新的常量放入池中，String类的intern()方法就是这样的。

看看下面代码的执行结果是什么并且比较一下Java 7以前和以后的运行结果是否一致。

String s1 = new StringBuilder("go")
    .append("od").toString();
System.out.println(s1.intern() == s1);String s2 = new StringBuilder("ja")
    .append("va").toString();
System.out.println(s2.intern() == s2);



11、switch 是否能作用在byte 上，是否能作用在long 上，是否能作用在String上？
答：在Java 5以前，switch(expr)中，expr只能是byte、short、char、int。从Java 5开始，Java中引入了枚举类型，expr也可以是enum类型，从Java 7开始，expr还可以是字符串（String），
但是长整型（long）在目前所有的版本中都是不可以的。

12、用最有效率的方法计算2乘以8？
答： 2 << 3（左移3位相当于乘以2的3次方，右移3位相当于除以2的3次方）。

    补充：我们为编写的类重写hashCode方法时，可能会看到如下所示的代码，其实我们不太理解为什么要使用这样的乘法运算来产生哈希码（散列码），
    而且为什么这个数是个素数，为什么通常选择31这个数？前两个问题的答案你可以自己百度一下，选择31是因为可以用移位和减法运算来代替乘法，从而得到更好的性能。
    说到这里你可能已经想到了：31 * num 等价于(num << 5) - num，左移5位相当于乘以2的5次方再减去自身就相当于乘以31，现在的VM都能自动完成这个优化。

   

public class PhoneNumber {
    private int areaCode;
    private String prefix;
    private String lineNumber;

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + areaCode;
        result = prime * result
                + ((lineNumber == null) ? 0 : lineNumber.hashCode());
        result = prime * result + ((prefix == null) ? 0 : prefix.hashCode());
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        PhoneNumber other = (PhoneNumber) obj;
        if (areaCode != other.areaCode)
            return false;
        if (lineNumber == null) {
            if (other.lineNumber != null)
                return false;
        } else if (!lineNumber.equals(other.lineNumber))
            return false;
        if (prefix == null) {
            if (other.prefix != null)
                return false;
        } else if (!prefix.equals(other.prefix))
            return false;
        return true;
    }

}


14、在Java中，如何跳出当前的多重嵌套循环？
答：在最外层循环前加一个标记如A，然后用break A;可以跳出多重循环。（Java中支持带标签的break和continue语句，作用有点类似于C和C++中的goto语句，
但是就像要避免使用goto一样，应该避免使用带标签的break和continue，因为它不会让你的程序变得更优雅，很多时候甚至有相反的作用，所以这种语法其实不知道更好）

15、构造器（constructor）是否可被重写（override）？
答：构造器不能被继承，因此不能被重写，但可以被重载。

16、两个对象值相同(x.equals(y) == true)，但却可有不同的hash code，这句话对不对？
答：不对，如果两个对象x和y满足x.equals(y) == true，它们的哈希码（hash code）应当相同。Java对于eqauls方法和hashCode方法是这样规定的：
(1)如果两个对象相同（equals方法返回true），那么它们的hashCode值一定要相同；(2)如果两个对象的hashCode相同，它们并不一定相同。当然，你未必要按照要求去做，
但是如果你违背了上述原则就会发现在使用容器时，相同的对象可以出现在Set集合中，同时增加新元素的效率会大大下降（对于使用哈希存储的系统，如果哈希码频繁的冲突将会造成存取性能急剧下降）。

    补充：关于equals和hashCode方法，很多Java程序都知道，但很多人也就是仅仅知道而已，在Joshua Bloch的大作《Effective Java》
    （很多软件公司，《Effective Java》、《Java编程思想》以及《重构：改善既有代码质量》是Java程序员必看书籍，如果你还没看过，那就赶紧去亚马逊买一本吧）
    中是这样介绍equals方法的：首先equals方法必须满足自反性（x.equals(x)必须返回true）、对称性（x.equals(y)返回true时，y.equals(x)也必须返回true）、
    传递性（x.equals(y)和y.equals(z)都返回true时，x.equals(z)也必须返回true）和一致性（当x和y引用的对象信息没有被修改时，多次调用x.equals(y)应该得到同样的返回值），
    而且对于任何非null值的引用x，x.equals(null)必须返回false。实现高质量的equals方法的诀窍包括：
    1. 使用==操作符检查"参数是否为这个对象的引用"；
    2. 使用instanceof操作符检查"参数是否为正确的类型"；
    3. 对于类中的关键属性，检查参数传入对象的属性是否与之相匹配；
    4. 编写完equals方法后，问自己它是否满足对称性、传递性、一致性；
    5. 重写equals时总是要重写hashCode；
    6. 不要将equals方法参数中的Object对象替换为其他的类型，在重写时不要忘掉@Override注解。



18、当一个对象被当作参数传递到一个方法后，此方法可改变这个对象的属性，并可返回变化后的结果，那么这里到底是值传递还是引用传递？
答：是值传递。Java语言的方法调用只支持参数的值传递。当一个对象实例作为一个参数被传递到方法中时，参数的值就是对该对象的引用。对象的属性可以在被调用过程中被改变，但对对象引用的改变是不会影响到调用者的。C++和C#中可以通过传引用或传输出参数来改变传入的参数的值。在C#中可以编写如下所示的代码，但是在Java中却做不到。

using System;
namespace CS01 {
    class Program {
        public static void swap(ref int x, ref int y) {
            int temp = x;
            x = y;
            y = temp;
        }
        public static void Main (string[] args) {
            int a = 5, b = 10;
            swap (ref a, ref b);
            // a = 10, b = 5;
            Console.WriteLine ("a = {0}, b = {1}", a, b);
        }
    }
}

    说明：Java中没有传引用实在是非常的不方便，这一点在Java 8中仍然没有得到改进，正是如此在Java编写的代码中才会出现大量的Wrapper类
    （将需要通过方法调用修改的引用置于一个Wrapper类中，再将Wrapper对象传入方法），
    这样的做法只会让代码变得臃肿，尤其是让从C和C++转型为Java程序员的开发者无法容忍。





21、描述一下JVM加载class文件的原理机制？
答：JVM中类的装载是由类加载器（ClassLoader）和它的子类来实现的，Java中的类加载器是一个重要的Java运行时系统组件，它负责在运行时查找和装入类文件中的类。
由于Java的跨平台性，经过编译的Java源程序并不是一个可执行程序，而是一个或多个类文件。当Java程序需要使用某个类时，JVM会确保这个类已经被加载、连接（验证、准备和解析）和初始化。类的加载是指把类的.class文件中的数据读入到内存中，通常是创建一个字节数组读入.class文件，然后产生与所加载类对应的Class对象。加载完成后，Class对象还不完整，所以此时的类还不可用。当类被加载后就进入连接阶段，这一阶段包括验证、准备（为静态变量分配内存并设置默认的初始值）和解析（将符号引用替换为直接引用）三个步骤。最后JVM对类进行初始化，包括：1)如果类存在直接的父类并且这个类还没有被初始化，那么就先初始化父类；2)如果类中存在初始化语句，就依次执行这些初始化语句。
类的加载是由类加载器完成的，类加载器包括：根加载器（BootStrap）、扩展加载器（Extension）、系统加载器（System）和用户自定义类加载器（java.lang.ClassLoader的子类）。从Java 2（JDK 1.2）开始，类加载过程采取了父亲委托机制（PDM）。PDM更好的保证了Java平台的安全性，在该机制中，JVM自带的Bootstrap是根加载器，其他的加载器都有且仅有一个父类加载器。类的加载首先请求父类加载器加载，父类加载器无能为力时才由其子类加载器自行加载。JVM不会向Java程序提供对Bootstrap的引用。下面是关于几个类加载器的说明：

        Bootstrap：一般用本地代码实现，负责加载JVM基础核心类库（rt.jar）；

        Extension：从java.ext.dirs系统属性所指定的目录中加载类库，它的父加载器是Bootstrap；

        System：又叫应用类加载器，其父类是Extension。它是应用最广泛的类加载器。它从环境变量classpath或者系统属性java.class.path所指定的目录中记载类，是用户自定义加载器的默认父加载器。

22、char 型变量中能不能存贮一个中文汉字，为什么？
答：char类型可以存储一个中文汉字，因为Java中使用的编码是Unicode（不选择任何特定的编码，直接使用字符在字符集中的编号，这是统一的唯一方法），
一个char类型占2个字节（16比特），所以放一个中文是没问题的。

    补充：使用Unicode意味着字符在JVM内部和外部有不同的表现形式，在JVM内部都是Unicode，当这个字符被从JVM内部转移到外部时（
    例如存入文件系统中），需要进行编码转换。所以Java中有字节流和字符流，以及在字符流和字节流之间进行转换的转换流，
    如InputStreamReader和OutputStreamReader，这两个类是字节流和字符流之间的适配器类，承担了编码转换的任务；
    对于C程序员来说，要完成这样的编码转换恐怕要依赖于union（联合体/共用体）共享内存的特征来实现了。



24、静态嵌套类(Static Nested Class)和内部类（Inner Class）的不同？
答：Static Nested Class是被声明为静态（static）的内部类，它可以不依赖于外部类实例被实例化。
而通常的内部类需要在外部类实例化后才能实例化，其语法看起来挺诡异的，如下所示。

/**
 * 扑克类（一副扑克）
 * @author 骆昊
 *
 */
public class Poker {
    private static String[] suites = {"黑桃", "红桃", "草花", "方块"};
    private static int[] faces = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13};

    private Card[] cards;

    /**
     * 构造器
     * 
     */
    public Poker() {
        cards = new Card[52];
        for(int i = 0; i < suites.length; i++) {
            for(int j = 0; j < faces.length; j++) {
                cards[i * 13 + j] = new Card(suites[i], faces[j]);
            }
        }
    }

    /**
     * 洗牌 （随机乱序）
     * 
     */
    public void shuffle() {
        for(int i = 0, len = cards.length; i < len; i++) {
            int index = (int) (Math.random() * len);
            Card temp = cards[index];
            cards[index] = cards[i];
            cards[i] = temp;
        }
    }

    /**
     * 发牌
     * @param index 发牌的位置
     * 
     */
    public Card deal(int index) {
        return cards[index];
    }

    /**
     * 卡片类（一张扑克）
     * [内部类]
     * @author 骆昊
     *
     */
    public class Card {
        private String suite;   // 花色
        private int face;       // 点数

        public Card(String suite, int face) {
            this.suite = suite;
            this.face = face;
        }

        @Override
        public String toString() {
            String faceStr = "";
            switch(face) {
            case 1: faceStr = "A"; break;
            case 11: faceStr = "J"; break;
            case 12: faceStr = "Q"; break;
            case 13: faceStr = "K"; break;
            default: faceStr = String.valueOf(face);
            }
            return suite + faceStr;
        }
    }
}


测试代码：

class PokerTest {
    public static void main(String[] args) {
        Poker poker = new Poker();
        poker.shuffle();                // 洗牌
        Poker.Card c1 = poker.deal(0);  // 发第一张牌
        // 对于非静态内部类Card
        // 只有通过其外部类Poker对象才能创建Card对象
        Poker.Card c2 = poker.new Card("红心", 1);    // 自己创建一张牌
        System.out.println(c1);     // 洗牌后的第一张
        System.out.println(c2);     // 打印: 红心A
    }
}


    面试题 - 下面的代码哪些地方会产生编译错误？

class Outer {
    class Inner {}
    public static void foo() { new Inner(); }
    public void bar() { new Inner(); }
    public static void main(String[] args) {
        new Inner();
    }
}

    注意：Java中非静态内部类对象的创建要依赖其外部类对象，上面的面试题中foo和main方法都是静态方法，静态方法中没有this，也就是说没有所谓的外部类对象，因此无法创建内部类对象，如果要在静态方法中创建内部类对象，可以这样做：

    new Outer().new Inner();



26、抽象的（abstract）方法是否可同时是静态的（static）,是否可同时是本地方法（native），是否可同时被synchronized修饰？
答：都不能。抽象方法需要子类重写，而静态的方法是无法被重写的，因此二者是矛盾的。本地方法是由本地代码（如C代码）实现的方法，而抽象方法是没有实现的，也是矛盾的。
synchronized和方法的实现细节有关，抽象方法不涉及实现细节，因此也是相互矛盾的。

27、阐述静态变量和实例变量的区别。
答：静态变量是被static修饰符修饰的变量，也称为类变量，它属于类，不属于类的任何一个对象，一个类不管创建多少个对象，静态变量在内存中有且仅有一个拷贝；
实例变量必须依存于某一实例，需要先创建对象然后通过对象才能访问到它。静态变量可以实现让多个对象共享内存。

    补充：在Java开发中，上下文类和工具类中通常会有大量的静态成员。

28、是否可以从一个静态（static）方法内部发出对非静态（non-static）方法的调用？
答：不可以，静态方法只能访问静态成员，因为非静态方法的调用要先创建对象，在调用静态方法时可能对象并没有被初始化。

29、如何实现对象克隆？
答：有两种方式：
  1). 实现Cloneable接口并重写Object类中的clone()方法；
  2). 实现Serializable接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆，代码如下。
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;
public class MyUtil {
    private MyUtil() {
        throw new AssertionError();
    }
    @SuppressWarnings("unchecked")
    public static <T extends Serializable> T clone(T obj) throws Exception {
        ByteArrayOutputStream bout = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bout);
        oos.writeObject(obj);
        ByteArrayInputStream bin = new ByteArrayInputStream(bout.toByteArray());
        ObjectInputStream ois = new ObjectInputStream(bin);
        return (T) ois.readObject();
        // 说明：调用ByteArrayInputStream或ByteArrayOutputStream对象的close方法没有任何意义
        // 这两个基于内存的流只要垃圾回收器清理对象就能够释放资源，这一点不同于对外部资源（如文件流）的释放
    }
}

下面是测试代码：

import java.io.Serializable;
/**
 * 人类
 * @author 骆昊
 *
 */
class Person implements Serializable {
    private static final long serialVersionUID = -9102017020286042305L;
    private String name;    // 姓名
    private int age;        // 年龄
    private Car car;        // 座驾
    public Person(String name, int age, Car car) {
        this.name = name;
        this.age = age;
        this.car = car;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public Car getCar() {
        return car;
    }
    public void setCar(Car car) {
        this.car = car;
    }
    @Override
    public String toString() {
        return "Person [name=" + name + ", age=" + age + ", car=" + car + "]";
    }
}

/**
 * 小汽车类
 * @author 骆昊
 *
 */
class Car implements Serializable {
    private static final long serialVersionUID = -5713945027627603702L;
    private String brand;       // 品牌
    private int maxSpeed;       // 最高时速
    public Car(String brand, int maxSpeed) {
        this.brand = brand;
        this.maxSpeed = maxSpeed;
    }
    public String getBrand() {
        return brand;
    }
    public void setBrand(String brand) {
        this.brand = brand;
    }
    public int getMaxSpeed() {
        return maxSpeed;
    }
    public void setMaxSpeed(int maxSpeed) {
        this.maxSpeed = maxSpeed;
    }
    @Override
    public String toString() {
        return "Car [brand=" + brand + ", maxSpeed=" + maxSpeed + "]";
    }
}

class CloneTest {
    public static void main(String[] args) {
        try {
            Person p1 = new Person("Hao LUO", 33, new Car("Benz", 300));
            Person p2 = MyUtil.clone(p1);   // 深度克隆
            p2.getCar().setBrand("BYD");
            // 修改克隆的Person对象p2关联的汽车对象的品牌属性
            // 原来的Person对象p1关联的汽车不会受到任何影响
            // 因为在克隆Person对象时其关联的汽车对象也被克隆了
            System.out.println(p1);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

    注意：基于序列化和反序列化实现的克隆不仅仅是深度克隆，更重要的是通过泛型限定，可以检查出要克隆的对象是否支持序列化，这项检查是编译器完成的，
    不是在运行时抛出异常，这种是方案明显优于使用Object类的clone方法克隆对象。让问题在编译的时候暴露出来总是好过把问题留到运行时。


        
        
31、String s = new String("xyz");创建了几个字符串对象？
答：两个对象，一个是静态区的"xyz"，一个是用new创建在堆上的对象。

32、接口是否可继承（extends）接口？抽象类是否可实现（implements）接口？抽象类是否可继承具体类（concrete class）？
答：接口可以继承接口，而且支持多重继承。抽象类可以实现(implements)接口，抽象类可继承具体类也可以继承抽象类。

33、一个".java"源文件中是否可以包含多个类（不是内部类）？有什么限制？
答：可以，但一个源文件中最多只能有一个公开类（public class）而且文件名必须和公开类的类名完全保持一致。

34、Anonymous Inner Class(匿名内部类)是否可以继承其它类？是否可以实现接口？
答：可以继承其他类或实现其他接口，在Swing编程和Android开发中常用此方式来实现事件监听和回调。

35、内部类可以引用它的包含类（外部类）的成员吗？有没有什么限制？
答：一个内部类对象可以访问创建它的外部类对象的成员，包括私有成员。



37、指出下面程序的运行结果。

class A {    static {
        System.out.print("1");
    }
    public A() {
        System.out.print("2");
    }
}
class B extends A{
    static {
        System.out.print("a");
    }
    public B() {
        System.out.print("b");
    }
}
public class Hello {
    public static void main(String[] args) {
        A ab = new B();
        ab = new B();
    }
}


答：执行结果：1a2b2b。创建对象时构造器的调用顺序是：先初始化静态成员，然后调用父类构造器，再初始化非静态成员，最后调用自身构造器。

    提示：如果不能给出此题的正确答案，说明之前第21题Java类加载机制还没有完全理解，赶紧再看看吧。

38、数据类型之间的转换：
- 如何将字符串转换为基本数据类型？
- 如何将基本数据类型转换为字符串？
答：
- 调用基本数据类型对应的包装类中的方法parseXXX(String)或valueOf(String)即可返回相应基本类型；
- 一种方法是将基本数据类型与空字符串（""）连接（+）即可获得其所对应的字符串；另一种方法是调用String 类中的valueOf()方法返回相应字符串

39、如何实现字符串的反转及替换？
答：方法很多，可以自己写实现也可以使用String或StringBuffer/StringBuilder中的方法。有一道很常见的面试题是用递归实现字符串反转，代码如下所示：

public static String reverse(String originStr) {
        if(originStr == null || originStr.length() <= 1)
            return originStr;
        return reverse(originStr.substring(1)) + originStr.charAt(0);
    }

40、怎样将GB2312编码的字符串转换为ISO-8859-1编码的字符串？
答：代码如下所示：

String s1 = "你好";
String s2 = new String(s1.getBytes("GB2312"), "ISO-8859-1");


43、比较一下Java和JavaSciprt。
答：JavaScript 与Java是两个公司开发的不同的两个产品。Java 是原Sun Microsystems公司推出的面向对象的程序设计语言，特别适合于互联网应用程序开发；而JavaScript是Netscape公司的产品，为了扩展Netscape浏览器的功能而开发的一种可以嵌入Web页面中运行的基于对象和事件驱动的解释性语言。JavaScript的前身是LiveScript；而Java的前身是Oak语言。
下面对两种语言间的异同作如下比较：
- 基于对象和面向对象：Java是一种真正的面向对象的语言，即使是开发简单的程序，必须设计对象；JavaScript是种脚本语言，它可以用来制作与网络无关的，与用户交互作用的复杂软件。它是一种基于对象（Object-Based）和事件驱动（Event-Driven）的编程语言，因而它本身提供了非常丰富的内部对象供设计人员使用。
- 解释和编译：Java的源代码在执行之前，必须经过编译。JavaScript是一种解释性编程语言，其源代码不需经过编译，由浏览器解释执行。（目前的浏览器几乎都使用了JIT（即时编译）技术来提升JavaScript的运行效率）
- 强类型变量和类型弱变量：Java采用强类型变量检查，即所有变量在编译之前必须作声明；JavaScript中变量是弱类型的，甚至在使用变量前可以不作声明，JavaScript的解释器在运行时检查推断其数据类型。
- 代码格式不一样。

    补充：上面列出的四点是网上流传的所谓的标准答案。其实Java和JavaScript最重要的区别是一个是静态语言，一个是动态语言。
    目前的编程语言的发展趋势是函数式语言和动态语言。在Java中类（class）是一等公民，而JavaScript中函数（function）是一等公民，因此JavaScript支持函数式编程，可以使用Lambda函数和闭包（closure），当然Java 8也开始支持函数式编程，提供了对Lambda表达式以及函数式接口的支持。对于这类问题，在面试的时候最好还是用自己的语言回答会更加靠谱，不要背网上所谓的标准答案。

44、什么时候用断言（assert）？
答：断言在软件开发中是一种常用的调试方式，很多开发语言中都支持这种机制。一般来说，断言用于保证程序最基本、关键的正确性。断言检查通常在开发和测试时开启。
为了保证程序的执行效率，在软件发布后断言检查通常是关闭的。断言是一个包含布尔表达式的语句，在执行这个语句时假定该表达式为true；如果表达式的值为false，
那么系统会报告一个AssertionError。断言的使用如下面的代码所示：

assert(a > 0); // throws an AssertionError if a <= 0

断言可以有两种形式：
assert Expression1;
assert Expression1 : Expression2 ;
Expression1 应该总是产生一个布尔值。
Expression2 可以是得出一个值的任意表达式；这个值用于生成显示更多调试信息的字符串消息。

要在运行时启用断言，可以在启动JVM时使用-enableassertions或者-ea标记。要在运行时选择禁用断言，可以在启动JVM时使用-da或者-disableassertions标记。要在系统类中启用或禁用断言，可使用-esa或-dsa标记。还可以在包的基础上启用或者禁用断言。

    注意：断言不应该以任何方式改变程序的状态。简单的说，如果希望在不满足某些条件时阻止代码的执行，就可以考虑用断言来阻止它。


73、XML文档定义有几种形式？它们之间有何本质区别？解析XML文档有哪几种方式？
答：XML文档定义分为DTD和Schema两种形式，二者都是对XML语法的约束，其本质区别在于Schema本身也是一个XML文件，可以被XML解析器解析，而且可以为XML承载的数据定义类型，约束能力较之DTD更强大。对XML的解析主要有DOM（文档对象模型，Document Object Model）、SAX（Simple API for XML）和StAX（Java 6中引入的新的解析XML的方式，Streaming API for XML），其中DOM处理大型文件时其性能下降的非常厉害，这个问题是由DOM树结构占用的内存较多造成的，而且DOM解析方式必须在解析文件之前把整个文档装入内存，适合对XML的随机访问（典型的用空间换取时间的策略）；SAX是事件驱动型的XML解析方式，它顺序读取XML文件，不需要一次全部装载整个文件。当遇到像文件开头，文档结束，或者标签开头与标签结束时，它会触发一个事件，用户通过事件回调代码来处理XML文件，适合对XML的顺序访问；顾名思义，StAX把重点放在流上，实际上StAX与其他解析方式的本质区别就在于应用程序能够把XML作为一个事件流来处理。将XML作为一组事件来处理的想法并不新颖（SAX就是这样做的），但不同之处在于StAX允许应用程序代码把这些事件逐个拉出来，而不用提供在解析器方便时从解析器中接收事件的处理程序。

74、你在项目中哪些地方用到了XML？
答：XML的主要作用有两个方面：数据交换和信息配置。在做数据交换时，XML将数据用标签组装成起来，然后压缩打包加密后通过网络传送给接收者，接收解密与解压缩后再从XML文件中还原相关信息进行处理，XML曾经是异构系统间交换数据的事实标准，但此项功能几乎已经被JSON（JavaScript Object Notation）取而代之。当然，目前很多软件仍然使用XML来存储配置信息，我们在很多项目中通常也会将作为配置信息的硬代码写在XML文件中，Java的很多框架也是这么做的，而且这些框架都选择了dom4j作为处理XML的工具，因为Sun公司的官方API实在不怎么好用。

    补充：现在有很多时髦的软件（如Sublime）已经开始将配置文件书写成JSON格式，我们已经强烈的感受到XML的另一项功能也将逐渐被业界抛弃。


83、简述正则表达式及其用途。
答：在编写处理字符串的程序时，经常会有查找符合某些复杂规则的字符串的需要。正则表达式就是用于描述这些规则的工具。换句话说，正则表达式就是记录文本规则的代码。

    说明：计算机诞生初期处理的信息几乎都是数值，但是时过境迁，今天我们使用计算机处理的信息更多的时候不是数值而是字符串，正则表达式就是在进行字符串匹配和处理的时候最为强大的工具，绝大多数语言都提供了对正则表达式的支持。

84、Java中是如何支持正则表达式操作的？
答：Java中的String类提供了支持正则表达式操作的方法，包括：matches()、replaceAll()、replaceFirst()、split()。此外，Java中可以用Pattern类表示正则表达式对象，它提供了丰富的API进行各种正则表达式操作，请参考下面面试题的代码。

    面试题： - 如果要从字符串中截取第一个英文左括号之前的字符串，例如：北京市(朝阳区)(西城区)(海淀区)，截取结果为：北京市，那么正则表达式怎么写？

import java.util.regex.Matcher;import java.util.regex.Pattern;
class RegExpTest {
    public static void main(String[] args) {
        String str = "北京市(朝阳区)(西城区)(海淀区)";
        Pattern p = Pattern.compile(".*?(?=\\()");
        Matcher m = p.matcher(str);
        if(m.find()) {
            System.out.println(m.group());
        }
    }
}


    14

        说明：上面的正则表达式中使用了懒惰匹配和前瞻，如果不清楚这些内容，推荐读一下网上很有名的《正则表达式30分钟入门教程》。

    85、获得一个类的类对象有哪些方式？
    答：
    - 方法1：类型.class，例如：String.class
    - 方法2：对象.getClass()，例如："hello".getClass()
    - 方法3：Class.forName()，例如：Class.forName("java.lang.String")

    86、如何通过反射创建对象？
    答：
    - 方法1：通过类对象调用newInstance()方法，例如：String.class.newInstance()
    - 方法2：通过类对象的getConstructor()或getDeclaredConstructor()方法获得构造器（Constructor）对象并调用其newInstance()方法创建对象，例如：String.class.getConstructor(String.class).newInstance("Hello");

    87、如何通过反射获取和设置对象私有字段的值？
    答：可以通过类对象的getDeclaredField()方法字段（Field）对象，然后再通过字段对象的setAccessible(true)将其设置为可以访问，接下来就可以通过get/set方法来获取/设置字段的值了。下面的代码实现了一个反射的工具类，其中的两个静态方法分别用于获取和设置私有字段的值，字段可以是基本类型也可以是对象类型且支持多级对象操作，例如ReflectionUtil.get(dog, "owner.car.engine.id");可以获得dog对象的主人的汽车的引擎的ID号。

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Modifier;
import java.util.ArrayList;
import java.util.List;
/** * 反射工具类 * @author 骆昊 * */
public class ReflectionUtil {
    private ReflectionUtil() {
        throw new AssertionError();
    }
    /**     * 通过反射取对象指定字段(属性)的值     * @param target 目标对象     * @param fieldName 字段的名字     * @throws 如果取不到对象指定字段的值则抛出异常     * @return 字段的值     */
    public static Object getValue(Object target, String fieldName) {
        Class<?> clazz = target.getClass();
        String[] fs = fieldName.split("\\.");
        try {
            for(int i = 0; i < fs.length - 1; i++) {
                Field f = clazz.getDeclaredField(fs[i]);
                f.setAccessible(true);
                target = f.get(target);
                clazz = target.getClass();
            }
            Field f = clazz.getDeclaredField(fs[fs.length - 1]);
            f.setAccessible(true);
            return f.get(target);
        }
        catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    /**     * 通过反射给对象的指定字段赋值     * @param target 目标对象     * @param fieldName 字段的名称     * @param value 值     */
    public static void setValue(Object target, String fieldName, Object value) {
        Class<?> clazz = target.getClass();
        String[] fs = fieldName.split("\\.");
        try {
            for(int i = 0; i < fs.length - 1; i++) {
                Field f = clazz.getDeclaredField(fs[i]);
                f.setAccessible(true);
                Object val = f.get(target);
                if(val == null) {
                    Constructor<?> c = f.getType().getDeclaredConstructor();
                    c.setAccessible(true);
                    val = c.newInstance();
                    f.set(target, val);
                }
                target = val;
                clazz = target.getClass();
            }
            Field f = clazz.getDeclaredField(fs[fs.length - 1]);
            f.setAccessible(true);
            f.set(target, value);
        }
        catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}


88、如何通过反射调用对象的方法？
答：请看下面的代码：

import java.lang.reflect.Method;
class MethodInvokeTest {
    public static void main(String[] args) throws Exception {
        String str = "hello";
        Method m = str.getClass().getMethod("toUpperCase");
        System.out.println(m.invoke(str));  // HELLO
    }
}
    