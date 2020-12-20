#
Chapter III: Exception

## 1.1 Exception
An exception is an event that occurs during the execution of a program that disrupts the normal flow of instructions. Two categories:

* **Checked Exception(compile time exception)**: Applications are expected to be able to catch and meaningfully do something with the rest, such as ``FileNotFoundException`` and ``TimeoutException``. Compiler forces client handle this exception ( either catch the exception or declare it in a throws clause.)

* **Unchecked Exception**:
* **RuntimeException**: Runtime exceptions are those exceptions that are thrown at runtime because of either wrong input data or because of wrong business logic etc. These are not checked by the compiler at compile time.

* **Error**: It is mostly thrown by JVM which is fatal and **there is no way for the application to recover from that error**. For instance: ``OutOfMemoryError``. **Errors should not be caught or handled.**


## 1.2 Exception Examples
* ``IOException`` (checked)
* ``FileNotFoundException``
* ``EOFException``
* ``FileSystemException``

* ``RuntimeException`` (unchecked)
* ``NullPointerException``
* ``IndexOutOfBoundsException``
* ``ArithmeticException``



## 1.3 Handle Exception
Two ways:
* try / catch / finally
* throws

1.3.1 try / catch / finally
* You may have one try, one finally, and multiple catches. Note: exception2 must be exception1's superclass.
* finally block always be executed even try have a return clause.
* For an overridded method in subclass, its defined catch exception must be same or a derived exception to the exception in superclass.
* If the exception from try block is caught by catch block, it looks like catch block eats the exception and continue to run the rest of code. If not, the app will be interrupted and stopped. The rest of code won't be executed.
* The finally block may not execute if
* System.exit() is called. So the JVM exits while the try or catch code is being executed.
* JVM crashes. Likewise, if the thread executing the try or catch code is interrupted or killed, the finally block may not execute even though the application as a whole continues.
* The try{} block never ends.
```
try {}
catch(e1){}
catch(e2){}
.
.
catch(en){}
finally{}
```

1.3.2 Throws
```
public class A {
public void methodA() throws IOException {}
}

public class B1 extends A {
public void methodA() throws FileNotFoundException {} // this exception has to be IOException or its subclasses
}

public class B2 extends A {
public void methodA() throws Exception { //error, because Exception is the superclass of IOException}
}
```


## 1.4 Customize Exception

```
class MyException extends Exception {
private int idnumber;
public MyException(String message, int id) {
super(message);
this.idnumber = id;
}
public int getId() {
return idnumber;
}
}


public class Test{
public void regist(int num) throws MyException {
if (num < 0) {
throw new MyException(“num cannot be negative”,3);
}
else {
System.out.println("Regitster: " + num );
}
}
public void manager() {
try {
regist(100);
} catch (MyException e) {
System.out.print("Register error: "+e.getId());
}
System.out.print("Register ended");
}
public static void main(String args[]){
Test test = new Test();
t.manager();
}
}
```

##2. The try-with-resources statement
The try-with-resources statement is a try statement that declares **one or more resources**. A resource is an object that must be closed after the program is finished with it. The try-with-resources statement ensures that each resource is closed at the end of the statement. Any object that implements ``java.lang.AutoCloseable``, which includes all objects which implement ``java.io.Closeable``, can be used as a resource.

###2.1 Example
Reads the first line from a file. It uses an instance of BufferedReader to read data from the file. BufferedReader is a resource that must be closed after the program is finished with it:

```java
// example 1:
static String readFirstLineFromFile(String path) throws IOException {
try (BufferedReader br = new BufferedReader(new FileReader(path))) {
return br.readLine();
}
}

// example 2:
public static void viewTable(Connection con) throws SQLException {
String query = "select COF_NAME, SUP_ID, PRICE, SALES, TOTAL from COFFEES";

try (Statement stmt = con.createStatement()) {
ResultSet rs = stmt.executeQuery(query);

while (rs.next()) {
String coffeeName = rs.getString("COF_NAME");
int supplierID = rs.getInt("SUP_ID");
float price = rs.getFloat("PRICE");
int sales = rs.getInt("SALES");
int total = rs.getInt("TOTAL");
}
} catch (SQLException e) {
JDBCTutorialUtilities.printSQLException(e);
}
}

```
The class BufferedReader, in Java SE 7 and later, implements the interface ``java.lang.AutoCloseable``. Because the BufferedReader instance is declared in a try-with-resource statement, it will be closed regardless of whether the try statement completes normally or abruptly.

