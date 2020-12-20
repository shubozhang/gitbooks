#
Best Practices and Guidance for Unit Testing

###1. Unit Testing General Rules
```
1. Should not depend on any code or source outside the unit tested. Where there are dependencies they should be replaced by false objects, Mocks, and stubs.
2. Should not require manual intervention.
3. Should not manipulate real data on any environment.
4. Test cases should be automatically be re-runnable in the exact same way.
5. Do not skip unit tests:
* Do not use JUnit's @Ignore annotation.
* Do not use Maven's maven.test.skip property.
6. Test coverage should be at least 80%.
```


###2 Naming Convention

####2.1 Maven standard directory layout:

| Directory | Description |
| -- | -- |
| src/main/java | Application/Library sources |
| src/main/resources | Application/Library resources |
| src/test/java | Test sources |
| src/test/resources | Test resources |
| src/it/java | Integration test sources |
| src/it/resources | Integration test resources |


####2.2 Test case names

```
// source class
public class org.finra.app.Foo {
public void createOrder() {}
}

// Test class
public class org.finra.app.FooTest{
@Test
public void testCreateOrder() {}
}
```


###3. Implementation Practices
####3.1 Do not initialize in a unit test class constructor
Use of @Before, @After – @Before and @After are annotations that can be use to tag a method if you want it to be called upon initialization and destruction of test object respectively.
```
@Before
public void setData(){
this.totalNumberOfApplicants = 9;
listOfValidStrings.add("object_1");
listOfValidStrings.add("object_2");
listOfValidStrings.add("object_3");
}
@After // tearDown()
public void after() throws Exception {
dummyAccount = null;
assertNull(dummyAccount);
}
```

####3.2 Use the most appropriate assertion methods
JUnit has many assertion methods:
* assertEquals
* assertTrue
* assertFalse
* assertNull
* assertNotNull
* assertArrayEquals
* assertSame

```
// Good
assertTrue(classUnderTest.methodUnderTest())
// Inappropriate
assertEquals(true, classUnderTest.methodUnderTest()).

// Good
assertEquals(expectedReturnValue, classUnderTest.methodUnderTest())
// Inappropriate
assertTrue(classUnderTest.methodUnderTest().equals(expectedReturnValue)).

// Good
assertEquals(expectedCollection, classUnderTest.getCollection())
// Rather than asserting on the collection's size and each of the collection's members.
```

####3.3 Do not use System.out.println() to verify test case

####3.4 Do not unit-test configuration settings
You may test it in integration testing.


####3.5 Mock out all external services and state
* Benefits: reduced code dependency and faster tests execution.

* Mocks are prerequisites for fast execution of tests and ability to concentrate on a single unit of functionality.

* Otherwise, behavior in those external services overlaps multiple tests, and state data means that different unit tests can influence each other’s outcome.


###4. JUnit Quick Guide
You may learn more at https://github.com/junit-team/junit4/wiki

####4.1 Assertions
```
@Test
public void testAssertArrayEquals() {

byte[] expected = "trial".getBytes();
byte[] actual = "trial".getBytes();
org.junit.Assert.assertArrayEquals("failure - byte arrays not same", expected, actual);
}

@Test
public void testAssertEquals() {
assertEquals("failure - strings are not equal", "text", "text");
}

@Test
public void testAssertFalse() {
boolean test = false;
assertFalse("failure - should be false", test);
}

@Test
public void testAssertNotNull() {
assertNotNull("should not be null", new Object());
}

@Test
public void testAssertNotSame() {
assertNotSame("should not be same Object", new Object(), new Object());
}

@Test
public void testAssertNull() {
assertNull("should be null", null);
}

@Test
public void testAssertSame() {
Integer aNumber = Integer.valueOf(768);
assertSame("should be same", aNumber, aNumber);
}

// JUnit Matchers assertThat
@Test
public void testAssertThat(){
int x = 5;
String responseString = "color";
assertThat(x, is(5));
assertThat(x, is(not(4)));
assertTrue(responseString.contains("color") || responseString.contains("colour"));
assertThat(responseString, anyOf(containsString("color"), containsString("colour")));

}
@Test
public void testAssertThatBothContainsString() {
assertThat("albumen", both(containsString("a")).and(containsString("b")));
}

@Test
public void testAssertThathasItemsContainsString() {
assertThat(Arrays.asList("one", "two", "three"), hasItems("one", "three"));
}

@Test
public void testAssertThatEveryItemContainsString() {
assertThat(Arrays.asList(new String[] { "fun", "ban", "net" }), everyItem(containsString("n")));
}
```

