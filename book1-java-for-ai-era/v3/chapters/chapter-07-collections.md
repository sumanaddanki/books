# Chapter 7: Collections - The Right Data Structure

> "AI defaults to ArrayList. It doesn't ask if you need fast lookup, ordering, or uniqueness."

---

## The Collections Hierarchy

```
                        Iterable
                            |
                        Collection
                            |
            +---------------+---------------+
            |               |               |
          List            Set            Queue
            |               |               |
     +------+------+   +---+---+       +---+---+
     |      |      |   |       |       |       |
ArrayList  LinkedList  HashSet TreeSet  PriorityQueue
     |                     |
  Vector               LinkedHashSet
```

```
                          Map
                            |
              +-------------+-------------+
              |             |             |
          HashMap      TreeMap      LinkedHashMap
              |
        Hashtable
```

---

## List: Ordered Collection

### ArrayList vs LinkedList

```
ArrayList:
+---+---+---+---+---+---+---+---+
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |  Contiguous memory
+---+---+---+---+---+---+---+---+
  ^
  | Direct access by index: O(1)

LinkedList:
+---+    +---+    +---+    +---+
| A |--->| B |--->| C |--->| D |  Nodes with pointers
+---+    +---+    +---+    +---+
  ^
  | Must traverse to find: O(n)
```

### When to Use What

| Operation | ArrayList | LinkedList |
|-----------|-----------|------------|
| Get by index | O(1) | O(n) |
| Add at end | O(1)* | O(1) |
| Add at start | O(n) | O(1) |
| Add in middle | O(n) | O(1)** |
| Search | O(n) | O(n) |
| Memory | Compact | More overhead |

*Amortized - occasional resize
**Once you have the position

### Decision Flow

```
         Need List?
              |
              v
    Random access by index?
              |
       +------+------+
       |             |
      Yes           No
       |             |
       v             v
  ArrayList    Frequent add/remove
                  at start?
                     |
              +------+------+
              |             |
             Yes           No
              |             |
              v             v
        LinkedList     ArrayList
```

### Disaster 1: ArrayList for Queue Operations

**AI code:**
```java
List<Task> taskQueue = new ArrayList<>();

// Adding tasks at end - OK
taskQueue.add(newTask);

// Processing tasks from front - SLOW!
while (!taskQueue.isEmpty()) {
    Task task = taskQueue.remove(0);  // O(n) - shifts all elements!
    process(task);
}
```

**With 100,000 tasks:** Each remove shifts remaining elements. Total: ~5 billion operations.

**Fix - Use proper Queue:**
```java
Queue<Task> taskQueue = new LinkedList<>();

taskQueue.add(newTask);

while (!taskQueue.isEmpty()) {
    Task task = taskQueue.poll();  // O(1)
    process(task);
}
```

---

## Set: Unique Elements

### HashSet vs TreeSet vs LinkedHashSet

```
HashSet - No order guaranteed:
{ "Dog", "Cat", "Bird" } → Could iterate as Cat, Bird, Dog

TreeSet - Sorted order:
{ "Dog", "Cat", "Bird" } → Always iterates as Bird, Cat, Dog

LinkedHashSet - Insertion order:
{ "Dog", "Cat", "Bird" } → Always iterates as Dog, Cat, Bird
```

### Performance

| Operation | HashSet | TreeSet | LinkedHashSet |
|-----------|---------|---------|---------------|
| Add | O(1) | O(log n) | O(1) |
| Contains | O(1) | O(log n) | O(1) |
| Remove | O(1) | O(log n) | O(1) |
| Iterate order | None | Sorted | Insertion |

### Decision Flow

```
           Need unique elements?
                    |
                    v
               Need sorted?
                    |
             +------+------+
             |             |
            Yes           No
             |             |
             v             v
          TreeSet     Need insertion order?
                           |
                    +------+------+
                    |             |
                   Yes           No
                    |             |
                    v             v
             LinkedHashSet    HashSet
                              (fastest)
```

### Disaster 2: ArrayList for Uniqueness

