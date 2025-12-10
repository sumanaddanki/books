# Chapter 10: Streams and Lambda Expressions - Functional Java

> "Streams don't store data - they transform it. Understanding this changes how you think about collections."

---

## What You'll Learn

- What functional programming means in Java
- Lambda expressions: syntax and usage
- Method references
- The Stream API: creating, transforming, collecting
- Intermediate vs terminal operations
- Parallel streams and when to use them
- Common mistakes AI makes with streams

---

## The Shift to Functional Style

Before Java 8, processing collections was verbose:

```java
// Old way: Find names starting with "A"
List<String> names = Arrays.asList("Alice", "Bob", "Anna", "Charlie");
List<String> result = new ArrayList<>();

for (String name : names) {
    if (name.startsWith("A")) {
        result.add(name);
    }
}
```

With streams and lambdas:

```java
// Modern way
List<String> result = names.stream()
    .filter(name -> name.startsWith("A"))
    .collect(Collectors.toList());
```

Same result, more declarative, less boilerplate.

---

## Lambda Expressions

A lambda is an anonymous function - code you can pass around like data.

### Basic Syntax

```java
// Full syntax
(parameters) -> { statements; }

// Simplified forms
(a, b) -> a + b              // Multiple parameters, expression body
x -> x * 2                    // Single parameter (parens optional)
() -> System.out.println("Hi") // No parameters
(String s) -> s.length()      // Explicit type (usually inferred)
```

### Lambda Examples

```java
// Runnable (no params, no return)
Runnable task = () -> System.out.println("Running!");

// Comparator (two params, returns int)
Comparator<String> byLength = (a, b) -> a.length() - b.length();

// Predicate (one param, returns boolean)
Predicate<Integer> isEven = n -> n % 2 == 0;

// Function (one param, returns different type)
Function<String, Integer> toLength = s -> s.length();

// Consumer (one param, no return)
Consumer<String> printer = s -> System.out.println(s);

// Supplier (no params, returns value)
Supplier<Double> random = () -> Math.random();
```

### Multi-Line Lambdas

When you need multiple statements:

```java
Comparator<Person> byAge = (p1, p2) -> {
    int ageCompare = Integer.compare(p1.getAge(), p2.getAge());
    if (ageCompare != 0) {
        return ageCompare;
    }
    return p1.getName().compareTo(p2.getName());
};
```

### Functional Interfaces

Lambdas work with **functional interfaces** - interfaces with exactly one abstract method.

```java
@FunctionalInterface
public interface Calculator {
    int calculate(int a, int b);
}

// Using the functional interface
Calculator add = (a, b) -> a + b;
Calculator multiply = (a, b) -> a * b;

System.out.println(add.calculate(5, 3));      // 8
System.out.println(multiply.calculate(5, 3)); // 15
```

### Built-in Functional Interfaces

| Interface | Method | Description |
|-----------|--------|-------------|
| `Predicate<T>` | `test(T)` → boolean | Test a condition |
| `Function<T,R>` | `apply(T)` → R | Transform T to R |
| `Consumer<T>` | `accept(T)` → void | Process T |
| `Supplier<T>` | `get()` → T | Provide a T |
| `BiFunction<T,U,R>` | `apply(T,U)` → R | Two inputs |
| `UnaryOperator<T>` | `apply(T)` → T | Same type in/out |
| `BinaryOperator<T>` | `apply(T,T)` → T | Two same type inputs |

---

## Method References

A shorthand for lambdas that call existing methods.

### Types of Method References

```java
// 1. Static method reference
// Lambda: s -> Integer.parseInt(s)
Function<String, Integer> parse = Integer::parseInt;

// 2. Instance method of particular object
// Lambda: s -> System.out.println(s)
Consumer<String> printer = System.out::println;

// 3. Instance method of arbitrary object
// Lambda: s -> s.toUpperCase()
Function<String, String> upper = String::toUpperCase;

// 4. Constructor reference
// Lambda: () -> new ArrayList<>()
Supplier<List<String>> listMaker = ArrayList::new;
```

### Method Reference Examples

```java
List<String> names = Arrays.asList("alice", "bob", "charlie");

// Using lambda
names.stream().map(s -> s.toUpperCase()).forEach(s -> System.out.println(s));

// Using method references (cleaner)
names.stream().map(String::toUpperCase).forEach(System.out::println);
```

---

## The Stream API

A stream represents a sequence of elements supporting sequential and parallel operations.

### Creating Streams

