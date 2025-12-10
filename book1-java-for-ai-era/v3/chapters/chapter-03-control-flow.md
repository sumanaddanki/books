# Chapter 3: Control Flow - Decisions, Loops, and Streams

> "AI writes loops fast. It doesn't ask whether a loop is even the right choice."

---

## The Two Powers

Every program does two things:
1. **Makes decisions** - If this, do that
2. **Repeats actions** - Do this for each item

In Java, you have multiple ways to do both. The question isn't "how to write it" - AI handles that. The question is "which approach is right for this situation?"

---

## Part 1: Making Decisions

### If-Else: The Basics

```java
if (user.isAdmin()) {
    showAdminPanel();
} else if (user.isModerator()) {
    showModeratorPanel();
} else {
    showUserPanel();
}
```

**AI usually gets this right.** But watch for:

### Disaster 1: Missing Else

**AI code:**
```java
String status;
if (score >= 90) {
    status = "A";
} else if (score >= 80) {
    status = "B";
} else if (score >= 70) {
    status = "C";
}
return status;  // Compiler error! status might not be initialized
```

**What if score is 50?** No branch executes. `status` is never assigned.

**Fix:**
```java
String status;
if (score >= 90) {
    status = "A";
} else if (score >= 80) {
    status = "B";
} else if (score >= 70) {
    status = "C";
} else {
    status = "F";  // Default case
}
```

### Switch: Traditional vs Modern

**Traditional switch (pre-Java 14):**
```java
String day;
switch (dayNum) {
    case 1:
        day = "Monday";
        break;  // Forget this = bug
    case 2:
        day = "Tuesday";
        break;
    default:
        day = "Unknown";
}
```

**Modern switch (Java 14+):**
```java
String day = switch (dayNum) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    case 4 -> "Thursday";
    case 5 -> "Friday";
    case 6, 7 -> "Weekend";
    default -> "Unknown";
};
```

**Modern is better:**
- No fall-through bugs (no `break` needed)
- Expression returns value directly
- Multiple cases in one line

**AI sometimes generates old-style switch.** If using Java 14+, ask for modern syntax.

---

## Part 2: Loops - Traditional Style

### For Loop: When You Know the Count

```java
for (int i = 0; i < 10; i++) {
    System.out.println(i);
}
```

**Components:**
- `int i = 0` - Initialize
- `i < 10` - Continue condition
- `i++` - After each iteration

### Disaster 2: Off-By-One

**AI code:**
```java
// Process 10 items
for (int i = 0; i <= items.size(); i++) {
    process(items.get(i));
}
```

**The bug:** `<=` should be `<`. This processes 11 items (0 through 10), and `items.get(10)` on a 10-item list throws IndexOutOfBoundsException.

**Correct:**
```java
for (int i = 0; i < items.size(); i++) {  // < not <=
    process(items.get(i));
}
```

### Enhanced For Loop: Iterating Collections

```java
for (String name : names) {
    System.out.println(name);
}
```

**Cleaner than index-based loop when you don't need the index.**

### While Loop: Condition-Based

```java
while (hasMoreData()) {
    process(readNext());
}
```

**Use when:** You don't know how many iterations upfront.

### Disaster 3: Infinite Loop

**AI code:**
```java
int i = 0;
while (i < 10) {
    if (isSpecial(i)) {
        continue;  // Skip this one
    }
    process(i);
    i++;  // Never reached if isSpecial!
}
```

**The bug:** If `isSpecial(i)` is true, `continue` skips `i++`. Loop never advances.

**Fix:**
```java
int i = 0;
while (i < 10) {
    int current = i;
    i++;  // Increment first
    if (isSpecial(current)) {
        continue;
    }
    process(current);
}
```

---

## Part 3: Streams - Modern Java Approach

Java 8 introduced streams for functional-style operations on collections.

### Traditional vs Stream

**Traditional:**
```java
List<String> activeUserEmails = new ArrayList<>();
for (User user : users) {
    if (user.isActive()) {
        activeUserEmails.add(user.getEmail());
    }
}
```

**Stream:**
```java
List<String> activeUserEmails = users.stream()
    .filter(User::isActive)
    .map(User::getEmail)
    .collect(Collectors.toList());
```

### Common Stream Operations

| Operation | Purpose | Example |
|-----------|---------|---------|
| `filter()` | Keep items matching condition | `.filter(u -> u.isActive())` |
| `map()` | Transform each item | `.map(User::getEmail)` |
| `sorted()` | Sort items | `.sorted(Comparator.comparing(User::getName))` |
| `distinct()` | Remove duplicates | `.distinct()` |
| `limit()` | Take first N | `.limit(10)` |
| `findFirst()` | Get first match | `.findFirst()` |
| `collect()` | Gather results | `.collect(Collectors.toList())` |
| `forEach()` | Do something with each | `.forEach(System.out::println)` |
| `count()` | Count items | `.count()` |
| `anyMatch()` | Check if any match | `.anyMatch(User::isAdmin)` |

