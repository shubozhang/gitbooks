#
Best Practices in Implementation

Best Practices for Designing the API

1. When deciding on checked exceptions vs. unchecked exceptions, ask yourself, "What action can the client code take when the exception occurs?"
If the client can take some alternate action to recover from the exception, make it a checked exception. If the client cannot do anything useful, then make the exception unchecked. By useful, I mean taking steps to recover from the exception and not just logging the exception.

To summarize:
Client's reaction when exception happens
Exception type
Client code cannot do anything
Make it an unchecked exception
Client code will take some useful recovery action based on information in exception
Make it a checked exception

Moreover, prefer unchecked exceptions for all programming errors: unchecked exceptions have the benefit of not forcing the client API to explicitly deal with them. They propagate to where you want to catch them, or they go all the way out and get reported. The Java API has many unchecked exceptions, such as NullPointerException, IllegalArgumentException, and IllegalStateException. I prefer working with standard exceptions provided in Java rather than creating my own. They make my code easy to understand and avoid increasing the memory footprint of code.

1. Preserve encapsulation \(for checked exception\).
Never let implementation-specific checked exceptions escalate to the higher layers. For example, do not propagate SQLException from data access code to the business objects layer. Business objects layer do not need to know about SQLException. You have two options:
Convert SQLException into another checked exception, if the client code is expected to recuperate from the exception.
Convert SQLException into an unchecked exception, if the client code cannot do anything about it.
Most of the time, client code cannot do anything about SQLExceptions. Do not hesitate to convert them into unchecked exceptions. Consider the following piece of code:
public void dataAccessCode\(\){
try{
```
..some code that throws SQLException
```

}catch\(SQLException ex\){
```
ex.printStacktrace();
```

}
}

This catch block just suppresses the exception and does nothing. The justification is that there is nothing my client could do about an SQLException. How about dealing with it in the following manner?

```
public void dataAccessCode(){
try{
..some code that throws SQLException
}catch(SQLException ex){
throw new RuntimeException(ex);
}
}
```

This converts SQLException to RuntimeException. If SQLException occurs, the catch clause throws a new RuntimeException. The execution thread is suspended and the exception gets reported. However, I am not corrupting my business object layer with unnecessary exception handling, especially since it cannot do anything about an SQLException. If my catch needs the root exception cause, I can make use of the getCause\(\) method.

If you are confident that the business layer can take some recovery action when SQLException occurs, you can convert it into a more meaningful checked exception. But I have found that just throwing RuntimeExceptionsuffices most of the time

1. Try not to create new custom exceptions if they do not have useful information for client code.
What is wrong with following code?
```
public class DuplicateUsernameException
extends Exception {}
```

It is not giving any useful information to the client code, other than an indicative exception name. Do not forget that Java Exception classes are like other classes, wherein you can add methods that you think the client code will invoke to get more information.
We could add useful methods to DuplicateUsernameException, such as:

```
public class DuplicateUsernameException
extends Exception {
public DuplicateUsernameException
(String username){....}
public String requestedUsername(){...}
public String[] availableNames(){...}
}
```

The new version provides two useful methods: requestedUsername\(\), which returns the requested name, and availableNames\(\), which returns an array of available usernames similar to the one requested. The client could use these methods to inform that the requested username is not available and that other usernames are available. But if you are not going to add extra information, then just throw a standard exception:

throw new Exception\("Username already taken"\);

Even better, if you think the client code is not going to take any action other than logging if the username is already taken, throw a unchecked exception:

throw new RuntimeException\("Username already taken"\);

Alternatively, you can even provide a method that checks if the username is already taken.
It is worth repeating that checked exceptions are to be used in situations where the client API can take some productive action based on the information in the exception. Prefer unchecked exceptions for all programmatic errors. They make your code more readable.

1. Document exceptions.
You can use Javadoc's @throws tag to document both checked and unchecked exceptions that your API throws. However, I prefer to write unit tests to document exceptions. Tests allow me to see the exceptions in action and hence serve as documentation that can be executed. Whatever you do, have some way by which the client code can learn of the exceptions that your API throws. Here is a sample unit test that tests forIndexOutOfBoundsException:

public void testIndexOutOfBoundsException\(\) {
ArrayList blankList = new ArrayList\(\);
try {
blankList.get\(10\);
fail\("Should raise an IndexOutOfBoundsException"\);
} catch \(IndexOutOfBoundsException success\) {}
}

The code above should throw an IndexOutOfBoundsException when blankList.get\(10\) is invoked. If it does not, the fail\("Should raise an IndexOutOfBoundsException"\) statement explicitly fails the test. By writing unit tests for exceptions, you not only document how the exceptions work, but also make your code robust by testing for exceptional scenarios.
Best Practices for Using Exceptions

The next set of best practices show how the client code should deal with an API that throws checked exceptions.

1. Always clean up after yourself
If you are using resources like database connections or network connections, make sure you clean them up. If the API you are invoking uses only unchecked exceptions, you should still clean up resources after use, with try -finally blocks.

```
public void dataAccessCode(){
Connection conn = null;
try{
conn = getConnection();
..some code that throws SQLException
}catch(SQLException ex){
ex.printStacktrace();
} finally{
DBUtil.closeConnection(conn);
}
}

class DBUtil{
public static void closeConnection
(Connection conn){
try{
conn.close();
} catch(SQLException ex){
logger.error("Cannot close connection");
throw new RuntimeException(ex);
}
}
}
```

