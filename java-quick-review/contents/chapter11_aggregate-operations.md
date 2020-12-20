#
Aggregate Operations

Note: To better understand the concepts in this section, review the sections Lambda Expressions and Method References.

For what do you use collections? You don't simply store objects in a collection and leave them there. In most cases, you use collections to retrieve items stored in them.

Consider again the scenario described in the section Lambda Expressions. Suppose that you are creating a social networking application. You want to create a feature that enables an administrator to perform any kind of action, such as sending a message, on members of the social networking application that satisfy certain criteria.

```java
public class Person {
public enum Sex {
MALE, FEMALE
}
String name;
LocalDate birthday;
Sex gender;
String emailAddress;
// ...
public int getAge() {
// ...
}
public String getName() {
// ...
}
}
```

The following example prints the name of all members contained in the collection roster with a for-each loop:
```java
for (Person p : roster) {
System.out.println(p.getName());
}
```
The following example prints all members contained in the collection roster but with the aggregate operation forEach:
```java
roster
.stream()
.forEach(e -> System.out.println(e.getName());
```
Although, in this example, the version that uses aggregate operations is longer than the one that uses a for-each loop, you will see that versions that use bulk-data operations will be more concise for more complex tasks.

The following topics are covered:
* Pipelines and Streams
* Differences Between Aggregate Operations and Iterators

