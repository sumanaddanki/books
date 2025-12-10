# Chapter 14: Unit Testing - Writing Code That Tests Code

> "Untested code is broken code - you just don't know it yet."

---

## What You'll Learn

- Why testing matters (especially with AI-generated code)
- JUnit 5 fundamentals
- Writing effective test cases
- Assertions and matchers
- Test lifecycle and annotations
- Mocking with Mockito
- Test-Driven Development (TDD)
- Testing AI-generated code

---

## Why Testing Matters

Without tests:
- You discover bugs in production
- You're afraid to change code
- "It works on my machine" syndrome
- AI-generated code is accepted on faith

With tests:
- Bugs are caught early
- Refactoring becomes safe
- Documentation through examples
- AI output can be verified

**Testing is especially critical for AI-generated code.** AI writes plausible-looking code that may have subtle bugs. Tests catch them.

---

## Setting Up JUnit 5

### Maven Dependency

```xml
<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.1.2</version>
        </plugin>
    </plugins>
</build>
```

### Test Class Structure

```
src/
├── main/java/
│   └── com/example/
│       └── Calculator.java
└── test/java/
    └── com/example/
        └── CalculatorTest.java
```

---

## Your First Test

### The Code to Test

```java
package com.example;

public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Cannot divide by zero");
        }
        return a / b;
    }
}
```

### The Test

```java
package com.example;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    @Test
    void addTwoPositiveNumbers() {
        Calculator calc = new Calculator();
        int result = calc.add(2, 3);
        assertEquals(5, result);
    }

    @Test
    void addNegativeNumbers() {
        Calculator calc = new Calculator();
        assertEquals(-5, calc.add(-2, -3));
    }

    @Test
    void subtractNumbers() {
        Calculator calc = new Calculator();
        assertEquals(2, calc.subtract(5, 3));
    }

    @Test
    void divideByZeroThrowsException() {
        Calculator calc = new Calculator();
        assertThrows(IllegalArgumentException.class, () -> {
            calc.divide(10, 0);
        });
    }
}
```

### Running Tests

```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=CalculatorTest

# Run specific test method
mvn test -Dtest=CalculatorTest#addTwoPositiveNumbers
```

---

## JUnit 5 Annotations

### Basic Annotations

```java
import org.junit.jupiter.api.*;

class MyTest {

    @Test
    void regularTest() {
        // Normal test method
    }

    @BeforeEach
    void setUp() {
        // Runs before each test
    }

    @AfterEach
    void tearDown() {
        // Runs after each test
    }

    @BeforeAll
    static void setUpAll() {
        // Runs once before all tests
    }

    @AfterAll
    static void tearDownAll() {
        // Runs once after all tests
    }

    @Disabled("Reason for disabling")
    @Test
    void skippedTest() {
        // This test won't run
    }

    @DisplayName("A meaningful test name")
    @Test
    void testWithDisplayName() {
        // Shows nice name in reports
    }
}
```

### Lifecycle Example

```java
class DatabaseTest {

    private static Connection connection;
    private UserRepository repo;

    @BeforeAll
    static void connectToDatabase() {
        connection = Database.connect();  // Expensive operation once
    }

    @AfterAll
    static void closeConnection() {
        connection.close();
    }

    @BeforeEach
    void setUp() {
        repo = new UserRepository(connection);
        repo.clear();  // Clean state for each test
    }

    @Test
    void saveUser() {
        User user = new User("Alice");
        repo.save(user);
        assertEquals(1, repo.count());
    }

    @Test
    void findUser() {
        repo.save(new User("Bob"));
        User found = repo.findByName("Bob");
        assertNotNull(found);
    }
}
```

---

## Assertions

### Basic Assertions

```java
import static org.junit.jupiter.api.Assertions.*;

@Test
void demonstrateAssertions() {
    // Equality
    assertEquals(4, 2 + 2);
    assertEquals(3.14159, Math.PI, 0.001);  // With delta for floating point
    assertEquals("hello", "hello");

    // Boolean
    assertTrue(5 > 3);
    assertFalse(3 > 5);

    // Null checks
    assertNull(null);
    assertNotNull("not null");

    // Same instance
    String s1 = "hello";
    String s2 = s1;
    assertSame(s1, s2);

    // Array equality
    assertArrayEquals(new int[]{1, 2, 3}, new int[]{1, 2, 3});

    // With message
    assertEquals(4, 2 + 2, "Math should work");
}
```

