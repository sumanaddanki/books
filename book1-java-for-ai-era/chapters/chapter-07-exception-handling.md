# Chapter 7: Exception Handling - When Things Go Wrong

> "The mark of a professional isn't avoiding errors - it's handling them gracefully."

---

## What You'll Learn

- What exceptions are and why they exist
- The difference between checked and unchecked exceptions
- Using try-catch-finally blocks effectively
- Creating custom exceptions
- Best practices for exception handling
- Common mistakes AI makes with exceptions

---

## Why Exceptions Matter

Every program encounters unexpected situations:
- User enters invalid data
- File doesn't exist
- Network connection fails
- Database is unavailable
- Memory runs out

Without proper handling, these situations crash your program. With exceptions, you can respond gracefully.

**AI-generated code often has poor exception handling** - either catching everything silently or not handling errors at all. Understanding exceptions lets you spot and fix these issues.

---

## What Is an Exception?

An exception is an event that disrupts the normal flow of program execution.

```java
public class ExceptionDemo {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3};
        System.out.println(numbers[10]);  // ArrayIndexOutOfBoundsException!
    }
}
```

Output:
```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 10 out of bounds for length 3
    at ExceptionDemo.main(ExceptionDemo.java:4)
```

The program crashes. But with exception handling:

```java
public class ExceptionDemo {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3};
        try {
            System.out.println(numbers[10]);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Invalid index! Array only has " + numbers.length + " elements.");
        }
        System.out.println("Program continues...");
    }
}
```

Output:
```
Invalid index! Array only has 3 elements.
Program continues...
```

The program handles the error and continues.

---

## The Exception Hierarchy

```
java.lang.Throwable
├── java.lang.Error (Don't catch these!)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── ...
└── java.lang.Exception
    ├── RuntimeException (Unchecked)
    │   ├── NullPointerException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── IllegalArgumentException
    │   ├── ArithmeticException
    │   └── ...
    └── Other Exceptions (Checked)
        ├── IOException
        ├── SQLException
        ├── FileNotFoundException
        └── ...
```

### Errors vs Exceptions

**Errors:** Serious problems you shouldn't try to handle
- `OutOfMemoryError` - JVM ran out of memory
- `StackOverflowError` - Infinite recursion

**Exceptions:** Problems you can and should handle
- `IOException` - File/network issues
- `NullPointerException` - Null reference access

---

## Checked vs Unchecked Exceptions

This distinction is crucial and often confuses beginners (and AI!).

### Checked Exceptions

**Must** be handled or declared. The compiler enforces this.

```java
// This won't compile!
public void readFile() {
    FileReader reader = new FileReader("file.txt");  // Compiler error!
}

// Option 1: Handle it
public void readFile() {
    try {
        FileReader reader = new FileReader("file.txt");
    } catch (FileNotFoundException e) {
        System.out.println("File not found!");
    }
}

// Option 2: Declare it
public void readFile() throws FileNotFoundException {
    FileReader reader = new FileReader("file.txt");
}
```

**Common checked exceptions:**
- `IOException`
- `SQLException`
- `FileNotFoundException`
- `ClassNotFoundException`

### Unchecked Exceptions (Runtime Exceptions)

**Don't** require explicit handling. Usually indicate programming bugs.

```java
// Compiles fine, but may crash at runtime
public void divide(int a, int b) {
    int result = a / b;  // ArithmeticException if b is 0
}
```

**Common unchecked exceptions:**
- `NullPointerException`
- `ArrayIndexOutOfBoundsException`
- `IllegalArgumentException`
- `ArithmeticException`
- `NumberFormatException`

### Why the Distinction?

- **Checked:** External factors beyond your control (files, network, database)
- **Unchecked:** Programming errors you should fix in code

---

## Try-Catch-Finally

### Basic Try-Catch

```java
try {
    // Code that might throw an exception
    int result = 10 / 0;
} catch (ArithmeticException e) {
    // Handle the exception
    System.out.println("Cannot divide by zero!");
}
```

### Multiple Catch Blocks

```java
try {
    String text = null;
    int num = Integer.parseInt(text);
} catch (NullPointerException e) {
    System.out.println("Value was null!");
} catch (NumberFormatException e) {
    System.out.println("Invalid number format!");
}
```

**Order matters!** Catch more specific exceptions first:

```java
// WRONG - won't compile
try {
    // ...
} catch (Exception e) {           // Catches everything
    // ...
} catch (IOException e) {         // Never reached!
    // ...
}

// CORRECT
try {
    // ...
} catch (FileNotFoundException e) {  // Most specific first
    // ...
} catch (IOException e) {            // Then broader
    // ...
} catch (Exception e) {              // Most general last
    // ...
}
```

### Multi-Catch (Java 7+)

```java
try {
    // ...
} catch (IOException | SQLException e) {
    System.out.println("Database or file error: " + e.getMessage());
}
```

### The Finally Block

Code in `finally` **always** executes, whether exception occurs or not.

```java
FileReader reader = null;
try {
    reader = new FileReader("file.txt");
    // Process file
} catch (IOException e) {
    System.out.println("Error reading file");
} finally {
    // Always runs - cleanup code
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            // Ignore
        }
    }
}
```

