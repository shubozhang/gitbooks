`
HashMap`is a very powerful data structure in[Java](http://crunchify.com/category/java-tutorials/). We use it everyday and almost in all applications. There are quite a few examples which I have written before on[How to Implement Threadsafe cache](http://crunchify.com/implement-simple-threadsafe-cache-using-hashmap-without-using-synchronized-collection/), How to convert[Hashmap to Arraylist](http://crunchify.com/how-to-convert-hashmap-to-arraylist-in-java/)?

We used Hashmap in both above examples but those are pretty simple use cases of Hashmap.`HashMap is a non-synchronized`collection class.

#### Do you have any of below questions?

* What’s the difference between ConcurrentHashMap and Collections.synchronizedMap\(Map\)?
* What’s the difference between ConcurrentHashMap and Collections.synchronizedMap\(Map\) in term of performance?
* ConcurrentHashMap vs Collections.synchronizedMap\(\)
* Popular HashMap and ConcurrentHashMap interview questions

In this tutorial we will go over all above queries and reason `why and how`we could Synchronize Hashmap?





### Why?

The Map object is an associative containers that store elements, formed by a combination of a uniquely identify`key` and a mapped`value`. If you have very highly concurrent application in which you may want to modify or read key value in different threads then it’s ideal to use Concurrent Hashmap. Best example is[Producer Consumer](http://crunchify.com/java-producer-consumer-example-handle-concurrent-read-write/) which handles concurrent read/write.

So what does the thread-safe Map means? If`multiple threads`access a hash map concurrently, and at least one of the threads modifies the map structurally, it`must be synchronized externally`to avoid an inconsistent view of the contents.



### How?

There are two ways we could synchronized[HashMap](http://crunchify.com/java-hashmap-containskeyobject-key-and-containsvalueobject-value-check-if-key-exists-in-map/)

1. Java Collections synchronizedMap\(\) method
2. Use ConcurrentHashMap

```java
//Hashtable
Map<String, String> normalMap = new Hashtable<String, String>();
//synchronizedMap
synchronizedHashMap = Collections.synchronizedMap(new HashMap<String, String>());
//ConcurrentHashMap
concurrentHashMap = new ConcurrentHashMap<String, String>();
```




ConcurrentHashMap

You should use ConcurrentHashMap when you need very high concurrency in your project.
It is thread safe without synchronizing the whole map.
Reads can happen very fast while write is done with a lock.
There is no locking at the object level.
The locking is at a much finer granularity at a hashmap bucket level.
ConcurrentHashMap doesn’t throw a ConcurrentModificationException if one thread tries to modify it while another is iterating over it.
ConcurrentHashMap uses multitude of locks.


SynchronizedHashMap

Synchronization at Object level.
Every read/write operation needs to acquire lock.
Locking the entire collection is a performance overhead.
This essentially gives access to only one thread to the entire map & blocks all the other threads.
It may cause contention.
SynchronizedHashMap returns Iterator, which fails-fast on concurrent modification.
Now let’s take a look at code

Create class CrunchifyConcurrentHashMapVsSynchronizedHashMap.java
Create object for each HashTable, SynchronizedMap and CrunchifyConcurrentHashMap
Add and retrieve 500k entries from Map
Measure start and end time and display time in milliseconds
We will use ExecutorService to run 5 threads in parallel

```java
package crunchify.com.tutorials;
import java.util.Collections;
import java.util.HashMap;
import java.util.Hashtable;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
/**
* @author Crunchify.com
*
*/
public class CrunchifyConcurrentHashMapVsSynchronizedMap {
public final static int THREAD_POOL_SIZE = 5;
public static Map<String, Integer> crunchifyHashTableObject = null;
public static Map<String, Integer> crunchifySynchronizedMapObject = null;
public static Map<String, Integer> crunchifyConcurrentHashMapObject = null;
public static void main(String[] args) throws InterruptedException {
// Test with Hashtable Object
crunchifyHashTableObject = new Hashtable<String, Integer>();
crunchifyPerformTest(crunchifyHashTableObject);
// Test with synchronizedMap Object
crunchifySynchronizedMapObject = Collections.synchronizedMap(new HashMap<String, Integer>());
crunchifyPerformTest(crunchifySynchronizedMapObject);
// Test with ConcurrentHashMap Object
crunchifyConcurrentHashMapObject = new ConcurrentHashMap<String, Integer>();
crunchifyPerformTest(crunchifyConcurrentHashMapObject);
}
public static void crunchifyPerformTest(final Map<String, Integer> crunchifyThreads) throws InterruptedException {
System.out.println("Test started for: " + crunchifyThreads.getClass());
long averageTime = 0;
for (int i = 0; i < 5; i++) {
long startTime = System.nanoTime();
ExecutorService crunchifyExServer = Executors.newFixedThreadPool(THREAD_POOL_SIZE);
for (int j = 0; j < THREAD_POOL_SIZE; j++) {
crunchifyExServer.execute(new Runnable() {
@SuppressWarnings("unused")
@Override
public void run() {
for (int i = 0; i < 500000; i++) {
Integer crunchifyRandomNumber = (int) Math.ceil(Math.random() * 550000);
// Retrieve value. We are not using it anywhere
Integer crunchifyValue = crunchifyThreads.get(String.valueOf(crunchifyRandomNumber));
// Put value
crunchifyThreads.put(String.valueOf(crunchifyRandomNumber), crunchifyRandomNumber);
}
}
});
}
// Make sure executor stops
crunchifyExServer.shutdown();
// Blocks until all tasks have completed execution after a shutdown request
crunchifyExServer.awaitTermination(Long.MAX_VALUE, TimeUnit.DAYS);
long entTime = System.nanoTime();
long totalTime = (entTime - startTime) / 1000000L;
averageTime += totalTime;
System.out.println("2500K entried added/retrieved in " + totalTime + " ms");
}
System.out.println("For " + crunchifyThreads.getClass() + " the average time is " + averageTime / 5 + " ms\n");
}
}
```
