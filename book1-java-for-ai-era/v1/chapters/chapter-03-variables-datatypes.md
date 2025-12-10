# Chapter 3: Variables, Data Types & Operators

> "In programming, as in life, knowing what type of thing you're dealing with determines what you can do with it."

---

## What You'll Learn

- What variables are and how to declare them
- Java's eight primitive types and when to use each
- The difference between primitives and reference types
- Type casting: implicit and explicit
- Operators for math, comparison, and logic
- Common pitfalls that AI won't warn you about

---

## What Is a Variable?

A variable is a named container that holds a value.

**Analogy:** Think of a variable as a labeled box.
- The label is the variable name
- The box size/type is the data type
- The contents is the value

```java
int age = 25;
```

Here:
- `int` is the type (size of box)
- `age` is the name (label)
- `25` is the value (contents)

### Why Types Matter

Java is **statically typed** - you must declare what type of data a variable holds, and it can only hold that type.

```java
int age = 25;        // OK
age = "twenty-five"; // ERROR! Can't put String in int box
```

Some languages (like Python, JavaScript) are **dynamically typed** - variables can hold any type. Java's approach catches errors at compile time, before your code runs.

**This is why AI-generated Java code sometimes fails to compile** - it might mix types incorrectly.

---

## The Eight Primitive Types

Java has exactly eight primitive (built-in) types. Memorize them.

### Integer Types (Whole Numbers)

| Type | Size | Range | Use Case |
|------|------|-------|----------|
| `byte` | 8 bits | -128 to 127 | File data, small numbers |
| `short` | 16 bits | -32,768 to 32,767 | Rarely used |
| `int` | 32 bits | ±2.1 billion | **Default choice for integers** |
| `long` | 64 bits | ±9.2 quintillion | Large numbers, timestamps |

```java
byte smallNumber = 100;
short mediumNumber = 30000;
int normalNumber = 2000000000;
long bigNumber = 9000000000000000000L;  // Note the 'L' suffix!
```

**The `L` suffix:** Long literals need `L` at the end, otherwise Java treats them as `int`.

```java
long wrong = 9000000000000000000;   // ERROR! Too big for int
long right = 9000000000000000000L;  // OK with L suffix
```

### Floating-Point Types (Decimals)

| Type | Size | Precision | Use Case |
|------|------|-----------|----------|
| `float` | 32 bits | ~7 digits | Graphics, games (where precision < performance) |
| `double` | 64 bits | ~15 digits | **Default choice for decimals** |

```java
float price = 19.99f;      // Note the 'f' suffix!
double precise = 19.99;    // No suffix needed (default)
```

**The `f` suffix:** Float literals need `f`, otherwise Java treats them as `double`.

### Character Type

| Type | Size | Range | Use Case |
|------|------|-------|----------|
| `char` | 16 bits | Unicode characters | Single characters |

```java
char letter = 'A';
char unicode = '\u0041';  // Also 'A' in Unicode
char newline = '\n';      // Escape sequence
```

**Single quotes for char, double quotes for String:**
```java
char c = 'A';      // Single character
String s = "A";    // String (even with one character)
```

### Boolean Type

| Type | Size | Values | Use Case |
|------|------|--------|----------|
| `boolean` | 1 bit* | `true` or `false` | Conditions, flags |

```java
boolean isActive = true;
boolean hasPermission = false;
```

*Actual memory usage varies by JVM implementation.

---

## Primitive Types: Memory Visualization

```
byte:    [████████]                                    (8 bits)
short:   [████████████████]                            (16 bits)
int:     [████████████████████████████████]            (32 bits)
long:    [████████████████████████████████████████...] (64 bits)

float:   [████████████████████████████████]            (32 bits)
double:  [████████████████████████████████████████...] (64 bits)

char:    [████████████████]                            (16 bits)
boolean: [█]                                           (1 bit)
```

---

## Declaring and Initializing Variables

### Declaration (Creating the Box)

```java
int count;        // Declaration only
```

### Initialization (Putting Something In)

```java
count = 10;       // Initialization
```

### Combined (Most Common)

```java
int count = 10;   // Declaration + initialization
```

### Multiple Variables

```java
int x, y, z;              // Declare three ints
int a = 1, b = 2, c = 3;  // Declare and initialize three ints
```

### Constants (Final Variables)

```java
final double PI = 3.14159;
final int MAX_USERS = 100;

PI = 3.14;  // ERROR! Cannot reassign final variable
```

**Convention:** Constants use `UPPER_SNAKE_CASE`.

---

## Reference Types vs Primitives

Everything that isn't a primitive is a **reference type**.

### Primitives Hold Values Directly

```java
int a = 5;
int b = a;    // b gets a COPY of 5
a = 10;
// a is 10, b is still 5
```

### References Hold Memory Addresses

```java
String s1 = "Hello";
String s2 = s1;    // s2 points to SAME object as s1
```

**Visualization:**

