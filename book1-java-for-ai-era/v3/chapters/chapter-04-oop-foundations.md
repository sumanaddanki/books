# Chapter 4: OOP Foundations - Classes, Objects, and Structure

> "AI creates classes without knowing your codebase. It doesn't ask if a similar class already exists."

---

## Why OOP Matters for AI Users

Object-Oriented Programming isn't just a coding style. It's how Java code is organized. When AI generates code:

- It creates new classes without checking if you have existing ones
- It duplicates functionality that could be inherited
- It makes everything public when it shouldn't be
- It confuses `static` and instance usage

Understanding OOP lets you catch these mistakes.

---

## Class vs Object: The Mental Model

### Class = Blueprint

A class defines:
- What data something has (fields)
- What it can do (methods)

```java
public class User {
    // Fields - what data a User has
    private String name;
    private String email;
    private int age;

    // Methods - what a User can do
    public void sendEmail(String message) { ... }
    public boolean isAdult() { return age >= 18; }
}
```

### Object = Instance

An object is a specific thing created from the blueprint:

```java
User alice = new User();    // One specific user
User bob = new User();      // Another specific user
```

**One class, unlimited objects.**

### Why This Matters

When AI creates a new class, ask:
- Does this class already exist in my codebase?
- Should this extend an existing class?
- Is AI duplicating functionality?

---

## Constructors: Creating Objects

### Default Constructor

If you don't write one, Java provides:
```java
public User() {
    // Empty - fields get default values
}
```

### Parameterized Constructor

Initialize fields on creation:
```java
public class User {
    private String name;
    private String email;

    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }
}

// Usage
User alice = new User("Alice", "alice@example.com");
```

### Disaster 1: AI Creates Objects Wrong

**AI code:**
```java
User user = new User();
user.setName("Alice");
user.setEmail("alice@example.com");
user.setAge(25);
user.setRole("admin");
user.setDepartment("Engineering");
// 10 more setters...
```

**Problems:**
- Object is in invalid state between constructor and setters
- Easy to forget a required field
- Verbose and error-prone

**Better - Builder Pattern:**
```java
User user = User.builder()
    .name("Alice")
    .email("alice@example.com")
    .age(25)
    .role("admin")
    .build();
```

**Or use constructor for required fields:**
```java
User user = new User("Alice", "alice@example.com");
user.setAge(25);  // Optional fields via setters
```

### Constructor Overloading

Multiple constructors with different parameters:
```java
public class User {
    public User() { }                           // Default
    public User(String name) { ... }            // Name only
    public User(String name, String email) { }  // Name and email
}
```

### Constructor Chaining

One constructor calls another:
```java
public User(String name) {
    this(name, null);  // Calls the two-parameter constructor
}

public User(String name, String email) {
    this.name = name;
    this.email = email;
}
```

---

## The `this` Keyword

`this` refers to the current object.

### Common Use 1: Distinguish Field from Parameter

```java
public void setName(String name) {
    this.name = name;  // this.name is field, name is parameter
}
```

### Common Use 2: Return Current Object (Fluent API)

```java
public User withName(String name) {
    this.name = name;
    return this;  // Returns the same object
}

// Enables chaining:
user.withName("Alice").withEmail("alice@example.com");
```

### Common Use 3: Pass Current Object

```java
public void register() {
    userService.save(this);  // Pass this object to another method
}
```

---

## Static vs Instance: The Critical Distinction

### Instance (Default)

Belongs to each object. Each object has its own copy.

```java
public class User {
    private String name;  // Each user has their own name

    public String getName() {  // Called on specific user
        return this.name;
    }
}

alice.getName();  // Alice's name
bob.getName();    // Bob's name - different
```

### Static

Belongs to the class itself. Shared by all objects.

```java
public class User {
    private static int totalUsers = 0;  // Shared counter

    public User() {
        totalUsers++;  // Increment shared counter
    }

    public static int getTotalUsers() {  // Called on class
        return totalUsers;
    }
}

User.getTotalUsers();  // Called on class, not object
```

### When to Use Static

| Use Static For | Don't Use Static For |
|----------------|---------------------|
| Utility methods (`Math.abs()`) | Object state |
| Constants (`MAX_SIZE`) | Instance-specific data |
| Factory methods | Methods needing object state |
| Counters/global state | Regular business logic |

### Disaster 2: AI Confuses Static

**AI code:**
```java
public class UserService {
    private static Connection dbConnection;  // WRONG!

    public User findUser(Long id) {
        return dbConnection.query(...);
    }
}
```

