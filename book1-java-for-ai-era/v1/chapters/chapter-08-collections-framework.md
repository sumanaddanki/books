# Chapter 8: Collections Framework - Organizing Your Data

> "Choose the right data structure, and your code writes itself. Choose wrong, and you'll fight it forever."

---

## What You'll Learn

- Why arrays aren't enough
- The Collections Framework hierarchy
- Lists: ArrayList, LinkedList - when to use each
- Sets: HashSet, TreeSet, LinkedHashSet
- Maps: HashMap, TreeMap, LinkedHashMap
- Choosing the right collection for your needs
- Performance implications AI might miss
- Iterating, sorting, and searching collections

---

## Why Collections?

Arrays have limitations:
- **Fixed size** - Can't grow or shrink
- **No built-in operations** - No easy add, remove, search
- **Primitive only** - Can't use generics effectively

```java
// Array limitations
int[] numbers = new int[5];
// Want to add a 6th element? Too bad!
// Want to remove an element? Shift everything yourself!
// Want to check if it contains something? Loop through!
```

Collections solve all these problems.

---

## The Collections Framework Hierarchy

```
                      Iterable
                          │
                      Collection
                    /     |     \
                 List    Set    Queue
                /   \    / \      |
         ArrayList  LinkedList  HashSet  TreeSet  PriorityQueue

                       Map (separate hierarchy)
                      /    \
                HashMap   TreeMap
```

### Core Interfaces

| Interface | Description | Order | Duplicates |
|-----------|-------------|-------|------------|
| `List` | Ordered collection | Yes (by index) | Yes |
| `Set` | Unique elements | Depends | No |
| `Queue` | FIFO processing | Yes | Yes |
| `Map` | Key-value pairs | Depends | Keys: No, Values: Yes |

---

## Lists

Lists maintain insertion order and allow duplicates.

### ArrayList

**Use when:** You need fast access by index, mostly reading

```java
import java.util.ArrayList;
import java.util.List;

List<String> names = new ArrayList<>();

// Add elements
names.add("Alice");
names.add("Bob");
names.add("Charlie");
names.add("Alice");  // Duplicates allowed!

// Access by index - O(1)
String first = names.get(0);  // "Alice"

// Check size
int size = names.size();  // 4

// Check if contains - O(n)
boolean hasAlice = names.contains("Alice");  // true

// Remove by index
names.remove(0);  // Removes "Alice"

// Remove by value (first occurrence)
names.remove("Alice");  // Removes the other "Alice"

// Iterate
for (String name : names) {
    System.out.println(name);
}
```

**Performance:**
- Access by index: O(1) - Fast!
- Add at end: O(1) amortized
- Add/remove in middle: O(n) - Slow!
- Search: O(n)

**Backed by:** Dynamic array (resizes when needed)

### LinkedList

**Use when:** You frequently add/remove from the beginning or middle

```java
import java.util.LinkedList;
import java.util.List;

List<String> names = new LinkedList<>();

names.add("Alice");
names.add("Bob");
names.addFirst("Zoe");   // Add at beginning - O(1)
names.addLast("Charlie"); // Add at end - O(1)

String first = names.getFirst();  // "Zoe"
String last = names.getLast();    // "Charlie"

names.removeFirst();  // O(1)
names.removeLast();   // O(1)
```

**Performance:**
- Access by index: O(n) - Slow!
- Add/remove at ends: O(1) - Fast!
- Add/remove in middle: O(1) after finding position
- Search: O(n)

**Backed by:** Doubly-linked list

### ArrayList vs LinkedList

| Operation | ArrayList | LinkedList |
|-----------|-----------|------------|
| get(index) | O(1) | O(n) |
| add(end) | O(1)* | O(1) |
| add(beginning) | O(n) | O(1) |
| add(middle) | O(n) | O(1)** |
| remove(index) | O(n) | O(n) |
| Memory | Less | More |

*Amortized
**After finding position

**Rule of thumb:** Use `ArrayList` unless you have a specific reason for `LinkedList`.

---

## Sets

Sets contain unique elements only.

### HashSet

