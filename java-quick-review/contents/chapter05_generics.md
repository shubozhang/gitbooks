#
Chapter V: Generics

## 1. Why Generics

Before JDK 1.5, compiler can only guarantee return type is object. To return an Integer, you have to use casting. So it is easy to generate runtime error \(`ClassCastException`\) consequence.

```java
List myIntList = new ArrayList();// line 1
myIntList.add(new Integer(0));// line 2
Integer x = (Integer) myIntList.iterator().next();// cast
```

Generics is designed to solve above drawback. It can restrict the data type of a collection at compile time.

```
List<Integer> myIntList = new ArrayList<Integer>(); // Line 1
myIntList.add(new Integer(0)); // Line 2
Integer x = myIntList.iterator().next(); // no cast
```

* Complied classes do not have &lt;&gt;, in order to be compatible with class loader.
* By using generics, programmers can implement generic algorithms that work on collections of different types, can be customized, and are type safe and easier to read.

## 2. Wildcards

### 2.1 `?` wildcard

```java
public static void printCollection(Collection<?> c) {
Iterator<?> itr = c.iterator();
while (itr.hasNext()) {
System.out.println(itr.next());
}
}
```

### 2.2 Upper Bounded Wildcards: `? extends E`

`E` is the upper bound of wildcard `?`

```java
// ? extends E : accept E and E's son
public static void test2() {
ArrayList<Person> al = new ArrayList<Person>();
al.add(new Person("abc",30));
al.add(new Person("abc4",34));

ArrayList<Student> al2 = new ArrayList<Student>();
al2.add(new Student("stu1",11));
al2.add(new Student("stu2",22));

ArrayList<Worker> al3 = new ArrayList<Worker>();
al3.add(new Worker("work1",30));
al3.add(new Worker("work2",34));

printCollection(al);
printCollection(al2);
printCollection(al3);
}

public static void printCollection(Collection<? extends Person> al) {
Iterator<? extends Person> it = al.iterator();
while(it.hasNext()) {
System.out.println(it.next());
}
}
```

### 2.3 Lower Bounded Wildcard: `? super E`

`E` is the lower bound of wildcard `?`

```java
// ? super E: accept E and E's father. Note: Does not accept father's other son.
public static void test3() {
ArrayList<Person> al = new ArrayList<Person>();
al.add(new Person("person_print3",30));
al.add(new Person("person_print3",34));

ArrayList<Student> al2 = new ArrayList<Student>();
al2.add(new Student("student_print3",11));
al2.add(new Student("student_print3",22));

printCollection(al);
printCollection(al2);
}

// this printCollection does not accept Worker object
public static void printCollection(Collection<? super Student> al) {
Iterator<? super Student> it = al.iterator();
while(it.hasNext()){
System.out.println(it.next());
}
}
```

* ? extends E: it is usually used to add E and E's sub-classes into collection.

* ? super E: it is usually used to get E and E's super-classes from collection.



