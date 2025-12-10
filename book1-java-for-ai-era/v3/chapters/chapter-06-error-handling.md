# Chapter 6: Error Handling - Exceptions Done Right

> "AI catches exceptions everywhere. It doesn't think about whether catching them is the right thing to do."

---

## The Exception Hierarchy

```
                    Throwable
                        |
           +------------+------------+
           |                         |
         Error                   Exception
     (Don't catch)                   |
                        +------------+------------+
                        |                         |
              RuntimeException            Checked Exceptions
              (Unchecked)                 (Must handle)
                   |                           |
         NullPointerException          IOException
         IllegalArgumentException      SQLException
         IndexOutOfBoundsException     FileNotFoundException
```

### What Goes Where

| Type | Examples | Handle? | Why |
|------|----------|---------|-----|
| **Error** | OutOfMemoryError, StackOverflowError | No | JVM is broken |
| **Checked Exception** | IOException, SQLException | Required | Recoverable failures |
| **Unchecked Exception** | NullPointerException, IllegalArgumentException | Optional | Programming bugs |

---

## The Exception Flow

```
   Code executes
        |
        v
   Exception thrown?
        |
   +----+----+
   |         |
  No        Yes
   |         |
   v         v
Continue   Is there a catch block?
   |         |
   |    +----+----+
   |    |         |
   |   No        Yes
   |    |         |
   |    v         v
   |  Propagate   Run catch block
   |  up stack         |
   |    |              v
   |    |         Is there finally?
   |    |              |
   |    |         +----+----+
   |    |         |         |
   |    |        Yes       No
   |    |         |         |
   |    |         v         |
   |    |    Run finally    |
   |    |         |         |
   v    v         v         v
        Continue or propagate
```

---

## Checked vs Unchecked

### Checked Exceptions

**Must** be handled or declared:

```java
// Compiler forces you to handle
public void readFile(String path) throws IOException {
    FileReader reader = new FileReader(path);  // throws FileNotFoundException
    // ...
}

// Or catch it
public void readFile(String path) {
    try {
        FileReader reader = new FileReader(path);
    } catch (FileNotFoundException e) {
        // Handle the error
    }
}
```

### Unchecked Exceptions (RuntimeException)

No compiler enforcement:

```java
public void process(String text) {
    int length = text.length();  // Throws NPE if text is null
    // Compiler doesn't force you to catch
}
```

### Which to Use?

```
                  Can caller recover?
                         |
              +----------+----------+
              |                     |
             Yes                   No
              |                     |
              v                     v
   Checked Exception         Unchecked Exception
   (IOException)             (IllegalArgumentException)
```

---

## The try-catch-finally Pattern

```java
try {
    // Code that might fail
    FileReader reader = new FileReader(path);
    // ...
} catch (FileNotFoundException e) {
    // Handle specific exception
    log.error("File not found: " + path);
    throw new ConfigurationException("Missing config file", e);
} catch (IOException e) {
    // Handle broader exception
    log.error("IO error", e);
} finally {
    // Always runs - cleanup
    closeResources();
}
```

### Disaster 1: Empty Catch Block

**AI code:**
```java
try {
    user = userRepository.findById(id);
} catch (Exception e) {
    // Silently swallowed!
}
return user;  // Could be null, no one knows why
```

**Problems:**
- Error is hidden
- Debugging is impossible
- System fails silently

**Fix:**
```java
try {
    user = userRepository.findById(id);
} catch (RepositoryException e) {
    log.error("Failed to find user: " + id, e);
    throw new UserNotFoundException("User not found: " + id, e);
}
```

### Disaster 2: Catching Too Broad

**AI code:**
```java
try {
    processPayment();
} catch (Exception e) {
    return "Payment failed";
}
```

**Problem:** This catches EVERYTHING:
- NullPointerException (bug in your code)
- OutOfMemoryError (JVM dying)
- Payment declined (expected business case)

**Fix - Be specific:**
```java
try {
    processPayment();
} catch (PaymentDeclinedException e) {
    log.info("Payment declined: " + e.getReason());
    return "Payment declined: " + e.getReason();
} catch (PaymentGatewayException e) {
    log.error("Gateway error", e);
    throw new ServiceUnavailableException("Payment service unavailable");
}
// Let bugs (NPE) and system errors propagate!
```

---

## try-with-resources: The Modern Way

### The Problem with Manual Cleanup

```java
FileReader reader = null;
try {
    reader = new FileReader(path);
    // use reader
} catch (IOException e) {
    // handle
} finally {
    if (reader != null) {
        try {
            reader.close();  // What if THIS throws?
        } catch (IOException e) {
            // Swallow? Log? Rethrow?
        }
    }
}
```

**Ugly and error-prone.**

### The Solution: try-with-resources

```java
try (FileReader reader = new FileReader(path)) {
    // use reader
} catch (IOException e) {
    // handle
}
// reader.close() called automatically!
```

### How It Works