```java
// From collection
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream1 = list.stream();

// From array
String[] array = {"a", "b", "c"};
Stream<String> stream2 = Arrays.stream(array);

// From values
Stream<String> stream3 = Stream.of("a", "b", "c");

// Empty stream
Stream<String> empty = Stream.empty();

// Infinite streams
Stream<Integer> ones = Stream.generate(() -> 1);        // 1, 1, 1, ...
Stream<Integer> naturals = Stream.iterate(1, n -> n + 1); // 1, 2, 3, ...

// From range
IntStream range = IntStream.range(1, 10);      // 1 to 9
IntStream rangeClosed = IntStream.rangeClosed(1, 10);  // 1 to 10
```

### Stream Pipeline

A stream pipeline consists of:
1. **Source** - Where data comes from
2. **Intermediate operations** - Transform the stream (lazy)
3. **Terminal operation** - Produces result (triggers execution)

```java
List<String> result = names.stream()       // Source
    .filter(n -> n.length() > 3)           // Intermediate
    .map(String::toUpperCase)              // Intermediate
    .sorted()                              // Intermediate
    .collect(Collectors.toList());         // Terminal
```

---

## Intermediate Operations

These return a new stream and are **lazy** - they don't execute until a terminal operation is called.

### filter() - Keep matching elements

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> evens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());
// [2, 4, 6, 8, 10]
```

### map() - Transform elements

```java
List<String> names = Arrays.asList("alice", "bob", "charlie");

List<Integer> lengths = names.stream()
    .map(String::length)
    .collect(Collectors.toList());
// [5, 3, 7]

List<String> upperNames = names.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
// ["ALICE", "BOB", "CHARLIE"]
```

### flatMap() - Flatten nested structures

```java
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2, 3),
    Arrays.asList(4, 5, 6),
    Arrays.asList(7, 8, 9)
);

List<Integer> flat = nested.stream()
    .flatMap(List::stream)
    .collect(Collectors.toList());
// [1, 2, 3, 4, 5, 6, 7, 8, 9]

// Split words and flatten
List<String> sentences = Arrays.asList("Hello World", "Java Streams");
List<String> words = sentences.stream()
    .flatMap(s -> Arrays.stream(s.split(" ")))
    .collect(Collectors.toList());
// ["Hello", "World", "Java", "Streams"]
```

### sorted() - Sort elements

```java
List<String> names = Arrays.asList("Charlie", "Alice", "Bob");

// Natural order
List<String> sorted = names.stream()
    .sorted()
    .collect(Collectors.toList());
// ["Alice", "Bob", "Charlie"]

// Custom comparator
List<String> byLength = names.stream()
    .sorted(Comparator.comparing(String::length))
    .collect(Collectors.toList());
// ["Bob", "Alice", "Charlie"]

// Reverse order
List<String> reversed = names.stream()
    .sorted(Comparator.reverseOrder())
    .collect(Collectors.toList());
// ["Charlie", "Bob", "Alice"]
```

### distinct() - Remove duplicates

```java
List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3, 4);

List<Integer> unique = numbers.stream()
    .distinct()
    .collect(Collectors.toList());
// [1, 2, 3, 4]
```

### limit() and skip() - Control size

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// First 3
List<Integer> first3 = numbers.stream()
    .limit(3)
    .collect(Collectors.toList());
// [1, 2, 3]

// Skip first 5
List<Integer> after5 = numbers.stream()
    .skip(5)
    .collect(Collectors.toList());
// [6, 7, 8, 9, 10]

// Pagination (skip 3, take 3)
List<Integer> page2 = numbers.stream()
    .skip(3)
    .limit(3)
    .collect(Collectors.toList());
// [4, 5, 6]
```

### peek() - Debug without consuming

```java
List<String> result = names.stream()
    .filter(n -> n.length() > 3)
    .peek(n -> System.out.println("After filter: " + n))
    .map(String::toUpperCase)
    .peek(n -> System.out.println("After map: " + n))
    .collect(Collectors.toList());
```

---

## Terminal Operations

These trigger stream processing and produce a result.

### collect() - Gather results

```java
// To List
List<String> list = names.stream().collect(Collectors.toList());

// To Set
Set<String> set = names.stream().collect(Collectors.toSet());

// To specific collection
TreeSet<String> treeSet = names.stream()
    .collect(Collectors.toCollection(TreeSet::new));

// Joining strings
String joined = names.stream().collect(Collectors.joining(", "));
// "Alice, Bob, Charlie"

// To Map
Map<String, Integer> nameLengths = names.stream()
    .collect(Collectors.toMap(
        name -> name,           // Key
        String::length          // Value
    ));
// {Alice=5, Bob=3, Charlie=7}
```

### forEach() - Process each element