### Exception Testing

```java
@Test
void testException() {
    // Assert exception is thrown
    assertThrows(IllegalArgumentException.class, () -> {
        methodThatThrows();
    });

    // Capture exception for further assertions
    IllegalArgumentException exception = assertThrows(
        IllegalArgumentException.class,
        () -> methodThatThrows()
    );
    assertEquals("Invalid input", exception.getMessage());

    // Assert no exception
    assertDoesNotThrow(() -> {
        safeMethod();
    });
}
```

### Grouped Assertions

```java
@Test
void testPerson() {
    Person person = new Person("Alice", 25);

    // All assertions run, even if one fails
    assertAll("person",
        () -> assertEquals("Alice", person.getName()),
        () -> assertEquals(25, person.getAge()),
        () -> assertTrue(person.isAdult())
    );
}
```

### Timeout

```java
@Test
@Timeout(5)  // Fail if takes more than 5 seconds
void testWithTimeout() {
    longRunningOperation();
}

@Test
void testWithAssertTimeout() {
    assertTimeout(Duration.ofSeconds(2), () -> {
        operation();
    });
}
```

---

## Parameterized Tests

Run same test with different inputs:

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;

class ParameterizedTests {

    @ParameterizedTest
    @ValueSource(ints = {1, 2, 3, 4, 5})
    void testWithValueSource(int number) {
        assertTrue(number > 0);
    }

    @ParameterizedTest
    @ValueSource(strings = {"hello", "world", "java"})
    void testStrings(String value) {
        assertFalse(value.isEmpty());
    }

    @ParameterizedTest
    @NullAndEmptySource
    void testNullAndEmpty(String value) {
        assertTrue(value == null || value.isEmpty());
    }

    @ParameterizedTest
    @CsvSource({
        "1, 2, 3",
        "4, 5, 9",
        "10, 20, 30"
    })
    void testAddition(int a, int b, int expected) {
        assertEquals(expected, a + b);
    }

    @ParameterizedTest
    @MethodSource("provideTestData")
    void testWithMethodSource(String input, int expected) {
        assertEquals(expected, input.length());
    }

    static Stream<Arguments> provideTestData() {
        return Stream.of(
            Arguments.of("hello", 5),
            Arguments.of("world", 5),
            Arguments.of("", 0)
        );
    }
}
```

---

## Nested Tests

Organize related tests:

```java
@DisplayName("Calculator Tests")
class CalculatorTest {

    Calculator calc;

    @BeforeEach
    void setUp() {
        calc = new Calculator();
    }

    @Nested
    @DisplayName("Addition")
    class AdditionTests {

        @Test
        @DisplayName("adds positive numbers")
        void positiveNumbers() {
            assertEquals(5, calc.add(2, 3));
        }

        @Test
        @DisplayName("adds negative numbers")
        void negativeNumbers() {
            assertEquals(-5, calc.add(-2, -3));
        }
    }

    @Nested
    @DisplayName("Division")
    class DivisionTests {

        @Test
        @DisplayName("divides evenly")
        void evenDivision() {
            assertEquals(5, calc.divide(10, 2));
        }

        @Test
        @DisplayName("throws on divide by zero")
        void divideByZero() {
            assertThrows(IllegalArgumentException.class, () -> {
                calc.divide(10, 0);
            });
        }
    }
}
```

---

## Mocking with Mockito

When testing code with dependencies, mock them.

### Maven Dependency

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>
```

### Basic Mocking

```java
import org.mockito.*;
import org.mockito.junit.jupiter.MockitoExtension;
import org.junit.jupiter.api.extension.ExtendWith;

@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    UserRepository repository;  // Mock dependency

    @InjectMocks
    UserService service;  // Class under test

    @Test
    void findUserById() {
        // Define mock behavior
        when(repository.findById(1L))
            .thenReturn(Optional.of(new User(1L, "Alice")));

        // Call method under test
        User user = service.findById(1L);

        // Assert result
        assertEquals("Alice", user.getName());

        // Verify interaction
        verify(repository).findById(1L);
    }

    @Test
    void saveUser() {
        User user = new User("Bob");

        when(repository.save(any(User.class)))
            .thenAnswer(invocation -> {
                User saved = invocation.getArgument(0);
                saved.setId(1L);
                return saved;
            });

        User saved = service.createUser("Bob");

        assertNotNull(saved.getId());
        verify(repository).save(any(User.class));
    }

    @Test
    void userNotFound() {
        when(repository.findById(anyLong()))
            .thenReturn(Optional.empty());

        assertThrows(UserNotFoundException.class, () -> {
            service.findById(999L);
        });
    }
}
```