### When to Use Streams vs Loops

| Use Streams When | Use Loops When |
|------------------|----------------|
| Filtering/transforming data | Need to break/return early |
| Chain of operations | Need index access |
| Code reads like "what" not "how" | Performance-critical tight loops |
| No side effects needed | Modifying external state |
| Working with collections | Complex control flow |

### Disaster 4: Stream Overkill

**AI code:**
```java
// Just need to check if any user is admin
boolean hasAdmin = users.stream()
    .filter(u -> u.getRole().equals("admin"))
    .map(u -> true)
    .findFirst()
    .orElse(false);
```

**Overcomplicated.** Simpler:
```java
boolean hasAdmin = users.stream()
    .anyMatch(u -> u.getRole().equals("admin"));
```

**Or even:**
```java
boolean hasAdmin = users.stream()
    .anyMatch(u -> "admin".equals(u.getRole()));  // Null-safe
```

### Disaster 5: Stream When Loop is Better

**AI code:**
```java
// Find user and update their status
users.stream()
    .filter(u -> u.getId().equals(targetId))
    .findFirst()
    .ifPresent(u -> {
        u.setStatus("active");
        userRepository.save(u);
        auditLog.record("User activated: " + u.getId());
    });
```

**Problems:**
- Side effects inside stream (save, log)
- Hard to add error handling
- Can't easily return early

**Better as loop:**
```java
for (User user : users) {
    if (user.getId().equals(targetId)) {
        user.setStatus("active");
        userRepository.save(user);
        auditLog.record("User activated: " + user.getId());
        return;  // Clear exit
    }
}
throw new UserNotFoundException(targetId);  // Clear failure path
```

### Disaster 6: Modifying Collection While Streaming

**AI code:**
```java
users.stream()
    .filter(User::isInactive)
    .forEach(u -> users.remove(u));  // ConcurrentModificationException!
```

**Fix with collect:**
```java
List<User> inactiveUsers = users.stream()
    .filter(User::isInactive)
    .collect(Collectors.toList());
users.removeAll(inactiveUsers);
```

**Or use removeIf:**
```java
users.removeIf(User::isInactive);
```

---

## Part 4: Method References

Shorthand for simple lambdas:

| Lambda | Method Reference |
|--------|------------------|
| `u -> u.getName()` | `User::getName` |
| `s -> System.out.println(s)` | `System.out::println` |
| `s -> new User(s)` | `User::new` |
| `s -> this.process(s)` | `this::process` |

**AI sometimes uses verbose lambdas when method references are cleaner.**

---

## What to Tell AI

**Bad:** "Loop through users"

**Good:** "Filter active users and get their emails using Java streams"

**Bad:** "Check each item"

**Good:** "Use anyMatch to check if any user is admin - return early if found"

**Bad:** "Process the list"

**Good:** "Use a traditional for-loop because I need to break early and handle errors for each item"

---

## Red Flags in AI Output

🚩 **Old-style switch when using Java 14+**
```java
switch(x) { case 1: ... break; }  // Use arrow syntax
```

🚩 **Off-by-one in loop bounds**
```java
for (int i = 0; i <= list.size(); i++)  // Should be <
```

🚩 **Side effects in streams**
```java
.forEach(item -> database.save(item))  // Use loop for side effects
```

🚩 **Overcomplicated stream**
```java
.filter().map().filter().map().collect()  // Can this be simpler?
```

🚩 **Modifying collection during iteration**
```java
for (Item i : items) { items.remove(i); }  // ConcurrentModificationException
```

🚩 **No exit condition in while loop**
```java
while (true) { ... }  // How does this ever stop?
```

---

## Quick Mental Checklist

- [ ] Does the loop have a guaranteed exit condition?
- [ ] Is it `<` or `<=`? Which is correct for this range?
- [ ] Am I modifying the collection I'm iterating?
- [ ] Would a stream be cleaner here? Or would a loop?
- [ ] Does the stream have side effects that belong in a loop?
- [ ] Is there a built-in method (anyMatch, removeIf) for this?

---

## Chapter 3 Quiz

**Q1: What's wrong with this loop?**
```java
for (int i = 1; i <= items.size(); i++) {
    process(items.get(i));
}
```
- a) Should start at 0
- b) Should use `<` not `<=`
- c) Both a and b
- d) Nothing wrong

<details>
<summary>Answer</summary>

