#
Chapter VIII: Concurrency

##1. Processes and Threads

In concurrent programming, there are two basic units of execution: processes and threads. Most implementations of the Java virtual machine run as a single process.

* **Processes**

A process has a self-contained execution environment. A process generally has a complete, private set of basic run-time resources; in particular, each process has its own memory space. To facilitate communication between processes, most operating systems support Inter Process Communication (IPC) resources, such as pipes and sockets. IPC is used not just for communication between processes on the same system, but processes on different systems.

* **Threads**

Threads exist within a process — every process has at least one. Threads share the process's resources, including memory and open files. This makes for efficient, but potentially problematic, communication.

Every application has at least one thread — or several, if you count "system" threads that do things like memory management and signal handling. But from the application programmer's point of view, you start with just one thread, called the main thread. This thread has the ability to create additional threads.


##2. Implement Multithread
* **extends Thread **

```
1. Thread() // create a new thread object
2. Thread(Runnable target) // create a new thread object based on Runnable
3. Thread(Runnable t, String name) // assign a name for thread
4. Thread(String name) // create a new thread object with an assgned name
```

* **implement Runnable**

```
1. Need to @Overrid run()
2. It is good for shared resources scenario.
```

**Example: **
```
// Extends Thread
public class MyThread extends Thread {
@Override
public void run() {
for (int i = 0; i < 20; i++) {
System.out.println("MyThread is running");
}
}
}
```
```
// Implement Runnable
public class MyRunnable implements Runnable {
@Override
public void run() {
for(int i = 0; i < 20; i++) {
System.out.println("MyRunnable is running");
}
}
}
```
```
// Test two cases
public class A_StartingThreads {
public static void main(String[] args) {
MyThread myThread = new MyThread();
myThread.run();
MyRunnable myRunnable = new MyRunnable();
myRunnable.run();

/*
* Common pitfall: Calling run() instead of start()
* */
Thread thread = new Thread(new MyRunnable());
thread.start();
for (int i = 0; i < 10; i++) {
new Thread("" + i){
public void run() {
System.out.println("Thread: " + getName() + " is running" );
}
}.start();
}
}
}
```
Summary:
```
Note: Use start() to invoke Thread, not run() method.
* Runnable way is preferable.
1 When having the Runnable's executed by a thread pool it is easy to queue up the Runnable instances until a thread from the pool is idle.
2 Sometimes you may have to implement Runnable as well as subclass Thread. For instance, if creating a subclass of Thread that can execute more than one Runnable.
```


##3. Thread Life Cycle
![](thread.png)
**Five States: **
* NEW
* Runnable
* Running (yield)
* Blocking (wait / sleep / suspend)
* Dead


**sleep()** method:
```
Thread.sleep(1000) // sleep 1 second

// interrupt() can wake up the sleep thread
```


**join()** method:
```
@Override
public void run() {
// some job
Thread_out.join(); // current thread will be blocked until this Thread_out completes its job
}
```

**isAlive()**: it is used to check if current thread is runnable or running.


##3. Thread Priority
* Java uses [1 - 10] to setup priority. 10 has the highest priority and 0 has the lowest priority.

* 3 constant thread priority
* MAX_PRIORITY: 10
* MIN_PRIORITY: 0
* NORM_PRIORITY: 5

* Thread.setPriority() / Thread.getPriority()

Note: JVM cannot guarantee that the higher priority thread will run before the lower ones.

##4. Thread Synchronization
* **Thread Interference** describes how errors are introduced when multiple threads access shared data.
* **Memory Consistency Errors** describes errors that result from inconsistent views of shared memory.
* **Synchronized Methods** describes a simple idiom that can effectively prevent thread interference and memory consistency errors.
* **Implicit Locks and Synchronization** describes a more general synchronization idiom, and describes how synchronization is based on implicit locks.
* **Atomic Access** talks about the general idea of operations that can't be interfered with by other threads.

###4.1 Thread Interference
Consider a simple class called Counter
```java
class Counter {
private int c = 0;
public void increment() {
c++;
}
public void decrement() {
c--;
}
public int value() {
return c;
}
}
```
Interference happens when two operations, running in different threads, but acting on the same data, interleave. This means that the two operations consist of multiple steps, and the sequences of steps overlap.

Note the single expression c++ can be decomposed into three steps:
```
Retrieve the current value of c.
Increment the retrieved value by 1.
Store the incremented value back in c.
```

Suppose Thread A invokes increment at about the same time Thread B invokes decrement. If the initial value of c is 0, their interleaved actions might follow this sequence:
```
1. Thread A: Retrieve c.
2. Thread B: Retrieve c.
3. Thread A: Increment retrieved value; result is 1.
4. Thread B: Decrement retrieved value; result is -1.
5. Thread A: Store result in c; c is now 1.
6. Thread B: Store result in c; c is now -1.
```
Thread A's result is lost, overwritten by Thread B. This particular interleaving is only one possibility. Under different circumstances it might be Thread B's result that gets lost, or there could be no error at all. Because they are unpredictable, thread interference bugs can be difficult to detect and fix.


###4.2 Memory Consistency Errors

Memory consistency errors occur when different threads have inconsistent views of what should be the same data. The programmer does not need a detailed understanding of these causes. All that is needed is a strategy for avoiding them.

The key to avoiding memory consistency errors is understanding the happens-before relationship. This relationship is simply a guarantee that memory writes by one specific statement are visible to another specific statement. To see this, consider the following example. Suppose a simple int field is defined and initialized:
```java
int counter = 0;
```
The counter field is shared between two threads, A and B. Suppose thread A increments counter:
```java
counter++;
```
Then, shortly afterwards, thread B prints out counter:
```java
System.out.println(counter);
```
If the two statements had been executed in the same thread, it would be safe to assume that the value printed out would be **1**. But if the two statements are executed in separate threads, the value printed out might well be **0**, because there's no guarantee that thread A's change to counter will be visible to thread B — unless the programmer has established a happens-before relationship between these two statements.

