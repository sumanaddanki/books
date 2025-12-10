# Chapter 9: Generics - Type Safety at Scale

> "Generics let you find bugs at compile time instead of runtime - when they're cheapest to fix."

---

## What You'll Learn

- Why generics exist and what problems they solve
- Generic classes and interfaces
- Generic methods
- Bounded type parameters
- Wildcards: ?, extends, super
- Type erasure and its implications
- Common generic patterns
- What AI often gets wrong with generics

---

## The Problem: Type Safety Before Generics

Before Java 5, collections couldn't enforce types:

```java
// Pre-generics code (don't write this!)
List names = new ArrayList();
names.add("Alice");
names.add("Bob");
names.add(42);  // No error at compile time!

for (Object obj : names) {
    String name = (String) obj;  // ClassCastException at runtime!
    System.out.println(name);
}
```

Problems:
1. No compile-time type checking
2. Requires explicit casting
3. Runtime errors instead of compile-time errors

---

## The Solution: Generics

```java
// With generics
List<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
// names.add(42);  // Compile error! Caught early.

for (String name : names) {
    System.out.println(name);  // No casting needed
}
```

Benefits:
1. Compile-time type checking
2. No explicit casting
3. Self-documenting code

---

## Generic Classes

### Defining a Generic Class

```java
public class Box<T> {
    private T content;

    public void put(T item) {
        this.content = item;
    }

    public T get() {
        return content;
    }

    public boolean isEmpty() {
        return content == null;
    }
}
```

`T` is a **type parameter** - a placeholder for an actual type.

### Using Generic Classes

```java
// Box of Strings
Box<String> stringBox = new Box<>();
stringBox.put("Hello");
String message = stringBox.get();  // No casting!

// Box of Integers
Box<Integer> intBox = new Box<>();
intBox.put(42);
int number = intBox.get();  // Auto-unboxing

// Box of custom objects
Box<Person> personBox = new Box<>();
personBox.put(new Person("Alice"));
Person person = personBox.get();
```

### Multiple Type Parameters

```java
public class Pair<K, V> {
    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public K getKey() { return key; }
    public V getValue() { return value; }
}

// Usage
Pair<String, Integer> nameAge = new Pair<>("Alice", 25);
String name = nameAge.getKey();    // "Alice"
Integer age = nameAge.getValue();  // 25

Pair<Integer, String> idName = new Pair<>(1, "Bob");
```

### Naming Conventions

| Letter | Meaning |
|--------|---------|
| T | Type |
| E | Element |
| K | Key |
| V | Value |
| N | Number |
| S, U, V | Additional types |

---

## Generic Interfaces

```java
public interface Repository<T, ID> {
    T findById(ID id);
    List<T> findAll();
    T save(T entity);
    void delete(T entity);
}

// Implementation
public class UserRepository implements Repository<User, Long> {
    @Override
    public User findById(Long id) {
        // Implementation
        return null;
    }

    @Override
    public List<User> findAll() {
        return new ArrayList<>();
    }

    @Override
    public User save(User entity) {
        return entity;
    }

    @Override
    public void delete(User entity) {
        // Implementation
    }
}
```

---

## Generic Methods

Methods can have their own type parameters, independent of the class:

```java
public class Utility {

    // Generic method
    public static <T> T getFirst(List<T> list) {
        if (list.isEmpty()) {
            return null;
        }
        return list.get(0);
    }

    // Multiple type parameters
    public static <T, U> Pair<T, U> makePair(T first, U second) {
        return new Pair<>(first, second);
    }

    // Generic method in non-generic class
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.println(element);
        }
    }
}

// Usage - type is inferred
List<String> names = Arrays.asList("Alice", "Bob");
String first = Utility.getFirst(names);  // Type inferred as String

// Explicit type (rarely needed)
String first2 = Utility.<String>getFirst(names);
```

---

## Bounded Type Parameters

Sometimes you need to restrict what types can be used.

### Upper Bound: extends

"T must be Number or a subclass of Number"

