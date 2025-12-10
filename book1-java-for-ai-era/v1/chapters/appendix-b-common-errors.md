# Appendix B: Common Java Errors and How to Fix Them

A quick reference for the most common Java errors and their solutions.

---

## Compilation Errors

### "Cannot find symbol"

**Error:**
```
error: cannot find symbol
   System.out.println(mesage);
                      ^
  symbol:   variable mesage
```

**Causes:**
- Typo in variable/method name
- Variable not declared
- Missing import
- Variable out of scope

**Fix:**
- Check spelling
- Declare the variable before using
- Add the correct import
- Ensure variable is accessible in current scope

---

### "Incompatible types"

**Error:**
```
error: incompatible types: String cannot be converted to int
   int x = "hello";
           ^
```

**Causes:**
- Assigning wrong type to variable
- Method returns different type than expected

**Fix:**
- Use correct type
- Add explicit cast if appropriate
- Parse strings: `Integer.parseInt("123")`

---

### "Missing return statement"

**Error:**
```
error: missing return statement
}
^
```

**Causes:**
- Method has return type but doesn't return in all paths

**Fix:**
```java
// Bad
public int getValue(boolean flag) {
    if (flag) {
        return 1;
    }
    // Missing return when flag is false!
}

// Good
public int getValue(boolean flag) {
    if (flag) {
        return 1;
    }
    return 0;  // Always return
}
```

---

### "Unreported exception"

**Error:**
```
error: unreported exception IOException; must be caught or declared to be thrown
   FileReader reader = new FileReader("file.txt");
                       ^
```

**Causes:**
- Calling method that throws checked exception
- Not handling or declaring the exception

**Fix:**
```java
// Option 1: Handle it
try {
    FileReader reader = new FileReader("file.txt");
} catch (IOException e) {
    // Handle error
}

// Option 2: Declare it
public void readFile() throws IOException {
    FileReader reader = new FileReader("file.txt");
}
```

---

### "Variable might not have been initialized"

**Error:**
```
error: variable x might not have been initialized
   System.out.println(x);
                      ^
```

**Fix:**
```java
// Bad
int x;
if (condition) {
    x = 5;
}
System.out.println(x);  // x might not be set

// Good
int x = 0;  // Initialize
if (condition) {
    x = 5;
}
System.out.println(x);
```

---

## Runtime Errors

### NullPointerException

**Error:**
```
Exception in thread "main" java.lang.NullPointerException
   at Main.main(Main.java:5)
```

**Causes:**
- Calling method on null object
- Accessing field of null object
- Array is null

**Fix:**
```java
// Check for null
if (object != null) {
    object.method();
}

// Or use Optional
Optional.ofNullable(object).ifPresent(o -> o.method());
```

---

### ArrayIndexOutOfBoundsException

**Error:**
```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 5 out of bounds for length 3
```

**Causes:**
- Accessing array with invalid index
- Off-by-one error in loop

**Fix:**
```java
// Bad
int[] arr = new int[3];
arr[3] = 1;  // Index 0, 1, 2 are valid, not 3!

// Good
for (int i = 0; i < arr.length; i++) {  // Use < not <=
    // Access arr[i]
}
```

---

### ClassCastException

**Error:**
```
Exception in thread "main" java.lang.ClassCastException:
class java.lang.String cannot be cast to class java.lang.Integer
```

**Causes:**
- Invalid type cast
- Wrong assumption about object type

**Fix:**
```java
// Check type first
if (obj instanceof Integer) {
    Integer i = (Integer) obj;
}

// Or use pattern matching (Java 16+)
if (obj instanceof Integer i) {
    // Use i directly
}
```

---

### NumberFormatException

**Error:**
```
Exception in thread "main" java.lang.NumberFormatException: For input string: "abc"
```

**Causes:**
- Parsing invalid string as number

**Fix:**
```java
// Validate before parsing
String input = "abc";
try {
    int num = Integer.parseInt(input);
} catch (NumberFormatException e) {
    System.out.println("Invalid number: " + input);
}
```

---

### ConcurrentModificationException

**Error:**
```
Exception in thread "main" java.util.ConcurrentModificationException
```

**Causes:**
- Modifying collection while iterating

**Fix:**
```java
// Bad
for (String item : list) {
    if (condition) {
        list.remove(item);  // Throws!
    }
}

// Good - use Iterator
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    if (condition) {
        it.remove();  // Safe
    }
}

// Good - use removeIf
list.removeIf(item -> condition);
```

---

### StackOverflowError

**Error:**
```
Exception in thread "main" java.lang.StackOverflowError
```

**Causes:**
- Infinite recursion
- Recursion too deep

**Fix:**
```java
// Bad - infinite recursion
public int factorial(int n) {
    return n * factorial(n - 1);  // Never stops!
}

// Good - base case
public int factorial(int n) {
    if (n <= 1) return 1;  // Base case
    return n * factorial(n - 1);
}
```

---

### OutOfMemoryError

**Error:**
```
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
```

**Causes:**
- Creating too many objects
- Memory leak
- Data structure too large

**Fix:**
- Increase heap: `java -Xmx2g`
- Stream large files instead of loading
- Check for memory leaks
- Use appropriate data structures

---

## Logic Errors

### Comparing Strings with ==

**Problem:**
```java
String a = "hello";
String b = new String("hello");
if (a == b) {  // FALSE - compares references
    // Won't execute
}
```

**Fix:**
```java
if (a.equals(b)) {  // TRUE - compares content
    // Will execute
}
```

---

### Integer Division

**Problem:**
```java
double result = 5 / 2;  // Result is 2.0, not 2.5!
```

**Fix:**
```java
double result = 5.0 / 2;    // 2.5
double result = (double) 5 / 2;  // 2.5
```

---

### Forgetting break in switch

**Problem:**
```java
switch (day) {
    case 1:
        System.out.println("Monday");
        // Falls through!
    case 2:
        System.out.println("Tuesday");
}
```

**Fix:**
```java
switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
}

// Or use switch expression (Java 14+)
switch (day) {
    case 1 -> System.out.println("Monday");
    case 2 -> System.out.println("Tuesday");
}
```

---

### Assignment vs Comparison

**Problem:**
```java
if (x = 5) {  // Assignment, not comparison!
    // Compile error for int (good!)
    // But boolean would compile
}
```

**Fix:**
```java
if (x == 5) {  // Comparison
    // Correct
}
```

---

## Quick Troubleshooting Guide

| Error | First Thing to Check |
|-------|---------------------|
| NullPointerException | What's null? Add null checks |
| ArrayIndexOutOfBounds | Check loop bounds (< vs <=) |
| Cannot find symbol | Check spelling and imports |
| Incompatible types | Check type assignments |
| IOException | Add try-catch or throws |
| ClassCastException | Check instanceof before cast |
| ConcurrentModification | Use Iterator or removeIf |
| StackOverflow | Check recursion base case |
