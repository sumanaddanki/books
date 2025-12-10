# Chapter 5: Object-Oriented Programming - Classes and Objects

> "The real world is made of objects. So is well-designed software."

---

## What You'll Learn

- Why Object-Oriented Programming (OOP) exists
- The difference between classes and objects
- How to define classes with attributes and methods
- Creating objects with the `new` keyword
- Constructors: default and parameterized
- The `this` keyword and its uses
- Static vs instance members
- Access modifiers: public, private, protected, default
- Why understanding OOP is crucial for evaluating AI-generated code

---

## Why Object-Oriented Programming?

Before OOP, programs were organized as sequences of instructions (procedural programming). This worked for small programs but became chaotic as programs grew:

- **Code duplication** - Same logic copied everywhere
- **Tight coupling** - Change one thing, break everything
- **Hard to understand** - Where does this data belong?
- **Difficult to extend** - Adding features means rewriting

OOP solves these by organizing code around **objects** - bundles of related data and behavior.

### The Real-World Analogy

Think about a car:

**Procedural thinking:**
- There's an engine somewhere
- There are wheels somewhere
- There's a function to start the car
- There's a function to accelerate

**Object-oriented thinking:**
- A Car object has: engine, wheels, color, speed
- A Car can: start(), accelerate(), brake(), turn()
- The Car encapsulates all car-related stuff

OOP mirrors how we naturally think about the world.

---

## Classes vs Objects

This is the most fundamental concept in OOP.

### Class: The Blueprint

A class defines the structure - what attributes and behaviors something will have.

```java
// This is a blueprint, not an actual student
public class Student {
    String name;
    int age;
    double gpa;

    void study() {
        System.out.println(name + " is studying.");
    }
}
```

### Object: The Reality

An object is a specific instance created from the blueprint.

```java
// These are actual students, created from the blueprint
Student alice = new Student();
Student bob = new Student();

alice.name = "Alice";
alice.age = 20;

bob.name = "Bob";
bob.age = 22;
```

### The Analogy

| Blueprint | Reality |
|-----------|---------|
| House plans | Your actual house |
| Cookie cutter | Individual cookies |
| Class | Object |

You can create **many objects** from **one class**.

---

## Defining a Class

### Basic Structure

```java
public class ClassName {
    // Attributes (instance variables)
    // Constructors
    // Methods
}
```

### A Complete Example

```java
public class BankAccount {
    // Attributes (what the object HAS)
    String accountNumber;
    String ownerName;
    double balance;

    // Constructor (how to CREATE the object)
    public BankAccount(String accountNumber, String ownerName) {
        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = 0.0;
    }

    // Methods (what the object can DO)
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Deposited: $" + amount);
        }
    }

    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            System.out.println("Withdrew: $" + amount);
        } else {
            System.out.println("Invalid withdrawal amount");
        }
    }

    public double getBalance() {
        return balance;
    }

    public void displayInfo() {
        System.out.println("Account: " + accountNumber);
        System.out.println("Owner: " + ownerName);
        System.out.println("Balance: $" + balance);
    }
}
```

---

## Creating Objects

### The `new` Keyword

Objects are created using `new`:

```java
BankAccount myAccount = new BankAccount("12345", "Alice");
```

**What happens:**
1. Memory is allocated for the new object
2. Instance variables are initialized
3. Constructor is called
4. Reference to object is returned

### Using Objects

```java
public class BankDemo {
    public static void main(String[] args) {
        // Create objects
        BankAccount aliceAccount = new BankAccount("001", "Alice");
        BankAccount bobAccount = new BankAccount("002", "Bob");

        // Use objects
        aliceAccount.deposit(1000);
        aliceAccount.withdraw(250);
        aliceAccount.displayInfo();

        System.out.println();

        bobAccount.deposit(500);
        bobAccount.displayInfo();
    }
}
```

Output:
```
Deposited: $1000
Withdrew: $250
Account: 001
Owner: Alice
Balance: $750.0

Deposited: $500
Account: 002
Owner: Bob
Balance: $500.0
```

---

## Constructors

Constructors are special methods that initialize objects.

### Default Constructor