```java
names.stream().forEach(System.out::println);

// Or directly on collection
names.forEach(System.out::println);
```

### count() - Count elements

```java
long count = names.stream()
    .filter(n -> n.startsWith("A"))
    .count();
```

### findFirst() and findAny()

```java
Optional<String> first = names.stream()
    .filter(n -> n.length() > 4)
    .findFirst();

first.ifPresent(System.out::println);

// findAny() - may return different result in parallel streams
Optional<String> any = names.parallelStream()
    .filter(n -> n.length() > 4)
    .findAny();
```

### anyMatch(), allMatch(), noneMatch()

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

boolean anyEven = numbers.stream().anyMatch(n -> n % 2 == 0);   // true
boolean allPositive = numbers.stream().allMatch(n -> n > 0);   // true
boolean noneNegative = numbers.stream().noneMatch(n -> n < 0); // true
```

### reduce() - Combine elements

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Sum
int sum = numbers.stream().reduce(0, (a, b) -> a + b);
// Or
int sum2 = numbers.stream().reduce(0, Integer::sum);

// Max
Optional<Integer> max = numbers.stream().reduce(Integer::max);

// Concatenate strings
String concat = names.stream().reduce("", (a, b) -> a + b);
```

### min() and max()

```java
Optional<Integer> min = numbers.stream().min(Integer::compareTo);
Optional<Integer> max = numbers.stream().max(Integer::compareTo);

// With objects
Optional<Person> youngest = people.stream()
    .min(Comparator.comparing(Person::getAge));
```

---

## Collectors in Depth

### Grouping

```java
List<Person> people = Arrays.asList(
    new Person("Alice", 25, "Engineering"),
    new Person("Bob", 30, "Marketing"),
    new Person("Charlie", 25, "Engineering"),
    new Person("Diana", 35, "Marketing")
);

// Group by department
Map<String, List<Person>> byDept = people.stream()
    .collect(Collectors.groupingBy(Person::getDepartment));
// {Engineering=[Alice, Charlie], Marketing=[Bob, Diana]}

// Group by age
Map<Integer, List<Person>> byAge = people.stream()
    .collect(Collectors.groupingBy(Person::getAge));

// Group and count
Map<String, Long> countByDept = people.stream()
    .collect(Collectors.groupingBy(
        Person::getDepartment,
        Collectors.counting()
    ));
// {Engineering=2, Marketing=2}

// Group and collect names
Map<String, List<String>> namesByDept = people.stream()
    .collect(Collectors.groupingBy(
        Person::getDepartment,
        Collectors.mapping(Person::getName, Collectors.toList())
    ));
```

### Partitioning

```java
// Partition by condition (two groups: true/false)
Map<Boolean, List<Person>> partitioned = people.stream()
    .collect(Collectors.partitioningBy(p -> p.getAge() >= 30));
// {false=[Alice, Charlie], true=[Bob, Diana]}
```

### Statistics

```java
// Summarizing
IntSummaryStatistics stats = people.stream()
    .collect(Collectors.summarizingInt(Person::getAge));

System.out.println("Count: " + stats.getCount());
System.out.println("Sum: " + stats.getSum());
System.out.println("Min: " + stats.getMin());
System.out.println("Max: " + stats.getMax());
System.out.println("Average: " + stats.getAverage());

// Individual stats
double avgAge = people.stream()
    .collect(Collectors.averagingInt(Person::getAge));

int totalAge = people.stream()
    .collect(Collectors.summingInt(Person::getAge));
```

---

## Primitive Streams

For better performance with primitives:

```java
// IntStream, LongStream, DoubleStream
IntStream intStream = IntStream.range(1, 100);
LongStream longStream = LongStream.of(1L, 2L, 3L);
DoubleStream doubleStream = DoubleStream.of(1.0, 2.0, 3.0);

// Convert from object stream
IntStream lengths = names.stream()
    .mapToInt(String::length);

// Sum, average, max, min are built-in
int sum = IntStream.range(1, 101).sum();  // 5050
OptionalDouble avg = IntStream.range(1, 101).average();

// Convert back to object stream
Stream<Integer> boxed = IntStream.range(1, 10).boxed();
```

---

## Parallel Streams

Process elements in parallel for better performance on large datasets.

```java
// Create parallel stream
List<Integer> numbers = IntStream.range(1, 1_000_000)
    .boxed()
    .collect(Collectors.toList());

// Sequential
long start = System.currentTimeMillis();
long count1 = numbers.stream()
    .filter(n -> isPrime(n))
    .count();
System.out.println("Sequential: " + (System.currentTimeMillis() - start) + "ms");

// Parallel
start = System.currentTimeMillis();
long count2 = numbers.parallelStream()
    .filter(n -> isPrime(n))
    .count();
System.out.println("Parallel: " + (System.currentTimeMillis() - start) + "ms");
```

