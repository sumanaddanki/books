# Chapter 8: Modern Java - Streams, Lambdas, and Optional

> "AI loves modern Java syntax. It writes fluent streams without considering if they're readable or appropriate."

---

## The Modern Java Toolkit

Java 8 (2014) changed everything:

```
Before Java 8:
- Anonymous inner classes everywhere
- Verbose iteration code
- Null checks everywhere
- Imperative "how to do it" style

After Java 8:
- Lambdas (compact functions)
- Streams (functional pipelines)
- Optional (null-safe containers)
- Method references (even more compact)
```

AI generates this syntax constantly. You need to understand it to review it.

---

## Lambdas: Functions as Values

### The Transformation

```
Anonymous inner class:                Lambda:
+---------------------------+        +-------------------+
| new Comparator<String>() {|   →    | (a, b) -> a.length() |
|   public int compare(     |        |   - b.length()    |
|     String a, String b) { |        +-------------------+
|     return a.length()     |
|       - b.length();       |
|   }                       |
| }                         |
+---------------------------+
```

### Lambda Syntax

```
(parameters) -> expression
(parameters) -> { statements; }

Examples:
() -> 42                          // No parameters
x -> x * 2                        // Single parameter (parens optional)
(x, y) -> x + y                   // Multiple parameters
(String s) -> s.length()          // Explicit type
(x, y) -> { return x + y; }       // Block body needs return
```

### Common Functional Interfaces

| Interface | Method | Takes | Returns | Use Case |
|-----------|--------|-------|---------|----------|
| `Predicate<T>` | test(T) | T | boolean | Filtering |
| `Function<T,R>` | apply(T) | T | R | Transforming |
| `Consumer<T>` | accept(T) | T | void | Side effects |
| `Supplier<T>` | get() | nothing | T | Lazy creation |
| `Comparator<T>` | compare(T,T) | T, T | int | Sorting |
| `Runnable` | run() | nothing | void | Async tasks |

### Lambda Examples

```java
// Predicate - filter
Predicate<User> isActive = user -> user.isActive();
users.stream().filter(isActive);

// Function - transform
Function<User, String> getName = user -> user.getName();
users.stream().map(getName);

// Consumer - do something
Consumer<User> sendWelcome = user -> emailService.sendWelcome(user);
users.forEach(sendWelcome);

// Comparator - sort
Comparator<User> byAge = (u1, u2) -> u1.getAge() - u2.getAge();
users.sort(byAge);
```

---

## Method References: Even Shorter

```
Lambda:                    Method Reference:
user -> user.getName()  →  User::getName
user -> process(user)   →  this::process
name -> new User(name)  →  User::new
s -> System.out.println(s) → System.out::println
```

### Four Types

```
Type                    Syntax              Example
-------------------------------------------------------
Static method           Class::method       Math::abs
Instance method         object::method      this::process
                        Class::method       User::getName
Constructor             Class::new          User::new
```

### When to Use

```
         Is it a simple single-method call?
                      |
               +------+------+
               |             |
              Yes           No
               |             |
               v             v
      Method Reference      Lambda
      User::getName         u -> u.getName().toUpperCase()
```

---

## Streams: Functional Pipelines

### The Stream Flow

```
Source         Intermediate Operations          Terminal
  |                     |                          |
  v                     v                          v
+------+    +--------+--------+--------+    +----------+
| List | →  | filter | map    | sorted | →  | collect  |
+------+    +--------+--------+--------+    +----------+
              (lazy)   (lazy)   (lazy)        (triggers)
```

**Key insight:** Intermediate operations are lazy. Nothing happens until terminal operation.

### Creating Streams

```java
// From collection
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();

// From array
String[] array = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(array);

// From values
Stream<String> stream = Stream.of("a", "b", "c");

// Infinite streams
Stream<Integer> infinite = Stream.iterate(0, n -> n + 1);
Stream<Double> randoms = Stream.generate(Math::random);
```

### Common Operations