There are several actions that create happens-before relationships. One of them is **synchronization**, as we will see in the following sections.

We've already seen two actions that create happens-before relationships.

* When a statement invokes Thread.start, every statement that has a happens-before relationship with that statement also has a happens-before relationship with every statement executed by the new thread. The effects of the code that led up to the creation of the new thread are visible to the new thread.
* When a thread terminates and causes a Thread.join in another thread to return, then all the statements executed by the terminated thread have a happens-before relationship with all the statements following the successful join. The effects of the code in the thread are now visible to the thread that performed the join.

For a list of actions that create happens-before relationships, refer to the Summary page of the java.util.concurrent package..


###4.3 Synchronized Methods
The Java programming language provides two basic synchronization idioms:
* synchronized methods
* synchronized statements

This section is about synchronized methods.

To make a method synchronized, simply add the synchronized keyword to its declaration:
```java
public class SynchronizedCounter {
private int c = 0;

public synchronized void increment() {
c++;
}

public synchronized void decrement() {
c--;
}

public synchronized int value() {
return c;
}
}
```
If count is an instance of SynchronizedCounter, then making these methods synchronized has two effects:
* First, it is not possible for two invocations of synchronized methods on the same object to interleave. When one thread is executing a synchronized method for an object, all other threads that invoke synchronized methods for the same object block (suspend execution) until the first thread is done with the object.
* Second, when a synchronized method exits, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object. This guarantees that changes to the state of the object are visible to all threads.

Note that constructors cannot be synchronized — using the synchronized keyword with a constructor is a syntax error. Synchronizing constructors doesn't make sense, because only the thread that creates an object should have access to it while it is being constructed.

**Warning**: When constructing an object that will be shared between threads, be very careful that a reference to the object does not "leak" prematurely. For example, suppose you want to maintain a ``List`` called instances containing every instance of class. You might be tempted to add the following line to your constructor:
```java
instances.add(this);
```
But then other threads can use instances to access the object before construction of the object is complete.

Synchronized methods enable a simple strategy for preventing thread interference and memory consistency errors: if an object is visible to more than one thread, all reads or writes to that object's variables are done through synchronized methods. (An important exception: ``final`` fields, which cannot be modified after the object is constructed, can be safely read through non-synchronized methods, once the object is constructed) This strategy is effective, but can present problems with liveness, as we'll see later in this lesson.


###4.4 Intrinsic Locks and Synchronization

Synchronization is built around an internal entity known as the **intrinsic lock** or **monitor lock**. (The API specification often refers to this entity simply as a "monitor.") Intrinsic locks play a role in both aspects of synchronization: enforcing exclusive access to an object's state and establishing happens-before relationships that are essential to visibility.

Every object has an intrinsic lock associated with it. By convention, a thread that needs exclusive and consistent access to an object's fields has to acquire the object's intrinsic lock before accessing them, and then release the intrinsic lock when it's done with them. A thread is said to own the intrinsic lock between the time it has acquired the lock and released the lock. As long as a thread owns an intrinsic lock, no other thread can acquire the same lock. The other thread will block when it attempts to acquire the lock.

When a thread releases an intrinsic lock, a happens-before relationship is established between that action and any subsequent acquisition of the same lock.

**Locks In Synchronized Methods**

When a thread invokes a synchronized method, it automatically acquires the intrinsic lock for that method's object and releases it when the method returns. The lock release occurs even if the return was caused by an uncaught exception.

You might wonder what happens when a static synchronized method is invoked, since a static method is associated with a class, not an object. In this case, the thread acquires the intrinsic lock for the Class object associated with the class. Thus access to class's static fields is controlled by a lock that's distinct from the lock for any instance of the class.

**Synchronized Statements**

Another way to create synchronized code is with synchronized statements. Unlike synchronized methods, synchronized statements must specify the object that provides the intrinsic lock:
```java
public void addName(String name) {
synchronized(this) {
lastName = name;
nameCount++;
}
nameList.add(name);
}
```
In this example, the ``addName`` method needs to synchronize changes to ``lastName`` and ``nameCount``, but also needs to avoid synchronizing invocations of other objects' methods. (Invoking other objects' methods from synchronized code can create problems that are described in the section on Liveness.) Without synchronized statements, there would have to be a separate, unsynchronized method for the sole purpose of invoking ``nameList.add``.

Synchronized statements are also useful for improving concurrency with fine-grained synchronization. Suppose, for example, class MsLunch has two instance fields, c1 and c2, that are never used together. All updates of these fields must be synchronized, but there's no reason to prevent an update of c1 from being interleaved with an update of c2 — and doing so reduces concurrency by creating unnecessary blocking. Instead of using synchronized methods or otherwise using the lock associated with this, we create two objects solely to provide locks.
```java
public class MsLunch {
private long c1 = 0;
private long c2 = 0;
private Object lock1 = new Object();
private Object lock2 = new Object();

public void inc1() {
synchronized(lock1) {
c1++;
}
}

public void inc2() {
synchronized(lock2) {
c2++;
}
}
}
```
Use this idiom with extreme care. You must be absolutely sure that it really is safe to interleave access of the affected fields.

**Reentrant Synchronization**

Recall that a thread cannot acquire a lock owned by another thread. But a thread can acquire a lock that it already owns. Allowing a thread to acquire the same lock more than once enables reentrant synchronization. This describes a situation where synchronized code, directly or indirectly, invokes a method that also contains synchronized code, and both sets of code use the same lock. Without reentrant synchronization, synchronized code would have to take many additional precautions to avoid having a thread cause itself to block.

###4.5 Atomic Access

In programming, an atomic action is one that effectively happens all at once. An atomic action cannot stop in the middle: it either happens completely, or it doesn't happen at all. No side effects of an atomic action are visible until the action is complete.

