# Appendix A: Java Keywords Reference

A complete reference of all Java keywords with brief explanations.

---

## Reserved Keywords

These words cannot be used as identifiers (variable names, method names, etc.).

### Access Modifiers

| Keyword | Description |
|---------|-------------|
| `public` | Accessible from anywhere |
| `private` | Accessible only within the class |
| `protected` | Accessible within package and subclasses |
| (default) | Package-private (no keyword needed) |

### Class/Interface Keywords

| Keyword | Description |
|---------|-------------|
| `class` | Declares a class |
| `interface` | Declares an interface |
| `enum` | Declares an enumeration |
| `record` | Declares a record (Java 16+) |
| `extends` | Inherit from a class |
| `implements` | Implement an interface |
| `abstract` | Abstract class or method |
| `final` | Cannot be changed/overridden/inherited |
| `sealed` | Restricts which classes can extend (Java 17+) |
| `non-sealed` | Allows any class to extend (Java 17+) |
| `permits` | Lists allowed subclasses for sealed (Java 17+) |

### Method Keywords

| Keyword | Description |
|---------|-------------|
| `void` | Method returns nothing |
| `return` | Return from method |
| `static` | Belongs to class, not instance |
| `synchronized` | Thread-safe access |
| `native` | Implemented in native code |
| `strictfp` | Strict floating-point calculations |

### Variable Keywords

| Keyword | Description |
|---------|-------------|
| `final` | Variable cannot be reassigned |
| `volatile` | Variable may be modified by multiple threads |
| `transient` | Variable excluded from serialization |
| `var` | Local variable type inference (Java 10+) |

### Control Flow

| Keyword | Description |
|---------|-------------|
| `if` | Conditional statement |
| `else` | Alternative branch |
| `switch` | Multi-way branch |
| `case` | Branch in switch |
| `default` | Default branch in switch |
| `for` | Loop statement |
| `while` | Loop while condition true |
| `do` | Do-while loop |
| `break` | Exit loop or switch |
| `continue` | Skip to next iteration |
| `yield` | Return value from switch expression (Java 14+) |

### Exception Handling

| Keyword | Description |
|---------|-------------|
| `try` | Start exception handling block |
| `catch` | Handle exception |
| `finally` | Always executed block |
| `throw` | Throw an exception |
| `throws` | Declare method may throw exception |

### Object/Type Keywords

| Keyword | Description |
|---------|-------------|
| `new` | Create new object |
| `this` | Reference to current object |
| `super` | Reference to parent class |
| `instanceof` | Test object type |

### Primitive Types

| Keyword | Size | Range |
|---------|------|-------|
| `boolean` | 1 bit | true/false |
| `byte` | 8 bits | -128 to 127 |
| `short` | 16 bits | -32,768 to 32,767 |
| `char` | 16 bits | Unicode character |
| `int` | 32 bits | -2^31 to 2^31-1 |
| `long` | 64 bits | -2^63 to 2^63-1 |
| `float` | 32 bits | IEEE 754 floating point |
| `double` | 64 bits | IEEE 754 double precision |

### Package/Import

| Keyword | Description |
|---------|-------------|
| `package` | Declare package |
| `import` | Import classes/packages |

### Module Keywords (Java 9+)

| Keyword | Description |
|---------|-------------|
| `module` | Declare a module |
| `requires` | Module dependency |
| `exports` | Package visible to other modules |
| `opens` | Package open for reflection |
| `uses` | Service consumer |
| `provides` | Service provider |
| `with` | Implementation class for service |
| `to` | Target module for exports/opens |
| `transitive` | Transitive dependency |

### Other Keywords

| Keyword | Description |
|---------|-------------|
| `assert` | Assertion for debugging |
| `null` | Null reference literal |
| `true` | Boolean true literal |
| `false` | Boolean false literal |

---

## Contextual Keywords

These are only keywords in specific contexts:

| Keyword | Context |
|---------|---------|
| `var` | Local variable declaration |
| `yield` | Switch expressions |
| `record` | Record declarations |
| `sealed` | Sealed class declarations |
| `permits` | Sealed class declarations |

---

## Reserved But Unused

| Keyword | Status |
|---------|--------|
| `const` | Reserved, never implemented |
| `goto` | Reserved, never implemented |

---

## Common Misconceptions

These are **NOT** keywords (they're identifiers):

- `String` - It's a class name
- `System` - It's a class name
- `main` - It's a method name
- `args` - It's a parameter name
- `length` - It's a property/method name
- `null` - Actually IS a keyword (literal)