```java
public class Calculator<T extends Number> {
    private T value;

    public Calculator(T value) {
        this.value = value;
    }

    public double doubleValue() {
        return value.doubleValue();  // Number method is available!
    }
}

// Usage
Calculator<Integer> intCalc = new Calculator<>(42);
Calculator<Double> doubleCalc = new Calculator<>(3.14);
// Calculator<String> stringCalc = new Calculator<>("hello");  // Error!
```

### Multiple Bounds

```java
// T must implement both Comparable AND Serializable
public class SortableData<T extends Comparable<T> & Serializable> {
    private T data;

    public int compareTo(SortableData<T> other) {
        return this.data.compareTo(other.data);
    }
}
```

Note: If one bound is a class, it must come first.

### Bounded Generic Methods

```java
// Find max element - T must be Comparable
public static <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) > 0 ? a : b;
}

// Usage
String maxString = max("apple", "banana");  // "banana"
Integer maxInt = max(10, 20);               // 20
```

---

## Wildcards

When you don't know or care about the specific type.

### Unbounded Wildcard: ?

"Any type"

```java
public static void printList(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}

// Works with any List
printList(Arrays.asList("a", "b", "c"));
printList(Arrays.asList(1, 2, 3));
printList(Arrays.asList(new Person("Alice")));
```

### Upper Bounded Wildcard: ? extends

"Unknown type that is Number or subclass"

```java
public static double sum(List<? extends Number> numbers) {
    double sum = 0;
    for (Number num : numbers) {
        sum += num.doubleValue();
    }
    return sum;
}

// Works with List of any Number subtype
List<Integer> integers = Arrays.asList(1, 2, 3);
List<Double> doubles = Arrays.asList(1.5, 2.5, 3.5);

sum(integers);  // 6.0
sum(doubles);   // 7.5
```

**But you can't add to it:**
```java
public static void addNumber(List<? extends Number> numbers) {
    // numbers.add(42);      // Error! Can't add
    // numbers.add(3.14);    // Error! Can't add
    Number n = numbers.get(0);  // OK to read
}
```

### Lower Bounded Wildcard: ? super

"Unknown type that is Integer or superclass"

```java
public static void addIntegers(List<? super Integer> list) {
    list.add(1);   // OK! Can add Integer
    list.add(2);   // OK!
    // list.add(3.14);  // Error! Can't add Double

    Object obj = list.get(0);  // Can only read as Object
}

// Works with List<Integer>, List<Number>, List<Object>
List<Number> numbers = new ArrayList<>();
addIntegers(numbers);
```

### PECS Principle

**P**roducer **E**xtends, **C**onsumer **S**uper

- If you **read** from a collection → use `extends` (producer)
- If you **write** to a collection → use `super` (consumer)
- If you do both → don't use wildcards

```java
// Producer - reading elements
public static void copy(List<? extends Number> source,    // Read from
                        List<? super Number> dest) {       // Write to
    for (Number num : source) {
        dest.add(num);
    }
}
```

---

## Type Erasure

Generics are a compile-time feature. At runtime, type information is "erased".

### What Happens at Compile Time

```java
// What you write
List<String> strings = new ArrayList<>();
strings.add("hello");
String s = strings.get(0);

// What the compiler generates (roughly)
List strings = new ArrayList();
strings.add("hello");
String s = (String) strings.get(0);  // Cast added by compiler
```

### Implications of Type Erasure

**1. Can't use primitives as type parameters:**
```java
// List<int> numbers;  // Error!
List<Integer> numbers;  // OK - use wrapper
```

**2. Can't create generic arrays:**
```java
// T[] array = new T[10];  // Error!
```

**3. Can't use instanceof with generic types:**
```java
// if (obj instanceof List<String>)  // Error!
if (obj instanceof List<?>)  // OK
```

**4. Can't catch generic exceptions:**
```java
// catch (MyException<String> e)  // Error!
```

---

## Common Generic Patterns

### Generic Factory

```java
public interface Factory<T> {
    T create();
}

public class PersonFactory implements Factory<Person> {
    @Override
    public Person create() {
        return new Person();
    }
}
```