**AI code:**
```java
List<Long> processedIds = new ArrayList<>();

for (Event event : events) {
    if (!processedIds.contains(event.getId())) {  // O(n) each time!
        processedIds.add(event.getId());
        process(event);
    }
}
```

**With 50,000 events:** contains() scans entire list each time. Increasingly slow.

**Fix - Use Set:**
```java
Set<Long> processedIds = new HashSet<>();

for (Event event : events) {
    if (processedIds.add(event.getId())) {  // O(1) - returns false if already present
        process(event);
    }
}
```

---

## Map: Key-Value Pairs

### HashMap vs TreeMap vs LinkedHashMap

```
HashMap - No order:
{"c"→3, "a"→1, "b"→2} → Could iterate as a→1, c→3, b→2

TreeMap - Sorted by key:
{"c"→3, "a"→1, "b"→2} → Always iterates as a→1, b→2, c→3

LinkedHashMap - Insertion order:
{"c"→3, "a"→1, "b"→2} → Always iterates as c→3, a→1, b→2
```

### Performance

| Operation | HashMap | TreeMap | LinkedHashMap |
|-----------|---------|---------|---------------|
| Put | O(1) | O(log n) | O(1) |
| Get | O(1) | O(log n) | O(1) |
| Remove | O(1) | O(log n) | O(1) |
| Key order | None | Sorted | Insertion |

### Decision Flow

```
           Need key→value mapping?
                    |
                    v
           Need keys sorted?
                    |
             +------+------+
             |             |
            Yes           No
             |             |
             v             v
          TreeMap    Need insertion order?
                           |
                    +------+------+
                    |             |
                   Yes           No
                    |             |
                    v             v
             LinkedHashMap   HashMap
                             (fastest)
```

### Disaster 3: Wrong Map for Sorted Display

**AI code:**
```java
Map<String, Integer> scores = new HashMap<>();
scores.put("Charlie", 85);
scores.put("Alice", 90);
scores.put("Bob", 80);

// Display leaderboard... but order is random!
for (Map.Entry<String, Integer> entry : scores.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

**Output could be:** Bob: 80, Charlie: 85, Alice: 90 (random order)

**Fix - Use TreeMap for sorted keys:**
```java
Map<String, Integer> scores = new TreeMap<>();
// ... same additions ...
// Now iterates in alphabetical order: Alice, Bob, Charlie
```

**Or sort by value:**
```java
scores.entrySet().stream()
    .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
    .forEach(e -> System.out.println(e.getKey() + ": " + e.getValue()));
// Output: Alice: 90, Charlie: 85, Bob: 80
```

---

## Queue and Deque

```
Queue (FIFO - First In First Out):
   add/offer →  [A][B][C][D]  → poll/remove
   (end)                        (front)

Deque (Double-ended):
   addFirst/offerFirst ↔ [A][B][C][D] ↔ addLast/offerLast
   pollFirst/removeFirst             pollLast/removeLast
```

### Queue Operations

| Operation | Throws Exception | Returns null |
|-----------|------------------|--------------|
| Add | add(e) | offer(e) |
| Remove | remove() | poll() |
| Examine | element() | peek() |

### Common Implementations

```java
// Standard queue
Queue<Task> queue = new LinkedList<>();

// Thread-safe queue
Queue<Task> queue = new ConcurrentLinkedQueue<>();

// Bounded blocking queue
BlockingQueue<Task> queue = new ArrayBlockingQueue<>(100);

// Priority queue (sorted)
Queue<Task> queue = new PriorityQueue<>(Comparator.comparing(Task::getPriority));
```

---

## Thread-Safe Collections

### The Problem

```java
// NOT thread-safe!
Map<String, User> cache = new HashMap<>();

// Thread 1: cache.put("user1", user);
// Thread 2: cache.get("user1");
// Race condition!
```

### Solutions

```
              Need thread-safety?
                      |
               +------+------+
               |             |
              Yes           No
               |             |
               v             v
     High concurrency?    HashMap
               |          ArrayList
        +------+------+   HashSet
        |             |
       Yes           No
        |             |
        v             v
  ConcurrentHashMap   Collections.synchronizedMap()
  CopyOnWriteArrayList
