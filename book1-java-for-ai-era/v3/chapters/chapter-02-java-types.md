# Chapter 2: Java Types - The Foundation of Everything

> "Every Java bug I've debugged started with someone not thinking clearly about their data types."

---

## Why Types Matter

Before any code exists, you must answer: **"What kind of data am I working with?"**

Get this wrong and you'll face:
- Money that vanishes (floating-point errors)
- Counters that go negative (overflow)
- Comparisons that fail mysteriously (String pool)
- NullPointerExceptions everywhere (primitives vs objects)

AI doesn't think about types. It picks what looks reasonable. You need to know when "reasonable" is wrong.

---

## Primitives: The Building Blocks

Java has exactly 8 primitive types. They hold values directly in memory.

| Type | Size | Range | Use Case |
|------|------|-------|----------|
| `byte` | 8 bits | -128 to 127 | Raw data, files |
| `short` | 16 bits | -32,768 to 32,767 | Rarely used |
| `int` | 32 bits | ±2.1 billion | Default for whole numbers |
| `long` | 64 bits | ±9.2 quintillion | Large counts, timestamps |
| `float` | 32 bits | ~7 decimal digits | Graphics, games |
| `double` | 64 bits | ~15 decimal digits | Scientific calculations |
| `boolean` | 1 bit | true/false | Flags, conditions |
| `char` | 16 bits | Unicode character | Single characters |

### The Key Insight

**Primitives cannot be null.** They always have a value.

```java
int count;      // Has undefined value (compiler may warn)
int count = 0;  // Has value 0
```

---

## Wrapper Classes: Objects for Primitives

Each primitive has an object wrapper:

| Primitive | Wrapper |
|-----------|---------|
| `int` | `Integer` |
| `long` | `Long` |
| `double` | `Double` |
| `boolean` | `Boolean` |
| `char` | `Character` |

### Why Wrappers Exist

1. **Can be null** - Useful for "no value" scenarios
2. **Work with generics** - `List<Integer>` works, `List<int>` doesn't
3. **Have utility methods** - `Integer.parseInt()`, `Integer.MAX_VALUE`

### The Autoboxing Trap

Java automatically converts between primitives and wrappers:

```java
Integer count = 5;        // Autoboxing: int → Integer
int value = count;        // Unboxing: Integer → int
```

**Disaster scenario:**

```java
Integer count = null;
int value = count;        // NullPointerException!
```

AI often uses `Integer` when `int` would be safer.

---

## Numbers: When AI Gets It Wrong

### Disaster 1: Integer Overflow

**The scenario:**
Video view counter on a popular platform.

**AI code:**
```java
int viewCount = 0;
// ... video goes viral ...
viewCount++;  // Eventually reaches 2,147,483,647
viewCount++;  // Becomes -2,147,483,648 (overflow!)
```

**What you need to know:**
- `int` max: ~2.1 billion
- `long` max: ~9.2 quintillion
- For counters that could grow large: use `long`

```java
long viewCount = 0L;  // Safe for billions of views
```

### Disaster 2: The Money That Vanished

**The scenario:**
E-commerce transaction calculations.

**AI code:**
```java
double price = 19.99;
double quantity = 3;
double total = price * quantity;  // Expect: 59.97
System.out.println(total);        // Actual: 59.96999999999999
```

**Why it happens:**
`double` uses binary floating-point. It cannot represent 0.1 exactly.

```java
System.out.println(0.1 + 0.2);  // 0.30000000000000004
System.out.println(0.1 + 0.2 == 0.3);  // false!
```

**The fix - BigDecimal:**
```java
BigDecimal price = new BigDecimal("19.99");
BigDecimal quantity = new BigDecimal("3");
BigDecimal total = price.multiply(quantity);  // Exactly 59.97
```

**Rule:** Money = BigDecimal. Always.

### Disaster 3: Integer Division

**AI code:**
```java
int score = 7;
int total = 10;
double percentage = score / total;  // Expect: 0.7
System.out.println(percentage);      // Actual: 0.0
```

**Why:** `7 / 10` is integer division = 0. Then 0 is converted to 0.0.

**Fix:**
```java
double percentage = (double) score / total;  // 0.7
// OR
double percentage = score / (double) total;  // 0.7
```