### When to Use Parallel Streams

**Good candidates:**
- Large datasets (thousands+ elements)
- CPU-intensive operations
- No shared mutable state
- Unordered operations

**Poor candidates:**
- Small datasets (overhead > benefit)
- I/O-bound operations
- Operations requiring order
- Operations with side effects

```java
// WRONG - shared mutable state
List<Integer> results = new ArrayList<>();
numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .forEach(results::add);  // Not thread-safe!

// CORRECT
List<Integer> results = numbers.parallelStream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());  // Thread-safe
```

---

## Optional with Streams

### Creating Optionals

```java
Optional<String> empty = Optional.empty();
Optional<String> present = Optional.of("Hello");
Optional<String> nullable = Optional.ofNullable(mayBeNull);
```

### Using Optionals

```java
Optional<String> name = findUserName(id);

// Check and get
if (name.isPresent()) {
    System.out.println(name.get());
}

// Or better - ifPresent
name.ifPresent(System.out::println);

// Default value
String result = name.orElse("Unknown");

// Default with supplier (lazy)
String result2 = name.orElseGet(() -> computeDefault());

// Throw if empty
String result3 = name.orElseThrow(() -> new UserNotFoundException(id));

// Transform
Optional<Integer> length = name.map(String::length);

// Filter
Optional<String> longName = name.filter(n -> n.length() > 5);

// Chain operations
String upper = findUserName(id)
    .filter(n -> n.length() > 3)
    .map(String::toUpperCase)
    .orElse("UNKNOWN");
```

### Optional in Streams

```java
List<Optional<String>> optionals = Arrays.asList(
    Optional.of("a"),
    Optional.empty(),
    Optional.of("b")
);

// Extract present values
List<String> values = optionals.stream()
    .filter(Optional::isPresent)
    .map(Optional::get)
    .collect(Collectors.toList());
// ["a", "b"]

// Or using flatMap (Java 9+)
List<String> values2 = optionals.stream()
    .flatMap(Optional::stream)
    .collect(Collectors.toList());
```

---

## Hands-On Project: Data Analysis Pipeline

```java
import java.util.*;
import java.util.stream.*;

public class SalesAnalyzer {

    public static void main(String[] args) {
        List<Sale> sales = generateSampleData();

        // 1. Total revenue
        double totalRevenue = sales.stream()
            .mapToDouble(Sale::getAmount)
            .sum();
        System.out.println("Total Revenue: $" + totalRevenue);

        // 2. Average sale amount
        double avgSale = sales.stream()
            .mapToDouble(Sale::getAmount)
            .average()
            .orElse(0);
        System.out.println("Average Sale: $" + avgSale);

        // 3. Sales by category
        Map<String, Double> byCategory = sales.stream()
            .collect(Collectors.groupingBy(
                Sale::getCategory,
                Collectors.summingDouble(Sale::getAmount)
            ));
        System.out.println("\nSales by Category:");
        byCategory.forEach((cat, amount) ->
            System.out.println("  " + cat + ": $" + amount));

        // 4. Top 5 sales
        System.out.println("\nTop 5 Sales:");
        sales.stream()
            .sorted(Comparator.comparing(Sale::getAmount).reversed())
            .limit(5)
            .forEach(s -> System.out.println("  " + s));

        // 5. Sales above average
        long aboveAvg = sales.stream()
            .filter(s -> s.getAmount() > avgSale)
            .count();
        System.out.println("\nSales above average: " + aboveAvg);

        // 6. Unique customers
        Set<String> customers = sales.stream()
            .map(Sale::getCustomer)
            .collect(Collectors.toSet());
        System.out.println("Unique customers: " + customers.size());

        // 7. Revenue by customer (sorted by revenue desc)
        System.out.println("\nTop Customers:");
        sales.stream()
            .collect(Collectors.groupingBy(
                Sale::getCustomer,
                Collectors.summingDouble(Sale::getAmount)
            ))
            .entrySet().stream()
            .sorted(Map.Entry.<String, Double>comparingByValue().reversed())
            .limit(3)
            .forEach(e -> System.out.println("  " + e.getKey() + ": $" + e.getValue()));
    }

    static List<Sale> generateSampleData() {
        return Arrays.asList(
            new Sale("Alice", "Electronics", 599.99),
            new Sale("Bob", "Books", 29.99),
            new Sale("Alice", "Electronics", 149.99),
            new Sale("Charlie", "Clothing", 89.99),
            new Sale("Bob", "Electronics", 999.99),
            new Sale("Diana", "Books", 15.99),
            new Sale("Alice", "Clothing", 199.99),
            new Sale("Charlie", "Electronics", 349.99),
            new Sale("Diana", "Clothing", 79.99),
            new Sale("Bob", "Books", 45.99)
        );
    }
}

class Sale {
    private String customer;
    private String category;
    private double amount;

    public Sale(String customer, String category, double amount) {
        this.customer = customer;
        this.category = category;
        this.amount = amount;
    }

    public String getCustomer() { return customer; }
    public String getCategory() { return category; }
    public double getAmount() { return amount; }

    @Override
    public String toString() {
        return customer + " - " + category + ": $" + amount;
    }
}
```