```
Intermediate (return Stream):
+------------------+------------------------------+
| filter(pred)     | Keep elements matching pred  |
| map(func)        | Transform each element       |
| flatMap(func)    | Transform + flatten          |
| sorted()         | Sort elements                |
| distinct()       | Remove duplicates            |
| limit(n)         | Take first n                 |
| skip(n)          | Skip first n                 |
| peek(consumer)   | Debug/log (side effect)      |
+------------------+------------------------------+

Terminal (consume Stream):
+------------------+------------------------------+
| collect(coll)    | Gather into collection       |
| forEach(cons)    | Do something with each       |
| count()          | Count elements               |
| findFirst()      | Get first element (Optional) |
| anyMatch(pred)   | Any element matches?         |
| allMatch(pred)   | All elements match?          |
| reduce(func)     | Combine all into one         |
| toArray()        | Convert to array             |
+------------------+------------------------------+
```

### Stream Examples

```java
// Filter and collect
List<User> admins = users.stream()
    .filter(User::isAdmin)
    .collect(Collectors.toList());

// Map to different type
List<String> emails = users.stream()
    .map(User::getEmail)
    .collect(Collectors.toList());

// Chain operations
List<String> activeAdminNames = users.stream()
    .filter(User::isActive)
    .filter(User::isAdmin)
    .map(User::getName)
    .sorted()
    .collect(Collectors.toList());

// Reduce to single value
int totalAge = users.stream()
    .mapToInt(User::getAge)
    .sum();

// Group by
Map<String, List<User>> byDept = users.stream()
    .collect(Collectors.groupingBy(User::getDepartment));
```

---

## Optional: No More Null

### The Problem with Null

```
         +--------+
         | null   |
         +--------+
              |
     +--------+--------+
     |                 |
"Not found"      "Not initialized"
     |                 |
     |      "Error"    |
     |         |       |
     +----+----+----+--+
          |
     NullPointerException!
```

### Optional as Container

```
+-------------------+     +-------------------+
| Optional.empty()  |     | Optional.of(user) |
|                   |     |                   |
|    [nothing]      |     |    [User obj]     |
|                   |     |                   |
+-------------------+     +-------------------+
     "No value"              "Has value"
```

### Creating Optional

```java
// From value (must not be null)
Optional<User> opt = Optional.of(user);

// From nullable value
Optional<User> opt = Optional.ofNullable(maybeUser);

// Empty
Optional<User> opt = Optional.empty();
```

### Using Optional

```java
// Check and get (old style - avoid)
if (opt.isPresent()) {
    User user = opt.get();
}

// Better - ifPresent
opt.ifPresent(user -> sendEmail(user));

// Better - orElse
User user = opt.orElse(defaultUser);

// Better - orElseGet (lazy)
User user = opt.orElseGet(() -> createDefaultUser());

// Better - orElseThrow
User user = opt.orElseThrow(() -> new UserNotFoundException());

// Transform
Optional<String> name = opt.map(User::getName);

// Chain
String city = opt
    .map(User::getAddress)
    .map(Address::getCity)
    .orElse("Unknown");
```

### Disaster 1: Optional.get() Without Check

**AI code:**
```java
Optional<User> maybeUser = repository.findById(id);
User user = maybeUser.get();  // NoSuchElementException if empty!
```

**Fix:**
```java
User user = repository.findById(id)
    .orElseThrow(() -> new UserNotFoundException(id));
```

### Disaster 2: Optional as Field

**AI code:**
```java
public class User {
    private Optional<String> middleName;  // DON'T!
}
```

**Why it's wrong:**
- Serialization issues
- Increases memory usage
- Not what Optional was designed for

**Fix:**
```java
public class User {
    private String middleName;  // Can be null

    public Optional<String> getMiddleName() {
        return Optional.ofNullable(middleName);
    }
}
```

### When to Use Optional

```
              Is this a return value that might be absent?
                              |
                       +------+------+
                       |             |
                      Yes           No
                       |             |
                       v             |
                   Return          |
                   Optional        |
                       |             |
                       |             v
                       |      Is this a method parameter?
                       |             |
                       |      +------+------+
                       |      |             |
                       |     Yes           No
                       |      |             |
                       |      v             v
                       | Use overloading   Field? Use null
                       | or nullable       + getter returns Optional
```