###2.2 try-with-resources VS finally{}
You can use a finally block to ensure that a resource is closed regardless of whether the try statement completes normally or abruptly.

```java
static String readFirstLineFromFileWithFinallyBlock(String path) throws IOException {
BufferedReader br = new BufferedReader(new FileReader(path));
try {
return br.readLine(); // suppressed
} finally {
if (br != null) br.close(); // throws exception
}
}
```
In this example, if the methods ``readLine()`` and ``close()`` both throw exceptions, then the method ``readFirstLineFromFileWithFinallyBlock()`` **throws the exception thrown from the finally block; the exception thrown from the try block is suppressed**.

In contrast, in the example ``readFirstLineFromFile()``, if exceptions are thrown from both the try block and the try-with-resources statement, then the method readFirstLineFromFile **throws the exception thrown from the try block; the exception thrown from the try-with-resources block is suppressed**.

###2.3 Multi try-with-resources
You may declare one or more resources in a try-with-resources statement.

```java
public static void writeToFileZipFileContents(String zipFileName,String outputFileName) throws java.io.IOException {

java.nio.charset.Charset charset = java.nio.charset.StandardCharsets.US_ASCII;
java.nio.file.Path outputFilePath = java.nio.file.Paths.get(outputFileName);

// Open zip file and create output file with
// try-with-resources statement

try (
java.util.zip.ZipFile zf = new java.util.zip.ZipFile(zipFileName);
java.io.BufferedWriter writer = java.nio.file.Files.newBufferedWriter(outputFilePath, charset)
) {
// Enumerate each entry
for (java.util.Enumeration entries = zf.entries(); entries.hasMoreElements();) {
// Get the entry name and write it to the output file
String newLine = System.getProperty("line.separator");
String zipEntryName = ((java.util.zip.ZipEntry)entries.nextElement()).getName() + newLine;
writer.write(zipEntryName, 0, zipEntryName.length());
}
}
}
```
When the block of code that directly follows it terminates, either normally or because of an exception, the close methods of the BufferedWriter and ZipFile objects are automatically called in this order. Note that the close methods of resources are called in the opposite order of their creation.

**Note**: A try-with-resources statement can have catch and finally blocks just like an ordinary try statement. **In a try-with-resources statement, any catch or finally block is run after the resources declared have been closed**.

###2.4 Suppressed Exceptions

An exception can be thrown from the block of code associated with the try-with-resources statement. In the example writeToFileZipFileContents, an exception can be thrown from the try block, and up to two exceptions can be thrown from the try-with-resources statement when it tries to close the ZipFile and BufferedWriter objects. If an exception is thrown from the try block and one or more exceptions are thrown from the try-with-resources statement, then those exceptions thrown from the try-with-resources statement are suppressed, and the exception thrown by the block is the one that is thrown by the writeToFileZipFileContents method. You can retrieve these suppressed exceptions by calling the Throwable.getSuppressed method from the exception thrown by the try block.

###2.5 Classes That Implement the AutoCloseable or Closeable Interface

See the Javadoc of the AutoCloseable and Closeable interfaces for a list of classes that implement either of these interfaces. The Closeable interface extends the AutoCloseable interface. The close method of the Closeable interface throws exceptions of type IOException while the close method of the AutoCloseable interface throws exceptions of type Exception. Consequently, subclasses of the AutoCloseable interface can override this behavior of the close method to throw specialized exceptions, such as IOException, or no exception at all.


##3 Advantages of Exceptions
###3.1 Advantage 1: Separating Error-Handling Code from "Regular" Code

Exceptions provide the means to separate the details of what to do when something out of the ordinary happens from the main logic of a program. In traditional programming, error detection, reporting, and handling often lead to confusing spaghetti code. For example, consider the pseudocode method here that reads an entire file into memory.

```java
readFile {
open the file;
determine its size;
allocate that much memory;
read the file into memory;
close the file;
}
```
At first glance, this function seems simple enough, but it ignores all the following potential errors.

* What happens if the file can't be opened?
* What happens if the length of the file can't be determined?
* What happens if enough memory can't be allocated?
* What happens if the read fails?
* What happens if the file can't be closed?