We have already seen that an increment expression, such as c++, does not describe an atomic action. Even very simple expressions can define complex actions that can decompose into other actions. However, there are actions you can specify that are atomic:
* Reads and writes are atomic for **reference variables** and for **most primitive variables** (all types except long and double).
* Reads and writes are atomic for **all variables declared volatile** (including long and double variables).
Atomic actions cannot be interleaved, so they can be used without fear of thread interference. However, this does not eliminate all need to synchronize atomic actions, because memory consistency errors are still possible. Using ``volatile`` variables reduces the risk of memory consistency errors, because any write to a volatile variable establishes a happens-before relationship with subsequent reads of that same variable. This means that changes to a volatile variable are always visible to other threads. What's more, it also means that when a thread reads a volatile variable, it sees not just the latest change to the volatile, but also the side effects of the code that led up the change.

Using simple atomic variable access is more efficient than accessing these variables through synchronized code, but requires more care by the programmer to avoid memory consistency errors. Whether the extra effort is worthwhile depends on the size and complexity of the application.

Some of the classes in the java.util.concurrent package provide atomic methods that do not rely on synchronization. We'll discuss them in the section on High Level Concurrency Objects.

##5. Liveness
* Deadlock
* Starvation and Livelock

###5.1 Deadlock
When Deadlock runs, it's extremely likely that both threads will block when they attempt to invoke bowBack. Neither block will ever end, because each thread is waiting for the other to exit bow.

```java
public class Deadlock {
static class Friend {
private final String name;
public Friend(String name) {
this.name = name;
}
public String getName() {
return this.name;
}
public synchronized void bow(Friend bower) {
System.out.format("%s: %s"
+ " has bowed to me!%n",
this.name, bower.getName());
bower.bowBack(this);
}
public synchronized void bowBack(Friend bower) {
System.out.format("%s: %s"
+ " has bowed back to me!%n",
this.name, bower.getName());
}
}

public static void main(String[] args) {
final Friend alphonse = new Friend("Alphonse");
final Friend gaston = new Friend("Gaston");
new Thread(new Runnable() {
public void run() { alphonse.bow(gaston); }
}).start();
new Thread(new Runnable() {
public void run() { gaston.bow(alphonse); }
}).start();
}
}
```

###5.2 Starvation and Livelock
* **Starvation** describes a situation where a thread is unable to gain regular access to shared resources and is unable to make progress. This happens when shared resources are made unavailable for long periods by "greedy" threads. For example, suppose an object provides a synchronized method that often takes a long time to return. If one thread invokes this method frequently, other threads that also need frequent synchronized access to the same object will often be blocked.

* **Livelock**: A thread often acts in response to the action of another thread. If the other thread's action is also a response to the action of another thread, then livelock may result. As with deadlock, livelocked threads are unable to make further progress. However, the threads are not blocked — they are simply too busy responding to each other to resume work. This is comparable to two people attempting to pass each other in a corridor: Alphonse moves to his left to let Gaston pass, while Gaston moves to his right to let Alphonse pass. Seeing that they are still blocking each other, Alphone moves to his right, while Gaston moves to his left. They're still blocking each other, so...

##6. Guarded Blocks

Threads often have to coordinate their actions. The most common coordination idiom is the guarded block. Such a block begins by polling a condition that must be true before the block can proceed. There are a number of steps to follow in order to do this correctly.

Suppose, for example guardedJoy is a method that must not proceed until a shared variable joy has been set by another thread. Such a method could, in theory, simply loop until the condition is satisfied, but that loop is wasteful, since it executes continuously while waiting.
```java
public void guardedJoy() {
// Simple loop guard. Wastes
// processor time. Don't do this!
while(!joy) {}
System.out.println("Joy has been achieved!");
}
```
A more efficient guard invokes Object.wait to suspend the current thread. The invocation of wait does not return until another thread has issued a notification that some special event may have occurred — though not necessarily the event this thread is waiting for:
```java
public synchronized void guardedJoy() {
// This guard only loops once for each special event, which may not
// be the event we're waiting for.
while(!joy) {
try {
wait();
} catch (InterruptedException e) {}
}
System.out.println("Joy and efficiency have been achieved!");
}
```
**Note**: Always invoke wait inside a loop that tests for the condition being waited for. Don't assume that the interrupt was for the particular condition you were waiting for, or that the condition is still true.

Like many methods that suspend execution, wait can throw InterruptedException. In this example, we can just ignore that exception — we only care about the value of joy.

Why is this version of guardedJoy synchronized? Suppose d is the object we're using to invoke wait. When a thread invokes d.wait, it must own the intrinsic lock for d — otherwise an error is thrown. Invoking wait inside a synchronized method is a simple way to acquire the intrinsic lock.

When wait is invoked, the thread releases the lock and suspends execution. At some future time, another thread will acquire the same lock and invoke Object.notifyAll, informing all threads waiting on that lock that something important has happened:
```java
public synchronized notifyJoy() {
joy = true;
notifyAll();
}
```
Some time after the second thread has released the lock, the first thread reacquires the lock and resumes by returning from the invocation of wait.

**Note**: There is a second notification method, notify, which wakes up a single thread. Because notify doesn't allow you to specify the thread that is woken up, it is useful only in massively parallel applications — that is, programs with a large number of threads, all doing similar chores. In such an application, you don't care which thread gets woken up.

Let's use guarded blocks to create a Producer-Consumer application. This kind of application shares data between two threads: the producer, that creates the data, and the consumer, that does something with it. The two threads communicate using a shared object. Coordination is essential: the consumer thread must not attempt to retrieve the data before the producer thread has delivered it, and the producer thread must not attempt to deliver new data if the consumer hasn't retrieved the old data.

In this example, the data is a series of text messages, which are shared through an object of type Drop:

```java
public class Drop {
// Message sent from producer
// to consumer.
private String message;
// True if consumer should wait
// for producer to send message,
// false if producer should wait for
// consumer to retrieve message.
private boolean empty = true;

public synchronized String take() {
// Wait until message is
// available.
while (empty) {
try {
wait();
} catch (InterruptedException e) {}
}
// Toggle status.
empty = true;
// Notify producer that
// status has changed.
notifyAll();
return message;
}

public synchronized void put(String message) {
// Wait until message has
// been retrieved.
while (!empty) {
try {
wait();
} catch (InterruptedException e) {}
}
// Toggle status.
empty = false;
// Store message.
this.message = message;
// Notify consumer that status
// has changed.
notifyAll();
}
}
```
The producer thread, defined in Producer, sends a series of familiar messages. The string "DONE" indicates that all messages have been sent. To simulate the unpredictable nature of real-world applications, the producer thread pauses for random intervals between messages.