```
   try (Resource r = new Resource())
                 |
                 v
         Execute try block
                 |
                 v
         Exception thrown?
                 |
        +--------+--------+
        |                 |
       No                Yes
        |                 |
        v                 v
   r.close()         r.close()
        |                 |
        v                 v
    Continue         Throw exception
                     (original + suppressed)
```

### Disaster 3: AI Forgets try-with-resources

**AI code:**
```java
public List<String> readLines(String path) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(path));
    List<String> lines = new ArrayList<>();
    String line;
    while ((line = reader.readLine()) != null) {
        lines.add(line);
    }
    reader.close();  // Never reached if exception!
    return lines;
}
```

**Resource leak:** If readLine() throws, reader never closes.

**Fix:**
```java
public List<String> readLines(String path) throws IOException {
    try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
        List<String> lines = new ArrayList<>();
        String line;
        while ((line = reader.readLine()) != null) {
            lines.add(line);
        }
        return lines;
    }
}
```

### Multiple Resources

```java
try (
    FileReader fr = new FileReader(input);
    FileWriter fw = new FileWriter(output)
) {
    // Use both
}
// Both closed automatically, in reverse order
```

---

## Creating Custom Exceptions

### When to Create

```
              Is the error domain-specific?
                         |
              +----------+----------+
              |                     |
             Yes                   No
              |                     |
              v                     v
       Create custom           Use standard
       exception               exception
```

### Custom Exception Pattern

```java
public class UserNotFoundException extends RuntimeException {

    private final Long userId;

    public UserNotFoundException(Long userId) {
        super("User not found: " + userId);
        this.userId = userId;
    }

    public UserNotFoundException(Long userId, Throwable cause) {
        super("User not found: " + userId, cause);
        this.userId = userId;
    }

    public Long getUserId() {
        return userId;
    }
}
```

### Exception Chaining

**Don't lose the original cause:**

```java
// BAD - Original exception lost
try {
    repository.save(user);
} catch (SQLException e) {
    throw new ServiceException("Failed to save user");  // Where's the SQL error?
}

// GOOD - Chain the cause
try {
    repository.save(user);
} catch (SQLException e) {
    throw new ServiceException("Failed to save user", e);  // Cause preserved
}
```

---

## Exception Handling Strategies

### Strategy 1: Handle and Recover

```java
public User findUser(Long id) {
    try {
        return userRepository.findById(id);
    } catch (RepositoryException e) {
        log.warn("Failed to find user {}, returning guest", id);
        return User.guest();  // Fallback
    }
}
```

### Strategy 2: Log and Rethrow

```java
public User findUser(Long id) {
    try {
        return userRepository.findById(id);
    } catch (RepositoryException e) {
        log.error("Failed to find user: " + id, e);
        throw e;  // Let caller handle
    }
}
```

### Strategy 3: Wrap and Throw

```java
public User findUser(Long id) {
    try {
        return userRepository.findById(id);
    } catch (SQLException e) {
        throw new UserServiceException("Database error finding user: " + id, e);
    }
}
```

### Choosing a Strategy

```
               Can this method recover?
                         |
              +----------+----------+
              |                     |
             Yes                   No
              |                     |
              v                     |
         Handle &                   v
         Recover            Should caller know details?
              |                     |
              |          +----------+----------+
              |          |                     |
              |         Yes                   No
              |          |                     |
              |          v                     v
              |       Rethrow             Wrap & Throw
              |       as-is               (hide details)
              v          v                     v
```

---

## Disaster 4: Throwing in finally

**AI code:**
```java
try {
    processPayment();
} finally {
    closeConnection();  // If this throws...
}
```

**Problem:** If both `processPayment()` and `closeConnection()` throw, the original exception is lost!

**Fix:**
```java
Exception primary = null;
try {
    processPayment();
} catch (Exception e) {
    primary = e;
    throw e;
} finally {
    try {
        closeConnection();
    } catch (Exception e) {
        if (primary != null) {
            primary.addSuppressed(e);  // Preserve both exceptions
        } else {
            throw e;
        }
    }
}
```

**Better - use try-with-resources** which handles this automatically.

---

## What to Tell AI

**Bad:** "Add error handling"

**Good:** "Add try-with-resources for file operations. Catch specific exceptions. Log errors with context. Chain the original cause when wrapping exceptions."

**Bad:** "Handle exceptions"

**Good:** "For PaymentDeclinedException, return a user-friendly message. For PaymentGatewayException, log the error and throw ServiceUnavailableException. Don't catch generic Exception."

**Bad:** "Make it not crash"

**Good:** "Validate inputs at the start and throw IllegalArgumentException for invalid data. Use Optional for values that might not exist."

---

## Red Flags in AI Output

🚩 **Empty catch block**
```java
catch (Exception e) { }  // Silent failure
```

🚩 **Catching Exception or Throwable**
```java
catch (Exception e) { ... }  // Too broad
```

🚩 **Lost exception cause**
```java
throw new MyException(e.getMessage());  // Should be new MyException(msg, e)
```

🚩 **Resources without try-with-resources**
```java
FileReader reader = new FileReader(path);  // Leak risk!
```

🚩 **Throwing in finally**
```java
finally { something.close(); }  // Can lose primary exception
```