```
Primitives:
┌─────┐     ┌─────┐
│  5  │     │  5  │
└──┬──┘     └──┬──┘
   a           b      (separate copies)

References:
┌─────┐     ┌─────────┐
│ ref │────►│ "Hello" │
└──┬──┘     └─────────┘
   s1             ▲
                  │
┌─────┐           │
│ ref │───────────┘
└──┬──┘
   s2      (both point to same object)
```

### The String Exception

Strings are reference types but behave specially:

```java
String a = "Hello";
String b = "Hello";
// a and b might point to the same object (string pooling)

String c = new String("Hello");
// c is definitely a different object
```

We'll explore this more in later chapters.

---

## Type Casting

Sometimes you need to convert between types.

### Implicit Casting (Widening)

Smaller to larger type happens automatically:

```java
int myInt = 100;
long myLong = myInt;      // int → long (automatic)
double myDouble = myLong;  // long → double (automatic)
```

**Safe direction:** `byte → short → int → long → float → double`

### Explicit Casting (Narrowing)

Larger to smaller type requires explicit cast:

```java
double myDouble = 9.78;
int myInt = (int) myDouble;  // myInt is 9 (decimal truncated!)

long bigValue = 1000L;
byte smallValue = (byte) bigValue;  // Dangerous! May overflow
```

**Warning:** Explicit casting can lose data!

```java
int big = 130;
byte small = (byte) big;  // small is -126 (overflow!)
```

### Common Casting Mistakes

```java
// Dividing integers
int a = 5;
int b = 2;
double result = a / b;     // result is 2.0, not 2.5!

// Fix:
double result = (double) a / b;  // Now result is 2.5
```

**This is a common AI mistake** - generating integer division when you need decimal results.

---

## Operators

### Arithmetic Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `+` | Addition | `5 + 3` → `8` |
| `-` | Subtraction | `5 - 3` → `2` |
| `*` | Multiplication | `5 * 3` → `15` |
| `/` | Division | `5 / 3` → `1` (integer!) |
| `%` | Modulus (remainder) | `5 % 3` → `2` |

**Integer division truncates:**
```java
System.out.println(7 / 2);    // 3 (not 3.5!)
System.out.println(7.0 / 2);  // 3.5
System.out.println(7 / 2.0);  // 3.5
```

### Assignment Operators

| Operator | Equivalent |
|----------|------------|
| `+=` | `x = x + y` |
| `-=` | `x = x - y` |
| `*=` | `x = x * y` |
| `/=` | `x = x / y` |
| `%=` | `x = x % y` |

```java
int x = 10;
x += 5;   // x is now 15
x *= 2;   // x is now 30
```

### Increment/Decrement

| Operator | Meaning |
|----------|---------|
| `++x` | Pre-increment (increment, then use) |
| `x++` | Post-increment (use, then increment) |
| `--x` | Pre-decrement |
| `x--` | Post-decrement |

```java
int a = 5;
System.out.println(a++);  // Prints 5, then a becomes 6
System.out.println(++a);  // a becomes 7, then prints 7
```

**This is confusing!** When in doubt, use separate statements:
```java
a = a + 1;  // Clear and explicit
```

### Comparison Operators

| Operator | Meaning |
|----------|---------|
| `==` | Equal to |
| `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal |
| `<=` | Less than or equal |

```java
int x = 5;
boolean result = (x > 3);   // true
boolean equal = (x == 5);   // true
boolean notEqual = (x != 5); // false
```

**Warning:** `==` for reference types compares addresses, not values!
```java
String a = new String("hello");
String b = new String("hello");
System.out.println(a == b);      // false (different objects!)
System.out.println(a.equals(b)); // true (same content)
```

### Logical Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `&&` | AND | `true && false` → `false` |
| `\|\|` | OR | `true \|\| false` → `true` |
| `!` | NOT | `!true` → `false` |

```java
int age = 25;
boolean hasLicense = true;

boolean canDrive = (age >= 16) && hasLicense;  // true
boolean isMinorOrNoLicense = (age < 18) || !hasLicense;  // false
```

**Short-circuit evaluation:**
```java
// && stops if first condition is false
// || stops if first condition is true

boolean result = (false && expensiveFunction());  // expensiveFunction() never called!
```

---

## Operator Precedence

Operations happen in a specific order:

1. `()` - Parentheses (highest)
2. `!`, `++`, `--` - Unary operators
3. `*`, `/`, `%` - Multiplication, division
4. `+`, `-` - Addition, subtraction
5. `<`, `>`, `<=`, `>=` - Comparison
6. `==`, `!=` - Equality
7. `&&` - Logical AND
8. `||` - Logical OR
9. `=`, `+=`, `-=` - Assignment (lowest)

**When in doubt, use parentheses!**

```java
// Confusing
int result = 2 + 3 * 4;  // Is it 20 or 14?