```java
import java.util.Random;

public class Producer implements Runnable {
private Drop drop;

public Producer(Drop drop) {
this.drop = drop;
}

public void run() {
String importantInfo[] = {
"Mares eat oats",
"Does eat oats",
"Little lambs eat ivy",
"A kid will eat ivy too"
};
Random random = new Random();

for (int i = 0;
i < importantInfo.length;
i++) {
drop.put(importantInfo[i]);
try {
Thread.sleep(random.nextInt(5000));
} catch (InterruptedException e) {}
}
drop.put("DONE");
}
}
```
The consumer thread, defined in Consumer, simply retrieves the messages and prints them out, until it retrieves the "DONE" string. This thread also pauses for random intervals.

```java
import java.util.Random;

public class Consumer implements Runnable {
private Drop drop;

public Consumer(Drop drop) {
this.drop = drop;
}

public void run() {
Random random = new Random();
for (String message = drop.take();
! message.equals("DONE");
message = drop.take()) {
System.out.format("MESSAGE RECEIVED: %s%n", message);
try {
Thread.sleep(random.nextInt(5000));
} catch (InterruptedException e) {}
}
}
}
```
Finally, here is the main thread, defined in ProducerConsumerExample, that launches the producer and consumer threads.

```java
public class ProducerConsumerExample {
public static void main(String[] args) {
Drop drop = new Drop();
(new Thread(new Producer(drop))).start();
(new Thread(new Consumer(drop))).start();
}
}
```
Note: The Drop class was written in order to demonstrate guarded blocks. To avoid re-inventing the wheel, examine the existing data structures in the Java Collections Framework before trying to code your own data-sharing objects. For more information, refer to the Questions and Exercises section.


##7. Immutable Objects

An object is considered immutable if its state cannot change after it is constructed. Maximum reliance on immutable objects is widely accepted as a sound strategy for creating simple, reliable code.

Immutable objects are particularly useful in concurrent applications. Since they cannot change state, they cannot be corrupted by thread interference or observed in an inconsistent state.

Programmers are often reluctant to employ immutable objects, because they worry about the cost of creating a new object as opposed to updating an object in place. The impact of object creation is often overestimated, and can be offset by some of the efficiencies associated with immutable objects. These include decreased overhead due to garbage collection, and the elimination of code needed to protect mutable objects from corruption.

The following subsections take a class whose instances are mutable and derives a class with immutable instances from it. In so doing, they give general rules for this kind of conversion and demonstrate some of the advantages of immutable objects.

###7.1 A Synchronized Class Example

```java
public class SynchronizedRGB {

// Values must be between 0 and 255.
private int red;
private int green;
private int blue;
private String name;

private void check(int red,int green,int blue) {
if (red < 0 || red > 255
|| green < 0 || green > 255
|| blue < 0 || blue > 255) {
throw new IllegalArgumentException();
}
}

public SynchronizedRGB(int red,int green,int blue,String name) {
check(red, green, blue);
this.red = red;
this.green = green;
this.blue = blue;
this.name = name;
}

public void set(int red,int green,int blue, String name) {
check(red, green, blue);
synchronized (this) {
this.red = red;
this.green = green;
this.blue = blue;
this.name = name;
}
}

public synchronized int getRGB() {
return ((red << 16) | (green << 8) | blue);
}

public synchronized String getName() {
return name;
}

public synchronized void invert() {
red = 255 - red;
green = 255 - green;
blue = 255 - blue;
name = "Inverse of " + name;
}
}
```

SynchronizedRGB must be used carefully to avoid being seen in an inconsistent state. Suppose, for example, a thread executes the following code:
```java
SynchronizedRGB color = new SynchronizedRGB(0, 0, 0, "Pitch Black");
...
int myColorInt = color.getRGB(); //Statement 1
String myColorName = color.getName(); //Statement 2
```
If another thread invokes color.set after Statement 1 but before Statement 2, the value of myColorInt won't match the value of myColorName. To avoid this outcome, the two statements must be bound together:
```java
synchronized (color) {
int myColorInt = color.getRGB();
String myColorName = color.getName();
}
```
This kind of inconsistency is only possible for mutable objects — it will not be an issue for the immutable version of SynchronizedRGB.

###7.2 A Strategy for Defining Immutable Objects

The following rules define a simple strategy for creating immutable objects. Not all classes documented as "immutable" follow these rules. This does not necessarily mean the creators of these classes were sloppy — they may have good reason for believing that instances of their classes never change after construction. However, such strategies require sophisticated analysis and are not for beginners.

* Don't provide "setter" methods — methods that modify fields or objects referred to by fields.
* Make all fields ``final`` and ``private``.
* Don't allow subclasses to override methods. The simplest way to do this is to declare the class as ``final``. A more sophisticated approach is to make the constructor ``private`` and construct instances in factory methods.
* If the instance fields include references to mutable objects, don't allow those objects to be changed:
* Don't provide methods that modify the mutable objects.
* Don't share references to the mutable objects. Never store references to external, mutable objects passed to the constructor; if necessary, create copies, and store references to the copies. Similarly, create copies of your internal mutable objects when necessary to avoid returning the originals in your methods.


Applying this strategy to SynchronizedRGB results in the following steps:

* There are two setter methods in this class. The first one, set, arbitrarily transforms the object, and has no place in an immutable version of the class. The second one, invert, can be adapted by having it create a new object instead of modifying the existing one.
* All fields are already private; they are further qualified as final.
* The class itself is declared final.
* Only one field refers to an object, and that object is itself immutable. Therefore, no safeguards against changing the state of "contained" mutable objects are necessary.