### Mock Behaviors

```java
// Return value
when(mock.method()).thenReturn(value);

// Throw exception
when(mock.method()).thenThrow(new RuntimeException());

// Multiple calls
when(mock.method())
    .thenReturn(firstValue)
    .thenReturn(secondValue);

// Answer with logic
when(mock.method(anyString()))
    .thenAnswer(invocation -> {
        String arg = invocation.getArgument(0);
        return arg.toUpperCase();
    });

// Void methods
doNothing().when(mock).voidMethod();
doThrow(new RuntimeException()).when(mock).voidMethod();
```

### Verifying Interactions

```java
// Verify called once (default)
verify(mock).method();

// Verify called specific times
verify(mock, times(3)).method();
verify(mock, never()).method();
verify(mock, atLeast(2)).method();
verify(mock, atMost(5)).method();

// Verify order
InOrder inOrder = inOrder(mock1, mock2);
inOrder.verify(mock1).firstMethod();
inOrder.verify(mock2).secondMethod();

// Verify with argument matchers
verify(mock).method(eq("exact"));
verify(mock).method(anyString());
verify(mock).method(argThat(s -> s.startsWith("test")));

// Capture arguments
ArgumentCaptor<User> captor = ArgumentCaptor.forClass(User.class);
verify(repository).save(captor.capture());
User captured = captor.getValue();
assertEquals("Alice", captured.getName());
```

---

## Test-Driven Development (TDD)

Write tests first, then code.

### TDD Cycle: Red-Green-Refactor

1. **Red:** Write a failing test
2. **Green:** Write minimal code to pass
3. **Refactor:** Improve code while keeping tests green

### TDD Example

**Step 1: Write failing test**

```java
@Test
void emptyStackIsEmpty() {
    Stack<Integer> stack = new Stack<>();
    assertTrue(stack.isEmpty());
}
```

**Step 2: Write minimal code**

```java
public class Stack<T> {
    public boolean isEmpty() {
        return true;  // Minimal implementation
    }
}
```

**Step 3: Add another test**

```java
@Test
void pushMakesStackNonEmpty() {
    Stack<Integer> stack = new Stack<>();
    stack.push(1);
    assertFalse(stack.isEmpty());
}
```

**Step 4: Implement**

```java
public class Stack<T> {
    private int size = 0;

    public boolean isEmpty() {
        return size == 0;
    }

    public void push(T item) {
        size++;
    }
}
```

**Continue the cycle...**

```java
@Test
void popReturnsLastPushed() {
    Stack<Integer> stack = new Stack<>();
    stack.push(42);
    assertEquals(42, stack.pop());
}
```

---

## Testing Best Practices

### 1. One Assertion Per Test (Ideally)

```java
// BAD - Multiple unrelated assertions
@Test
void testUser() {
    User user = createUser();
    assertEquals("Alice", user.getName());
    assertEquals(25, user.getAge());
    assertNotNull(user.getEmail());
    assertTrue(user.isActive());
}

// GOOD - Focused tests
@Test
void userHasCorrectName() {
    User user = createUser();
    assertEquals("Alice", user.getName());
}

@Test
void userHasCorrectAge() {
    User user = createUser();
    assertEquals(25, user.getAge());
}
```

### 2. Test Names Describe Behavior

```java
// BAD
@Test
void test1() { }

// GOOD
@Test
void emptyCartHasZeroTotal() { }

@Test
void addingItemIncreasesCartTotal() { }

@Test
void removingLastItemMakesCartEmpty() { }
```

### 3. Arrange-Act-Assert Pattern

```java
@Test
void calculateDiscountForPremiumUser() {
    // Arrange
    User user = new User("Alice", UserType.PREMIUM);
    Product product = new Product("Laptop", 1000);
    PricingService service = new PricingService();

    // Act
    double finalPrice = service.calculatePrice(user, product);

    // Assert
    assertEquals(900, finalPrice);  // 10% premium discount
}
```