If you don't write any constructor, Java provides a default one:

```java
public class Simple {
    int value;
    // Java provides: public Simple() { }
}

Simple s = new Simple();  // value is 0
```

### Custom Constructors

```java
public class Product {
    String name;
    double price;
    int quantity;

    // Constructor with all parameters
    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }
}

Product laptop = new Product("MacBook", 1299.99, 10);
```

### Constructor Overloading

Multiple constructors with different parameters:

```java
public class Product {
    String name;
    double price;
    int quantity;

    // Full constructor
    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    // Constructor with default quantity
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
        this.quantity = 0;  // Default value
    }

    // Constructor with just name
    public Product(String name) {
        this.name = name;
        this.price = 0.0;
        this.quantity = 0;
    }
}

// All valid:
Product p1 = new Product("Laptop", 999.99, 5);
Product p2 = new Product("Mouse", 29.99);
Product p3 = new Product("Unknown Item");
```

### Constructor Chaining with `this()`

Avoid code duplication by calling one constructor from another:

```java
public class Product {
    String name;
    double price;
    int quantity;

    // Main constructor
    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    // Calls main constructor
    public Product(String name, double price) {
        this(name, price, 0);  // Calls the 3-parameter constructor
    }

    // Calls 2-parameter constructor
    public Product(String name) {
        this(name, 0.0);  // Which calls the 3-parameter constructor
    }
}
```

**Rule:** `this()` must be the first statement in the constructor.

---

## The `this` Keyword

`this` refers to the current object.

### Distinguishing Instance Variables from Parameters

```java
public class Person {
    String name;  // Instance variable

    public Person(String name) {  // Parameter with same name
        this.name = name;  // this.name = instance variable
                           // name = parameter
    }
}
```

### Returning the Current Object

Useful for method chaining:

```java
public class Builder {
    String value = "";

    public Builder append(String text) {
        this.value += text;
        return this;  // Returns current object
    }

    public String build() {
        return value;
    }
}

// Method chaining
String result = new Builder()
    .append("Hello")
    .append(" ")
    .append("World")
    .build();
// result = "Hello World"
```

### Passing Current Object to Methods

```java
public class Event {
    public void register(Listener listener) {
        // ...
    }
}

public class MyListener {
    public void setup(Event event) {
        event.register(this);  // Pass this object
    }
}
```

---

## Instance vs Static Members

### Instance Members

Belong to each object. Each object has its own copy.

```java
public class Counter {
    int count = 0;  // Instance variable

    void increment() {  // Instance method
        count++;
    }
}

Counter c1 = new Counter();
Counter c2 = new Counter();

c1.increment();
c1.increment();
c2.increment();

System.out.println(c1.count);  // 2
System.out.println(c2.count);  // 1 (separate count!)
```

### Static Members

Belong to the class. Shared by all objects.

```java
public class Counter {
    static int totalCount = 0;  // Static variable (shared)
    int myCount = 0;            // Instance variable (per object)

    void increment() {
        myCount++;
        totalCount++;  // All objects affect this
    }
}

Counter c1 = new Counter();
Counter c2 = new Counter();

c1.increment();
c1.increment();
c2.increment();

System.out.println(c1.myCount);        // 2
System.out.println(c2.myCount);        // 1
System.out.println(Counter.totalCount); // 3 (shared!)
```

### When to Use Static

**Use static for:**
- Constants: `static final double PI = 3.14159;`
- Utility methods: `Math.sqrt()`, `Arrays.sort()`
- Factory methods: `LocalDate.now()`
- Counters/tracking across all instances

**Don't use static for:**
- Data that varies per object
- Methods that need instance data

### Static Methods Can't Access Instance Members

```java
public class Example {
    int instanceVar = 10;
    static int staticVar = 20;

    static void staticMethod() {
        System.out.println(staticVar);    // OK
        System.out.println(instanceVar);  // ERROR! No instance
    }

    void instanceMethod() {
        System.out.println(staticVar);    // OK
        System.out.println(instanceVar);  // OK
    }
}
```

---

## Access Modifiers

Control who can access your class members.

### The Four Levels