```java
final public class ImmutableRGB {

// Values must be between 0 and 255.
final private int red;
final private int green;
final private int blue;
final private String name;

private void check(int red,int green,int blue) {
if (red < 0 || red > 255
|| green < 0 || green > 255
|| blue < 0 || blue > 255) {
throw new IllegalArgumentException();
}
}

public ImmutableRGB(int red,int green,int blue,String name) {
check(red, green, blue);
this.red = red;
this.green = green;
this.blue = blue;
this.name = name;
}

public int getRGB() {
return ((red << 16) | (green << 8) | blue);
}

public String getName() {
return name;
}

public ImmutableRGB invert() {
return new ImmutableRGB(255 - red,
255 - green,
255 - blue,
"Inverse of " + name);
}
}
```


##8. High Level Concurrency Objects
Most of these features are implemented in the new ``java.util.concurrent`` packages. There are also new concurrent data structures in the Java Collections Framework.

* **Lock objects:** support locking idioms that simplify many concurrent applications.
* **Executors: ** define a high-level API for launching and managing threads.
* **Concurrent collections: ** make it easier to manage large collections of data, and can greatly reduce the need for synchronization.
* **Atomic variables: ** have features that minimize synchronization and help avoid memory consistency errors.
* **ThreadLocalRandom** (in JDK 7) provides efficient generation of pseudorandom numbers from multiple threads.

###8.1 Lock Objects
Synchronized code relies on a simple kind of reentrant lock. This kind of lock is easy to use, but has many limitations. More sophisticated locking idioms are supported by the java.util.concurrent.locks package.

``Lock`` objects work very much like the implicit locks used by synchronized code. As with implicit locks, only one thread can own a Lock object at a time. Lock objects also support a wait/notify mechanism, through their associated Condition objects.

**The biggest advantage** of Lock objects over implicit locks is their ability to back out of an attempt to acquire a lock. The ``tryLock`` method backs out if the lock is not available immediately or before a timeout expires (if specified). The ``lockInterruptibly`` method backs out if another thread sends an interrupt before the lock is acquired.

Let's use Lock objects to solve the deadlock problem we saw in Liveness. Alphonse and Gaston have trained themselves to notice when a friend is about to bow. We model this improvement by requiring that our Friend objects must acquire locks for both participants before proceeding with the bow. Here is the source code for the improved model, Safelock. To demonstrate the versatility of this idiom, we assume that Alphonse and Gaston are so infatuated with their newfound ability to bow safely that they can't stop bowing to each other:

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.Random;