### 4. Test Edge Cases

```java
@Test
void divideByZeroThrowsException() { }

@Test
void emptyListReturnsNoResults() { }

@Test
void nullInputIsHandledGracefully() { }

@Test
void veryLargeInputDoesNotCauseOverflow() { }

@Test
void negativeNumbersAreHandledCorrectly() { }
```

### 5. Keep Tests Independent

```java
// BAD - Tests depend on each other
class OrderTests {
    static Order order;

    @Test
    void createOrder() {
        order = new Order();  // Other tests depend on this!
    }

    @Test
    void addItem() {
        order.addItem(new Item());  // Fails if createOrder didn't run
    }
}

// GOOD - Each test is independent
class OrderTests {
    @Test
    void createOrder() {
        Order order = new Order();
        assertNotNull(order);
    }

    @Test
    void addItem() {
        Order order = new Order();  // Fresh instance
        order.addItem(new Item());
        assertEquals(1, order.getItemCount());
    }
}
```

---

## Testing AI-Generated Code

AI code needs testing more than hand-written code!

### Strategy: Test the Contract

```java
// AI generated this method - test its contract
public List<Integer> removeDuplicates(List<Integer> input);

@Test
void removeDuplicatesRemovesDuplicates() {
    List<Integer> input = Arrays.asList(1, 2, 2, 3, 3, 3);
    List<Integer> result = removeDuplicates(input);
    assertEquals(3, result.size());  // Only unique values
}

@Test
void removeDuplicatesPreservesOrder() {
    List<Integer> input = Arrays.asList(3, 1, 2, 1, 3);
    List<Integer> result = removeDuplicates(input);
    assertEquals(Arrays.asList(3, 1, 2), result);
}

@Test
void removeDuplicatesHandlesEmptyList() {
    List<Integer> result = removeDuplicates(Collections.emptyList());
    assertTrue(result.isEmpty());
}

@Test
void removeDuplicatesHandlesNull() {
    // Does it throw? Return empty? Document behavior
    assertThrows(NullPointerException.class, () -> {
        removeDuplicates(null);
    });
}
```

### Strategy: Verify Edge Cases AI Might Miss

```java
// AI-generated sorting method
@Test
void sortHandlesEmptyArray() { }

@Test
void sortHandlesSingleElement() { }

@Test
void sortHandlesAlreadySorted() { }

@Test
void sortHandlesReverseSorted() { }

@Test
void sortHandlesDuplicates() { }

@Test
void sortHandlesNegativeNumbers() { }

@Test
void sortIsStable() { }  // Often missed by AI
```

---

## Hands-On Project: Testing a Shopping Cart