##1.1 Pipelines and Streams
A pipeline is a sequence of aggregate operations. The following example prints the male members contained in the collection roster with a pipeline that consists of the aggregate operations filter and forEach:
```java
roster
.stream()
.filter(e -> e.getGender() == Person.Sex.MALE)
.forEach(e -> System.out.println(e.getName()));
```
Compare this example to the following that prints the male members contained in the collection roster with a for-each loop:
```java
for (Person p : roster) {
if (p.getGender() == Person.Sex.MALE) {
System.out.println(p.getName());
}
}
```
A pipeline contains the following components:
* A source: This could be a collection, an array, a generator function, or an I/O channel. In this example, the source is the collection roster.
* Zero or more intermediate operations. An intermediate operation, such as filter, produces a new stream.
* A stream is a sequence of elements. Unlike a collection, it is not a data structure that stores elements. Instead, a stream carries values from a source through a pipeline. This example creates a stream from the collection roster by invoking the method stream.
* The filter operation returns a new stream that contains elements that match its predicate (this operation's parameter). In this example, the predicate is the lambda expression e -> e.getGender() == Person.Sex.MALE. It returns the boolean value true if the gender field of object e has the value Person.Sex.MALE. Consequently, the filter operation in this example returns a stream that contains all male members in the collection roster.

* A terminal operation. A terminal operation, such as forEach, produces a non-stream result, such as a primitive value (like a double value), a collection, or in the case of forEach, no value at all. In this example, the parameter of the forEach operation is the lambda expression e -> System.out.println(e.getName()), which invokes the method getName on the object e. (The Java runtime and compiler infer that the type of the object e is Person.)

The following example calculates the average age of all male members contained in the collection roster with a pipeline that consists of the aggregate operations filter, mapToInt, and average:
```java
double average = roster
.stream()
.filter(p -> p.getGender() == Person.Sex.MALE)
.mapToInt(Person::getAge)
.average()
.getAsDouble();
```
The mapToInt operation returns a new stream of type IntStream (which is a stream that contains only integer values). The operation applies the function specified in its parameter to each element in a particular stream. In this example, the function is Person::getAge, which is a method reference that returns the age of the member. (Alternatively, you could use the lambda expression e -> e.getAge().) Consequently, the mapToInt operation in this example returns a stream that contains the ages of all male members in the collection roster.

The average operation calculates the average value of the elements contained in a stream of type IntStream. It returns an object of type OptionalDouble. If the stream contains no elements, then the average operation returns an empty instance of OptionalDouble, and invoking the method getAsDouble throws a NoSuchElementException. The JDK contains many terminal operations such as average that return one value by combining the contents of a stream. These operations are called reduction operations; see the section Reduction for more information.


##1.2 Differences Between Aggregate Operations and Iterators
Aggregate operations, like forEach, appear to be like iterators. However, they have several fundamental differences:

* **They use internal iteration**: Aggregate operations do not contain a method like next to instruct them to process the next element of the collection. With internal delegation, your application determines what collection it iterates, but the JDK determines how to iterate the collection. With external iteration, your application determines both what collection it iterates and how it iterates it. However, external iteration can only iterate over the elements of a collection sequentially. Internal iteration does not have this limitation. It can more easily take advantage of parallel computing, which involves dividing a problem into subproblems, solving those problems simultaneously, and then combining the results of the solutions to the subproblems. See the section Parallelism for more information.

* **They process elements from a stream**: Aggregate operations process elements from a stream, not directly from a collection. Consequently, they are also called stream operations.

* **They support behavior as parameters**: You can specify lambda expressions as parameters for most aggregate operations. This enables you to customize the behavior of a particular aggregate operation.



##2. Reduction (one of the terminal operations)
Terminal operations:
![](/assets/terminal-operations.PNG)

The section Aggregate Operations describes the following pipeline of operations, which calculates the average age of all male members in the collection roster:
```java
double average = roster
.stream()
.filter(p -> p.getGender() == Person.Sex.MALE)
.mapToInt(Person::getAge)
.average()
.getAsDouble();
```
The JDK contains many **terminal operations** (such as **average, sum, min, max, and count**) that return one value by combining the contents of a stream. These operations are called **reduction operations**. The JDK also contains reduction operations that return a collection instead of a single value. Many reduction operations perform a specific task, such as finding the average of values or grouping elements into categories. However, the JDK provides you with the general-purpose reduction operations reduce and collect, which this section describes in detail.

This section covers the following topics:
* The Stream.reduce Method
* The Stream.collect Method


###2.1 The Stream.reduce Method
The Stream.reduce method is a general-purpose reduction operation. Consider the following pipeline, which calculates the sum of the male members' ages in the collection roster. It uses the Stream.sum reduction operation:
```java
Integer totalAge = roster
.stream()
.mapToInt(Person::getAge)
.sum();
```
Compare this with the following pipeline, which uses the Stream.reduce operation to calculate the same value:
```java
Integer totalAgeReduce = roster
.stream()
.map(Person::getAge)
.reduce(0,(a, b) -> a + b);
```
The ``reduce`` operation in this example takes two arguments:
* **identity**: The identity element is both the initial value of the reduction and the default result if there are no elements in the stream. In this example, the identity element is 0; this is the initial value of the sum of ages and the default value if no members exist in the collection roster.

* **accumulator**: The accumulator function takes two parameters: a partial result of the reduction (in this example, the sum of all processed integers so far) and the next element of the stream (in this example, an integer). It returns a new partial result. In this example, the accumulator function is a lambda expression that adds two Integer values and returns an Integer value:
```java
(a, b) -> a + b
```

The ``reduce`` operation always returns a new value. However, the accumulator function also returns a new value every time it processes an element of a stream. Suppose that you want to reduce the elements of a stream to a more complex object, such as a collection. This might hinder the performance of your application. If your reduce operation involves adding elements to a collection, then every time your accumulator function processes an element, it creates a new collection that includes the element, which is inefficient. It would be more efficient for you to update an existing collection instead. You can do this with the Stream.collect method, which the next section describes.

###2.2 The Stream.collect Method
Unlike the reduce method, which always creates a new value when it processes an element, the collect method modifies, or mutates, an existing value.

Consider how to find the average of values in a stream. You require two pieces of data: the total number of values and the sum of those values. However, like the reduce method and all other reduction methods, the collect method returns only one value. You can create a new data type that contains member variables that keep track of the total number of values and the sum of those values, such as the following class, Averager:
```java
class Averager implements IntConsumer{
private int total = 0;
private int count = 0;
public double average() {
return count > 0 ? ((double) total)/count : 0;
}
public void accept(int i) { total += i; count++; }
public void combine(Averager other) {
total += other.total;
count += other.count;
}
}
```
The following pipeline uses the Averager class and the collect method to calculate the average age of all male members:
```
Averager averageCollect = roster.stream()
.filter(p -> p.getGender() == Person.Sex.MALE)
.map(Person::getAge)
.collect(Averager::new, Averager::accept, Averager::combine);
System.out.println("Average age of male members: " +
averageCollect.average());
```

The ``collect`` operation in this example takes three arguments:
* **supplier**: The supplier is a factory function; it constructs new instances. For the collect operation, it creates instances of the result container. In this example, it is a new instance of the Averager class.
* **accumulator**: The accumulator function incorporates a stream element into a result container. In this example, it modifies the Averager result container by incrementing the count variable by one and adding to the total member variable the value of the stream element, which is an integer representing the age of a male member.
* **combiner**: The combiner function takes two result containers and merges their contents. In this example, it modifies an Averager result container by incrementing the count variable by the count member variable of the other Averager instance and adding to the total member variable the value of the other Averager instance's total member variable.


Note the following:
* The supplier is a lambda expression (or a method reference) as opposed to a value like the identity element in the reduce operation.
* The accumulator and combiner functions do not return a value.
* You can use the collect operations with parallel streams; see the section Parallelism for more information. (If you run the collect method with a parallel stream, then the JDK creates a new thread whenever the combiner function creates a new object, such as an Averager object in this example. Consequently, you do not have to worry about synchronization.)

Although the JDK provides you with the average operation to calculate the average value of elements in a stream, you can use the collect operation and a custom class if you need to calculate several values from the elements of a stream.

The collect operation is best suited for collections. The following example puts the names of the male members in a collection with the collect operation:
```java
List<String> namesOfMaleMembersCollect = roster
.stream()
.filter(p -> p.getGender() == Person.Sex.MALE)
.map(p -> p.getName())
.collect(Collectors.toList());
```
This version of the collect operation takes one parameter of type Collector. This class encapsulates the functions used as arguments in the collect operation that requires three arguments (supplier, accumulator, and combiner functions).

The Collectors class contains many useful reduction operations, such as accumulating elements into collections and summarizing elements according to various criteria. These reduction operations return instances of the class Collector, so you can use them as a parameter for the collect operation.

This example uses the Collectors.toList operation, which accumulates the stream elements into a new instance of List. As with most operations in the Collectors class, the toList operator returns an instance of Collector, not a collection.

The following example groups members of the collection roster by gender:
```java
Map<Person.Sex, List<Person>> byGender =
roster
.stream()
.collect(
Collectors.groupingBy(Person::getGender));
```

The groupingBy operation returns a map whose keys are the values that result from applying the lambda expression specified as its parameter (which is called a classification function). In this example, the returned map contains two keys, Person.Sex.MALE and Person.Sex.FEMALE. The keys' corresponding values are instances of List that contain the stream elements that, when processed by the classification function, correspond to the key value. For example, the value that corresponds to key Person.Sex.MALE is an instance of List that contains all male members.

The following example retrieves the names of each member in the collection roster and groups them by gender:
```java
Map<Person.Sex, List<String>> namesByGender =
roster
.stream()
.collect(
Collectors.groupingBy(
Person::getGender,
Collectors.mapping(
Person::getName,
Collectors.toList())));
```
The groupingBy operation in this example takes two parameters, a classification function and an instance of Collector. The Collector parameter is called a downstream collector. This is a collector that the Java runtime applies to the results of another collector. Consequently, this groupingBy operation enables you to apply a collect method to the List values created by the groupingBy operator. This example applies the collector mapping, which applies the mapping function Person::getName to each element of the stream. Consequently, the resulting stream consists of only the names of members. A pipeline that contains one or more downstream collectors, like this example, is called a multilevel reduction.

The following example retrieves the total age of members of each gender:
```java
Map<Person.Sex, Integer> totalAgeByGender =
roster
.stream()
.collect(
Collectors.groupingBy(
Person::getGender,
Collectors.reducing(0,
Person::getAge,
Integer::sum)));
```
The reducing operation takes three parameters:

identity: Like the Stream.reduce operation, the identity element is both the initial value of the reduction and the default result if there are no elements in the stream. In this example, the identity element is 0; this is the initial value of the sum of ages and the default value if no members exist.
mapper: The reducing operation applies this mapper function to all stream elements. In this example, the mapper retrieves the age of each member.
operation: The operation function is used to reduce the mapped values. In this example, the operation function adds Integer values.
The following example retrieves the average age of members of each gender:
```java
Map<Person.Sex, Double> averageAgeByGender = roster
.stream()
.collect(
Collectors.groupingBy(
Person::getGender,
Collectors.averagingInt(Person::getAge)));
```


##3. Parallelism
Parallel computing involves dividing a problem into subproblems, solving those problems simultaneously (in parallel, with each subproblem running in a separate thread), and then combining the results of the solutions to the subproblems. Java SE provides the fork/join framework, which enables you to more easily implement parallel computing in your applications. However, with this framework, you must specify how the problems are subdivided (partitioned). With aggregate operations, the Java runtime performs this partitioning and combining of solutions for you.

One difficulty in implementing parallelism in applications that use collections is that collections are not thread-safe, which means that multiple threads cannot manipulate a collection without introducing thread interference or memory consistency errors. The Collections Framework provides synchronization wrappers, which add automatic synchronization to an arbitrary collection, making it thread-safe. However, synchronization introduces thread contention. You want to avoid thread contention because it prevents threads from running in parallel. Aggregate operations and parallel streams enable you to implement parallelism with non-thread-safe collections provided that you do not modify the collection while you are operating on it.

Note that parallelism is not automatically faster than performing operations serially, although it can be if you have enough data and processor cores. While aggregate operations enable you to more easily implement parallelism, it is still your responsibility to determine if your application is suitable for parallelism.

This section covers the following topics:
* Executing Streams in Parallel
* Concurrent Reduction
* Ordering
* Side Effects
* Laziness
* Interference
* Stateful Lambda Expressions


###3.1 Executing Streams in Parallel

You can execute streams in serial or in parallel. When a stream executes in parallel, the Java runtime partitions the stream into multiple substreams. Aggregate operations iterate over and process these substreams in parallel and then combine the results.

When you create a stream, it is always a serial stream unless otherwise specified. To create a parallel stream, invoke the operation Collection.parallelStream. Alternatively, invoke the operation BaseStream.parallel. For example, the following statement calculates the average age of all male members in parallel:
```java
double average = roster
.parallelStream()
.filter(p -> p.getGender() == Person.Sex.MALE)
.mapToInt(Person::getAge)
.average()
.getAsDouble();
```


###3.2 Concurrent Reduction
Consider again the following example (which is described in the section Reduction) that groups members by gender. This example invokes the collect operation, which reduces the collection roster into a Map:
```java
Map<Person.Sex, List<Person>> byGender =
roster
.stream()
.collect(
Collectors.groupingBy(Person::getGender));
The following is the parallel equivalent:

ConcurrentMap<Person.Sex, List<Person>> byGender =
roster
.parallelStream()
.collect(
Collectors.groupingByConcurrent(Person::getGender));
```
This is called a ``concurrent reduction``. The Java runtime performs a concurrent reduction if all of the the following are true for a particular pipeline that contains the collect operation:
* The stream is parallel.
* The parameter of the collect operation, the collector, has the characteristic Collector.Characteristics.CONCURRENT. To determine the characteristics of a collector, invoke the Collector.characteristics method.
* Either the stream is unordered, or the collector has the characteristic Collector.Characteristics.UNORDERED. To ensure that the stream is unordered, invoke the BaseStream.unordered operation.

**Note**: This example returns an instance of ConcurrentMap instead of Map and invokes the groupingByConcurrent operation instead of groupingBy. (See the section Concurrent Collections for more information about ConcurrentMap.) Unlike the operation groupingByConcurrent, the operation groupingBy performs poorly with parallel streams. (This is because it operates by merging two maps by key, which is computationally expensive.) Similarly, the operation Collectors.toConcurrentMap performs better with parallel streams than the operation Collectors.toMap.


###3.3 Ordering

The order in which a pipeline processes the elements of a stream depends on whether the stream is executed in serial or in parallel, the source of the stream, and intermediate operations. For example, consider the following example that prints the elements of an instance of ArrayList with the forEach operation several times:
```java
Integer[] intArray = {1, 2, 3, 4, 5, 6, 7, 8 };
List<Integer> listOfIntegers =
new ArrayList<>(Arrays.asList(intArray));

System.out.println("listOfIntegers:");
listOfIntegers
.stream()
.forEach(e -> System.out.print(e + " "));
System.out.println("");

System.out.println("listOfIntegers sorted in reverse order:");
Comparator<Integer> normal = Integer::compare;
Comparator<Integer> reversed = normal.reversed();
Collections.sort(listOfIntegers, reversed);
listOfIntegers
.stream()
.forEach(e -> System.out.print(e + " "));
System.out.println("");
System.out.println("Parallel stream");
listOfIntegers
.parallelStream()
.forEach(e -> System.out.print(e + " "));
System.out.println("");
System.out.println("Another parallel stream:");
listOfIntegers
.parallelStream()
.forEach(e -> System.out.print(e + " "));
System.out.println("");
System.out.println("With forEachOrdered:");
listOfIntegers
.parallelStream()
.forEachOrdered(e -> System.out.print(e + " "));
System.out.println("");
```
This example consists of five pipelines. It prints output similar to the following:
```java
listOfIntegers:
1 2 3 4 5 6 7 8
listOfIntegers sorted in reverse order:
8 7 6 5 4 3 2 1
Parallel stream:
3 4 1 6 2 5 7 8
Another parallel stream:
6 3 1 5 7 8 4 2
With forEachOrdered:
8 7 6 5 4 3 2 1
```

This example does the following:
* The first pipeline prints the elements of the list listOfIntegers in the order that they were added to the list.
* The second pipeline prints the elements of listOfIntegers after it was sorted by the method Collections.sort.
* The third and fourth pipelines print the elements of the list in an apparently random order. Remember that stream operations use internal iteration when processing elements of a stream. Consequently, when you execute a stream in parallel, the Java compiler and runtime determine the order in which to process the stream's elements to maximize the benefits of parallel computing unless otherwise specified by the stream operation.
* The fifth pipeline uses the method forEachOrdered, which processes the elements of the stream in the order specified by its source, regardless of whether you executed the stream in serial or parallel. Note that you may lose the benefits of parallelism if you use operations like forEachOrdered with parallel streams.



###3.4 Side Effects

A method or an expression has a side effect if, in addition to returning or producing a value, it also modifies the state of the computer. Examples include mutable reductions (operations that use the collect operation; see the section Reduction for more information) as well as invoking the System.out.println method for debugging. The JDK handles certain side effects in pipelines well. In particular, the collect method is designed to perform the most common stream operations that have side effects in a parallel-safe manner. Operations like forEach and peek are designed for side effects; a lambda expression that returns void, such as one that invokes System.out.println, can do nothing but have side effects. Even so, you should use the forEach and peek operations with care; if you use one of these operations with a parallel stream, then the Java runtime may invoke the lambda expression that you specified as its parameter concurrently from multiple threads. In addition, never pass as parameters lambda expressions that have side effects in operations such as filter and map. The following sections discuss interference and stateful lambda expressions, both of which can be sources of side effects and can return inconsistent or unpredictable results, especially in parallel streams. However, the concept of laziness is discussed first, because it has a direct effect on interference.

###3.5 Laziness

All intermediate operations are lazy. An expression, method, or algorithm is lazy if its value is evaluated only when it is required. (An algorithm is eager if it is evaluated or processed immediately.) Intermediate operations are lazy because they do not start processing the contents of the stream until the terminal operation commences. Processing streams lazily enables the Java compiler and runtime to optimize how they process streams. For example, in a pipeline such as the filter-mapToInt-average example described in the section Aggregate Operations, the average operation could obtain the first several integers from the stream created by the mapToInt operation, which obtains elements from the filter operation. The average operation would repeat this process until it had obtained all required elements from the stream, and then it would calculate the average.

###3.6 Interference

Lambda expressions in stream operations should not interfere. Interference occurs when the source of a stream is modified while a pipeline processes the stream. For example, the following code attempts to concatenate the strings contained in the List listOfStrings. However, it throws a ConcurrentModificationException:
```java
try {
List<String> listOfStrings =
new ArrayList<>(Arrays.asList("one", "two"));
// This will fail as the peek operation will attempt to add the
// string "three" to the source after the terminal operation has
// commenced.
String concatenatedString = listOfStrings
.stream()
// Don't do this! Interference occurs here.
.peek(s -> listOfStrings.add("three"))
.reduce((a, b) -> a + " " + b)
.get();
System.out.println("Concatenated string: " + concatenatedString);
} catch (Exception e) {
System.out.println("Exception caught: " + e.toString());
}
```
This example concatenates the strings contained in listOfStrings into an Optional<String> value with the reduce operation, which is a terminal operation. However, the pipeline here invokes the intermediate operation peek, which attempts to add a new element to listOfStrings. Remember, all intermediate operations are lazy. This means that the pipeline in this example begins execution when the operation get is invoked, and ends execution when the get operation completes. The argument of the peek operation attempts to modify the stream source during the execution of the pipeline, which causes the Java runtime to throw a ConcurrentModificationException.


###3.7 Stateful Lambda Expressions

Avoid using stateful lambda expressions as parameters in stream operations. A stateful lambda expression is one whose result depends on any state that might change during the execution of a pipeline. The following example adds elements from the List listOfIntegers to a new List instance with the map intermediate operation. It does this twice, first with a serial stream and then with a parallel stream:
```java
List<Integer> serialStorage = new ArrayList<>();
System.out.println("Serial stream:");
listOfIntegers
.stream()
// Don't do this! It uses a stateful lambda expression.
.map(e -> { serialStorage.add(e); return e; })
.forEachOrdered(e -> System.out.print(e + " "));
System.out.println("");
serialStorage
.stream()
.forEachOrdered(e -> System.out.print(e + " "));
System.out.println("");

System.out.println("Parallel stream:");
List<Integer> parallelStorage = Collections.synchronizedList(
new ArrayList<>());
listOfIntegers
.parallelStream()
// Don't do this! It uses a stateful lambda expression.
.map(e -> { parallelStorage.add(e); return e; })
.forEachOrdered(e -> System.out.print(e + " "));
System.out.println("");
parallelStorage
.stream()
.forEachOrdered(e -> System.out.print(e + " "));
System.out.println("");
```

The lambda expression e -> { parallelStorage.add(e); return e; } is a stateful lambda expression. Its result can vary every time the code is run. This example prints the following:
```java
Serial stream:
8 7 6 5 4 3 2 1
8 7 6 5 4 3 2 1
Parallel stream:
8 7 6 5 4 3 2 1
1 3 6 2 4 5 8 7
```

The operation forEachOrdered processes elements in the order specified by the stream, regardless of whether the stream is executed in serial or parallel. However, when a stream is executed in parallel, the map operation processes elements of the stream specified by the Java runtime and compiler. Consequently, the order in which the lambda expression e -> { parallelStorage.add(e); return e; } adds elements to the List parallelStorage can vary every time the code is run. For deterministic and predictable results, ensure that lambda expression parameters in stream operations are not stateful.

**Note**: This example invokes the method synchronizedList so that the List parallelStorage is thread-safe. Remember that collections are not thread-safe. This means that multiple threads should not access a particular collection at the same time. Suppose that you do not invoke the method synchronizedList when creating parallelStorage:
```java
List<Integer> parallelStorage = new ArrayList<>();
```
The example behaves erratically because multiple threads access and modify parallelStorage without a mechanism like synchronization to schedule when a particular thread may access the List instance. Consequently, the example could print output similar to the following:
```java
Parallel stream:
8 7 6 5 4 3 2 1
null 3 5 4 7 8 1 2
```
