# Chapter 5: OOP Pillars - Encapsulation, Inheritance, Polymorphism, Abstraction

> "AI writes code that works. It doesn't think about how that code will evolve, extend, or be maintained."

---

## The Four Pillars

These aren't just interview questions. They're tools for evaluating whether AI-generated code is structured well:

1. **Encapsulation** - Hiding internal details
2. **Inheritance** - Sharing code between related classes
3. **Polymorphism** - Same interface, different behaviors
4. **Abstraction** - Hiding complexity behind simple interfaces

When AI ignores these, you get code that works today but breaks tomorrow.

---

## Pillar 1: Encapsulation

### The Concept

**Hide the "how", expose the "what".**

A car hides its engine complexity. You just use steering wheel, pedals, gear shift.

### In Java

```java
// BAD - Exposed internals
public class BankAccount {
    public double balance;  // Anyone can modify!
}

account.balance = -1000;  // Oops, negative balance

// GOOD - Encapsulated
public class BankAccount {
    private double balance;

    public void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("Amount must be positive");
        this.balance += amount;
    }

    public void withdraw(double amount) {
        if (amount > balance) throw new InsufficientFundsException();
        this.balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

### Why It Matters

| Without Encapsulation | With Encapsulation |
|----------------------|-------------------|
| Any code can break invariants | Rules enforced in one place |
| Changes ripple everywhere | Changes isolated |
| No validation | Validation guaranteed |
| Debugging nightmare | Clear responsibility |

### Disaster 1: AI Exposes Everything

**AI code:**
```java
public class Order {
    public List<OrderItem> items;
    public double totalPrice;
    public String status;
}

// Later in code...
order.totalPrice = -50;  // Invalid!
order.status = "DELIVERED";  // Skipped SHIPPED!
order.items.clear();  // Lost all items!
```

**Fix - Encapsulate behavior:**
```java
public class Order {
    private List<OrderItem> items = new ArrayList<>();
    private double totalPrice;
    private OrderStatus status = OrderStatus.CREATED;

    public void addItem(OrderItem item) {
        items.add(item);
        recalculateTotal();
    }

    public void ship() {
        if (status != OrderStatus.PAID) {
            throw new IllegalStateException("Can only ship paid orders");
        }
        this.status = OrderStatus.SHIPPED;
    }

    public void deliver() {
        if (status != OrderStatus.SHIPPED) {
            throw new IllegalStateException("Can only deliver shipped orders");
        }
        this.status = OrderStatus.DELIVERED;
    }

    public List<OrderItem> getItems() {
        return Collections.unmodifiableList(items);  // Prevent modification
    }
}
```

---

## Pillar 2: Inheritance

### The Concept

**Share code between related classes.** Child class gets all parent capabilities, can add or modify.

### In Java

```java
// Parent class
public class Animal {
    protected String name;

    public void eat() {
        System.out.println(name + " is eating");
    }

    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// Child class
public class Dog extends Animal {
    public void bark() {
        System.out.println(name + " says woof!");
    }

    @Override
    public void eat() {
        System.out.println(name + " is eating dog food");  // Override parent behavior
    }
}
```

### The `extends` Keyword

```java
public class Manager extends Employee { }
// Manager IS-AN Employee
// Manager has everything Employee has, plus more
```

### The `super` Keyword

Access parent class:

```java
public class Dog extends Animal {
    @Override
    public void eat() {
        super.eat();  // Call parent's eat() first
        System.out.println("... and drinking water");
    }
}
```

### Disaster 2: AI Ignores Existing Base Classes

**Context:** Your team has `BaseEntity` with id, createdAt, updatedAt.

**AI code:**
```java
public class User {
    private Long id;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    private String name;
    private String email;
    // ... duplicate code
}

public class Product {
    private Long id;
    private LocalDateTime createdAt;
    private LocalDateTime updatedAt;
    private String name;
    private BigDecimal price;
    // ... same duplicate code
}
```

**Problems:**
- Duplicated 3 fields in every entity
- When you add `deletedAt`, must change every class
- Inconsistent behavior across entities

**Fix:**
```java
public abstract class BaseEntity {
    protected Long id;
    protected LocalDateTime createdAt;
    protected LocalDateTime updatedAt;

    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }

    @PreUpdate
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
}

public class User extends BaseEntity {
    private String name;
    private String email;
    // id, createdAt, updatedAt inherited
}

public class Product extends BaseEntity {
    private String name;
    private BigDecimal price;
    // id, createdAt, updatedAt inherited
}
```

### When to Use Inheritance

| Use Inheritance | Don't Use Inheritance |
|-----------------|----------------------|
| True IS-A relationship | Just to share code |
| Child is specialized parent | Unrelated classes |
| Shared behavior makes sense | Implementation might diverge |
| `Dog extends Animal` | `Car extends Engine` (HAS-A, not IS-A) |

### Composition Over Inheritance

Sometimes HAS-A is better than IS-A:

```java
// BAD - Stack doesn't need all of ArrayList
public class Stack extends ArrayList { }

// GOOD - Stack HAS-A list internally
public class Stack {
    private List<Object> items = new ArrayList<>();

    public void push(Object item) { items.add(item); }
    public Object pop() { return items.remove(items.size() - 1); }
}
```

---

## Pillar 3: Polymorphism

### The Concept

**Same interface, different implementations.** You call the same method, but behavior depends on the actual object type.

### In Java

```java
public class Animal {
    public void makeSound() {
        System.out.println("Some sound");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Woof!");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Meow!");
    }
}

// Polymorphism in action
Animal myPet = new Dog();  // Variable type: Animal, Object type: Dog
myPet.makeSound();         // Prints "Woof!" - Dog's implementation
```

### Why It Matters

```java
public void makeAllSoundsounds(List<Animal> animals) {
    for (Animal animal : animals) {
        animal.makeSound();  // Works for Dog, Cat, any Animal subclass
    }
}
```

One method handles all animal types, present and future.

### Disaster 3: AI Uses instanceof Instead of Polymorphism

**AI code:**
```java
public double calculatePay(Employee emp) {
    if (emp instanceof Manager) {
        return emp.getSalary() + ((Manager)emp).getBonus();
    } else if (emp instanceof Engineer) {
        return emp.getSalary() + ((Engineer)emp).getStockOptions();
    } else if (emp instanceof Intern) {
        return ((Intern)emp).getStipend();
    }
    return emp.getSalary();
}
```

**Problems:**
- Every new employee type = modify this method
- Easy to forget a type
- Violates Open/Closed Principle

**Fix - Use polymorphism:**
```java
public abstract class Employee {
    abstract double calculatePay();
}

public class Manager extends Employee {
    @Override
    double calculatePay() {
        return salary + bonus;
    }
}

public class Engineer extends Employee {
    @Override
    double calculatePay() {
        return salary + stockOptions;
    }
}

// Usage - no instanceof needed
double total = employees.stream()
    .mapToDouble(Employee::calculatePay)
    .sum();
```

---

## Pillar 4: Abstraction

### The Concept

**Hide complexity, expose simplicity.** Define "what" without "how".

### Abstract Classes

Partial implementation - some methods defined, some left abstract:

```java
public abstract class Shape {
    protected String color;

    public String getColor() {          // Concrete method
        return color;
    }

    public abstract double getArea();   // Abstract - subclasses must implement
}

public class Circle extends Shape {
    private double radius;

    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
}

public class Rectangle extends Shape {
    private double width, height;

    @Override
    public double getArea() {
        return width * height;
    }
}
```

### Interfaces

Pure contract - just "what", no "how":

```java
public interface PaymentProcessor {
    PaymentResult process(Payment payment);
    void refund(String transactionId);
}