**c) Both** - Arrays/Lists are 0-indexed. Starting at 1 skips first item. Using `<=` goes one past the end, causing IndexOutOfBoundsException.
</details>

---

**Q2: When should you prefer a traditional loop over streams?**
- a) When filtering data
- b) When you need to break early or handle errors
- c) When transforming each element
- d) When collecting results

<details>
<summary>Answer</summary>

**b) Break early or handle errors** - Streams don't support break/continue. For complex control flow or try-catch per item, use loops.
</details>

---

**Q3: What's the output?**
```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);
long result = nums.stream()
    .filter(n -> n > 2)
    .count();
System.out.println(result);
```
- a) 2
- b) 3
- c) 5
- d) 12

<details>
<summary>Answer</summary>

**b) 3** - Filter keeps 3, 4, 5 (three numbers greater than 2). `count()` returns 3.
</details>

---

**Q4: AI generated this. What's the problem?**
```java
users.stream()
    .filter(u -> u.isActive())
    .forEach(u -> {
        u.setLastSeen(now);
        userRepository.save(u);
    });
```
- a) Nothing wrong
- b) Should use method reference
- c) Side effects (save) should be in a loop, not stream
- d) filter() is unnecessary

<details>
<summary>Answer</summary>

**c) Side effects in stream** - Database saves are side effects. They belong in a traditional loop where you can handle errors, transactions, and logging properly.
</details>

---

**Q5: What's the modern switch syntax for Java 14+?**
- a) `switch(x) { case 1: return "one"; break; }`
- b) `switch(x) { case 1 -> "one"; }`
- c) `match(x) { case 1 => "one" }`
- d) `when(x) { 1 -> "one" }`

<details>
<summary>Answer</summary>

**b) Arrow syntax** - `case 1 -> "one"` is the modern expression syntax. No break needed, returns value directly.
</details>

---

**Q6: How do you safely remove items from a list while iterating?**
- a) Use for-each and call remove()
- b) Use stream().forEach() with remove()
- c) Use removeIf() or collect items to remove first
- d) Not possible in Java

<details>
<summary>Answer</summary>

**c) removeIf() or collect first** - Direct removal during iteration causes ConcurrentModificationException. Use `list.removeIf(condition)` or collect items first, then removeAll.
</details>

---

**Q7: What does `users.stream().anyMatch(User::isAdmin)` return?**
- a) List of admin users
- b) Count of admin users
- c) true if any user is admin, false otherwise
- d) The first admin user found

<details>
<summary>Answer</summary>

**c) Boolean - true if any match** - `anyMatch()` short-circuits on first match and returns boolean. For first match itself, use `filter().findFirst()`.
</details>

---

**Q8: What's the method reference equivalent of `u -> u.getName()`?**
- a) `User.getName()`
- b) `User::getName`
- c) `User->getName`
- d) `getName::User`

<details>
<summary>Answer</summary>

**b) User::getName** - Double colon syntax. `Class::instanceMethod` calls that method on each element.
</details>

---

**Q9: This loop runs forever. Why?**
```java
int i = 0;
while (i < 5) {
    if (i == 3) continue;
    System.out.println(i);
    i++;
}
```
- a) Should use `for` loop
- b) `continue` skips `i++`, so i stays 3 forever
- c) `while` loops can't use `continue`
- d) `i < 5` is always true

<details>
<summary>Answer</summary>

**b) continue skips i++** - When i reaches 3, `continue` jumps to condition check. `i++` is never executed, so i stays 3 forever.
</details>

---

**Q10: Which is more readable for "get names of active users sorted alphabetically"?**
- a) Traditional for loop with if statement and Collections.sort()
- b) `users.stream().filter(User::isActive).map(User::getName).sorted().collect(toList())`
- c) Both are equally readable
- d) Neither - use SQL

<details>
<summary>Answer</summary>

**b) Stream** - For filter-map-sort-collect pipelines, streams read like the requirement: "filter active, map to names, sort, collect." This is streams' sweet spot.
</details>

---

## Key Takeaways

- **Loops:** Watch for off-by-one errors and infinite loops
- **Modern switch:** Use arrow syntax in Java 14+ (cleaner, no fall-through)
- **Streams:** Great for filter/map/collect chains without side effects
- **Traditional loops:** Better for break/continue, errors, and side effects
- **Method references:** Use `User::getName` not `u -> u.getName()`
- **Don't modify collections while iterating** - use removeIf or collect first

---

## What's Next

**Chapter 4: OOP Foundations**

Classes, objects, constructors in Java. Why AI creates unnecessary objects. What `static` really means.

---

*"The right control structure makes code readable. The wrong one makes bugs invisible."*
