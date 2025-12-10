# Appendix C: Java Coding Conventions

Standard naming conventions and style guidelines for professional Java code.

---

## Naming Conventions

### Classes and Interfaces

**PascalCase** - Each word capitalized, no underscores

```java
// Good
public class UserAccount { }
public class HttpRequestHandler { }
public interface Serializable { }
public interface UserRepository { }

// Bad
public class userAccount { }      // Wrong: lowercase start
public class User_Account { }     // Wrong: underscore
public class USERACCOUNT { }      // Wrong: all caps
```

### Methods

**camelCase** - First word lowercase, subsequent words capitalized

```java
// Good
public void getUserById() { }
public String formatDate() { }
public boolean isValid() { }
public void setName(String name) { }

// Bad
public void GetUserById() { }     // Wrong: uppercase start
public void get_user_by_id() { }  // Wrong: underscores
```

### Variables

**camelCase** - Same as methods

```java
// Good
int userCount;
String firstName;
boolean isActive;
List<String> customerNames;

// Bad
int UserCount;       // Wrong: uppercase start
int user_count;      // Wrong: underscore
```

### Constants

**SCREAMING_SNAKE_CASE** - All uppercase with underscores

```java
// Good
public static final int MAX_RETRY_COUNT = 3;
public static final String DEFAULT_NAME = "Unknown";
private static final long TIMEOUT_MS = 5000;

// Bad
public static final int maxRetryCount = 3;  // Wrong: not uppercase
public static final int MAXRETRYCOUNT = 3;  // Wrong: no underscores
```

### Packages

**all lowercase** - No underscores, dot-separated

```java
// Good
package com.company.project;
package org.example.utils;
package io.github.username.app;

// Bad
package com.Company.Project;  // Wrong: uppercase
package com.company_project;  // Wrong: underscore
```

### Enums

**PascalCase** for type, **SCREAMING_SNAKE_CASE** for values

```java
// Good
public enum OrderStatus {
    PENDING,
    PROCESSING,
    SHIPPED,
    DELIVERED,
    CANCELLED
}

public enum Priority {
    LOW,
    MEDIUM,
    HIGH
}
```

---

## Naming Best Practices

### Be Descriptive

```java
// Bad
int d;              // What is d?
String s;           // What kind of string?
List<Integer> l;    // List of what?

// Good
int daysSinceLastLogin;
String customerEmail;
List<Integer> orderIds;
```

### Use Intention-Revealing Names

```java
// Bad
int x = user.getAge();
if (x >= 18) { }

// Good
int userAge = user.getAge();
if (userAge >= LEGAL_ADULT_AGE) { }
```

### Boolean Naming

Use is, has, can, should prefixes:

```java
// Good
boolean isActive;
boolean hasPermission;
boolean canEdit;
boolean shouldRetry;

// Methods
public boolean isValid() { }
public boolean hasItems() { }
public boolean canProceed() { }
```

### Collection Naming

Use plural nouns:

```java
// Good
List<User> users;
Set<String> emailAddresses;
Map<Integer, Order> ordersById;

// Bad
List<User> userList;    // Redundant "List"
Set<String> emailSet;   // Redundant "Set"
```

### Method Naming Patterns

| Pattern | Use For | Example |
|---------|---------|---------|
| `get*` | Retrieve value | `getUser()`, `getName()` |
| `set*` | Set value | `setAge()`, `setActive()` |
| `is*` | Boolean getter | `isValid()`, `isEmpty()` |
| `has*` | Check existence | `hasPermission()` |
| `can*` | Check capability | `canEdit()` |
| `find*` | Search/lookup | `findByEmail()` |
| `create*` | Factory method | `createUser()` |
| `to*` | Conversion | `toString()`, `toJson()` |
| `from*` | Parse/construct | `fromJson()` |
| `calculate*` | Computation | `calculateTotal()` |
| `validate*` | Validation | `validateInput()` |
| `process*` | Processing | `processOrder()` |

---

## Code Formatting

### Braces

Use K&R style (opening brace on same line):

```java
// Good
public void method() {
    if (condition) {
        // code
    } else {
        // code
    }
}

// Avoid
public void method()
{
    if (condition)
    {
        // code
    }
}
```

### Indentation

Use 4 spaces (not tabs):