**Problems:**
- Shared connection across all threads (thread safety!)
- Can't have different connections for different contexts
- Hard to test (can't mock)

**Fix:**
```java
public class UserService {
    private Connection dbConnection;  // Instance field

    public UserService(Connection dbConnection) {
        this.dbConnection = dbConnection;  // Inject dependency
    }
}
```

### Disaster 3: Static Method Accessing Instance Field

**AI code:**
```java
public class Calculator {
    private int lastResult;

    public static int add(int a, int b) {
        lastResult = a + b;  // COMPILE ERROR!
        return lastResult;
    }
}
```

**Why it fails:** Static methods don't have `this`. They can't access instance fields.

---

## Access Modifiers: Controlling Visibility

| Modifier | Same Class | Same Package | Subclass | Everywhere |
|----------|------------|--------------|----------|------------|
| `private` | ✓ | ✗ | ✗ | ✗ |
| (default) | ✓ | ✓ | ✗ | ✗ |
| `protected` | ✓ | ✓ | ✓ | ✗ |
| `public` | ✓ | ✓ | ✓ | ✓ |

### Disaster 4: AI Makes Everything Public

**AI code:**
```java
public class User {
    public String name;
    public String email;
    public String password;  // DANGER!
    public int age;
    public BigDecimal balance;
}
```

**Problems:**
- Anyone can modify any field
- No validation possible
- Password exposed!
- Can't change internal structure later

**Fix - Encapsulate:**
```java
public class User {
    private String name;
    private String email;
    private String passwordHash;  // Not password itself
    private int age;
    private BigDecimal balance;

    public String getName() { return name; }

    public void setAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("Invalid age");
        }
        this.age = age;
    }

    public boolean checkPassword(String password) {
        return hashService.check(password, this.passwordHash);
    }
}
```

---

## Records: Modern Data Classes (Java 14+)

For simple data carriers, use records:

**Traditional class:**
```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() { return x; }
    public int getY() { return y; }

    @Override
    public boolean equals(Object o) { ... }

    @Override
    public int hashCode() { ... }

    @Override
    public String toString() { ... }
}
```

**Record (same functionality):**
```java
public record Point(int x, int y) { }
```

Records automatically provide:
- Constructor
- Getters (`x()` not `getX()`)
- `equals()`, `hashCode()`, `toString()`
- Immutability

**AI sometimes writes verbose classes when records would suffice.**

---

## Common Java Patterns

### Pattern 1: Factory Method

Instead of constructor, use static method:

```java
public class User {
    private User() { }  // Private constructor

    public static User createAdmin(String name) {
        User user = new User();
        user.name = name;
        user.role = Role.ADMIN;
        return user;
    }

    public static User createGuest() {
        User user = new User();
        user.name = "Guest";
        user.role = Role.GUEST;
        return user;
    }
}

// Usage
User admin = User.createAdmin("Alice");
User guest = User.createGuest();
```

### Pattern 2: Singleton (Use Carefully)

Only one instance exists:

```java
public class Configuration {
    private static Configuration instance;

    private Configuration() { }  // Private constructor

    public static Configuration getInstance() {
        if (instance == null) {
            instance = new Configuration();
        }
        return instance;
    }
}
```

**Warning:** Singletons make testing hard. Prefer dependency injection.

---

## What to Tell AI

**Bad:** "Create a User class"

**Good:** "Create a User class that extends our BaseEntity. Use private fields with getters. Password should only be verifiable, not retrievable."

**Bad:** "Add a method to count users"

**Good:** "Add an instance method to count users. Don't use static - we need this to be testable with dependency injection."

**Bad:** "Make a data class"

**Good:** "Create a record for Point with x and y coordinates (Java 14+). Or if we need mutability, a traditional class with builder."

---

## Red Flags in AI Output

🚩 **All fields public**
```java
public String name;  // No encapsulation
```

🚩 **Static for instance-specific things**
```java
private static Connection conn;  // Shared = danger
```

🚩 **Creating new class when one exists**
"AI made a new Entity class but we already have BaseEntity"

🚩 **Verbose class that could be a record**
50 lines of boilerplate for a simple data holder

🚩 **No constructor validation**
```java
public User(int age) {
    this.age = age;  // What if age is -5?
}
```

🚩 **Mutable when immutable is better**
Setters on everything, when data shouldn't change after creation

---

## Quick Mental Checklist

- [ ] Should this class extend an existing one?
- [ ] Are fields private with controlled access?
- [ ] Is `static` used correctly (or incorrectly)?
- [ ] Could this be a record instead?
- [ ] Are required fields validated in constructor?
- [ ] Does this duplicate existing functionality?

---

## Chapter 4 Quiz

**Q1: AI created a new UserEntity class with id, createdAt, updatedAt. Your team has BaseEntity with these fields. What's the problem?**
- a) No problem - more classes are better
- b) Duplication - should extend BaseEntity
- c) Should use interface instead
- d) Should use static fields