```

### ConcurrentHashMap vs synchronizedMap

```
synchronizedMap:
+-------------------------+
|    One lock for all     |  One thread at a time
|    operations           |  = bottleneck
+-------------------------+

ConcurrentHashMap:
+-----+-----+-----+-----+
| Seg | Seg | Seg | Seg |  Multiple segments
| 0   | 1   | 2   | 3   |  = parallel access
+-----+-----+-----+-----+
```

---

## Common Collection Mistakes

### Mistake 1: Modifying During Iteration

```java
// WRONG - ConcurrentModificationException!
for (User user : users) {
    if (user.isInactive()) {
        users.remove(user);
    }
}

// RIGHT - Use iterator
Iterator<User> it = users.iterator();
while (it.hasNext()) {
    if (it.next().isInactive()) {
        it.remove();
    }
}

// OR - Use removeIf
users.removeIf(User::isInactive);
```

### Mistake 2: Wrong equals/hashCode

```java
// HashSet/HashMap needs proper equals AND hashCode
public class User {
    private Long id;
    private String name;

    // MUST implement both!
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof User)) return false;
        User user = (User) o;
        return Objects.equals(id, user.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

**Without proper hashCode:** Objects won't be found in HashSet/HashMap!

### Mistake 3: Mutable Keys

```java
// DANGEROUS!
Map<List<String>, Integer> map = new HashMap<>();
List<String> key = new ArrayList<>();
key.add("a");
map.put(key, 1);

key.add("b");  // Mutated key!
map.get(key);  // Returns null! Hash changed.
```

**Rule:** Map keys should be immutable.

---

## What to Tell AI

**Bad:** "Create a list of users"

**Good:** "Create a HashSet of user IDs for O(1) lookup of processed users"

**Bad:** "Store the data"

**Good:** "Use TreeMap to keep entries sorted by key for leaderboard display"

**Bad:** "Make a cache"

**Good:** "Use ConcurrentHashMap for thread-safe cache with high read concurrency"

**Bad:** "Track items"

**Good:** "Use LinkedHashMap to maintain insertion order while allowing key-based access"

---

## Red Flags in AI Output

🚩 **ArrayList with frequent contains() checks**
```java
list.contains(item)  // O(n) - consider HashSet
```

🚩 **ArrayList.remove(0) in loop**
```java
while (!list.isEmpty()) list.remove(0);  // O(n²) - use Queue
```

🚩 **HashMap when order matters**
```java
new HashMap<>();  // Order undefined - need LinkedHashMap or TreeMap?
```

🚩 **Non-thread-safe collection in concurrent code**
```java
Map<String, Data> cache = new HashMap<>();  // Race conditions
```

🚩 **Modifying collection during foreach**
```java
for (Item i : items) items.remove(i);  // ConcurrentModificationException
```

---

## Collection Selection Cheat Sheet

```
Need ordered list?
  → Access by index often? → ArrayList
  → Add/remove at ends often? → LinkedList
  → Thread-safe? → CopyOnWriteArrayList

Need unique elements?
  → Fast lookup, no order? → HashSet
  → Sorted? → TreeSet
  → Insertion order? → LinkedHashSet

Need key-value pairs?
  → Fast lookup, no order? → HashMap
  → Sorted keys? → TreeMap
  → Insertion order? → LinkedHashMap
  → Thread-safe? → ConcurrentHashMap

Need FIFO queue?
  → Standard? → LinkedList or ArrayDeque
  → Thread-safe? → ConcurrentLinkedQueue
  → Blocking? → ArrayBlockingQueue
  → Priority? → PriorityQueue
```

---

## Chapter 7 Quiz

**Q1: AI used ArrayList with frequent `contains()` checks for 50,000 items. What's the problem?**
- a) ArrayList has size limit
- b) contains() is O(n) - should use HashSet for O(1)
- c) Should use LinkedList
- d) No problem

<details>
<summary>Answer</summary>

**b) O(n) performance** - Each contains() scans entire list. With many checks, this becomes very slow. HashSet provides O(1) contains().
</details>

---