**Use when:** You need uniqueness and don't care about order

```java
import java.util.HashSet;
import java.util.Set;

Set<String> uniqueNames = new HashSet<>();

uniqueNames.add("Alice");
uniqueNames.add("Bob");
uniqueNames.add("Alice");  // Ignored! Already exists

System.out.println(uniqueNames.size());  // 2

// Fast lookup - O(1)
boolean hasAlice = uniqueNames.contains("Alice");  // true

// No guaranteed order!
for (String name : uniqueNames) {
    System.out.println(name);  // Order may vary
}
```

**Performance:**
- Add: O(1) average
- Remove: O(1) average
- Contains: O(1) average

**Backed by:** Hash table

### TreeSet

**Use when:** You need sorted unique elements

```java
import java.util.TreeSet;
import java.util.Set;

Set<String> sortedNames = new TreeSet<>();

sortedNames.add("Charlie");
sortedNames.add("Alice");
sortedNames.add("Bob");

// Always sorted!
for (String name : sortedNames) {
    System.out.println(name);
}
// Output: Alice, Bob, Charlie

// Navigation methods
TreeSet<Integer> numbers = new TreeSet<>();
numbers.add(5);
numbers.add(2);
numbers.add(8);
numbers.add(1);

System.out.println(numbers.first());    // 1
System.out.println(numbers.last());     // 8
System.out.println(numbers.lower(5));   // 2 (less than 5)
System.out.println(numbers.higher(5));  // 8 (greater than 5)
```

**Performance:**
- Add: O(log n)
- Remove: O(log n)
- Contains: O(log n)

**Backed by:** Red-black tree

### LinkedHashSet

**Use when:** You need uniqueness with insertion order preserved

```java
import java.util.LinkedHashSet;
import java.util.Set;

Set<String> orderedUnique = new LinkedHashSet<>();

orderedUnique.add("Charlie");
orderedUnique.add("Alice");
orderedUnique.add("Bob");

// Maintains insertion order
for (String name : orderedUnique) {
    System.out.println(name);
}
// Output: Charlie, Alice, Bob (insertion order)
```

**Performance:** Same as HashSet, slightly more memory

### Choosing a Set

| Need | Use |
|------|-----|
| Just uniqueness, fastest | `HashSet` |
| Sorted elements | `TreeSet` |
| Insertion order + uniqueness | `LinkedHashSet` |

---

## Maps

Maps store key-value pairs. Keys are unique, values can duplicate.

### HashMap

**Use when:** You need fast key-value lookups

```java
import java.util.HashMap;
import java.util.Map;

Map<String, Integer> ages = new HashMap<>();

// Add key-value pairs
ages.put("Alice", 25);
ages.put("Bob", 30);
ages.put("Charlie", 35);

// Get value by key - O(1)
int aliceAge = ages.get("Alice");  // 25

// Get with default
int unknownAge = ages.getOrDefault("Unknown", 0);  // 0

// Check if key exists
boolean hasAlice = ages.containsKey("Alice");  // true

// Check if value exists
boolean hasAge30 = ages.containsValue(30);  // true

// Update value
ages.put("Alice", 26);  // Overwrites

// Remove
ages.remove("Bob");

// Size
int size = ages.size();  // 2

// Iterate over entries
for (Map.Entry<String, Integer> entry : ages.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}

// Iterate over keys only
for (String name : ages.keySet()) {
    System.out.println(name);
}

// Iterate over values only
for (int age : ages.values()) {
    System.out.println(age);
}
```

**Performance:**
- put: O(1) average
- get: O(1) average
- containsKey: O(1) average

### TreeMap

**Use when:** You need keys in sorted order

```java
import java.util.TreeMap;
import java.util.Map;

Map<String, Integer> sortedAges = new TreeMap<>();

sortedAges.put("Charlie", 35);
sortedAges.put("Alice", 25);
sortedAges.put("Bob", 30);

// Keys are sorted
for (String name : sortedAges.keySet()) {
    System.out.println(name);
}
// Output: Alice, Bob, Charlie

// Navigation
TreeMap<String, Integer> treeMap = (TreeMap<String, Integer>) sortedAges;
System.out.println(treeMap.firstKey());  // Alice
System.out.println(treeMap.lastKey());   // Charlie
```