public class Safelock {
static class Friend {
private final String name;
private final Lock lock = new ReentrantLock();

public Friend(String name) {
this.name = name;
}

public String getName() {
return this.name;
}

public boolean impendingBow(Friend bower) {
Boolean myLock = false;
Boolean yourLock = false;
try {
myLock = lock.tryLock();
yourLock = bower.lock.tryLock();
} finally {
if (! (myLock && yourLock)) {
if (myLock) {
lock.unlock();
}
if (yourLock) {
bower.lock.unlock();
}
}
}
return myLock && yourLock;
}
public void bow(Friend bower) {
if (impendingBow(bower)) {
try {
System.out.format("%s: %s has"
+ " bowed to me!%n",
this.name, bower.getName());
bower.bowBack(this);
} finally {
lock.unlock();
bower.lock.unlock();
}
} else {
System.out.format("%s: %s started"
+ " to bow to me, but saw that"
+ " I was already bowing to"
+ " him.%n",
this.name, bower.getName());
}
}

public void bowBack(Friend bower) {
System.out.format("%s: %s has" +
" bowed back to me!%n",
this.name, bower.getName());
}
}

static class BowLoop implements Runnable {
private Friend bower;
private Friend bowee;

public BowLoop(Friend bower, Friend bowee) {
this.bower = bower;
this.bowee = bowee;
}
public void run() {
Random random = new Random();
for (;;) {
try {
Thread.sleep(random.nextInt(10));
} catch (InterruptedException e) {}
bowee.bow(bower);
}
}
}
public static void main(String[] args) {
final Friend alphonse = new Friend("Alphonse");
final Friend gaston = new Friend("Gaston");
new Thread(new BowLoop(alphonse, gaston)).start();
new Thread(new BowLoop(gaston, alphonse)).start();
}
}
```


###8.2 Executors

In all of the previous examples, there's a close connection between the task being done by a new thread, as defined by its Runnable object, and the thread itself, as defined by a Thread object. This works well for small applications, but in large-scale applications, it makes sense to separate thread management and creation from the rest of the application. Objects that encapsulate these functions are known as executors. The following subsections describe executors in detail.

* **Executor Interfaces** define the three executor object types.
* **Thread Pools** are the most common kind of executor implementation.
* **Fork/Join** is a framework (new in JDK 7) for taking advantage of multiple processors.


####8.2.1 Executor Interfaces

The java.util.concurrent package defines three executor interfaces:

Executor, a simple interface that supports launching new tasks.
ExecutorService, a subinterface of Executor, which adds features that help manage the lifecycle, both of the individual tasks and of the executor itself.
ScheduledExecutorService, a subinterface of ExecutorService, supports future and/or periodic execution of tasks.
Typically, variables that refer to executor objects are declared as one of these three interface types, not with an executor class type.

The Executor Interface

The Executor interface provides a single method, execute, designed to be a drop-in replacement for a common thread-creation idiom. If r is a Runnable object, and e is an Executor object you can replace

(new Thread(r)).start();
with

e.execute(r);
However, the definition of execute is less specific. The low-level idiom creates a new thread and launches it immediately. Depending on the Executor implementation, execute may do the same thing, but is more likely to use an existing worker thread to run r, or to place r in a queue to wait for a worker thread to become available. (We'll describe worker threads in the section on Thread Pools.)

The executor implementations in java.util.concurrent are designed to make full use of the more advanced ExecutorService and ScheduledExecutorService interfaces, although they also work with the base Executor interface.

The ExecutorService Interface

The ExecutorService interface supplements execute with a similar, but more versatile submit method. Like execute, submit accepts Runnable objects, but also accepts Callable objects, which allow the task to return a value. The submit method returns a Future object, which is used to retrieve the Callable return value and to manage the status of both Callable and Runnable tasks.

ExecutorService also provides methods for submitting large collections of Callable objects. Finally, ExecutorService provides a number of methods for managing the shutdown of the executor. To support immediate shutdown, tasks should handle interrupts correctly.

The ScheduledExecutorService Interface

The ScheduledExecutorService interface supplements the methods of its parent ExecutorService with schedule, which executes a Runnable or Callable task after a specified delay. In addition, the interface defines scheduleAtFixedRate and scheduleWithFixedDelay, which executes specified tasks repeatedly, at defined intervals.



####8.2.2 Thread Pools

Most of the executor implementations in java.util.concurrent use thread pools, which consist of worker threads. This kind of thread exists separately from the Runnable and Callable tasks it executes and is often used to execute multiple tasks.

Using worker threads minimizes the overhead due to thread creation. Thread objects use a significant amount of memory, and in a large-scale application, allocating and deallocating many thread objects creates a significant memory management overhead.

One common type of thread pool is the fixed thread pool. This type of pool always has a specified number of threads running; if a thread is somehow terminated while it is still in use, it is automatically replaced with a new thread. Tasks are submitted to the pool via an internal queue, which holds extra tasks whenever there are more active tasks than threads.

An important advantage of the fixed thread pool is that applications using it degrade gracefully. To understand this, consider a web server application where each HTTP request is handled by a separate thread. If the application simply creates a new thread for every new HTTP request, and the system receives more requests than it can handle immediately, the application will suddenly stop responding to all requests when the overhead of all those threads exceed the capacity of the system. With a limit on the number of the threads that can be created, the application will not be servicing HTTP requests as quickly as they come in, but it will be servicing them as quickly as the system can sustain.

A simple way to create an executor that uses a fixed thread pool is to invoke the newFixedThreadPool factory method in java.util.concurrent.Executors This class also provides the following factory methods:

The newCachedThreadPool method creates an executor with an expandable thread pool. This executor is suitable for applications that launch many short-lived tasks.
The newSingleThreadExecutor method creates an executor that executes a single task at a time.
Several factory methods are ScheduledExecutorService versions of the above executors.
If none of the executors provided by the above factory methods meet your needs, constructing instances of java.util.concurrent.ThreadPoolExecutor or java.util.concurrent.ScheduledThreadPoolExecutor will give you additional options.



####8.2.3 Fork/Join
The fork/join framework is an implementation of the ExecutorService interface that helps you take advantage of multiple processors. It is designed for work that can be broken into smaller pieces recursively. The goal is to use all the available processing power to enhance the performance of your application.

As with any ExecutorService implementation, the fork/join framework distributes tasks to worker threads in a thread pool. The fork/join framework is distinct because it uses a **work-stealing algorithm**. Worker threads that run out of things to do can steal tasks from other threads that are still busy.

The center of the fork/join framework is the ForkJoinPool class, an extension of the AbstractExecutorService class. ForkJoinPool implements the core work-stealing algorithm and can execute ForkJoinTask processes.

##### Basic Use

The first step for using the fork/join framework is to write code that performs a segment of the work. Your code should look similar to the following pseudocode:

if (my portion of the work is small enough)
do the work directly
else
split my work into two pieces
invoke the two pieces and wait for the results
Wrap this code in a ForkJoinTask subclass, typically using one of its more specialized types, either RecursiveTask (which can return a result) or RecursiveAction.

After your ForkJoinTask subclass is ready, create the object that represents all the work to be done and pass it to the invoke() method of a ForkJoinPool instance.

Blurring for Clarity

To help you understand how the fork/join framework works, consider the following example. Suppose that you want to blur an image. The original source image is represented by an array of integers, where each integer contains the color values for a single pixel. The blurred destination image is also represented by an integer array with the same size as the source.

Performing the blur is accomplished by working through the source array one pixel at a time. Each pixel is averaged with its surrounding pixels (the red, green, and blue components are averaged), and the result is placed in the destination array. Since an image is a large array, this process can take a long time. You can take advantage of concurrent processing on multiprocessor systems by implementing the algorithm using the fork/join framework. Here is one possible implementation:

public class ForkBlur extends RecursiveAction {
private int[] mSource;
private int mStart;
private int mLength;
private int[] mDestination;
// Processing window size; should be odd.
private int mBlurWidth = 15;
public ForkBlur(int[] src, int start, int length, int[] dst) {
mSource = src;
mStart = start;
mLength = length;
mDestination = dst;
}

protected void computeDirectly() {
int sidePixels = (mBlurWidth - 1) / 2;
for (int index = mStart; index < mStart + mLength; index++) {
// Calculate average.
float rt = 0, gt = 0, bt = 0;
for (int mi = -sidePixels; mi <= sidePixels; mi++) {
int mindex = Math.min(Math.max(mi + index, 0),
mSource.length - 1);
int pixel = mSource[mindex];
rt += (float)((pixel & 0x00ff0000) >> 16)
/ mBlurWidth;
gt += (float)((pixel & 0x0000ff00) >> 8)
/ mBlurWidth;
bt += (float)((pixel & 0x000000ff) >> 0)
/ mBlurWidth;
}
// Reassemble destination pixel.
int dpixel = (0xff000000 ) |
(((int)rt) << 16) |
(((int)gt) << 8) |
(((int)bt) << 0);
mDestination[index] = dpixel;
}
}
...
Now you implement the abstract compute() method, which either performs the blur directly or splits it into two smaller tasks. A simple array length threshold helps determine whether the work is performed or split.

protected static int sThreshold = 100000;

protected void compute() {
if (mLength < sThreshold) {
computeDirectly();
return;
}
int split = mLength / 2;
invokeAll(new ForkBlur(mSource, mStart, split, mDestination),
new ForkBlur(mSource, mStart + split, mLength - split,
mDestination));
}
If the previous methods are in a subclass of the RecursiveAction class, then setting up the task to run in a ForkJoinPool is straightforward, and involves the following steps:

Create a task that represents all of the work to be done.

// source image pixels are in src
// destination image pixels are in dst
ForkBlur fb = new ForkBlur(src, 0, src.length, dst);
Create the ForkJoinPool that will run the task.

ForkJoinPool pool = new ForkJoinPool();
Run the task.

pool.invoke(fb);
For the full source code, including some extra code that creates the destination image file, see the ForkBlur example.

Standard Implementations

Besides using the fork/join framework to implement custom algorithms for tasks to be performed concurrently on a multiprocessor system (such as the ForkBlur.java example in the previous section), there are some generally useful features in Java SE which are already implemented using the fork/join framework. One such implementation, introduced in Java SE 8, is used by the java.util.Arrays class for its parallelSort() methods. These methods are similar to sort(), but leverage concurrency via the fork/join framework. Parallel sorting of large arrays is faster than sequential sorting when run on multiprocessor systems. However, how exactly the fork/join framework is leveraged by these methods is outside the scope of the Java Tutorials. For this information, see the Java API documentation.

Another implementation of the fork/join framework is used by methods in the java.util.streams package, which is part of Project Lambda scheduled for the Java SE 8 release. For more information, see the Lambda Expressions section.


###8.3 Concurrent Collections

The java.util.concurrent package includes a number of additions to the Java Collections Framework. These are most easily categorized by the collection interfaces provided:

BlockingQueue defines a first-in-first-out data structure that blocks or times out when you attempt to add to a full queue, or retrieve from an empty queue.
ConcurrentMap is a subinterface of java.util.Map that defines useful atomic operations. These operations remove or replace a key-value pair only if the key is present, or add a key-value pair only if the key is absent. Making these operations atomic helps avoid synchronization. The standard general-purpose implementation of ConcurrentMap is ConcurrentHashMap, which is a concurrent analog of HashMap.
ConcurrentNavigableMap is a subinterface of ConcurrentMap that supports approximate matches. The standard general-purpose implementation of ConcurrentNavigableMap is ConcurrentSkipListMap, which is a concurrent analog of TreeMap.
All of these collections help avoid Memory Consistency Errors by defining a happens-before relationship between an operation that adds an object to the collection with subsequent operations that access or remove that object.


###8.4 Atomic Variables

The java.util.concurrent.atomic package defines classes that support atomic operations on single variables. All classes have get and set methods that work like reads and writes on volatile variables. That is, a set has a happens-before relationship with any subsequent get on the same variable. The atomic compareAndSet method also has these memory consistency features, as do the simple atomic arithmetic methods that apply to integer atomic variables.

To see how this package might be used, let's return to the Counter class we originally used to demonstrate thread interference:


class Counter {
private int c = 0;

public void increment() {
c++;
}

public void decrement() {
c--;
}

public int value() {
return c;
}

}
One way to make Counter safe from thread interference is to make its methods synchronized, as in SynchronizedCounter:


class SynchronizedCounter {
private int c = 0;

public synchronized void increment() {
c++;
}

public synchronized void decrement() {
c--;
}

public synchronized int value() {
return c;
}

}
For this simple class, synchronization is an acceptable solution. But for a more complicated class, we might want to avoid the liveness impact of unnecessary synchronization. Replacing the int field with an AtomicInteger allows us to prevent thread interference without resorting to synchronization, as in AtomicCounter:


import java.util.concurrent.atomic.AtomicInteger;

class AtomicCounter {
private AtomicInteger c = new AtomicInteger(0);

public void increment() {
c.incrementAndGet();
}

public void decrement() {
c.decrementAndGet();
}

public int value() {
return c.get();
}

}


####8.5 Concurrent Random Numbers

In JDK 7, java.util.concurrent includes a convenience class, ThreadLocalRandom, for applications that expect to use random numbers from multiple threads or ForkJoinTasks.

For concurrent access, using ThreadLocalRandom instead of Math.random() results in less contention and, ultimately, better performance.

All you need to do is call ThreadLocalRandom.current(), then call one of its methods to retrieve a random number. Here is one example:

int r = ThreadLocalRandom.current() .nextInt(4, 77);

###Synchronization Questions
**Q: What is synchronization and why is it important? **
```
A: With respect to multithreading, synchronization is the capability to control the access of multiple threads to shared resources. Without synchronization, it is possible for one thread to modify a shared object while another thread is in the process of using or updating that object's value. This often leads to significant errors.
```
**Q: What are synchronized methods and synchronized statements? **
```
1): Synchronized methods are methods that are used to control access to an object. A thread only executes a synchronized method after it has acquired the lock for the method's object or class.