**Q2: When should you use TreeMap instead of HashMap?**
- a) When you need faster access
- b) When you need keys in sorted order
- c) When you have many entries
- d) Always - TreeMap is better

<details>
<summary>Answer</summary>

**b) Sorted key order** - TreeMap keeps keys sorted. HashMap is faster (O(1) vs O(log n)) but has no ordering.
</details>

---

**Q3: What happens with this code?**
```java
for (String s : list) {
    if (s.isEmpty()) list.remove(s);
}
```
- a) Removes empty strings
- b) ConcurrentModificationException
- c) NullPointerException
- d) Nothing - list unchanged

<details>
<summary>Answer</summary>

**b) ConcurrentModificationException** - Can't modify collection while iterating with foreach. Use iterator.remove() or removeIf().
</details>

---

**Q4: Which collection for a chat message history that needs insertion order?**
- a) HashSet
- b) TreeSet
- c) LinkedHashSet
- d) ArrayList

<details>
<summary>Answer</summary>

**d) ArrayList** - Messages aren't unique, so not a Set. ArrayList maintains insertion order and allows duplicates. LinkedHashSet would lose duplicate messages.
</details>

---

**Q5: Why is HashSet O(1) for contains() while ArrayList is O(n)?**
- a) HashSet is newer
- b) HashSet uses hash codes to jump directly to bucket
- c) ArrayList has size limit
- d) HashSet sorts elements

<details>
<summary>Answer</summary>

**b) Hash-based lookup** - HashSet computes hash code and goes directly to the bucket. ArrayList must scan through elements one by one.
</details>

---

**Q6: Thread-safe map with high concurrency needs:**
- a) HashMap
- b) Collections.synchronizedMap(new HashMap<>())
- c) ConcurrentHashMap
- d) TreeMap

<details>
<summary>Answer</summary>

**c) ConcurrentHashMap** - Uses segment locking for parallel access. synchronizedMap uses single lock = bottleneck. HashMap isn't thread-safe.
</details>

---

**Q7: What's wrong with using mutable objects as HashMap keys?**
- a) Performance issue
- b) Key's hash changes after mutation, object becomes unfindable
- c) Memory leak
- d) Nothing wrong

<details>
<summary>Answer</summary>

**b) Hash changes** - HashMap uses hashCode to locate entries. If key mutates, hash changes, but entry stays in old bucket. Key becomes unfindable.
</details>

---

**Q8: AI code does `list.remove(0)` in a while loop processing 100,000 items. What's better?**
- a) Use `list.remove(list.size()-1)` instead
- b) Use Queue with poll()
- c) Use HashSet
- d) Keep as-is

<details>
<summary>Answer</summary>

**b) Queue with poll()** - ArrayList.remove(0) shifts all elements = O(n) each time = O(n²) total. Queue.poll() is O(1) each = O(n) total.
</details>

---

**Q9: Which maintains insertion order AND allows key-based lookup?**
- a) HashMap
- b) TreeMap
- c) LinkedHashMap
- d) HashSet

<details>
<summary>Answer</summary>

**c) LinkedHashMap** - Combines HashMap's O(1) lookup with linked list maintaining insertion order. HashMap has no order guarantee.
</details>

---

**Q10: What must a class implement to work correctly in HashSet?**
- a) Comparable
- b) Serializable
- c) equals() and hashCode()
- d) toString()

<details>
<summary>Answer</summary>

**c) equals() and hashCode()** - HashSet uses hashCode() to find bucket, equals() to match object. Without both properly implemented, objects won't be found.
</details>

---

## Key Takeaways

- **ArrayList:** Random access, most common
- **HashSet:** Unique elements, O(1) lookup
- **HashMap:** Key-value, O(1) lookup
- **TreeMap/TreeSet:** Sorted order, O(log n)
- **LinkedHash*:** Maintains insertion order
- **ConcurrentHashMap:** Thread-safe with good performance
- **Don't modify during iteration** - use removeIf() or iterator

---

## What's Next

**Chapter 8: Modern Java**

Streams, lambdas, Optional, and functional interfaces. The features that make Java code concise and expressive.

---

*"The right collection makes code fast. The wrong collection makes fast code slow."*