```java
public class Example {
    private int value;

    public void method() {
        if (condition) {
            for (int i = 0; i < 10; i++) {
                // Nested code
            }
        }
    }
}
```

### Line Length

Keep lines under 120 characters (80-100 preferred):

```java
// Bad - too long
String result = someObject.performVeryLongOperationWithManyParameters(parameter1, parameter2, parameter3, parameter4);

// Good - broken up
String result = someObject.performVeryLongOperationWithManyParameters(
    parameter1,
    parameter2,
    parameter3,
    parameter4
);
```

### Blank Lines

Use blank lines to separate logical sections:

```java
public class UserService {

    private final UserRepository repository;
    private final EmailService emailService;

    // Constructor
    public UserService(UserRepository repository, EmailService emailService) {
        this.repository = repository;
        this.emailService = emailService;
    }

    // Public methods
    public User createUser(String name, String email) {
        // Implementation
    }

    public User findUser(Long id) {
        // Implementation
    }

    // Private helper methods
    private void validateEmail(String email) {
        // Implementation
    }
}
```

---

## Class Structure

Recommended order of elements:

```java
public class ExampleClass {

    // 1. Static constants
    public static final int MAX_SIZE = 100;
    private static final Logger LOG = Logger.getLogger(ExampleClass.class);

    // 2. Static variables
    private static int instanceCount;

    // 3. Instance variables
    private final String name;
    private int value;

    // 4. Constructors
    public ExampleClass(String name) {
        this.name = name;
    }

    // 5. Static factory methods
    public static ExampleClass create(String name) {
        return new ExampleClass(name);
    }

    // 6. Public methods
    public String getName() {
        return name;
    }

    public void process() {
        // Implementation
    }

    // 7. Private methods
    private void validate() {
        // Implementation
    }

    // 8. equals, hashCode, toString
    @Override
    public boolean equals(Object o) {
        // Implementation
    }

    @Override
    public int hashCode() {
        // Implementation
    }

    @Override
    public String toString() {
        return "ExampleClass{name='" + name + "'}";
    }

    // 9. Inner classes
    private static class InnerClass {
        // Implementation
    }
}
```

---

## Comments

### When to Comment

- Complex algorithms
- Non-obvious business logic
- Public API documentation (JavaDoc)
- Workarounds with explanations

### When NOT to Comment

- Obvious code
- To explain bad code (fix the code instead)
- Commented-out code (delete it)

### JavaDoc

```java
/**
 * Calculates the total price including tax.
 *
 * @param items the list of items to calculate
 * @param taxRate the tax rate as decimal (e.g., 0.1 for 10%)
 * @return the total price including tax
 * @throws IllegalArgumentException if items is null or taxRate is negative
 */
public double calculateTotal(List<Item> items, double taxRate) {
    // Implementation
}
```

### Inline Comments

```java
// Use sparingly and only when necessary

// Calculate distance using Haversine formula
double distance = Math.acos(
    Math.sin(lat1) * Math.sin(lat2) +
    Math.cos(lat1) * Math.cos(lat2) * Math.cos(lon2 - lon1)
) * EARTH_RADIUS;
```

---

## Import Statements

### Order

1. `java.*` - Standard Java
2. `javax.*` - Java extensions
3. Third-party libraries (alphabetically)
4. Your project packages

```java
import java.util.List;
import java.util.Map;

import javax.validation.Valid;

import com.google.gson.Gson;
import org.springframework.stereotype.Service;

import com.mycompany.myproject.model.User;
import com.mycompany.myproject.repository.UserRepository;
```

### Avoid Wildcard Imports

```java
// Bad
import java.util.*;

// Good
import java.util.List;
import java.util.Map;
import java.util.ArrayList;
```

---

## Quick Reference Card

| Element | Convention | Example |
|---------|------------|---------|
| Class | PascalCase | `UserAccount` |
| Interface | PascalCase | `Comparable` |
| Method | camelCase | `getUserById` |
| Variable | camelCase | `userCount` |
| Constant | SCREAMING_SNAKE | `MAX_SIZE` |
| Package | lowercase | `com.example` |
| Enum type | PascalCase | `OrderStatus` |
| Enum value | SCREAMING_SNAKE | `PENDING` |
| Type parameter | Single uppercase | `T`, `E`, `K`, `V` |
