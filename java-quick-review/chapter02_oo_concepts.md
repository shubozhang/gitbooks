# Chapter II: OO Concept
  ### 1.Class and Object
  ### 2.Encapsulation
  ### 3.Abstraction
  ### 4.Inheritance
  ### 5.Polymorphism
  ### 6.Interface
  ### 7.static, final, and nested classes



## 1. Class and Object
* Class is a blueprint which describes a category of objects.
* Object is instance which is concrete.

### 1.1 Access modifier
Four types: private / default / protected / public
* private: is only accessible inside the class.
* default: is accessible inside the package.
* protected: is accessible inside the package and sub-classes outside the package.
* public, is accessible everywhere.

For class, only two modifiers public or default.

### 1.2 Constructor
```
public People(){}
```
* It has the same name as Class.
* No return type (You cannot put any return type, even for void)
* If you don't explicitly define any constructor, system will provide a default one (empty args). Otherwise, system will not provide any constructor. Note, when spring IOC injects an object, it requires object has an explicit empty constructor.
* If you do not want the class to be constructed outside, use private ``private People(){} ``


## 2. Encapsulation (hiding data)
Encapsulation is one of the four fundamental OOP concepts:
* **Encapsulation**: hiding data. Often referred to as "Information Hiding", revolves more specifically around internal data (fields / members representing the state) owned by a class instance, by enforcing access to the internal data in a controlled manner, and preventing direct, external change to these fields.
* **Abstraction**: occurs during class level design, with the objective of hiding the implementation complexity of how the the features offered by an API / design / system were implemented, in a sense simplifying the 'interface' to access the underlying implementation.
* **Inheritance**: refers to using the structure and behavior of a superclass in a subclass.
* **Polymorphism**: refers to changing the behavior of a superclass in the subclass.

Encapsulation is the technique of **making the fields in a class private** and **providing access to the fields via public methods**. If a field is declared private, it cannot be accessed by anyone outside the class, thereby hiding the fields within the class. For this reason, encapsulation is also referred to as data hiding.

Encapsulation can be described as a protective barrier that prevents the code and data being randomly accessed by other code defined outside the class. Access to the data and code is tightly controlled by an interface.

The main benefit of encapsulation is the ability to modify our implemented code without breaking the code of others who use our code. With this feature Encapsulation gives maintainability, flexibility and extensibility to our code.

```java
public class EncapTest{
private String name;
private String idNum;
private int age;

public int getAge(){
return age;
}
public String getName(){
return name;
}
public String getIdNum(){
return idNum;
}

//With setter(), you can put some defence conditions here. Otherwise, you lost the control
public void setAge( int newAge){
if (newAge <= 0) {
this.age = 0;
} else {
this.age = newAge;
}
}
public void setName(String newName){
this.name = newName;
}
public void setIdNum( String newId){
this.idNum = newId;
}
}
```


## 3. Abstraction
### 3.1 Abstract Class
* An abstract class is a class that is declared ``abstract`` —**it may or may not include abstract methods.**
* **Abstract classes cannot be instantiated, but they can be subclassed**.

### 3.2 Abstract Method
* An abstract method is a method that is declared without an implementation.
```java
public boolean validateDate(String date);
```
* When an abstract class is subclassed, the subclass usually provides implementations for all of the abstract methods in its parent class. However, if it does not, then the subclass must also be declared abstract.

**Note: Methods in an interface (see the Interfaces section) that are not declared as default or static are implicitly abstract, so the abstract modifier is not used with interface methods. (It can be used, but it is unnecessary.)**


### 3.3 static members
An abstract class may have static fields and static methods. You can use these static members with a class reference (for example, AbstractClass.staticMethod()) as you would with any other class.


### 3.4 Abstract Classes VS Interfaces

| Abstract Classes | Interfaces |
| --- | --- |
| No restriction on fields | All fields are ``public static final``|
| No restriction on methods | All methods are ``public`` |
| Only extends one class | Can **extend** multiple interfaces |
| Cannot be instantiated | Cannot be instantiated |


* **Consider using abstract classes if any of these statements apply to your situation**:
  * You want to share code among several closely related classes.
  * You expect that classes that extend your abstract class have many common methods or fields, or require access modifiers other than public (such as protected and private).
  * You want to declare non-static or non-final fields. This enables you to define methods that can access and modify the state of the object to which they belong.

* **Consider using interfaces if any of these statements apply to your situation**:
  * You expect that unrelated classes would implement your interface. For example, the interfaces ``Comparable`` and ``Cloneable`` are implemented by many unrelated classes.
  * You want to specify the behavior of a particular data type, but not concerned about who implements its behavior.
  * You want to take advantage of multiple inheritance of type.