---

## Strings: Not What You Think

### String Immutability

Strings cannot be changed. Every "modification" creates a new String.

```java
String name = "John";
name.toUpperCase();           // Returns "JOHN", but name is still "John"
name = name.toUpperCase();    // Now name is "JOHN"
```

### The String Pool

Java caches string literals:

```java
String a = "hello";
String b = "hello";
System.out.println(a == b);   // true - same object from pool

String c = new String("hello");
System.out.println(a == c);   // false - different objects!
System.out.println(a.equals(c));  // true - same content
```

**Rule:** Compare strings with `.equals()`, never `==`.

### Disaster 4: String Comparison Gone Wrong

**AI code:**
```java
public boolean isAdmin(String role) {
    return role == "admin";  // BUG!
}
```

**Why it fails:**
If `role` comes from user input or database, it's not from the string pool.

```java
String userRole = getUserRoleFromDB();  // Returns "admin"
isAdmin(userRole);  // Returns false! Different object.
```

**Fix:**
```java
public boolean isAdmin(String role) {
    return "admin".equals(role);  // Also null-safe!
}
```

---

## Null: The Billion Dollar Mistake

Tony Hoare, inventor of null, called it his "billion dollar mistake."

### Primitives vs Wrappers

```java
int count = 0;        // Always has a value
Integer count = null; // Can be "no value"
```

### Disaster 5: Null Surprise

**AI code:**
```java
public int getAge(User user) {
    return user.getAge();
}
```

**If user is null:** NullPointerException

**If getAge() returns Integer and is null:** NullPointerException during unboxing

**Defensive approach:**
```java
public int getAge(User user) {
    if (user == null) {
        return 0;  // Or throw meaningful exception
    }
    Integer age = user.getAge();
    return age != null ? age : 0;
}
```

**Modern approach - Optional:**
```java
public Optional<Integer> getAge(User user) {
    return Optional.ofNullable(user)
                   .map(User::getAge);
}
```

---

## Type Casting

### Safe (Widening): Small → Large

```java
int small = 100;
long big = small;     // Automatic, safe
double precise = big;  // Automatic, safe
```

### Dangerous (Narrowing): Large → Small

```java
long big = 3_000_000_000L;
int small = (int) big;  // -1294967296 (overflow!)

double precise = 9.99;
int rounded = (int) precise;  // 9 (truncated, not rounded!)
```

**AI often forgets casting issues.** Always check if values could overflow.

---

## What to Tell AI

**Bad:** "Create a price variable"

**Good:** "Create a price variable using BigDecimal for exact decimal arithmetic"

**Bad:** "Create a counter"

**Good:** "Create a view counter using long to handle billions of views"

**Bad:** "Compare the strings"

**Good:** "Compare strings using .equals() method, handling potential null values"

**Bad:** "Store the user's age"

**Good:** "Store age as Integer (nullable) since some users haven't provided it"

---

## Red Flags in AI Output

🚩 **`double` or `float` for money**
```java
double balance = 1000.50;  // NO!
```

🚩 **`int` for potentially large counts**
```java
int totalUsers = 0;  // Could overflow
```

🚩 **String comparison with `==`**
```java
if (status == "active")  // NO!
```

🚩 **No null checks before unboxing**
```java
int value = someInteger;  // NPE if null
```

🚩 **Integer division when decimal needed**
```java
double avg = sum / count;  // Loses decimals
```

---

## Quick Mental Checklist

Before accepting AI code with data:

- [ ] Is this money? → Must be `BigDecimal`
- [ ] Could this number exceed 2 billion? → Use `long`
- [ ] Is this comparing strings? → Must use `.equals()`
- [ ] Could this be null? → Check before using
- [ ] Is there division? → Watch for integer division
- [ ] Is there casting? → Check for overflow/truncation

---

## Chapter 2 Quiz

**Q1: AI generated `double accountBalance = 1000.00;` for a banking app. What should you do?**
- a) Accept it - double is precise enough
- b) Change to `float` to save memory
- c) Change to `BigDecimal` for exact calculations
- d) Change to `int` and store cents

<details>
<summary>Answer</summary>