**Performance:**
- put: O(log n)
- get: O(log n)
- containsKey: O(log n)

### LinkedHashMap

**Use when:** You need insertion order preserved

```java
import java.util.LinkedHashMap;
import java.util.Map;

Map<String, Integer> orderedAges = new LinkedHashMap<>();

orderedAges.put("Charlie", 35);
orderedAges.put("Alice", 25);
orderedAges.put("Bob", 30);

// Maintains insertion order
for (String name : orderedAges.keySet()) {
    System.out.println(name);
}
// Output: Charlie, Alice, Bob
```

### Choosing a Map

| Need | Use |
|------|-----|
| Fast lookups, no order | `HashMap` |
| Sorted by keys | `TreeMap` |
| Insertion order | `LinkedHashMap` |

---

## Common Operations

### Checking Empty and Size

```java
List<String> list = new ArrayList<>();

if (list.isEmpty()) {
    System.out.println("List is empty");
}

int size = list.size();
```

### Clearing Collections

```java
list.clear();  // Remove all elements
```

### Converting Between Collections

```java
// List to Set (remove duplicates)
List<String> listWithDupes = Arrays.asList("a", "b", "a", "c");
Set<String> unique = new HashSet<>(listWithDupes);

// Set to List
List<String> backToList = new ArrayList<>(unique);

// Array to List
String[] array = {"a", "b", "c"};
List<String> fromArray = new ArrayList<>(Arrays.asList(array));

// List to Array
String[] toArray = list.toArray(new String[0]);
```

### Sorting

```java
List<Integer> numbers = new ArrayList<>(Arrays.asList(3, 1, 4, 1, 5));

// Sort ascending
Collections.sort(numbers);  // [1, 1, 3, 4, 5]

// Sort descending
Collections.sort(numbers, Collections.reverseOrder());  // [5, 4, 3, 1, 1]

// Sort objects by property
List<Person> people = new ArrayList<>();
people.add(new Person("Bob", 30));
people.add(new Person("Alice", 25));

// Sort by name
Collections.sort(people, (p1, p2) -> p1.getName().compareTo(p2.getName()));

// Or using Comparator
people.sort(Comparator.comparing(Person::getName));
people.sort(Comparator.comparing(Person::getAge).reversed());
```

### Searching

```java
List<Integer> sorted = Arrays.asList(1, 2, 3, 4, 5);

// Binary search (list must be sorted!)
int index = Collections.binarySearch(sorted, 3);  // 2

// Find max/min
int max = Collections.max(sorted);  // 5
int min = Collections.min(sorted);  // 1
```

### Shuffling and Reversing

```java
Collections.shuffle(list);   // Random order
Collections.reverse(list);   // Reverse order
```

---

## Iterating Safely

### The ConcurrentModificationException Problem

```java
List<String> names = new ArrayList<>(Arrays.asList("Alice", "Bob", "Charlie"));

// WRONG - Throws ConcurrentModificationException
for (String name : names) {
    if (name.startsWith("B")) {
        names.remove(name);  // Modifying while iterating!
    }
}
```

### Solution 1: Use Iterator

```java
Iterator<String> iterator = names.iterator();
while (iterator.hasNext()) {
    String name = iterator.next();
    if (name.startsWith("B")) {
        iterator.remove();  // Safe removal
    }
}
```

### Solution 2: Use removeIf (Java 8+)

```java
names.removeIf(name -> name.startsWith("B"));
```

### Solution 3: Create New Collection

```java
List<String> filtered = names.stream()
    .filter(name -> !name.startsWith("B"))
    .collect(Collectors.toList());
```

---

## Hands-On Project: Inventory Management System