## 4. Inheritance
### 4.1 Subclass and Superclass
* A class that is derived from another class is called a **subclass** (also a derived class, extended class, or child class).
* The class from which the subclass is derived is called a **superclass** (also a base class or a parent class).
* A subclass inherits all the members (fields, methods, and nested classes) from its superclass. Constructors are not members, so they are not inherited by subclasses, but the constructor of the superclass can be invoked from the subclass by ``super()``.

**What you can do in a subclass**
* The inherited fields can be used directly, just like any other fields.
* You can declare a field in the subclass with the same name as the one in the superclass, thus **hiding it** (not recommended).
* You can declare new fields in the subclass that are not in the superclass.
* The inherited methods can be used directly as they are.
* You can write a new instance method in the subclass that has the same signature as the one in the superclass, thus **overriding it**. If you still want to use the method implementation in superclass, you can use super to invoke it, like **super.methodA()**.
* You can write a new static method in the subclass that has the same signature as the one in the superclass, thus **hiding it**.
* You can declare new methods in the subclass that are not in the superclass.
* You can write a subclass constructor that invokes the constructor of the superclass, either implicitly or by using the keyword **super**.

### 4.2 Private members in Superclass
* A subclass does not inherit the private members of its parent class. However, if the superclass has public or protected methods for accessing its private fields, these can also be used by the subclass.

* A nested class has access to all the private members of its enclosing class—both fields and methods. Therefore, a public or protected nested class inherited by a subclass has indirect access to all of the private members of the superclass.


### 4.3 Overriding and Hiding Methods
* An instance method in a subclass with the same signature and return type as an instance method in the superclass overrides the superclass's method.
* If a subclass defines a static method with the same signature as a static method in the superclass, then the method in the subclass hides the one in the superclass.

* **Rule 1: instance field and static member are invoked based on declared type.**

* **Rule 2: instance method is invoked based on assigned type.**

* **Rule 3: subclass cannot define a more restricted access modifier on an override method than superclass's method.**

Example:
```
public class Animal {
private int i = 10;
public int j = 20;
public static int z = 30;

public static void testStatic() {
System.out.println("calling super static method");
}
public void testInstanceMethod() {
System.out.println("calling super instance method");
}
public void testAccess() {}
}

public class Cat extends Animal {

public int j = 50;
public static int z = 60;

public static void testStatic() {
System.out.println("calling sub static method");
}
public void testInstanceMethod() {
System.out.println("calling sub instance method");
}
// Rule 3: error
protected void testAccess(){}

public static void main(String[] args) {
Animal animal = new Animal();
Cat cat = new Cat();
Animal animalCat = cat;

System.out.println(animalCat.j); // Rule 1: call super.j 20
System.out.println(animalCat.z); // Rule 1: call super.z 30
animalCat.testInstanceMethod(); // Rule 2: call sub.testInstanceMethod()
animalCat.testStatic(); // Rule 1: call super.testStatic()
}
}
```

### 4.4 Hiding fields
Within a class, a field that has the same name as a field in the superclass hides the superclass's field, even if their types are different. Within the subclass, the field in the superclass cannot be referenced by its simple name. Instead, the field must be accessed through **super**, which is covered in the next section. Generally speaking, we don't recommend hiding fields as it makes code difficult to read.

### 4.5 Keyword: super
* Accessing Superclass Members
If your method overrides one of its superclass's methods, you can invoke the overridden method through the use of the keyword super. You can also use super to refer to a hidden field (although hiding fields is discouraged).

* Subclasses Constructors

Example:
```
public MountainBike(int startHeight,
int startCadence,
int startSpeed,
int startGear) {
super(startCadence, startSpeed, startGear);
seatHeight = startHeight;
}
```
**Rule: Invocation of a superclass constructor must be the first line in the subclass constructor.**

### 4.6 Object as a Superclass

The Object class, in the ``java.lang package``, sits at the top of the class hierarchy tree. Every class is a descendant, direct or indirect, of the Object class.

* protected Object clone() throws CloneNotSupportedException
Creates and returns a copy of this object.
* public boolean equals(Object obj)
Indicates whether some other object is "equal to" this one.
* protected void finalize() throws Throwable
Called by the garbage collector on an object when garbage
collection determines that there are no more references to the object
* public final Class getClass()
Returns the runtime class of an object.
* public int hashCode()
Returns a hash code value for the object.
* public String toString()
Returns a string representation of the object.