### Generic Builder

```java
public class Builder<T extends Builder<T>> {
    protected String name;

    @SuppressWarnings("unchecked")
    public T withName(String name) {
        this.name = name;
        return (T) this;
    }
}

public class PersonBuilder extends Builder<PersonBuilder> {
    private int age;

    public PersonBuilder withAge(int age) {
        this.age = age;
        return this;
    }

    public Person build() {
        return new Person(name, age);
    }
}

// Usage with method chaining
Person person = new PersonBuilder()
    .withName("Alice")
    .withAge(25)
    .build();
```

### Generic Cache

```java
public class Cache<K, V> {
    private Map<K, V> cache = new HashMap<>();
    private Function<K, V> loader;

    public Cache(Function<K, V> loader) {
        this.loader = loader;
    }

    public V get(K key) {
        return cache.computeIfAbsent(key, loader);
    }

    public void invalidate(K key) {
        cache.remove(key);
    }

    public void clear() {
        cache.clear();
    }
}

// Usage
Cache<Integer, User> userCache = new Cache<>(id -> database.findUser(id));
User user = userCache.get(123);  // Loads from DB first time, cached after
```

---

## AI Integration: Generic Mistakes

### Problem 1: Raw Types

**AI generates:**
```java
List names = new ArrayList();  // Raw type!
Map data = new HashMap();      // Raw type!
```

**Fix:**
```java
List<String> names = new ArrayList<>();
Map<String, Object> data = new HashMap<>();
```

### Problem 2: Unnecessary Wildcards

**AI generates:**
```java
public void process(List<?> items) {
    for (Object item : items) {
        // Can only treat as Object!
    }
}
```

**Fix (if you know the type):**
```java
public void process(List<String> items) {
    for (String item : items) {
        // Full String functionality
    }
}
```

### Problem 3: Wrong Bounds

**AI generates:**
```java
// Trying to add elements to extends
public void fill(List<? extends Number> list, Number value) {
    list.add(value);  // Error!
}
```

**Fix:**
```java
public void fill(List<? super Number> list, Number value) {
    list.add(value);  // OK!
}
```

### Problem 4: Over-complicated Generics

**AI generates:**
```java
public <T extends Comparable<? super T>> T max(List<? extends T> list)
```

**Fix (simpler is better):**
```java
public <T extends Comparable<T>> T max(List<T> list)
```

---

## Exercises

### Exercise 9.1: Generic Stack
Implement a `Stack<T>` with:
- `push(T item)`
- `pop()` returns T
- `peek()` returns T without removing
- `isEmpty()`

### Exercise 9.2: Generic Pair
Create a `Triple<A, B, C>` class holding three values of different types.

### Exercise 9.3: Bounded Method
Write a method `<T extends Comparable<T>> T findMin(List<T> list)` that finds the minimum element.

### Exercise 9.4: Generic Filter
Write a generic method that filters a List based on a Predicate:
```java
<T> List<T> filter(List<T> list, Predicate<T> predicate)
```

### Exercise 9.5: Fix AI Code
Fix the generic issues in this AI-generated code:
```java
public class DataStore {
    private List data = new ArrayList();

    public void add(Object item) {
        data.add(item);
    }

    public Object get(int index) {
        return data.get(index);
    }

    public void processNumbers(List<? extends Number> numbers) {
        numbers.add(42);  // Add a number
    }
}
```

---

## Key Takeaways

- **Generics provide type safety** - Catch errors at compile time
- **Use meaningful type parameter names** - T, E, K, V by convention
- **extends for reading (upper bound)** - Producer Extends
- **super for writing (lower bound)** - Consumer Super
- **Avoid raw types** - Always specify type parameters
- **Type erasure happens at runtime** - No generic type info at runtime
- **Don't over-complicate** - Simple generics are better than clever ones

---

## What's Next

You've mastered type-safe programming with generics. In **Chapter 10**, we'll explore Streams and Lambda Expressions - the functional programming features that make Java code more concise and expressive.

---

*"Generics are like contracts - they make expectations explicit and catch violations early."*