2): Synchronized statements are similar to synchronized methods. A synchronized statement can only be executed after a thread has acquired the lock for the object or class referenced in the synchronized statement.
```
**Q: When a thread is created and started, what is its initial state? **
```
A: A thread is in the ready state after it has been created and started.
```

**Q: What is daemon thread and which method is used to create the daemon thread?**
```
A: Daemon thread is a low priority thread which runs intermittently in the back ground doing the garbage collection operation for the java runtime system.

setDaemon method is used to create a daemon thread.
```

**Q: What method must be implemented by all threads? **
```
A: run() method. Whether they
1) extends Thread
2) implements Runnable // this way is preferable. It can extend other class.
```
###More Questions
**Q: How does Java handle integer overflows and underflows? **
```
A: It uses those low order bytes of the result that can fit into the size of the type allowed by the operation.
```

**Q: Does garbage collection guarantee that a program will not run out of memory?**
```
A: No.
1) It is possible for programs to use up memory resources faster than they are garbage collected.
2) It is also possible for programs to create objects that are not subject to garbage collection
```

**Q: What is the difference between preemptive scheduling and time slicing? **
```
1) Under preemptive scheduling, the highest priority task executes until it enters the waiting or dead states or a higher priority task comes into existence.

2) Under time slicing, a task executes for a predefined slice of time and then reenters the pool of ready tasks. The scheduler then determines which task should execute next, based on priority and other factors.
```

**Q: What is the purpose of finalization? **
```
A: It is to give an unreachable object the opportunity to perform any cleanup processing before the object is garbage collected.
```

**Q: What is the difference between processes and threads? **
```
1) A process is an execution of a program
2) A thread is a single execution sequence within the process.
3) A process can contain multiple threads.
4) A thread is sometimes called a lightweight process.
```

**Q: Briefly explain high-level thread states? **
```
The state chart diagram below describes the thread states.