The following methods all play a part in synchronizing the activities of independently running threads in a program:
* public final void notify()
* public final void notifyAll()
* public final void wait()
* public final void wait(long timeout)
* public final void wait(long timeout, int nanos)


### 4.6.1 hashCode() Method

The value returned by hashCode() is the object's hash code, which is the object's memory address in hexadecimal.

**By definition, if two objects are equal, their hash code must also be equal.** If you override the equals() method, you change the way two objects are equated and Object's implementation of hashCode() is no longer valid. Therefore, if you override the equals() method, you must also override the hashCode() method as well.

### 4.6.2 equals() Method

The equals() method compares two objects for equality and returns true if they are equal. For two objects, **if you do not override equals() and hashCode() , they will never be equal**. Because super equals() compares the hashCode which is the memory address of object.

### 4.6.3 finalize() Method

The Object class provides a callback method, finalize(), that may be invoked on an object when it becomes garbage. Object's implementation of finalize() does nothing—you can override finalize() to do cleanup, such as freeing resources.

The finalize() method may be called automatically by the system, but when it is called, or even if it is called, is uncertain. Therefore, you should not rely on this method to do your cleanup for you. For example, if you don't close file descriptors in your code after performing I/O and you expect finalize() to close them for you, you may run out of file descriptors.


## 5. Polymorphism
### 5.1 Definition:
Polymprphism means many forms. Subclasses of a class can define their own unique behaviors and yet share some of the same functionality of the parent class.

Example:
```
public abstract class Human{
public abstract void goPee();
}
public class Male extends Human{
@Override
public void goPee(){
System.out.println("Stand Up");
}
}
public class Female extends Human{
@Override
public void goPee(){
System.out.println("Sit Down");
}
}
public static void main(String[] args){
ArrayList<Human> group = new ArrayList<Human>();
group.add(new Male());
group.add(new Female());
// ... add more...

// tell the class to take a pee break
for (Human person : group){ person.goPee(); }
}

//Running this would yield:
Stand Up
Sit Down
```

### 5.2 **Compile time polymorphism (static binding or method overloading)**
Method overloading, an object can have two or more methods with same name, BUT, with their method parameters different. If only return type is different for two methods, this is not allowed.
```
// Difference in parameter type
public static double Math.max(double a, double b){..}
public static float Math.max(float a, float b){..}
public static int Math.max(int a, int b){..}
public static long Math.max(long a, long b){..}

// Difference in parameter count
EmployeeFactory.create(String firstName, String lastName){...}
EmployeeFactory.create(Integer id, String firstName, String lastName){...}

//The following case is NOT allowed
public int test(int i) { return i;}
public void test(int i) { system.out.println(i)}
```


### 5.3 **Runtime polymorphism (dynamic binding or method overriding)**
Runtime polymorphism is essentially referred as method overriding. Method overriding is a feature which you get when you implement inheritance in your program.

*** Rule: Always invoked assigned object's instance method.**




## 6. Interface
### 6.1 Definition:
There are a number of situations in software engineering when it is important for disparate groups of programmers to agree to a "contract" that spells out how their software interacts. Each group should be able to write their code without any knowledge of how the other group's code is written. Generally speaking, interfaces are such contracts.

* Interface can contain only
* fields are constants (public static final)
* methods are public abstract, never final
* **default methods(have method body)** // in Java 8
* **static methods (have method body)** // in Java 8
* nested types
* Interfaces cannot be instantiated—they can only be **implemented** by classes or **extended** by other interfaces.

### 6.2 Default Methods (Java 8)
Default methods enable you to add new functionality to the interfaces of your libraries and ensure binary compatibility with code written for older versions of those interfaces.


### 6.3 Static Methods (Java 8)
You can define static methods in interfaces. (A static method is a method that is associated with the class in which it is defined rather than with any object. Every instance of the class shares its static methods.) This makes it easier for you to organize helper methods in your libraries; you can keep static methods specific to an interface in the same interface rather than in a separate class.


### 6.4 Implementation
* Defining an interface

```
public interface TimeClient {
//(public static final)
double E = 2.718282;
//abstract method: (public abstract)
void doSomething (int i, double x);
void setTime(int hour, int minute, int second);
void setDate(int day, int month, int year);
void setDateAndTime(int day, int month, int year,
int hour, int minute, int second);
LocalDateTime getLocalDateTime();
// static method (public): only in Java 8
static ZoneId getZoneId (String zoneString) {
try {
return ZoneId.of(zoneString);
} catch (DateTimeException e) {
System.err.println("Invalid time zone: " + zoneString +
"; using default time zone instead.");
return ZoneId.systemDefault();
}
}
// default method (public): only in Java 8
default ZonedDateTime getZonedDateTime(String zoneString) {
return ZonedDateTime.of(getLocalDateTime(), getZoneId(zoneString));
}
}
```