// Clear
int result = 2 + (3 * 4);  // Obviously 14
```

---

## Common Pitfalls

### Pitfall 1: Integer Overflow

```java
int max = Integer.MAX_VALUE;  // 2,147,483,647
int overflow = max + 1;       // -2,147,483,648 (wraps around!)
```

**AI often forgets overflow checks.**

### Pitfall 2: Floating-Point Precision

```java
double result = 0.1 + 0.2;
System.out.println(result);  // 0.30000000000000004 (not 0.3!)
System.out.println(result == 0.3);  // false!
```

**Never use `==` with floating-point numbers.** Use a tolerance:
```java
boolean closeEnough = Math.abs(result - 0.3) < 0.0001;
```

### Pitfall 3: Integer Division

```java
double average = 5 / 2;  // 2.0, not 2.5!
double correct = 5.0 / 2;  // 2.5
```

### Pitfall 4: Uninitialized Variables

```java
int x;
System.out.println(x);  // ERROR! Variable might not be initialized
```

Local variables must be initialized before use.

### Pitfall 5: Confusing = and ==

```java
int x = 5;
if (x = 10) {  // ERROR in Java (good!)
    // This ASSIGNS 10 to x, doesn't compare
}

if (x == 10) {  // Correct: COMPARES x to 10
    // ...
}
```

Java catches this at compile time (unlike C/C++).

---

## Hands-On Project: Simple Calculator

Let's build a calculator that demonstrates these concepts:

```java
import java.util.Scanner;

public class Calculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        // Get first number
        System.out.print("Enter first number: ");
        double num1 = scanner.nextDouble();

        // Get operator
        System.out.print("Enter operator (+, -, *, /): ");
        char operator = scanner.next().charAt(0);

        // Get second number
        System.out.print("Enter second number: ");
        double num2 = scanner.nextDouble();

        // Calculate result
        double result;

        if (operator == '+') {
            result = num1 + num2;
        } else if (operator == '-') {
            result = num1 - num2;
        } else if (operator == '*') {
            result = num1 * num2;
        } else if (operator == '/') {
            if (num2 == 0) {
                System.out.println("Error: Cannot divide by zero!");
                scanner.close();
                return;
            }
            result = num1 / num2;
        } else {
            System.out.println("Error: Unknown operator!");
            scanner.close();
            return;
        }

        System.out.println(num1 + " " + operator + " " + num2 + " = " + result);
        scanner.close();
    }
}
```

**What this demonstrates:**
- Variable declaration and initialization
- Different data types (`double`, `char`)
- Arithmetic operators
- Comparison operators
- User input handling
- Basic error handling (division by zero)

---

## AI Integration: Evaluating AI Suggestions

Now that you understand data types, let's see how AI might help - and where it might fail.

**Prompt:** "Write a Java method to calculate the average of an array of integers"

**AI might generate:**
```java
public static int average(int[] numbers) {
    int sum = 0;
    for (int num : numbers) {
        sum += num;
    }
    return sum / numbers.length;
}
```

**Can you spot the issues?**

1. **Return type is `int`** - Average should be `double`
2. **Integer division** - `sum / numbers.length` loses decimals
3. **No null check** - Crashes if `numbers` is null
4. **No empty array check** - Division by zero if array is empty
5. **Possible overflow** - Sum could overflow for large arrays

**Better version:**
```java
public static double average(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        throw new IllegalArgumentException("Array must not be null or empty");
    }

    long sum = 0;  // Use long to prevent overflow
    for (int num : numbers) {
        sum += num;
    }
    return (double) sum / numbers.length;
}
```

**This is why understanding matters.** You can now evaluate and improve AI suggestions.

---

## Exercises

### Exercise 3.1: Type Exploration
Write a program that prints:
- The minimum and maximum values of each integer type
- The result of `Integer.MAX_VALUE + 1`
- What `(byte) 128` produces

### Exercise 3.2: Precision Test
Write code that demonstrates floating-point imprecision:
- Add 0.1 ten times. Is it exactly 1.0?
- Compare the result using `==` and using a tolerance

### Exercise 3.3: Temperature Converter
Create a program that:
- Accepts a temperature in Fahrenheit
- Converts to Celsius: `C = (F - 32) * 5/9`
- Displays both values with 2 decimal places
- Watch out for integer division!

### Exercise 3.4: Age Calculator
Build a program that:
- Asks for birth year
- Calculates age
- Determines if user can vote (18+), drink (21+), rent a car (25+)
- Uses appropriate data types throughout

### Exercise 3.5: Operator Practice
Without running the code, predict the output:
```java
int a = 10, b = 3;
System.out.println(a / b);
System.out.println(a % b);
System.out.println((double) a / b);
System.out.println(a / (double) b);
System.out.println((double) (a / b));
```

---

## Key Takeaways

- **Java has 8 primitive types** - Know them and their sizes
- **Use `int` for integers, `double` for decimals** by default
- **Suffixes matter** - `L` for long, `f` for float
- **Implicit casting is safe, explicit is dangerous**
- **Integer division truncates** - Cast to double for decimals
- **Floating-point is imprecise** - Never use `==` for comparison
- **`==` compares references for objects** - Use `.equals()` for content

---

## What's Next

You now understand Java's type system - the foundation of all Java code. In **Chapter 4**, we'll learn how to make decisions and repeat actions with control flow: if statements, loops, and more.

Your programs are about to get much more interesting.

---

*"Strong typing is not a limitation - it's a safety net that catches bugs before they reach your users."*