**Use finally for:**
- Closing resources (files, connections)
- Releasing locks
- Cleanup operations

---

## Try-With-Resources (Java 7+)

A cleaner way to handle resources that must be closed:

```java
// Old way (verbose, error-prone)
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("file.txt"));
    String line = reader.readLine();
    System.out.println(line);
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            // Ignore
        }
    }
}

// New way (clean, automatic)
try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
    String line = reader.readLine();
    System.out.println(line);
} catch (IOException e) {
    e.printStackTrace();
}
// reader is automatically closed!
```

**Resources are automatically closed** when the try block exits (normally or via exception).

### Multiple Resources

```java
try (
    FileInputStream input = new FileInputStream("input.txt");
    FileOutputStream output = new FileOutputStream("output.txt")
) {
    // Use both streams
    // Both are automatically closed
} catch (IOException e) {
    e.printStackTrace();
}
```

### What Can Be Used in Try-With-Resources?

Any class implementing `AutoCloseable` or `Closeable`:
- `InputStream`, `OutputStream`
- `Reader`, `Writer`
- `Connection`, `Statement`, `ResultSet`
- `Scanner`
- And many more...

---

## Throwing Exceptions

### Using `throw`

Create and throw an exception:

```java
public void setAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative: " + age);
    }
    this.age = age;
}
```

### Using `throws`

Declare that a method might throw an exception:

```java
public void readConfig(String filename) throws IOException {
    // Method might throw IOException
    // Caller must handle it
    BufferedReader reader = new BufferedReader(new FileReader(filename));
    // ...
}
```

### throw vs throws

| `throw` | `throws` |
|---------|----------|
| Actually throws an exception | Declares possible exception |
| Used inside method body | Used in method signature |
| Followed by exception object | Followed by exception class |

```java
// throws in signature
public void process() throws IOException {
    // throw in body
    throw new IOException("Something went wrong");
}
```

---

## Creating Custom Exceptions

When built-in exceptions don't fit your needs:

### Simple Custom Exception

```java
public class InsufficientFundsException extends Exception {

    public InsufficientFundsException(String message) {
        super(message);
    }
}
```

### Custom Exception with Details

```java
public class InsufficientFundsException extends Exception {
    private double balance;
    private double amount;

    public InsufficientFundsException(double balance, double amount) {
        super("Insufficient funds: balance=" + balance + ", requested=" + amount);
        this.balance = balance;
        this.amount = amount;
    }

    public double getBalance() {
        return balance;
    }

    public double getAmount() {
        return amount;
    }

    public double getShortfall() {
        return amount - balance;
    }
}
```

### Using Custom Exceptions

```java
public class BankAccount {
    private double balance;

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException(balance, amount);
        }
        balance -= amount;
    }
}

// Usage
try {
    account.withdraw(1000);
} catch (InsufficientFundsException e) {
    System.out.println("Cannot withdraw: " + e.getMessage());
    System.out.println("You need: $" + e.getShortfall() + " more");
}
```

### Checked vs Unchecked Custom Exceptions

```java
// Checked (must be handled) - extends Exception
public class BusinessException extends Exception { }

// Unchecked (optional handling) - extends RuntimeException
public class ValidationException extends RuntimeException { }
```

---

## Exception Handling Best Practices

### 1. Don't Catch Generic Exception

```java
// BAD
try {
    // ...
} catch (Exception e) {
    // What exception? Who knows!
}

// GOOD
try {
    // ...
} catch (FileNotFoundException e) {
    // Handle missing file
} catch (IOException e) {
    // Handle other I/O issues
}
```

### 2. Don't Swallow Exceptions

```java
// TERRIBLE - Silent failure
try {
    // ...
} catch (Exception e) {
    // Nothing! Error is hidden
}

// BAD - Minimal logging
try {
    // ...
} catch (Exception e) {
    e.printStackTrace();  // Only prints to console
}

// GOOD - Proper handling
try {
    // ...
} catch (IOException e) {
    logger.error("Failed to read file: " + filename, e);
    throw new ServiceException("Configuration load failed", e);
}
```

### 3. Use Specific Exceptions

```java
// BAD
public void process() throws Exception {
    // Caller has no idea what might go wrong
}

// GOOD
public void process() throws IOException, SQLException {
    // Caller knows exactly what to handle
}
```

### 4. Include Context in Messages

```java
// BAD
throw new IllegalArgumentException("Invalid value");

// GOOD
throw new IllegalArgumentException("Invalid age: " + age + ". Must be between 0 and 150");
```

### 5. Clean Up Resources

```java
// Always use try-with-resources for AutoCloseable resources
try (Connection conn = dataSource.getConnection();
     PreparedStatement stmt = conn.prepareStatement(sql)) {
    // Use resources
}  // Automatically closed
```

### 6. Don't Use Exceptions for Flow Control

```java
// BAD - Exceptions are expensive
try {
    int value = Integer.parseInt(input);
} catch (NumberFormatException e) {
    value = 0;  // Using exception as if/else
}

// GOOD - Check first
if (input != null && input.matches("-?\\d+")) {
    int value = Integer.parseInt(input);
} else {
    int value = 0;
}
```