To handle such cases, the ``readFile()`` function must have more code to do error detection, reporting, and handling. Here is an example of what the function might look like.

```java
errorCodeType readFile {
initialize errorCode = 0;
open the file;
if (theFileIsOpen) {
determine the length of the file;
if (gotTheFileLength) {
allocate that much memory;
if (gotEnoughMemory) {
read the file into memory;
if (readFailed) {
errorCode = -1;
}
} else {
errorCode = -2;
}
} else {
errorCode = -3;
}
close the file;
if (theFileDidntClose && errorCode == 0) {
errorCode = -4;
} else {
errorCode = errorCode and -4;
}
} else {
errorCode = -5;
}
return errorCode;
}
```

Exceptions enable you to write the main flow of your code and to deal with the exceptional cases elsewhere. If the ``readFile()`` function used exceptions instead of traditional error-management techniques, it would look more like the following.

```java
readFile {
try {
open the file;
determine its size;
allocate that much memory;
read the file into memory;
close the file;
} catch (fileOpenFailed) {
doSomething;
} catch (sizeDeterminationFailed) {
doSomething;
} catch (memoryAllocationFailed) {
doSomething;
} catch (readFailed) {
doSomething;
} catch (fileCloseFailed) {
doSomething;
}
}
```
Note that exceptions don't spare you the effort of doing the work of detecting, reporting, and handling errors, but they do help you organize the work more effectively.

###3.2 Advantage 2: Propagating Errors Up the Call Stack

A second advantage of exceptions is the ability to propagate error reporting up the call stack of methods. Suppose that the ``readFile()`` method is the fourth method in a series of nested method calls made by the main program: method1 calls method2, which calls method3, which finally calls readFile.

```java
method1 {
call method2;
}

method2 {
call method3;
}

method3 {
call readFile;
}
```
Suppose also that method1 is the only method interested in the errors that might occur within readFile. Traditional error-notification techniques force method2 and method3 to propagate the error codes returned by readFile up the call stack until the error codes finally reach method1—the only method that is interested in them.

```java
method1 {
errorCodeType error;
error = call method2;
if (error)
doErrorProcessing;
else
proceed;
}

errorCodeType method2 {
errorCodeType error;
error = call method3;
if (error)
return error;
else
proceed;
}

errorCodeType method3 {
errorCodeType error;
error = call readFile;
if (error)
return error;
else
proceed;
}
```
Recall that the Java runtime environment searches backward through the call stack to find any methods that are interested in handling a particular exception. A method can duck any exceptions thrown within it, thereby allowing a method farther up the call stack to catch it. Hence, only the methods that care about errors have to worry about detecting errors.

```java
method1 {
try {
call method2;
} catch (exception e) {
doErrorProcessing;
}
}

method2 throws exception {
call method3;
}

method3 throws exception {
call readFile;
}
```
However, as the pseudocode shows, ducking an exception requires some effort on the part of the middleman methods. Any checked exceptions that can be thrown within a method must be specified in its throws clause.

###3.3 Advantage 3: Grouping and Differentiating Error Types

Because all exceptions thrown within a program are objects, the grouping or categorizing of exceptions is a natural outcome of the class hierarchy. An example of a group of related exception classes in the Java platform are those defined in java.io — IOException and its descendants. IOException is the most general and represents any type of error that can occur when performing I/O. Its descendants represent more specific errors. For example, FileNotFoundException means that a file could not be located on disk.

A method can write specific handlers that can handle a very specific exception. The FileNotFoundException class has no descendants, so the following handler can handle only one type of exception.

```java
catch (FileNotFoundException e) {
...
}
```
A method can catch an exception based on its group or general type by specifying any of the exception's superclasses in the catch statement. For example, to catch all I/O exceptions, regardless of their specific type, an exception handler specifies an IOException argument.

```java
catch (IOException e) {
...
}
```
This handler will be able to catch all I/O exceptions, including FileNotFoundException, EOFException, and so on. You can find details about what occurred by querying the argument passed to the exception handler. For example, use the following to print the stack trace.

```java
catch (IOException e) {
// Output goes to System.err.
e.printStackTrace();
// Send trace to stdout.
e.printStackTrace(System.out);
}
```
You could even set up an exception handler that handles any Exception with the handler here.