| Modifier | Class | Package | Subclass | World |
|----------|-------|---------|----------|-------|
| `public` | ✓ | ✓ | ✓ | ✓ |
| `protected` | ✓ | ✓ | ✓ | ✗ |
| (default) | ✓ | ✓ | ✗ | ✗ |
| `private` | ✓ | ✗ | ✗ | ✗ |

### Best Practice: Hide Your Data

```java
public class Person {
    // BAD: Public fields can be changed to anything
    public String name;
    public int age;
}

Person p = new Person();
p.age = -50;  // Invalid but allowed!
```

```java
public class Person {
    // GOOD: Private fields with controlled access
    private String name;
    private int age;

    public void setAge(int age) {
        if (age >= 0 && age <= 150) {
            this.age = age;
        } else {
            throw new IllegalArgumentException("Invalid age");
        }
    }

    public int getAge() {
        return age;
    }
}

Person p = new Person();
p.setAge(-50);  // Throws exception - invalid!
```

This is **encapsulation** - we'll explore it deeply in Chapter 6.

---

## Getters and Setters

Standard pattern for controlled access:

```java
public class Employee {
    private String name;
    private double salary;
    private String department;

    // Getter for name
    public String getName() {
        return name;
    }

    // Setter for name
    public void setName(String name) {
        if (name != null && !name.trim().isEmpty()) {
            this.name = name;
        }
    }

    // Getter for salary
    public double getSalary() {
        return salary;
    }

    // Setter for salary with validation
    public void setSalary(double salary) {
        if (salary >= 0) {
            this.salary = salary;
        }
    }

    // Getter only (read-only externally)
    public String getDepartment() {
        return department;
    }

    // Package-private setter (only same package can set)
    void setDepartment(String department) {
        this.department = department;
    }
}
```

### Naming Conventions

- Getter: `getPropertyName()` (or `isPropertyName()` for booleans)
- Setter: `setPropertyName(value)`

```java
private boolean active;

public boolean isActive() {  // Note: "is" not "get" for boolean
    return active;
}

public void setActive(boolean active) {
    this.active = active;
}
```

---

## Object Relationships

### Objects Containing Objects (Composition)

```java
public class Engine {
    private int horsepower;
    private String type;

    public Engine(int horsepower, String type) {
        this.horsepower = horsepower;
        this.type = type;
    }

    public void start() {
        System.out.println(type + " engine starting...");
    }
}

public class Car {
    private String model;
    private Engine engine;  // Car HAS-A Engine

    public Car(String model, Engine engine) {
        this.model = model;
        this.engine = engine;
    }

    public void start() {
        System.out.println("Starting " + model);
        engine.start();  // Delegate to engine
    }
}

// Usage
Engine v8 = new Engine(450, "V8");
Car mustang = new Car("Mustang", v8);
mustang.start();
```

Output:
```
Starting Mustang
V8 engine starting...
```

### Objects Referencing Objects

```java
public class Person {
    private String name;
    private Person bestFriend;  // Reference to another Person

    public Person(String name) {
        this.name = name;
    }

    public void setBestFriend(Person friend) {
        this.bestFriend = friend;
    }

    public void greetFriend() {
        if (bestFriend != null) {
            System.out.println(name + " says hi to " + bestFriend.name);
        }
    }
}

Person alice = new Person("Alice");
Person bob = new Person("Bob");

alice.setBestFriend(bob);
bob.setBestFriend(alice);

alice.greetFriend();  // Alice says hi to Bob
bob.greetFriend();    // Bob says hi to Alice
```

---

## The `toString()` Method

Default object printing isn't useful:

```java
Person p = new Person("Alice", 25);
System.out.println(p);  // Person@1b6d3586 (not helpful!)
```

Override `toString()` for meaningful output:

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}

Person p = new Person("Alice", 25);
System.out.println(p);  // Person{name='Alice', age=25}
```

---

## Hands-On Project: Student Management System

Let's build a complete system:

```java
// Student.java
public class Student {
    private static int nextId = 1;  // Shared counter

    private int id;
    private String name;
    private String email;
    private double[] grades;
    private int gradeCount;