* Implementing an interface
```
public class TestSimpleTimeClient {
public static void main(String[] args) {
TimeClient myTimeClient = new SimpleTimeClient();
System.out.println("Current time: " + myTimeClient.toString());
System.out.println("Time in California: " +
myTimeClient.getZonedDateTime("Blah blah").toString());
}
}
```

* Using an Interface as a Type
If you define a reference variable whose type is an interface, any object you assign to it must be an instance of a class that implements the interface.



## 7. Static, Final, and Nested Classes
### 7.1 Static
7.1.1 Static Method
* You can only access class's static fields or methods, non-static fields and methods are in-accessible.
* **this / super** cannot be used in static method,because you don't need an instance to access static method.

7.1.2 Static Block
When a class is loaded, its static block will be executed **(only once)** before main method. Static block is usually used for class's initialization.

Example 1: Singleton
```
class Source {
private static Connection conn = new Connection();
private Source() {}
public static Connection getConnection() {
return conn;
}
}
```
Example 2: main method
```
// 1) JVM needs to invoke main mehtod, so the access modifier must be public.
// 2) JVM does not create an instace for the class, so it must be a static method.
// 3) argument is a String array.
public static void main(String[] args) {
}
```

### 7.2 Final
``final`` can be used on variable, method, and class
* On variable: **final fields or local variables** are constants, can only be assigned once. And final variables must have assigned value when they are declared or be assigned in constructor explicitly.
* On method: indicate that the method **cannot be overridden** by subclasses. The Object class does this—a number of its methods are final.
* On class: indicate that the class **cannot be subclassed**. This is particularly useful, for example, when creating an immutable class like the String class. Example: The wrapper classes for the primitive types:``java.lang.Integer ,java.lang.Byte ,java.lang.Character ,java.lang.Short ,java.lang.Boolean ,java.lang.Long ,java.lang.Double ,java.lang.Float ``


**You might wish to make a method final if it has an implementation that should not be changed** and it is critical to the consistent state of the object. For example, you might want to make the getFirstPlayer method in this ChessAlgorithm class final:
```
class ChessAlgorithm {
enum ChessPlayer { WHITE, BLACK }
final ChessPlayer getFirstPlayer() { return ChessPlayer.WHITE; }
}
```
**Methods called from constructors should generally be declared final. If a constructor calls a non-final method, a subclass may redefine that method with surprising or undesirable results.**


### 7.3 Nested Classes (one implementation of encapsulation)
7.3.1 Definition: A nested class is a class which is defined within another class.

* Nestes classes
* static nested classes
* non-static nested classes (inner classes)
* local classes
* anonymous classes
* Non-static nested classes (inner classes) have access to other members of the enclosing class, even if they are declared private.
* **Its name must be different from the class's name.**
* **Its members cannot be declared as static.**

* Static nested classes do not have access to other members of the enclosing class.
* **It can use them only through an object reference.**

As a member of the OuterClass, a nested class can be declared private, public, protected, or package private. (Recall that outer classes can only be declared public or package private)
```
class OuterClass {
static class StaticNestedClass { }
class InnerClass { }
}
OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```

There are two special kinds of inner classes: local classes and anonymous classes.
* Local classes: declare an inner class within the body of a method.
* Annonymous classes: declare an inner class within the body of a method without naming the class.


7.3.2 When to Use Nested Classes, Local Classes, Anonymous Classes, and Lambda Expressions

* Nested Classes enable you to logically group classes that are only used in one place, increase the use of encapsulation, and create more readable and maintainable code.

* Local classes, anonymous classes, and lambda expressions also impart these advantages; however, they are intended to be used for more specific situations:

* Local class: Use it if you need to create more than one instance of a class, access its constructor, or introduce a new, named type (because, for example, you need to invoke additional methods later).

* Anonymous class: Use it if you need to declare fields or additional methods.

* Lambda expression: Use it if you are encapsulating a single unit of behavior that you want to pass to other code. For example, you would use a lambda expression if you want a certain action performed on each element of a collection, when a process is completed, or when a process encounters an error. Use it if you need a simple instance of a functional interface and none of the preceding criteria apply (for example, you do not need a constructor, a named type, fields, or additional methods).

* Nested class: Use it if your requirements are similar to those of a local class, you want to make the type more widely available, and you don't require access to local variables or method parameters.

* Use a non-static nested class (or inner class) if you require access to an enclosing instance's all fields and methods.

* Use a static nested class if you don't require this access.
