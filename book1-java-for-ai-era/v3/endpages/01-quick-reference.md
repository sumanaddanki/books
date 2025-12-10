# Appendix A: Quick Reference

## Type Selection

| Data | Use | Not |
|------|-----|-----|
| Money | `BigDecimal` | `double`, `float` |
| Large counts | `long` | `int` |
| Nullable values | Wrapper (`Integer`) | Primitive (`int`) |
| Fixed set of values | `enum` | `String` constants |

## Collection Selection

| Need | Use |
|------|-----|
| Ordered list, random access | `ArrayList` |
| Frequent add/remove at ends | `LinkedList` |
| Unique elements, fast lookup | `HashSet` |
| Unique + sorted | `TreeSet` |
| Unique + insertion order | `LinkedHashSet` |
| Key-value, fast lookup | `HashMap` |
| Key-value, sorted keys | `TreeMap` |
| Key-value, insertion order | `LinkedHashMap` |
| Thread-safe map | `ConcurrentHashMap` |
| FIFO queue | `LinkedList` or `ArrayDeque` |

## String Comparison

```java
// WRONG
if (str == "value")

// RIGHT
if ("value".equals(str))  // Null-safe
```

## Null Handling

```java
// Optional return
public Optional<User> findById(Long id)

// Using Optional
user.orElse(defaultUser)
user.orElseThrow(() -> new NotFoundException())
user.map(User::getName).orElse("Unknown")
```

## Exception Handling

```java
// Right pattern
try (Resource r = new Resource()) {
    // use resource
} catch (SpecificException e) {
    log.error("Context: {}", id, e);
    throw new ServiceException("Message", e);  // Chain cause
}
```

## Stream Operations

```java
// Filter + Map + Collect
List<String> names = users.stream()
    .filter(User::isActive)
    .map(User::getName)
    .collect(Collectors.toList());

// Group by
Map<String, List<User>> byDept = users.stream()
    .collect(Collectors.groupingBy(User::getDepartment));
```

## Method References

| Lambda | Method Reference |
|--------|------------------|
| `u -> u.getName()` | `User::getName` |
| `s -> System.out.println(s)` | `System.out::println` |
| `s -> new User(s)` | `User::new` |

## Access Modifiers

| Modifier | Same Class | Package | Subclass | Everywhere |
|----------|------------|---------|----------|------------|
| `private` | Yes | No | No | No |
| (default) | Yes | Yes | No | No |
| `protected` | Yes | Yes | Yes | No |
| `public` | Yes | Yes | Yes | Yes |