    public Student(String name, String email) {
        this.id = nextId++;  // Auto-increment ID
        this.name = name;
        this.email = email;
        this.grades = new double[10];  // Max 10 grades
        this.gradeCount = 0;
    }

    // Getters
    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    // Setters with validation
    public void setName(String name) {
        if (name != null && !name.trim().isEmpty()) {
            this.name = name;
        }
    }

    public void setEmail(String email) {
        if (email != null && email.contains("@")) {
            this.email = email;
        }
    }

    // Grade management
    public void addGrade(double grade) {
        if (grade >= 0 && grade <= 100 && gradeCount < grades.length) {
            grades[gradeCount++] = grade;
        }
    }

    public double getGPA() {
        if (gradeCount == 0) return 0.0;

        double sum = 0;
        for (int i = 0; i < gradeCount; i++) {
            sum += grades[i];
        }
        return sum / gradeCount;
    }

    public String getLetterGrade() {
        double gpa = getGPA();
        if (gpa >= 90) return "A";
        if (gpa >= 80) return "B";
        if (gpa >= 70) return "C";
        if (gpa >= 60) return "D";
        return "F";
    }

    // Display
    public void displayInfo() {
        System.out.println("ID: " + id);
        System.out.println("Name: " + name);
        System.out.println("Email: " + email);
        System.out.println("GPA: " + String.format("%.2f", getGPA()));
        System.out.println("Letter Grade: " + getLetterGrade());
    }

    @Override
    public String toString() {
        return "Student #" + id + ": " + name + " (" + getLetterGrade() + ")";
    }

    // Static method to get total students created
    public static int getTotalStudents() {
        return nextId - 1;
    }
}
```

```java
// StudentManager.java
public class StudentManager {
    private Student[] students;
    private int count;

    public StudentManager(int capacity) {
        students = new Student[capacity];
        count = 0;
    }

    public void addStudent(Student student) {
        if (count < students.length) {
            students[count++] = student;
            System.out.println("Added: " + student.getName());
        } else {
            System.out.println("Cannot add more students. Capacity reached.");
        }
    }

    public Student findById(int id) {
        for (int i = 0; i < count; i++) {
            if (students[i].getId() == id) {
                return students[i];
            }
        }
        return null;  // Not found
    }

    public Student findByName(String name) {
        for (int i = 0; i < count; i++) {
            if (students[i].getName().equalsIgnoreCase(name)) {
                return students[i];
            }
        }
        return null;
    }

    public void displayAllStudents() {
        System.out.println("\n=== All Students ===");
        if (count == 0) {
            System.out.println("No students registered.");
            return;
        }

        for (int i = 0; i < count; i++) {
            System.out.println(students[i]);
        }
        System.out.println("Total: " + count + " students");
    }

    public void displayHonorRoll() {
        System.out.println("\n=== Honor Roll (GPA >= 90) ===");
        boolean found = false;

        for (int i = 0; i < count; i++) {
            if (students[i].getGPA() >= 90) {
                students[i].displayInfo();
                System.out.println();
                found = true;
            }
        }

        if (!found) {
            System.out.println("No students on honor roll.");
        }
    }

    public double getAverageGPA() {
        if (count == 0) return 0.0;

        double sum = 0;
        for (int i = 0; i < count; i++) {
            sum += students[i].getGPA();
        }
        return sum / count;
    }
}
```

```java
// Main.java
public class Main {
    public static void main(String[] args) {
        // Create manager
        StudentManager manager = new StudentManager(100);

        // Create students
        Student alice = new Student("Alice Johnson", "alice@school.edu");
        Student bob = new Student("Bob Smith", "bob@school.edu");
        Student charlie = new Student("Charlie Brown", "charlie@school.edu");

        // Add grades
        alice.addGrade(95);
        alice.addGrade(88);
        alice.addGrade(92);

        bob.addGrade(75);
        bob.addGrade(82);
        bob.addGrade(78);

        charlie.addGrade(91);
        charlie.addGrade(94);
        charlie.addGrade(89);

        // Add to manager
        manager.addStudent(alice);
        manager.addStudent(bob);
        manager.addStudent(charlie);

        // Display all
        manager.displayAllStudents();

        // Find specific student
        System.out.println("\n=== Finding Bob ===");
        Student found = manager.findByName("Bob");
        if (found != null) {
            found.displayInfo();
        }

        // Honor roll
        manager.displayHonorRoll();

        // Statistics
        System.out.println("\n=== Statistics ===");
        System.out.println("Total students created: " + Student.getTotalStudents());
        System.out.println("Average GPA: " + String.format("%.2f", manager.getAverageGPA()));
    }
}
```

---

## AI Integration: Evaluating AI-Generated Classes

Now let's see how AI might generate similar code.

**Prompt:** "Create a Java class for managing student grades"

**AI might generate:**

```java
public class Student {
    public String name;
    public int[] grades;