---

## AI Integration: Stream Mistakes

### Problem 1: Reusing Streams

**AI generates:**
```java
Stream<String> stream = names.stream();
stream.forEach(System.out::println);
stream.count();  // IllegalStateException!
```

**Fix:**
```java
// Create new stream for each operation
names.stream().forEach(System.out::println);
long count = names.stream().count();
```

### Problem 2: Forgetting Terminal Operation

**AI generates:**
```java
// This does nothing!
names.stream()
    .filter(n -> n.startsWith("A"))
    .map(String::toUpperCase);
// Stream is not consumed - no processing happens
```

**Fix:**
```java
List<String> result = names.stream()
    .filter(n -> n.startsWith("A"))
    .map(String::toUpperCase)
    .collect(Collectors.toList());  // Terminal operation!
```

### Problem 3: Side Effects in Streams

**AI generates:**
```java
List<String> results = new ArrayList<>();
names.stream()
    .filter(n -> n.length() > 3)
    .forEach(results::add);  // Side effect!
```

**Fix:**
```java
List<String> results = names.stream()
    .filter(n -> n.length() > 3)
    .collect(Collectors.toList());  // Proper collection
```

### Problem 4: Overusing Streams

**AI generates:**
```java
// Overcomplicated
String first = names.stream()
    .findFirst()
    .orElse(null);
```

**Fix:**
```java
// Simple is better
String first = names.isEmpty() ? null : names.get(0);
```

### Problem 5: Wrong Collector

**AI generates:**
```java
// Throws exception on duplicate keys!
Map<Integer, String> map = people.stream()
    .collect(Collectors.toMap(
        Person::getAge,
        Person::getName
    ));
```

**Fix:**
```java
// Handle duplicates
Map<Integer, String> map = people.stream()
    .collect(Collectors.toMap(
        Person::getAge,
        Person::getName,
        (existing, replacement) -> existing  // Keep first
    ));
```

---

## Exercises

### Exercise 10.1: Filter and Transform
Given a list of integers, use streams to:
- Filter out negative numbers
- Square each number
- Sort in descending order
- Collect to a list

### Exercise 10.2: Word Frequency
Read a text and use streams to count word frequency, then display the top 10 most common words.

### Exercise 10.3: Grouping
Given a list of products with name, category, and price:
- Group products by category
- Find the most expensive product in each category
- Calculate total value per category

### Exercise 10.4: Custom Collector
Create a custom collector that collects strings into a comma-separated string with "and" before the last element.
Example: `["a", "b", "c"]` → `"a, b, and c"`

### Exercise 10.5: Fix AI Code
Fix the stream issues in this AI-generated code:

```java
public class ReportGenerator {
    private Stream<Transaction> transactions;

    public ReportGenerator(List<Transaction> data) {
        this.transactions = data.stream();
    }

    public double getTotalAmount() {
        return transactions.mapToDouble(Transaction::getAmount).sum();
    }

    public long getTransactionCount() {
        return transactions.count();
    }

    public void printHighValue(double threshold) {
        transactions
            .filter(t -> t.getAmount() > threshold)
            .map(Transaction::toString);
    }
}
```

---

## Key Takeaways

- **Lambdas enable functional style** - Pass behavior as data
- **Streams are pipelines** - Source → Transform → Collect
- **Intermediate operations are lazy** - Nothing happens until terminal operation
- **Terminal operations consume the stream** - Can only use once
- **Avoid side effects** - Use collect() instead of forEach()
- **Parallel streams need care** - Not always faster, need thread safety
- **Optional handles null safely** - Chain operations without null checks
- **Don't overcomplicate** - Simple loops are sometimes better

---

## What's Next

You've mastered functional programming in Java. In **Chapter 11**, we'll explore Concurrency - how to write programs that do multiple things at once safely and efficiently.

---

*"Streams are not about performance - they're about expressing intent clearly."*