public class StripeProcessor implements PaymentProcessor {
    @Override
    public PaymentResult process(Payment payment) {
        // Stripe-specific implementation
    }
}

public class PayPalProcessor implements PaymentProcessor {
    @Override
    public PaymentResult process(Payment payment) {
        // PayPal-specific implementation
    }
}
```

### Disaster 4: AI Hardcodes Implementation

**AI code:**
```java
public class OrderService {
    private StripeProcessor stripeProcessor = new StripeProcessor();

    public void processOrder(Order order) {
        stripeProcessor.charge(order.getTotal());
    }
}
```

**Problems:**
- Can't switch to PayPal without changing code
- Can't test without real Stripe calls
- Tight coupling

**Fix - Depend on abstraction:**
```java
public class OrderService {
    private PaymentProcessor paymentProcessor;

    public OrderService(PaymentProcessor processor) {
        this.paymentProcessor = processor;  // Inject dependency
    }

    public void processOrder(Order order) {
        paymentProcessor.process(order.getPayment());
    }
}

// Usage
OrderService service = new OrderService(new StripeProcessor());
// or for testing:
OrderService testService = new OrderService(new MockPaymentProcessor());
```

### Abstract Class vs Interface

| Abstract Class | Interface |
|---------------|-----------|
| IS-A relationship | CAN-DO relationship |
| Single inheritance | Multiple interfaces |
| Can have state (fields) | No state (pre-Java 8) |
| Can have constructors | No constructors |
| Partial implementation | Pure contract (mostly) |
| `Animal`, `Shape` | `Comparable`, `Serializable` |

---

## What to Tell AI

**Bad:** "Create payment processing"

**Good:** "Create a PaymentProcessor interface with process() and refund() methods. Then create StripeProcessor implementing it. The OrderService should depend on the interface, not the concrete class."

**Bad:** "Add a new employee type"

**Good:** "Create SalesRep extending Employee. Override calculatePay() to include commission. Don't modify the pay calculation loop - polymorphism handles it."

**Bad:** "Create a User class"

**Good:** "Create User extending our BaseEntity. Override toString(). Don't duplicate id/createdAt/updatedAt fields."

---

## Red Flags in AI Output

🚩 **`instanceof` checks in business logic**
```java
if (obj instanceof Type) { ... }  // Usually means missing polymorphism
```

🚩 **Hardcoded dependencies**
```java
private StripeProcessor processor = new StripeProcessor();  // Can't swap
```

🚩 **Duplicated fields across classes**
"Same id, createdAt, updatedAt in every entity"

🚩 **Public fields instead of methods**
```java
public List<Item> items;  // No control over modification
```

🚩 **Deep inheritance hierarchies**
Class extends class extends class extends... (usually bad design)

🚩 **Override without @Override**
```java
public String tostring() { }  // Bug! Should be toString()
```

---

## Quick Mental Checklist

- [ ] Are internals hidden (encapsulation)?
- [ ] Does this extend an existing base class (inheritance)?
- [ ] Can I add new types without changing existing code (polymorphism)?
- [ ] Am I depending on abstractions, not concretions (abstraction)?
- [ ] Is this IS-A or HAS-A relationship?
- [ ] Will this design survive requirement changes?

---

## Chapter 5 Quiz

**Q1: AI made all fields in Order class public. What principle is violated?**
- a) Inheritance
- b) Encapsulation
- c) Polymorphism
- d) Abstraction

<details>
<summary>Answer</summary>

**b) Encapsulation** - Public fields expose internal state. Anyone can modify them without validation. Should be private with controlled methods.
</details>

---

**Q2: AI duplicated id, createdAt, updatedAt in User, Product, Order classes. What should be done?**
- a) Keep it - explicit is better
- b) Create BaseEntity and have all classes extend it
- c) Use static fields
- d) Use interfaces

<details>
<summary>Answer</summary>

**b) Extend BaseEntity** - Common fields should be in a base class. DRY (Don't Repeat Yourself) principle.
</details>

---

**Q3: What does `@Override` do?**
- a) Makes method faster
- b) Tells compiler to verify method actually overrides parent method
- c) Makes method private
- d) Creates a new method

<details>
<summary>Answer</summary>

**b) Compiler verification** - If you misspell a method name or change signature, compiler catches it. Without @Override, you'd create a new method instead of overriding.
</details>

---

**Q4: AI wrote `if (emp instanceof Manager) {...}` for 5 employee types. What's the better approach?**
- a) Add more instanceof checks
- b) Use polymorphism - make calculatePay() abstract in Employee
- c) Use switch statement
- d) Keep it - it's explicit

<details>
<summary>Answer</summary>

**b) Polymorphism** - Each Employee subclass implements its own calculatePay(). No instanceof needed. Adding new types doesn't require changing existing code.
</details>

---

**Q5: What's the difference between abstract class and interface?**
- a) No difference
- b) Abstract class can have state and partial implementation; interface is pure contract
- c) Interface can have state; abstract class cannot
- d) Abstract class is faster

<details>
<summary>Answer</summary>

**b) State and implementation** - Abstract classes can have fields and implemented methods. Interfaces are primarily contracts (pure method signatures).
</details>

---

**Q6: AI hardcoded `new StripeProcessor()` in OrderService. What problem does this cause?**
- a) Performance issue
- b) Can't switch payment providers or write tests with mocks
- c) Memory leak
- d) No problem

<details>
<summary>Answer</summary>

**b) Can't switch or mock** - Hardcoded dependency means you can't use PayPal without changing code, and you can't test without real Stripe calls.
</details>

---

**Q7: When should you use inheritance vs composition?**
- a) Always use inheritance - it's OOP
- b) Inheritance for IS-A, composition for HAS-A relationships
- c) Always use composition - inheritance is bad
- d) Use whichever is shorter

<details>
<summary>Answer</summary>

**b) IS-A vs HAS-A** - Dog IS-A Animal (inheritance). Car HAS-A Engine (composition). Misusing inheritance leads to awkward designs.
</details>

---

**Q8: What does `super.method()` do?**
- a) Makes method static
- b) Calls the parent class's implementation of the method
- c) Makes method public
- d) Creates a new method

<details>
<summary>Answer</summary>

**b) Calls parent implementation** - Useful when you want to extend parent behavior, not replace it entirely.
</details>

---

**Q9: AI created `Stack extends ArrayList`. What's wrong?**
- a) Nothing wrong
- b) Stack isn't an ArrayList - should use composition (Stack HAS-A List)
- c) Should extend LinkedList instead
- d) Should be interface

<details>
<summary>Answer</summary>

**b) Wrong relationship** - Stack shouldn't expose all ArrayList methods (like get, set at index). Stack should HAS-A list internally, exposing only push/pop.
</details>

---

**Q10: Why return `Collections.unmodifiableList(items)` instead of `items` directly?**
- a) Performance
- b) Prevents external code from modifying the internal list
- c) Memory savings
- d) Type safety

<details>
<summary>Answer</summary>

**b) Prevents modification** - Returning the actual list lets callers add/remove items, breaking encapsulation. Unmodifiable wrapper prevents this while allowing read access.
</details>

---

## Key Takeaways

- **Encapsulation:** Private fields, public methods with validation
- **Inheritance:** Extend base classes, don't duplicate code
- **Polymorphism:** Same interface, different behaviors - no instanceof
- **Abstraction:** Depend on interfaces, not concrete classes
- **AI misses context** - It doesn't know your existing classes and interfaces
- **Design for change** - Code that's easy to extend is well-designed

---

## What's Next

**Chapter 6: Error Handling**

Exceptions in Java. When to throw, when to catch. What AI gets wrong about error handling.

---

*"Good OOP makes code easy to change. Bad OOP makes every change a risk."*