**c) BigDecimal** - For money, floating-point errors are unacceptable. `BigDecimal` provides exact decimal arithmetic.
</details>

---

**Q2: What's the output?**
```java
String a = "test";
String b = new String("test");
System.out.println(a == b);
```
- a) true
- b) false
- c) Compilation error
- d) Runtime exception

<details>
<summary>Answer</summary>

**b) false** - `a` is from the string pool, `b` is a new object. They're different objects even with same content. Use `.equals()` to compare content.
</details>

---

**Q3: What happens here?**
```java
Integer count = null;
int value = count;
```
- a) value becomes 0
- b) value becomes null
- c) NullPointerException
- d) Compilation error

<details>
<summary>Answer</summary>

**c) NullPointerException** - Unboxing null throws NPE. Always check wrapper objects for null before unboxing.
</details>

---

**Q4: AI used `int` for a video view counter. The video goes viral with 3 billion views. What happens?**
- a) Counter shows 3,000,000,000
- b) Counter shows a large negative number
- c) Counter stops at Integer.MAX_VALUE
- d) OutOfMemoryError

<details>
<summary>Answer</summary>

**b) Large negative number** - Integer overflow wraps around. 2.1 billion + more = negative numbers. Use `long` for large counters.
</details>

---

**Q5: What's the output?**
```java
System.out.println(7 / 10);
```
- a) 0.7
- b) 0
- c) 1
- d) 0.0

<details>
<summary>Answer</summary>

**b) 0** - Integer division truncates. 7/10 = 0 with remainder 7. Cast to double first for decimal result.
</details>

---

**Q6: Why should you use `"admin".equals(role)` instead of `role.equals("admin")`?**
- a) It's faster
- b) It's null-safe - won't throw NPE if role is null
- c) It uses less memory
- d) No difference

<details>
<summary>Answer</summary>

**b) Null-safe** - If `role` is null, `role.equals()` throws NPE. But `"admin".equals(null)` safely returns false.
</details>

---

**Q7: When should you use `Integer` instead of `int`?**
- a) Always - Integer is more modern
- b) When you need null to represent "no value"
- c) When the number could be very large
- d) Never - int is always better

<details>
<summary>Answer</summary>

**b) When null represents "no value"** - `int` always has a value. `Integer` can be null, useful for optional fields like "age not provided."
</details>

---

**Q8: What's wrong with this AI-generated code?**
```java
public double calculateAverage(int[] numbers) {
    int sum = 0;
    for (int n : numbers) sum += n;
    return sum / numbers.length;
}
```
- a) Should use long for sum
- b) Integer division loses decimal precision
- c) Doesn't handle empty array
- d) All of the above

<details>
<summary>Answer</summary>

**d) All of the above** - Sum could overflow with large arrays, integer division loses decimals, and empty array causes division by zero.
</details>

---

**Q9: What does String immutability mean for this code?**
```java
String name = "john";
name.toUpperCase();
System.out.println(name);
```
- a) Prints "JOHN"
- b) Prints "john"
- c) Compilation error
- d) Runtime exception

<details>
<summary>Answer</summary>

**b) Prints "john"** - Strings are immutable. `toUpperCase()` returns a new String; it doesn't modify the original. Must use `name = name.toUpperCase()`.
</details>

---

**Q10: What's the safest way to compare two strings that might be null?**
- a) `str1 == str2`
- b) `str1.equals(str2)`
- c) `Objects.equals(str1, str2)`
- d) `str1.compareTo(str2) == 0`

<details>
<summary>Answer</summary>

**c) Objects.equals(str1, str2)** - Handles null safely. Returns true if both null, false if one is null, and uses `.equals()` if both non-null.
</details>

---

## Key Takeaways

- **Money = BigDecimal**, never `double` or `float`
- **Large counts = `long`**, not `int` (max 2.1 billion)
- **String comparison = `.equals()`**, never `==`
- **Wrappers can be null**, primitives cannot
- **Integer division truncates** - cast for decimals
- **AI often picks wrong types** - always verify

---

## What's Next

**Chapter 3: Control Flow**

Decisions and loops in Java. When to use traditional loops, when to use streams. What AI gets wrong about iteration.

---

*"The right type prevents bugs. The wrong type hides them until production."*