---

## FlatMap: Unwrapping Nested Structures

### The Problem

```java
// map creates nested structure
Optional<Optional<Address>> nested = user.map(User::getAddress);

// flatMap flattens
Optional<Address> flat = user.flatMap(User::getAddress);
```

### Visualized

```
map():
+--------+     +----------------------+
| User   | →   | Optional<Optional<   |
+--------+     |     Address>>        |
               +----------------------+

flatMap():
+--------+     +------------------+
| User   | →   | Optional<Address>|
+--------+     +------------------+
```

### Stream flatMap

```java
// Each user has list of orders
// map gives Stream<List<Order>>
// flatMap gives Stream<Order>

List<Order> allOrders = users.stream()
    .flatMap(user -> user.getOrders().stream())
    .collect(Collectors.toList());
```

---

## Collectors: Gathering Results

### Common Collectors

```java
// To List
List<String> list = stream.collect(Collectors.toList());

// To Set
Set<String> set = stream.collect(Collectors.toSet());

// To Map
Map<Long, User> map = users.stream()
    .collect(Collectors.toMap(User::getId, user -> user));

// Joining strings
String names = users.stream()
    .map(User::getName)
    .collect(Collectors.joining(", "));

// Grouping
Map<String, List<User>> byDept = users.stream()
    .collect(Collectors.groupingBy(User::getDepartment));

// Partitioning (true/false groups)
Map<Boolean, List<User>> activePartition = users.stream()
    .collect(Collectors.partitioningBy(User::isActive));

// Counting per group
Map<String, Long> countByDept = users.stream()
    .collect(Collectors.groupingBy(
        User::getDepartment,
        Collectors.counting()
    ));
```

---

## Disaster 3: Stream Misuse

### Side Effects in Streams

**AI code:**
```java
List<String> results = new ArrayList<>();
users.stream()
    .filter(User::isActive)
    .forEach(u -> results.add(u.getName()));  // Side effect!
```

**Why it's bad:**
- Not thread-safe
- Defeats purpose of functional style
- Harder to understand

**Fix:**
```java
List<String> results = users.stream()
    .filter(User::isActive)
    .map(User::getName)
    .collect(Collectors.toList());
```

### Reusing Streams

**AI code:**
```java
Stream<User> stream = users.stream().filter(User::isActive);
long count = stream.count();
List<User> list = stream.collect(Collectors.toList());  // IllegalStateException!
```

**Streams can only be consumed once.**

**Fix:**
```java
List<User> activeUsers = users.stream()
    .filter(User::isActive)
    .collect(Collectors.toList());
long count = activeUsers.size();
```

---

## What to Tell AI

**Bad:** "Process the users"

**Good:** "Filter active users, map to their emails, collect to a Set for uniqueness"

**Bad:** "Handle null"

**Good:** "Return Optional from findById. Caller should use orElseThrow with UserNotFoundException"

**Bad:** "Sort the list"

**Good:** "Sort users by age descending using Comparator.comparing(User::getAge).reversed()"

**Bad:** "Use lambda"

**Good:** "Use method reference User::getName instead of lambda where possible"

---

## Red Flags in AI Output

🚩 **Optional.get() without check**
```java
optional.get()  // Can throw NoSuchElementException
```

🚩 **Optional as field or parameter**
```java
private Optional<String> name;  // Should be String + getter returns Optional
```

🚩 **Side effects in stream**
```java
.forEach(item -> externalList.add(item))  // Use collect instead
```

🚩 **Overly complex stream**
```java
.map().filter().flatMap().map().filter()...  // Consider breaking up or using loop
```

🚩 **Reusing stream**
```java
stream.count(); stream.collect();  // IllegalStateException
```

🚩 **Ignoring Optional**
```java
return optional.isPresent() ? optional.get() : null;  // Use orElse(null)
```

---

## Quick Mental Checklist

- [ ] Is Optional used only for return values?
- [ ] Is orElse/orElseThrow used instead of get()?
- [ ] Could this lambda be a method reference?
- [ ] Does this stream have side effects?
- [ ] Is this stream being reused (illegally)?
- [ ] Is the stream readable or too complex?