    public double getAverage() {
        int sum = 0;
        for (int g : grades) {
            sum += g;
        }
        return sum / grades.length;
    }
}
```

**Issues you can now identify:**

| Problem | Why It's Bad |
|---------|--------------|
| Public fields | No encapsulation, no validation |
| No constructor | How do you create a valid Student? |
| Integer division | Returns wrong average (int/int) |
| No null check | Crashes if grades is null |
| No bounds checking | What if grades is empty? |
| Fixed array | Can't add grades after creation |
| No ID system | Can't distinguish students |

**Your understanding lets you improve AI output significantly.**

---

## Common OOP Pitfalls

### Pitfall 1: Forgetting `this`

```java
public class Person {
    String name;

    public Person(String name) {
        name = name;  // WRONG! Assigns parameter to itself
    }
}

// Correct:
public Person(String name) {
    this.name = name;
}
```

### Pitfall 2: Static Methods Accessing Instance Data

```java
public class Counter {
    int count = 0;

    public static void increment() {
        count++;  // ERROR! Static can't access instance
    }
}
```

### Pitfall 3: Not Initializing Objects

```java
Student s;
s.setName("Alice");  // NullPointerException! s is null

// Correct:
Student s = new Student("Alice", "alice@email.com");
```

### Pitfall 4: Comparing Objects with ==

```java
Student s1 = new Student("Alice");
Student s2 = new Student("Alice");

if (s1 == s2) {  // FALSE! Different objects
    // ...
}

// Need to override equals() for content comparison
```

---

## Exercises

### Exercise 5.1: Rectangle Class
Create a `Rectangle` class with:
- Private width and height
- Constructor(s)
- Getters and setters with validation (positive values only)
- Methods: `getArea()`, `getPerimeter()`, `isSquare()`
- Override `toString()`

### Exercise 5.2: Book and Library
Create:
- `Book` class (title, author, ISBN, available)
- `Library` class that manages an array of Books
- Methods: addBook, findByTitle, findByAuthor, checkOut, returnBook

### Exercise 5.3: Counter with History
Create a `Counter` class that:
- Has a current count
- Tracks how many times increment/decrement was called
- Has static method to get total operations across all counters
- Can reset to zero

### Exercise 5.4: Temperature Class
Create a `Temperature` class that:
- Stores temperature internally in Celsius
- Can be constructed with Celsius or Fahrenheit
- Has `getCelsius()`, `getFahrenheit()`, `getKelvin()`
- Has `isFreezing()`, `isBoiling()`

### Exercise 5.5: Refactor AI Code
Take the AI-generated Student class from the AI Integration section and refactor it to be production-quality with all the concepts from this chapter.

---

## Key Takeaways

- **Class = blueprint, Object = instance** - One class, many objects
- **Constructors initialize objects** - Can be overloaded
- **`this` refers to current object** - Essential for disambiguation
- **Static = class-level, Instance = object-level** - Know when to use each
- **Access modifiers control visibility** - Prefer private fields
- **Getters/setters enable controlled access** - Add validation
- **Objects can contain/reference other objects** - Composition is powerful

---

## What's Next

You now understand the basics of classes and objects. In **Chapter 6**, we'll explore the Four Pillars of OOP: Encapsulation, Inheritance, Polymorphism, and Abstraction. These principles are what make OOP truly powerful - and what you need to understand to evaluate any Java code, AI-generated or otherwise.

---

*"OOP is not about objects. It's about messages between objects - how they communicate and collaborate."*