####4.2 Parameterized Tests
When running a parameterized test class, instances are created for the cross-product of the test methods and the test data elements.
```
// Source class
public class DomainUtils {
private static Pattern pDomainName;
private static final String DOMAIN_NAME_PATTERN = "^((?!-)[A-Za-z0-9-]{1,63}(?<!-)\\.)+[A-Za-z]{2,6}$";
static {
pDomainName = Pattern.compile(DOMAIN_NAME_PATTERN);
}

// Check if it is a valid domain name
public static boolean isValidDomainName(String domainName) {
return pDomainName.matcher(domainName).find();
}
}
```

```
// Test class
@RunWith(value = Parameterized.class)
public class DomainUtilsTest {
private String domain;
private boolean expected;

public DomainUtilsTest(String domain, boolean expected) {
this.domain = domain;
this.expected = expected;
}

@Parameters
public static Iterable<Object[]> data() {
return Arrays.asList(new Object[][] {
{ "google.com", true },
{ "finra.org", true },
{ "-finra.org", false },
{ "finra-.com", false },
{ "3423kjk", false },
{ "fi#$kdo.com", false }
}
);
}

@Test
public void test_validDomains() {
assertEquals(expected,DomainUtils.isValidDomainName(domain));
}
}
```

###5. Mockito Quick Guide
The Mockito library enables mock creation, verification and stubbing. Learn more at http://static.javadoc.io/org.mockito/mockito-core/2.2.9/org/mockito/Mockito.html#2

####5.1 Stubbing
```
//You can mock concrete classes, not just interfaces
LinkedList mockedList = mock(LinkedList.class);

//stubbing
when(mockedList.get(0)).thenReturn("first");
when(mockedList.get(1)).thenThrow(new RuntimeException());

//following prints "first"
System.out.println(mockedList.get(0));

//following throws runtime exception
System.out.println(mockedList.get(1));

//following prints "null" because get(999) was not stubbed
System.out.println(mockedList.get(999));

//Although it is possible to verify a stubbed invocation, usually it's just redundant
//If your code cares what get(0) returns, then something else breaks (often even before verify() gets executed).
//If your code doesn't care what get(0) returns, then it should not be stubbed. Not convinced? See here.
verify(mockedList).get(0);
```

####5.2 Verification
```
LinkedList mockedList = mock(LinkedList.class);
//using mock
mockedList.add("once");

mockedList.add("twice");
mockedList.add("twice");

mockedList.add("three times");
mockedList.add("three times");
mockedList.add("three times");

//following two verifications work exactly the same - times(1) is used by default
verify(mockedList).add("once");
verify(mockedList, times(1)).add("once");

//exact number of invocations verification
verify(mockedList, times(2)).add("twice");
verify(mockedList, times(3)).add("three times");

//verification using never(). never() is an alias to times(0)
verify(mockedList, never()).add("never happened");

//verification using atLeast()/atMost()
verify(mockedList, atLeastOnce()).add("three times");
verify(mockedList, atLeast(2)).add("five times");
verify(mockedList, atMost(5)).add("three times");
```

####5.3 Mock dependencies

```
//Source code
public class Foo {
private Bar bar;
public One(Bar bar) {
this.bar = bar;
}
public void work(){
bar.doSomething();
}
}

public class Bar {
public void doSomething(){
System.out.println("Bar is doing something...");
}
}
```


```
//Test code
public class FooTest {
@InjectMocks
private Foo foo;
@Mock
private Bar bar;
@Before
public void setup() {
MockitoAnnotations.initMocks(this);
}

@Test
public void testWork() {
foo.work();
Mockito.verify(bar).doSomething();
}
}
```