```java
// A (too) general exception handler
catch (Exception e) {
...
}
```
The Exception class is close to the top of the Throwable class hierarchy. Therefore, this handler will catch many other exceptions in addition to those that the handler is intended to catch. You may want to handle exceptions this way if all you want your program to do, for example, is print out an error message for the user and then exit.

In most situations, however, you want exception handlers to be as specific as possible. The reason is that the first thing a handler must do is determine what type of exception occurred before it can decide on the best recovery strategy. In effect, by not catching specific errors, the handler must accommodate any possibility. Exception handlers that are too general can make code more error-prone by catching and handling exceptions that weren't anticipated by the programmer and for which the handler was not intended.

As noted, you can create groups of exceptions and handle exceptions in a general fashion, or you can use the specific exception type to differentiate exceptions and handle exceptions in an exact fashion.

## 4. Best Practice for Exception
### 4.1 implement a re-try-catch
Ex1:
```
int count = 0;
int maxTries = 3;
while(true) {
try {
// Some Code
// break out of loop, or return, on success
} catch (SomeException e) {
// handle exception
if (++count == maxTries) { throw e;}
}
}
```

Ex2:
```
//You can use AOP and Java annotations from jcabi-aspects
@RetryOnFailure(attempts = 3, delay = 5)
public String load(URL url) {
return url.openConnection().getContent();
}
```


### 4.2 Always add log in catch block

Example one:
```java
public void consumeAndForgetAllExceptions(){
try {
...some code that throws exceptions
} catch (Exception ex){
ex.printStacktrace();
}
}
```
What is wrong with the code above?
Once an exception is thrown, normal program execution is suspended and control is transferred to the catch block. The catch block catches the exception and just suppresses it. Execution of the program continues after the catch block, as if nothing had happened.

Example two:
```java
public void someMethod() throws Exception{
}
```
This method is a blank one; it does not have any code in it. How can a blank method throw exceptions? Java does not stop you from doing this. Recently, I came across similar code where the method was declared to throw exceptions, but there was no code that actually generated that exception. When I asked the programmer, he replied "I know, it is corrupting the API, but I am used to doing it and it works."


Broadly speaking, there are three different situations that cause exceptions to be thrown:
* Exceptions due to programming errors(**Runtime or unchecked exception**): In this category, exceptions are generated due to programming errors (e.g., ``NullPointerException`` and ``IllegalArgumentException``). The client code usually cannot do anything about programming errors.

* Exceptions due to client code errors (**checked exception**): Client code attempts something not allowed by the API, and thereby violates its contract. The client can take some alternative course of action, if there is useful information provided in the exception. For example: an exception is thrown while parsing an XML document that is not well-formed. The exception contains useful information about the location in the XML document that causes the problem. The client can use this information to take recovery steps.


* Exceptions due to resource failures: Exceptions that get generated when resources fail. For example: the system runs out of memory or a network connection fails. The client's response to resource failures is context-driven. The client can retry the operation after some time or just log the resource failure and bring the application to a halt.
Types of Exceptions in Java

Java defines two kinds of exceptions:
* Checked exceptions: Exceptions that inherit from the Exception class are checked exceptions. It occurs at compile time. Client code has to handle the checked exceptions thrown by the API, either in a catch clause or by forwarding it outward with the throws clause.

* Unchecked exceptions: RuntimeException also extends from Exception. However, all of the exceptions that inherit from RuntimeException get special treatment. There is no requirement for the client code to deal with them, and hence they are called unchecked exceptions.

* The difference between throws and throw keywords, throws is used to postpone the handling of a checked exception and throw is used to invoke an exception explicitly.

A checked exception thrown by a lower layer is a forced contract on the invoking layer to catch or throw it. The checked exception contract between the API and its client soon changes into an unwanted burden if the client code is unable to deal with the exception effectively. Programmers of the client code may start taking shortcuts by suppressing the exception in an empty catch block or just throwing it and, in effect, placing the burden on the client's invoker.
Checked exceptions are also accused of breaking encapsulation. Consider the following:
```
public List getAllAccounts() throws
FileNotFoundException, SQLException{
...
}
```
The method getAllAccounts() throws two checked exceptions. The client of this method has to explicitly deal with the implementation-specific exceptions, even if it has no idea what file or database call has failed within getAllAccounts(), or has no business providing filesystem or database logic. Thus, the exception handling forces an inappropriately tight coupling between the method and its callers.