🚩 **Catch and return null**
```java
catch (Exception e) { return null; }  // Hidden failure
```

---

## Quick Mental Checklist

- [ ] Am I catching specific exceptions, not Exception?
- [ ] Am I using try-with-resources for closeable resources?
- [ ] Am I preserving exception causes when wrapping?
- [ ] Am I logging useful context?
- [ ] Can I recover, or should I propagate?
- [ ] Is the catch block doing something meaningful?

---

## Chapter 6 Quiz

**Q1: What's wrong with `catch (Exception e) { }`?**
- a) Nothing wrong
- b) Empty catch hides all errors including bugs
- c) Should be Throwable
- d) Missing finally

<details>
<summary>Answer</summary>

**b) Hides all errors** - Silent failure. NullPointerExceptions (bugs) are caught and hidden. Debugging becomes impossible.
</details>

---

**Q2: When should you use checked exceptions?**
- a) Always - they're safer
- b) For recoverable conditions where caller should handle
- c) For programming errors
- d) Never - only use RuntimeException

<details>
<summary>Answer</summary>

**b) Recoverable conditions** - Checked exceptions force callers to handle situations they can recover from (file not found, network error). Programming bugs should be unchecked.
</details>

---

**Q3: What does try-with-resources do?**
- a) Makes try blocks faster
- b) Automatically closes resources when block exits
- c) Catches all exceptions
- d) Creates resources in memory

<details>
<summary>Answer</summary>

**b) Automatic resource closing** - Resources declared in try() are automatically closed when the block exits, even if an exception occurs. Prevents leaks.
</details>

---

**Q4: What's the output?**
```java
try {
    throw new RuntimeException("A");
} finally {
    throw new RuntimeException("B");
}
```
- a) RuntimeException("A")
- b) RuntimeException("B")
- c) Both exceptions
- d) Compilation error

<details>
<summary>Answer</summary>

**b) RuntimeException("B")** - The finally block's exception overwrites the original. This is why throwing in finally is dangerous - you lose the original error.
</details>

---

**Q5: How do you preserve the original exception when wrapping?**
- a) `throw new MyException(e.getMessage())`
- b) `throw new MyException("msg", e)`
- c) `throw new MyException(e.toString())`
- d) `throw e`

<details>
<summary>Answer</summary>

**b) Pass exception as cause** - `new MyException("msg", e)` chains the original exception. Stack traces will show both, making debugging possible.
</details>

---

**Q6: AI generated this. What's the risk?**
```java
BufferedReader reader = new BufferedReader(new FileReader(path));
String line = reader.readLine();
reader.close();
return line;
```
- a) No risk
- b) If readLine() throws, reader never closes (leak)
- c) Closing reader throws exception
- d) BufferedReader is deprecated

<details>
<summary>Answer</summary>

**b) Resource leak** - If `readLine()` throws an exception, `close()` is never called. Use try-with-resources to guarantee cleanup.
</details>

---

**Q7: Which exceptions should NOT be caught?**
- a) IOException
- b) SQLException
- c) OutOfMemoryError
- d) FileNotFoundException

<details>
<summary>Answer</summary>

**c) OutOfMemoryError** - Errors (not Exceptions) indicate JVM problems. Catching them is pointless - the JVM is in an inconsistent state.
</details>

---

**Q8: What's the purpose of `e.addSuppressed(other)`?**
- a) Prevent exception logging
- b) Combine multiple exceptions that occurred during cleanup
- c) Convert checked to unchecked
- d) Make exception silent

<details>
<summary>Answer</summary>

**b) Combine exceptions** - When both try block and close() throw, addSuppressed keeps both. try-with-resources does this automatically.
</details>

---

**Q9: Best way to handle unknown user in `findUserById`?**
- a) Return null
- b) Return empty Optional or throw UserNotFoundException
- c) Print error and continue
- d) Catch and ignore

<details>
<summary>Answer</summary>

**b) Optional or specific exception** - `Optional.empty()` or `UserNotFoundException` clearly signals "not found." Returning null leads to NPEs elsewhere.
</details>

---

**Q10: Which is correct exception chaining?**
- a) `throw new ServiceException("Failed")`
- b) `throw new ServiceException("Failed: " + e.getMessage())`
- c) `throw new ServiceException("Failed", e)`
- d) `throw e`

<details>
<summary>Answer</summary>

**c) Pass exception as cause** - The original exception `e` becomes the cause. Full stack trace preserved. Options a/b lose the original exception's stack trace.
</details>

---

## Key Takeaways

- **Catch specific exceptions**, not `Exception` or `Throwable`
- **Use try-with-resources** for anything that needs closing
- **Chain exceptions** - don't lose the original cause
- **Empty catch blocks are bugs** - always do something meaningful
- **Let bugs propagate** - don't catch NullPointerException
- **Log context** - who, what, when

---

## What's Next

**Chapter 7: Collections**

ArrayList vs LinkedList. HashMap vs TreeMap. When each is right, when AI picks wrong.

---

*"Good error handling makes failures visible. Bad error handling makes them invisible until production."*