```java
// The class to test
public class ShoppingCart {
    private List<CartItem> items = new ArrayList<>();

    public void addItem(Product product, int quantity) {
        items.add(new CartItem(product, quantity));
    }

    public void removeItem(String productId) {
        items.removeIf(item -> item.getProduct().getId().equals(productId));
    }

    public double getTotal() {
        return items.stream()
            .mapToDouble(item -> item.getProduct().getPrice() * item.getQuantity())
            .sum();
    }

    public int getItemCount() {
        return items.stream().mapToInt(CartItem::getQuantity).sum();
    }

    public boolean isEmpty() {
        return items.isEmpty();
    }
}

// Comprehensive test class
class ShoppingCartTest {

    private ShoppingCart cart;
    private Product laptop;
    private Product mouse;

    @BeforeEach
    void setUp() {
        cart = new ShoppingCart();
        laptop = new Product("P1", "Laptop", 999.99);
        mouse = new Product("P2", "Mouse", 29.99);
    }

    @Nested
    @DisplayName("Empty Cart")
    class EmptyCartTests {

        @Test
        @DisplayName("new cart is empty")
        void newCartIsEmpty() {
            assertTrue(cart.isEmpty());
        }

        @Test
        @DisplayName("empty cart has zero total")
        void emptyCartHasZeroTotal() {
            assertEquals(0.0, cart.getTotal(), 0.001);
        }

        @Test
        @DisplayName("empty cart has zero items")
        void emptyCartHasZeroItems() {
            assertEquals(0, cart.getItemCount());
        }
    }

    @Nested
    @DisplayName("Adding Items")
    class AddingItemTests {

        @Test
        @DisplayName("adding item makes cart non-empty")
        void addingItemMakesCartNonEmpty() {
            cart.addItem(laptop, 1);
            assertFalse(cart.isEmpty());
        }

        @Test
        @DisplayName("adding item increases count")
        void addingItemIncreasesCount() {
            cart.addItem(laptop, 2);
            assertEquals(2, cart.getItemCount());
        }

        @Test
        @DisplayName("total reflects added items")
        void totalReflectsAddedItems() {
            cart.addItem(laptop, 1);
            cart.addItem(mouse, 2);
            assertEquals(999.99 + 29.99 * 2, cart.getTotal(), 0.001);
        }
    }

    @Nested
    @DisplayName("Removing Items")
    class RemovingItemTests {

        @BeforeEach
        void addItemsFirst() {
            cart.addItem(laptop, 1);
            cart.addItem(mouse, 2);
        }

        @Test
        @DisplayName("removing item decreases count")
        void removingItemDecreasesCount() {
            cart.removeItem("P2");
            assertEquals(1, cart.getItemCount());
        }

        @Test
        @DisplayName("removing all items makes cart empty")
        void removingAllItemsMakesCartEmpty() {
            cart.removeItem("P1");
            cart.removeItem("P2");
            assertTrue(cart.isEmpty());
        }

        @Test
        @DisplayName("removing non-existent item does nothing")
        void removingNonExistentItemDoesNothing() {
            int countBefore = cart.getItemCount();
            cart.removeItem("INVALID");
            assertEquals(countBefore, cart.getItemCount());
        }
    }
}
```

---

## AI Integration: Testing Mistakes

### Problem 1: No Edge Case Tests

**AI generates:**
```java
@Test
void testSort() {
    int[] arr = {3, 1, 2};
    sort(arr);
    assertArrayEquals(new int[]{1, 2, 3}, arr);
}
// Missing: empty, single element, duplicates, negative, large arrays
```

**Fix:** Add edge case tests.

### Problem 2: Tests That Always Pass

**AI generates:**
```java
@Test
void testSomething() {
    Result result = doSomething();
    assertNotNull(result);  // Too weak - almost always passes
}
```

**Fix:** Assert specific values.

### Problem 3: Testing Implementation, Not Behavior

**AI generates:**
```java
@Test
void testUseArrayList() {
    MyClass obj = new MyClass();
    assertTrue(obj.getInternalList() instanceof ArrayList);  // Implementation detail!
}
```

**Fix:** Test behavior, not implementation.

---

## Exercises

### Exercise 14.1: Test StringUtils
Write comprehensive tests for a StringUtils class with methods:
- `reverse(String)`
- `isPalindrome(String)`
- `countWords(String)`

### Exercise 14.2: Test with Mocks
Write tests for an EmailService that depends on an EmailSender interface.

### Exercise 14.3: TDD
Use TDD to implement a `PriceCalculator` that applies discounts based on quantity.

### Exercise 14.4: Parameterized Tests
Write parameterized tests for a password validator with various rules.

### Exercise 14.5: Test AI Code
Test this AI-generated method and find any bugs:

```java
public static List<Integer> findPrimes(int max) {
    List<Integer> primes = new ArrayList<>();
    for (int i = 2; i < max; i++) {
        boolean isPrime = true;
        for (int j = 2; j < i; j++) {
            if (i % j == 0) {
                isPrime = false;
            }
        }
        if (isPrime) {
            primes.add(i);
        }
    }
    return primes;
}
```

---

## Key Takeaways

- **Tests catch bugs early** - Before they reach production
- **JUnit 5 is the standard** - Modern, flexible, powerful
- **Test behavior, not implementation** - Tests should survive refactoring
- **Mock dependencies** - Isolate the code under test
- **TDD leads to better design** - Write tests first
- **AI code needs more testing** - Verify it actually works
- **Edge cases matter** - Empty, null, negative, large values

---

## What's Next

You can now write professional tests. In **Chapter 15**, we'll explore AI Prompting for Java - how to effectively use AI tools to write, understand, and improve Java code.

---

*"Tests are the safety net that lets you code without fear."*