---

## Chapter 8 Quiz

**Q1: What's wrong with `optional.get()` directly?**
- a) Performance issue
- b) Throws NoSuchElementException if empty
- c) Returns null
- d) Nothing wrong

<details>
<summary>Answer</summary>

**b) Throws exception if empty** - Never call get() without checking. Use orElse(), orElseThrow(), or ifPresent() instead.
</details>

---

**Q2: Convert `user -> user.getName()` to method reference:**
- a) `User.getName()`
- b) `User::getName`
- c) `getName::User`
- d) `User->getName`

<details>
<summary>Answer</summary>

**b) User::getName** - Method reference syntax is `Class::methodName` for instance methods.
</details>

---

**Q3: What does flatMap do compared to map?**
- a) Makes stream flat (one dimensional)
- b) Maps and then flattens nested structures
- c) Filters flat elements
- d) Same as map but faster

<details>
<summary>Answer</summary>

**b) Maps and flattens** - map can create nested structures (Stream<Stream<T>>). flatMap transforms and flattens to single Stream<T>.
</details>

---

**Q4: Why shouldn't Optional be used as a class field?**
- a) Performance
- b) Serialization issues, not its intended purpose
- c) Compilation error
- d) Thread safety

<details>
<summary>Answer</summary>

**b) Serialization issues, wrong purpose** - Optional was designed for return values that might be absent, not for storing nullable fields.
</details>

---

**Q5: What happens when you try to use a stream twice?**
- a) Works normally
- b) IllegalStateException
- c) Returns empty
- d) NullPointerException

<details>
<summary>Answer</summary>

**b) IllegalStateException** - Streams can only be consumed once. Collect to a List if you need to process multiple times.
</details>

---

**Q6: Which is the lazy operation?**
- a) collect()
- b) forEach()
- c) filter()
- d) count()

<details>
<summary>Answer</summary>

**c) filter()** - Intermediate operations (filter, map, sorted) are lazy. Terminal operations (collect, forEach, count) trigger execution.
</details>

---

**Q7: Best way to get value from Optional or throw exception:**
- a) `opt.get()`
- b) `opt.orElse(null)`
- c) `opt.orElseThrow(() -> new NotFoundException())`
- d) `if (opt.isPresent()) return opt.get()`

<details>
<summary>Answer</summary>

**c) orElseThrow** - Clean, functional style. Explicit about what happens when absent.
</details>

---

**Q8: What does `Collectors.groupingBy(User::getDepartment)` return?**
- a) `List<User>`
- b) `Set<String>`
- c) `Map<String, List<User>>`
- d) `Map<User, String>`

<details>
<summary>Answer</summary>

**c) Map<String, List<User>>** - Groups users by department. Key is department name, value is list of users in that department.
</details>

---

**Q9: AI wrote `users.stream().forEach(u -> results.add(u))`. What's wrong?**
- a) forEach is deprecated
- b) Side effect in stream - should use collect()
- c) Missing filter
- d) Nothing wrong

<details>
<summary>Answer</summary>

**b) Side effect** - Adding to external collection from forEach is a side effect. Use `.collect(Collectors.toList())` for functional style.
</details>

---

**Q10: What's `Stream.of("a","b","c").count()` return type?**
- a) int
- b) Integer
- c) long
- d) Long

<details>
<summary>Answer</summary>

**c) long** - Stream.count() returns primitive long, not int or Integer. Streams can be very large.
</details>

---

## Key Takeaways

- **Lambdas:** Compact functions, use method references when possible
- **Streams:** Lazy pipelines, terminal operation triggers processing
- **Optional:** Return value container, not field type
- **Never call Optional.get()** without orElse/orElseThrow
- **No side effects in streams** - use collect instead
- **Streams are single-use** - collect if you need to reprocess

---

## What's Next

**Chapter 9: Prompting AI for Java**

How to write prompts that get better Java code. Specific patterns that work.

---

*"Modern Java is powerful. Misused modern Java is powerful at creating bugs."*