1) Runnable — A thread becomes runnable when you call the start( ), but does not necessarily start running immediately. It will be pooled waiting for its turn to be picked for execution by the thread scheduler based on thread priorities.

2) Running: The processor is actively executing the thread code. It runs until it becomes blocked, or voluntarily gives up its turn with this static method Thread.yield( ). Because of context switching overhead, yield( ) should not be used very frequently

3) Waiting:
a. setup time is optional.
b. release both execution right and lock
c. Needs notify() to wake up.
A thread is in a blocked state while it waits for some external processing
such as file I/O to finish.A call to currObject.wait( ) method causes the
current thread to wait until some other thread invokescurrObject.notify( )
or the currObject.notifyAll( ) is executed.

4) Sleeping:
a. setup tiem is required.
b. release execution right, but not lock.
Java threads are forcibly put to sleep (suspended) with this overloaded method:
Thread.sleep(milliseconds), Thread.sleep(milliseconds, nanoseconds);

4) Blocked on I/O: Will move to runnable after I/O condition like reading bytes of data etc changes.

5) Blocked on synchronization: will move to running when a lock is acquired.

6) Dead: The thread is finished working.

```

**Q. What is the difference between yield and sleeping? **
```
1). When a task invokes yield( ), it changes from running state to runnable state.
**Sleep** causes thread to suspend itself for x milliseconds
**Yield** suspends the thread and immediately moves it to the ready queue (the queue which the CPU uses to run threads).
2) When a task invokes sleep ( ), it changes from running state to waiting/sleeping state.
3) The method wait(1000) causes the current thread to wait up to one second a signal from other threads. A thread could wait less than 1 second if it receives the notify( ) or notifyAll( ) method call.
4) The call to sleep(1000) causes the current thread to sleep for t least 1 second.
```
###Java Concurrency Interview Questions
**Q.What is atomic operation? What are atomic classes in Java Concurrency API? **
```
1) Atomic operations are performed in a single unit of task without interference from other operations.

2) Atomic operations are necessity in multi-threaded environment to avoid data inconsistency.

3) For example int++ is not an atomic operation. So by the time one threads read it’s value and increment it by one, other thread has read the older value leading to wrong result.

4) To solve this issue, we will have to make sure that increment operation on count is atomic, we can do that using Synchronization but Java 5 java.util.concurrent.atomic provides wrapper classes for int and long that can be used to achieve this atomically without usage of Synchronization.
```
**Q.What is Lock interface in Java Concurrency API? What are it’s benefits over synchronization?**
```
1) Lock interface provide more extensive locking operations than can be obtained using synchronized methods and statements.

2) They allow more flexible structuring, may have quite different properties, and may support multiple associated Condition objects.

3) The advantages of a lock are:
**it’s possible to make them fair
**it’s possible to make a thread responsive to interruption while waiting on a Lock object.
**it’s possible to try to acquire the lock, but return immediately or after a timeout if the lock can’t be acquired
**it’s possible to acquire and release locks in different scopes, and in different orders
```

**Q.What is Executors Framework?**
```
1) The Executor framework is a framework for standardizing invocation, scheduling, execution, and control of asynchronous tasks according to a set of execution policies.

2) Creating a lot many threads with no bounds to the maximum threshold can cause application to run out of heap memory. So, creating a ThreadPool is a better solution as a finite number of threads can be pooled and reused.

3) Executors framework facilitate process of creating Thread pools in java.
```
**Q.What is BlockingQueue? How can we implement Producer-Consumer problem using Blocking Queue? **
```
1) BlockingQueue is a Queue that supports operations that wait for the queue to become non-empty when retrieving and removing an element, and wait for space to become available in the queue when adding an element.

2) BlockingQueue doesn’t accept null values and throw NullPointerException if you try to store null value in the queue.

3) BlockingQueue implementations are thread-safe. All queuing methods are atomic in nature and use internal locks or other forms of concurrency control.

4) BlockingQueue interface is part of java collections framework and it’s primarily used for implementing producer consumer problem.
```
**Q.What is Callable and Future?**
```
1) Callable interface in concurrency package that is similar to Runnable interface but it can return any Object and able to throw Exception.

2) Callable interface use Generic to define the return type of Object. Executors class provide useful methods to execute Callable in a thread pool.

3) Since callable tasks run in parallel, we have to wait for the returned Object.

4) Callable tasks return java.util.concurrent.Future object. Using Future we can find out the status of the Callable task and get the returned Object.

5) It provides get() method that can wait for the Callable to finish and then return the result.
```

**Q.What is FutureTask Class? **
```
1) FutureTask is the base implementation class of Future interface and we can use it with Executors for asynchronous processing.

2) Most of the time we don’t need to use FutureTask class but it comes real handy if we want to override some of the methods of Future interface and want to keep most of the base implementation. We can just extend this class and override the methods according to our requirements.
```
**Q.What are Concurrent Collection Classes? **
```
1) Java Collection classes are fail-fast which means that if the Collection will be changed while some thread is traversing over it using iterator, the iterator.next() will throw ConcurrentModificationException.

2) Concurrent Collection classes support full concurrency of retrievals and adjustable expected concurrency for updates. Major classes are ConcurrentHashMap, CopyOnWriteArrayList and CopyOnWriteArraySet
```
**Q.What is Executors Class? **
```
1) Executors class provide utility methods for Executor, ExecutorService, ScheduledExecutorService, ThreadFactory, and Callable classes.

2) Executors class can be used to easily create Thread Pool in java, also this is the only class supporting execution of Callable implementations.
```