### 7. Log and Rethrow Carefully

```java
// BAD - Loses stack trace
catch (SQLException e) {
    throw new RuntimeException("Database error");
}

// GOOD - Preserves cause
catch (SQLException e) {
    throw new RuntimeException("Database error", e);
}
```

---

## Common Exception Patterns

### Pattern 1: Validate and Throw Early

```java
public void processOrder(Order order) {
    // Validate at the start
    if (order == null) {
        throw new IllegalArgumentException("Order cannot be null");
    }
    if (order.getItems().isEmpty()) {
        throw new IllegalArgumentException("Order must have at least one item");
    }

    // Now process - we know order is valid
    // ...
}
```

### Pattern 2: Convert Checked to Unchecked

```java
public String readFile(String path) {
    try {
        return Files.readString(Path.of(path));
    } catch (IOException e) {
        throw new UncheckedIOException("Failed to read: " + path, e);
    }
}
```

### Pattern 3: Exception Translation

```java
public User findUser(int id) throws UserNotFoundException {
    try {
        return database.query("SELECT * FROM users WHERE id = ?", id);
    } catch (SQLException e) {
        throw new UserNotFoundException("User not found: " + id, e);
    }
}
```

### Pattern 4: Optional Instead of Exception

```java
// Instead of throwing exception for "not found"
public Optional<User> findUser(int id) {
    User user = database.query(id);
    return Optional.ofNullable(user);
}

// Usage
findUser(123)
    .ifPresentOrElse(
        user -> System.out.println("Found: " + user),
        () -> System.out.println("User not found")
    );
```

---

## AI Integration: Exception Handling Mistakes

AI-generated code often has these exception handling problems:

### Problem 1: Empty Catch Blocks

**AI generates:**
```java
try {
    file.read();
} catch (IOException e) {
    // TODO: handle exception
}
```

**Fix:**
```java
try {
    file.read();
} catch (IOException e) {
    logger.error("Failed to read file", e);
    throw new RuntimeException("File read failed", e);
}
```

### Problem 2: Catching Exception

**AI generates:**
```java
try {
    process();
} catch (Exception e) {
    e.printStackTrace();
}
```

**Fix:**
```java
try {
    process();
} catch (SpecificException e) {
    // Handle specific case
}
```

### Problem 3: Not Closing Resources

**AI generates:**
```java
public String read(String path) throws IOException {
    FileReader reader = new FileReader(path);
    BufferedReader br = new BufferedReader(reader);
    return br.readLine();
    // Resources never closed!
}
```

**Fix:**
```java
public String read(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}
```

### Problem 4: Losing Exception Information

**AI generates:**
```java
catch (SQLException e) {
    throw new RuntimeException("Error");  // Lost original exception!
}
```

**Fix:**
```java
catch (SQLException e) {
    throw new RuntimeException("Database query failed", e);  // Preserve cause
}
```

---

## Exercises

### Exercise 7.1: Safe Division
Create a method `safeDivide(int a, int b)` that:
- Returns the result of a/b
- Throws `IllegalArgumentException` if b is 0
- Include a meaningful error message

### Exercise 7.2: File Reader
Write a method that:
- Reads a file and returns its contents as a String
- Uses try-with-resources
- Handles `FileNotFoundException` differently from other `IOException`s

### Exercise 7.3: Custom Exception
Create a `InvalidEmailException` that:
- Stores the invalid email address
- Has a method to suggest what's wrong (missing @, invalid domain, etc.)

### Exercise 7.4: Bank Account
Implement a `BankAccount` class with:
- `deposit(double)` - throws if amount negative
- `withdraw(double)` - throws `InsufficientFundsException` if not enough
- Proper exception handling throughout

### Exercise 7.5: Fix AI Code
The following AI-generated code has exception handling issues. Fix them:

```java
public List<User> loadUsers(String filename) {
    List<User> users = new ArrayList<>();
    try {
        BufferedReader reader = new BufferedReader(new FileReader(filename));
        String line;
        while ((line = reader.readLine()) != null) {
            try {
                users.add(parseUser(line));
            } catch (Exception e) {
            }
        }
    } catch (Exception e) {
        e.printStackTrace();
    }
    return users;
}
```

---

## Key Takeaways

- **Exceptions handle unexpected situations** - Don't let programs crash
- **Checked exceptions must be handled** - Compiler enforces this
- **Unchecked exceptions indicate bugs** - Fix the code
- **Use try-with-resources** - Automatic cleanup for resources
- **Be specific in catches** - Don't catch generic Exception
- **Never swallow exceptions** - At minimum, log them
- **Include context in messages** - Help debugging
- **Create custom exceptions** - When built-in ones don't fit

---

## What's Next

You now know how to handle errors gracefully. In **Chapter 8**, we'll explore the Collections Framework - Java's powerful data structures for storing and manipulating groups of objects.

---

*"Exception handling is like a seatbelt - you hope you never need it, but you'll be glad it's there when you do."*