DBUtil is a utility class that closes the Connection. The important point is the use of finally block, which executes whether or not an exception is caught. In this example, the finally closes the connection and throws aRuntimeException if there is problem with closing the connection.

1. Never use exceptions for flow control
Generating stack traces is expensive and the value of a stack trace is in debugging. In a flow-control situation, the stack trace would be ignored, since the client just wants to know how to proceed.
In the code below, a custom exception, MaximumCountReachedException, is used to control the flow.

```
public void useExceptionsForFlowControl() {
try {
while (true) {
increaseCount();
}
} catch (MaximumCountReachedException ex) {
}
//Continue execution
}

public void increaseCount()
throws MaximumCountReachedException {
if (count >= 5000)
throw new MaximumCountReachedException();
}
```

The useExceptionsForFlowControl\(\) uses an infinite loop to increase the count until the exception is thrown. This not only makes the code difficult to read, but also makes it slower. Use exception handling only in exceptional situations.

1. Do not suppress or ignore exceptions
When a method from an API throws a checked exception, it is trying to tell you that you should take some counter action. If the checked exception does not make sense to you, do not hesitate to convert it into an unchecked exception and throw it again, but do not ignore it by catching it with {} and then continue as if nothing had happened.

1. Do not catch top-level exceptions
Unchecked exceptions inherit from the RuntimeException class, which in turn inherits from Exception. By catching the Exception class, you are also catching RuntimeException as in the following code:

```
try{
..
}catch(Exception ex){
}
```

The code above ignores unchecked exceptions, as well.

1. Always Log exceptions\(just once\)
Logging the same exception stack trace more than once can confuse the programmer examining the stack trace about the original source of exception. So just log it once.

## \#

file path use "/" instead of "\", so the project becomes plastform independent.







**Java Clean Code Practices:**

**1\) Use of descriptive and meaningful variable**,**method and class names**as opposed to relying too much on comments. E.g. calculateTax\(BigDecimal amount\), UserDAOImpl.java, etc.

**Bad**: List list;

**Good**: List&lt;String&gt; accounts;

**2\) Class and functions should be small and focus on doing one thing.Do not duplicate the code. **

**Example:** StudentDao.java for data access logic only, Customer.java for model/entity object, StudentService.java for business logic, and CustomerValidator.java for validating input fields, etc.

Similarly, separate functions like calculateSalary\(Long employeeId\) will invoke other sub functions with meaningful names like

calculateBonus\(Long employeeId\), 
calculateLeaves\(Long employeeId\), etc

**3\) Methods should not take too many parameters.**

**Bad**: processOrder\(String id, String name, String address, BigDecimal price, int quantity, BigDecimal discount\);

**Good**: processOrder\(CustomerDetail customer, OrderDetail order\);

where CustomerDetail is a value object with attributes like code, name etc.

**4\) Use a standard code formatting template.**

Share the template across the development team.

**5\) **D**eclare the variables with the lowest possible scope.**

**For example**, if a variable “temp” is used only inside a loop, then declare it inside the loop, and not outside.

**6\) Don’t create variables that you don’t use again.**

**Example:** instead of Customer customer = repository.save\(customer\); 

return customer;

**Use: **return repository.save\(customer\);

**7\) Delete needless and commented out code. **

You have source control for the history. 

**8\) Do not use System.out.println statements.**

Use proper logging frameworks like slf4j and logback for logging.

**9\) Make a class final and the object immutable where possible.**


Immutable classes are thread-safe and more secured. 

**For example**, the Java String class is immutable and declared as final.

**10\) Minimize the accessibility of the packages, classes and its members like methods and variables.
**

**Example:**private, protected, default, and public access modifiers.

Code to interface as opposed to implementation.

**Bad**: ArrayList&lt;String&gt; names = new ArrayList&lt;String&gt;\(\);

**Good**: List&lt;String&gt; names = new ArrayList&lt;String&gt;\(\);


**11\) Use right data types.**

For example, use BigDecimal instead of floating point variables like float or double for monetary values. Use enums instead of int constants.

**12\) Avoid finalizers and properly override equals, hashCode, and toString methods.**

The equals and hashCode contract must be correctly implemented to prevent hard to debug defects.

**13\) Write fail-fast code by validating the input parameters.Apply design by contract.**

Return an empty collection or throw an exception as opposed to returning a null to avoid nullpointer exceptions which are hard to debug. 

**14\) Security Practices:**

Don’t log sensitive data.

Clearly document security related information.

Validate user inputs.

Favor immutable objects.

Security to prevent SQL injection attack.

Release resources \(Streams, Connections, etc\).Security to prevent denial of service attack \(DoS\) and resource leak issues.

Don’t let sensitive information like file paths, server names, host names, etc escape via exceptions.

Follow proper security best practices like SSL \(one-way, two-way, etc\), encrypting sensitive data, authentication/authorization, etc.

**15\) Exception Handling**

Use exceptions as opposed to return codes.

Don’t ignore or suppress exceptions. Standardize the use of checked and unchecked exceptions. Throw exceptions early and catch them late.

**17\) Concurrency**

Write thread-safe code with proper synchronization and use of immutable objects. Also, document thread-safety.

Keep synchronization section small and favor the use of the new concurrency libraries to prevent excessive synchronization.

**18\) Performance**

Reuse objects via flyweight design pattern.

Badly constructed SQL, REGEX, etc.

Inefficient Java coding and algorithms in frequently executed methods leading to death by thousand cuts.