<details>
<summary>Answer</summary>

**b) Should extend BaseEntity** - AI doesn't know your codebase. This duplicates existing functionality and creates inconsistency when BaseEntity is updated.
</details>

---

**Q2: What does `this` refer to?**
- a) The class definition
- b) The current object instance
- c) The parent class
- d) A static reference

<details>
<summary>Answer</summary>

**b) The current object instance** - `this` is a reference to the specific object the method was called on.
</details>

---

**Q3: When should you use `static`?**
- a) For all methods to improve performance
- b) For utility methods and shared constants
- c) For all database connections
- d) Never - static is outdated

<details>
<summary>Answer</summary>

**b) Utility methods and constants** - Static is for things shared across all instances: Math.abs(), constants, factory methods. Not for instance-specific state.
</details>

---

**Q4: What's wrong with this AI-generated code?**
```java
public class User {
    public String password;
}
```
- a) Should be static
- b) Password should never be stored as plain public String
- c) Needs a getter method
- d) Should be final

<details>
<summary>Answer</summary>

**b) Password exposure** - Public fields have no protection. Password should be private, hashed, and only verifiable (not retrievable).
</details>

---

**Q5: What's a record in Java 14+?**
- a) A way to log data
- b) A compact immutable data class with auto-generated methods
- c) A database table mapping
- d) A type of list

<details>
<summary>Answer</summary>

**b) Compact immutable data class** - Records auto-generate constructor, getters, equals, hashCode, toString. Perfect for simple data carriers.
</details>

---

**Q6: Why does this code fail to compile?**
```java
public class Counter {
    private int count;
    public static void increment() {
        count++;
    }
}
```
- a) count should be public
- b) Static methods can't access instance fields
- c) increment should return void
- d) Missing constructor

<details>
<summary>Answer</summary>

**b) Static can't access instance** - Static methods belong to the class, not an object. They have no `this` and can't access instance fields.
</details>

---

**Q7: What does this constructor do?**
```java
public User(String name) {
    this(name, null);
}
```
- a) Creates a user with null name
- b) Calls another constructor in the same class
- c) Calls the parent constructor
- d) Creates two users

<details>
<summary>Answer</summary>

**b) Calls another constructor** - `this(...)` calls another constructor in the same class. This is constructor chaining.
</details>

---

**Q8: Which access modifier means "same package only"?**
- a) private
- b) protected
- c) public
- d) No modifier (default)

<details>
<summary>Answer</summary>

**d) No modifier (default)** - Also called "package-private". Accessible within the same package only.
</details>

---

**Q9: AI generated 50 lines for a simple class with two fields, getters, equals, hashCode, toString. Using Java 17, what's the better approach?**
- a) Keep AI's version for clarity
- b) Use a record - one line instead of 50
- c) Use inheritance
- d) Use static fields

<details>
<summary>Answer</summary>

**b) Use a record** - `public record Point(int x, int y) {}` gives you all that functionality in one line.
</details>

---

**Q10: What's the problem with AI creating `private static Connection dbConnection` in a service class?**
- a) Should be public
- b) Shared across threads - not thread-safe, hard to test
- c) Should use int instead
- d) No problem

<details>
<summary>Answer</summary>

**b) Shared state problems** - Static connection is shared by all threads (concurrency issues) and all tests (can't mock). Use instance field with dependency injection.
</details>

---

## Key Takeaways

- **Class = blueprint, Object = instance** - One class, many objects
- **Check existing classes** - Before accepting AI's new class
- **Private fields + getters** - Not public fields
- **Static = shared** - Use for utilities/constants, not instance data
- **Records for data** - In Java 14+, simpler than full classes
- **Validate in constructors** - Don't accept invalid state

---

## What's Next

**Chapter 5: OOP Pillars**

Encapsulation, Inheritance, Polymorphism, Abstraction. The four concepts that let you evaluate whether AI is structuring code correctly.

---

*"The right class structure makes code maintainable. The wrong one makes it a nightmare to change."*