```java
import java.util.*;

public class Inventory {
    private Map<String, Product> products = new HashMap<>();
    private Map<String, Integer> stock = new HashMap<>();

    public void addProduct(Product product, int quantity) {
        products.put(product.getId(), product);
        stock.put(product.getId(), stock.getOrDefault(product.getId(), 0) + quantity);
    }

    public void removeStock(String productId, int quantity) {
        int current = stock.getOrDefault(productId, 0);
        if (quantity > current) {
            throw new IllegalArgumentException("Not enough stock");
        }
        stock.put(productId, current - quantity);
    }

    public List<Product> getLowStockProducts(int threshold) {
        List<Product> lowStock = new ArrayList<>();
        for (Map.Entry<String, Integer> entry : stock.entrySet()) {
            if (entry.getValue() < threshold) {
                lowStock.add(products.get(entry.getKey()));
            }
        }
        return lowStock;
    }

    public Set<String> getCategories() {
        Set<String> categories = new TreeSet<>();  // Sorted
        for (Product product : products.values()) {
            categories.add(product.getCategory());
        }
        return categories;
    }

    public Map<String, List<Product>> getProductsByCategory() {
        Map<String, List<Product>> byCategory = new HashMap<>();
        for (Product product : products.values()) {
            byCategory
                .computeIfAbsent(product.getCategory(), k -> new ArrayList<>())
                .add(product);
        }
        return byCategory;
    }
}

class Product {
    private String id;
    private String name;
    private String category;
    private double price;

    public Product(String id, String name, String category, double price) {
        this.id = id;
        this.name = name;
        this.category = category;
        this.price = price;
    }

    // Getters
    public String getId() { return id; }
    public String getName() { return name; }
    public String getCategory() { return category; }
    public double getPrice() { return price; }
}
```

---

## AI Integration: Collection Mistakes

### Problem 1: Wrong Collection Type

**AI generates:**
```java
// Using LinkedList for random access
LinkedList<User> users = new LinkedList<>();
// Later...
User user = users.get(500);  // O(n) - very slow!
```

**Fix:**
```java
ArrayList<User> users = new ArrayList<>();
User user = users.get(500);  // O(1) - fast!
```

### Problem 2: Not Using Generics Properly

**AI generates:**
```java
List list = new ArrayList();  // Raw type
list.add("string");
list.add(123);  // Type mixing!
```

**Fix:**
```java
List<String> list = new ArrayList<>();
list.add("string");
// list.add(123);  // Compile error - caught early!
```

### Problem 3: Inefficient Contains Check

**AI generates:**
```java
List<String> ids = getIds();  // 10,000 elements
for (Order order : orders) {
    if (ids.contains(order.getId())) {  // O(n) each time!
        process(order);
    }
}
```

**Fix:**
```java
Set<String> ids = new HashSet<>(getIds());  // Convert once
for (Order order : orders) {
    if (ids.contains(order.getId())) {  // O(1) each time!
        process(order);
    }
}
```

---

## Exercises

### Exercise 8.1: Word Counter
Create a program that:
- Reads text and counts word frequency
- Uses a Map to store word -> count
- Prints words sorted by frequency

### Exercise 8.2: Remove Duplicates
Write a method that removes duplicates from a List while maintaining order.

### Exercise 8.3: Student Grades
Create a system that:
- Stores students and their grades (Map<Student, List<Integer>>)
- Calculates average per student
- Finds top performers

### Exercise 8.4: Set Operations
Implement union, intersection, and difference for two Sets.

### Exercise 8.5: LRU Cache
Implement a simple LRU (Least Recently Used) cache using LinkedHashMap.

---

## Key Takeaways

- **ArrayList for most cases** - Fast random access
- **HashSet for uniqueness** - O(1) contains check
- **HashMap for key-value** - Fast lookups
- **TreeMap/TreeSet for sorting** - Automatic ordering
- **LinkedHashMap/LinkedHashSet for order** - Maintains insertion order
- **Use generics** - Type safety at compile time
- **Choose based on operations** - Know your access patterns
- **Iterate safely** - Use Iterator.remove() or removeIf()

---

## What's Next

You can now organize data effectively. In **Chapter 9**, we'll explore Generics - the powerful type system that makes collections type-safe and your code reusable.

---

*"Data structures are the building blocks of algorithms. Choose wisely."*
