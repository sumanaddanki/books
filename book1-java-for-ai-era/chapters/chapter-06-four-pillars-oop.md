# Chapter 6: The Four Pillars of Object-Oriented Programming

> "Master these four concepts, and you'll understand 90% of all Java code you'll ever read."

---

## What You'll Learn

- **Encapsulation:** Hiding complexity and protecting data
- **Inheritance:** Building on existing code
- **Polymorphism:** One interface, many implementations
- **Abstraction:** Hiding implementation details
- How these pillars work together in real code
- Why understanding these principles is essential for evaluating AI-generated code

---

## The Big Picture

The four pillars aren't just academic concepts - they're practical tools that solve real problems:

| Pillar | Problem It Solves |
|--------|-------------------|
| Encapsulation | "Anyone can break my data" |
| Inheritance | "I keep rewriting the same code" |
| Polymorphism | "I need different behaviors for different types" |
| Abstraction | "The complexity is overwhelming" |

Let's explore each one in depth.

---

## Pillar 1: Encapsulation

### What Is Encapsulation?

Encapsulation is bundling data with the methods that operate on it, and restricting direct access to the data.

**Analogy:** Think of a TV remote:
- You press buttons (methods)
- You don't touch the circuit board (data)
- The remote protects its internals from you

### The Problem Without Encapsulation

```java
public class BankAccount {
    public double balance;  // Anyone can access!
}

// Dangerous usage
BankAccount account = new BankAccount();
account.balance = -1000000;  // Invalid negative balance!
account.balance = Double.MAX_VALUE;  // Unrealistic!
```

No validation. No protection. Anyone can set any value.

### The Solution: Encapsulation

```java
public class BankAccount {
    private double balance;  // Hidden from outside

    public BankAccount(double initialDeposit) {
        if (initialDeposit >= 0) {
            this.balance = initialDeposit;
        } else {
            this.balance = 0;
        }
    }

    public double getBalance() {
        return balance;  // Read-only access
    }

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    public boolean withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            return true;
        }
        return false;  // Withdrawal failed
    }
}
```

Now:
- Balance can't go negative
- All changes go through validated methods
- Internal representation can change without breaking external code

### Benefits of Encapsulation

1. **Data Protection:** Invalid states are prevented
2. **Controlled Access:** You decide who can read/write
3. **Flexibility:** Change internals without changing interface
4. **Debugging:** All changes go through known paths

### Encapsulation Best Practices

```java
public class Person {
    // 1. Make fields private
    private String name;
    private LocalDate birthDate;
    private String ssn;  // Sensitive data

    // 2. Provide getters for readable data
    public String getName() {
        return name;
    }

    // 3. Provide setters with validation
    public void setName(String name) {
        if (name != null && name.length() >= 2) {
            this.name = name;
        }
    }

    // 4. Computed properties (no setter)
    public int getAge() {
        return Period.between(birthDate, LocalDate.now()).getYears();
    }

    // 5. Restricted access for sensitive data
    public String getLastFourSSN() {
        return "***-**-" + ssn.substring(ssn.length() - 4);
    }

    // No getSSN() - we don't expose full SSN!
}
```

---

## Pillar 2: Inheritance

### What Is Inheritance?

Inheritance allows a class to inherit attributes and methods from another class.

**Analogy:** Children inherit traits from parents:
- You get your parent's eye color (inherited)
- You develop your own personality (added)
- You might do some things differently (overridden)

### The Problem Without Inheritance

```java
public class Dog {
    String name;
    int age;

    void eat() { System.out.println(name + " is eating"); }
    void sleep() { System.out.println(name + " is sleeping"); }
    void bark() { System.out.println(name + " says Woof!"); }
}

public class Cat {
    String name;  // Duplicated!
    int age;      // Duplicated!

    void eat() { System.out.println(name + " is eating"); }   // Duplicated!
    void sleep() { System.out.println(name + " is sleeping"); }  // Duplicated!
    void meow() { System.out.println(name + " says Meow!"); }
}
```

Code duplication! If we fix a bug in `eat()`, we must fix it everywhere.

### The Solution: Inheritance

```java
// Parent class (superclass)
public class Animal {
    protected String name;
    protected int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void eat() {
        System.out.println(name + " is eating");
    }

    public void sleep() {
        System.out.println(name + " is sleeping");
    }

    public String getName() {
        return name;
    }
}

// Child class (subclass)
public class Dog extends Animal {

    public Dog(String name, int age) {
        super(name, age);  // Call parent constructor
    }

    public void bark() {
        System.out.println(name + " says Woof!");
    }
}

// Another child class
public class Cat extends Animal {

    public Cat(String name, int age) {
        super(name, age);
    }

    public void meow() {
        System.out.println(name + " says Meow!");
    }
}
```

Usage:
```java
Dog buddy = new Dog("Buddy", 3);
buddy.eat();   // Inherited from Animal
buddy.sleep(); // Inherited from Animal
buddy.bark();  // Defined in Dog

Cat whiskers = new Cat("Whiskers", 5);
whiskers.eat();  // Inherited from Animal
whiskers.meow(); // Defined in Cat
```

### The `extends` Keyword

```java
public class Child extends Parent {
    // Child inherits all non-private members from Parent
}
```

### The `super` Keyword

`super` refers to the parent class.

```java
public class Dog extends Animal {

    private String breed;

    public Dog(String name, int age, String breed) {
        super(name, age);  // MUST be first line - calls Animal constructor
        this.breed = breed;
    }

    @Override
    public void eat() {
        super.eat();  // Call parent's eat()
        System.out.println(name + " wags tail while eating");
    }
}
```

### Method Overriding

Child classes can provide their own implementation of inherited methods:

```java
public class Animal {
    public void makeSound() {
        System.out.println("Some generic sound");
    }
}

public class Dog extends Animal {
    @Override  // Good practice: always use this annotation
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
```

The `@Override` annotation:
- Documents intent to override
- Compiler checks that parent method exists
- Prevents typos in method names

### Inheritance Rules

1. **Single inheritance:** Java classes can only extend one class
2. **Everything extends Object:** All classes implicitly extend `java.lang.Object`
3. **Private members aren't inherited:** They exist but aren't accessible
4. **Constructors aren't inherited:** But you can call them with `super()`

### The `protected` Modifier

```java
public class Parent {
    private int privateVar;     // Only Parent can access
    protected int protectedVar; // Parent and children can access
    public int publicVar;       // Anyone can access
}

public class Child extends Parent {
    public void demo() {
        // privateVar = 1;    // ERROR - can't access
        protectedVar = 2;     // OK - protected is accessible
        publicVar = 3;        // OK - public is accessible
    }
}
```

### When to Use Inheritance

**Good use (IS-A relationship):**
- Dog IS-A Animal
- Car IS-A Vehicle
- SavingsAccount IS-A BankAccount

**Bad use (HAS-A relationship - use composition instead):**
- Car HAS-A Engine (don't extend Engine!)
- Person HAS-A Address (don't extend Address!)

---

## Pillar 3: Polymorphism

### What Is Polymorphism?

Polymorphism means "many forms." The same method call can behave differently depending on the object type.

**Analogy:** The word "open" means different things:
- Open a door (swing it)
- Open a book (turn pages)
- Open a file (load into memory)
- Same word, different behaviors based on context

### The Problem Without Polymorphism

```java
public class AnimalShelter {
    public void makeAllSoundsOld(Dog[] dogs, Cat[] cats, Bird[] birds) {
        for (Dog d : dogs) {
            d.bark();
        }
        for (Cat c : cats) {
            c.meow();
        }
        for (Bird b : birds) {
            b.chirp();
        }
    }
    // Add a new animal? Must modify this method!
}
```

### The Solution: Polymorphism

```java
public class Animal {
    public void makeSound() {
        System.out.println("...");
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

public class Bird extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Chirp!");
    }
}

public class AnimalShelter {
    public void makeAllSounds(Animal[] animals) {
        for (Animal animal : animals) {
            animal.makeSound();  // Correct method called automatically!
        }
    }
}
```

Usage:
```java
Animal[] shelter = {
    new Dog("Buddy", 3),
    new Cat("Whiskers", 2),
    new Bird("Tweety", 1),
    new Dog("Max", 5)
};

AnimalShelter s = new AnimalShelter();
s.makeAllSounds(shelter);
// Output: Woof! Meow! Chirp! Woof!
```

Add a new animal? Just create a new class that extends Animal. The shelter code doesn't change!

### Compile-Time vs Runtime Polymorphism

**Compile-time (Method Overloading):**
```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }
}

Calculator calc = new Calculator();
calc.add(1, 2);       // Calls int version
calc.add(1.5, 2.5);   // Calls double version
calc.add(1, 2, 3);    // Calls three-parameter version
```

The compiler decides which method to call based on arguments.

**Runtime (Method Overriding):**
```java
Animal animal = new Dog("Buddy", 3);  // Reference is Animal, object is Dog
animal.makeSound();  // Calls Dog's makeSound() - decided at runtime!
```

The JVM decides which method to call based on actual object type.

### Polymorphism with Arrays and Collections

```java
// Array can hold any Animal (or subclass)
Animal[] animals = new Animal[3];
animals[0] = new Dog("Buddy", 3);
animals[1] = new Cat("Whiskers", 2);
animals[2] = new Animal("Generic", 1);

// Process uniformly
for (Animal a : animals) {
    System.out.println(a.getName());  // All animals have getName()
    a.makeSound();  // Each calls its own version
}
```

### Type Checking and Casting

Sometimes you need to know the actual type:

```java
Animal animal = new Dog("Buddy", 3);

// Check type with instanceof
if (animal instanceof Dog) {
    Dog dog = (Dog) animal;  // Cast to Dog
    dog.bark();  // Now we can call Dog-specific methods
}

// Java 16+ pattern matching
if (animal instanceof Dog dog) {
    dog.bark();  // dog is automatically cast
}
```

**Warning:** Excessive type checking often indicates poor design. Prefer polymorphism.

---

## Pillar 4: Abstraction

### What Is Abstraction?

Abstraction means hiding complex implementation details and showing only what's necessary.

**Analogy:** Driving a car:
- You know: steering wheel, pedals, gear shift
- You don't need to know: fuel injection timing, transmission gear ratios
- The car abstracts away complexity

### Abstract Classes

An abstract class can't be instantiated directly. It's a template for subclasses.

```java
public abstract class Shape {
    protected String color;

    public Shape(String color) {
        this.color = color;
    }

    // Abstract method - no implementation
    public abstract double getArea();

    // Abstract method
    public abstract double getPerimeter();

    // Concrete method - has implementation
    public void displayColor() {
        System.out.println("Color: " + color);
    }
}
```

```java
public class Circle extends Shape {
    private double radius;

    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }

    @Override
    public double getPerimeter() {
        return 2 * Math.PI * radius;
    }
}

public class Rectangle extends Shape {
    private double width;
    private double height;

    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }

    @Override
    public double getArea() {
        return width * height;
    }

    @Override
    public double getPerimeter() {
        return 2 * (width + height);
    }
}
```

Usage:
```java
// Shape s = new Shape("red");  // ERROR! Can't instantiate abstract class

Shape circle = new Circle("red", 5);
Shape rectangle = new Rectangle("blue", 4, 6);

System.out.println("Circle area: " + circle.getArea());        // 78.54...
System.out.println("Rectangle area: " + rectangle.getArea());  // 24.0

// Polymorphism with abstract class
Shape[] shapes = { circle, rectangle };
for (Shape s : shapes) {
    s.displayColor();
    System.out.println("Area: " + s.getArea());
}
```

### Interfaces

An interface defines a contract - what methods a class must implement.

```java
public interface Drawable {
    void draw();  // Abstract by default

    // Java 8+: default methods
    default void show() {
        System.out.println("Showing drawable");
        draw();
    }
}

public interface Resizable {
    void resize(double factor);
}
```

A class can implement multiple interfaces:

```java
public class Circle extends Shape implements Drawable, Resizable {
    private double radius;

    // ... constructor and other methods ...

    @Override
    public void draw() {
        System.out.println("Drawing circle with radius " + radius);
    }

    @Override
    public void resize(double factor) {
        radius *= factor;
    }
}
```

### Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Inheritance | Single (extends one) | Multiple (implements many) |
| Fields | Can have instance fields | Only constants (static final) |
| Constructors | Yes | No |
| Method types | Abstract + concrete | Abstract + default + static |
| Use when | Sharing code among related classes | Defining a contract for unrelated classes |

**Choose abstract class when:**
- Classes share code (not just contract)
- You need constructors or fields
- Classes are closely related

**Choose interface when:**
- Defining capabilities (Comparable, Serializable)
- Multiple inheritance needed
- Classes are unrelated but share behavior

### Real-World Interface Example

```java
// Interface for anything that can be saved
public interface Saveable {
    void save();
    void load();
}

// Interface for anything that can be validated
public interface Validatable {
    boolean isValid();
    List<String> getValidationErrors();
}

// A document that can be saved and validated
public class Document implements Saveable, Validatable {
    private String content;
    private String filePath;

    @Override
    public void save() {
        // Save to file
    }

    @Override
    public void load() {
        // Load from file
    }

    @Override
    public boolean isValid() {
        return content != null && !content.isEmpty();
    }

    @Override
    public List<String> getValidationErrors() {
        List<String> errors = new ArrayList<>();
        if (content == null || content.isEmpty()) {
            errors.add("Content cannot be empty");
        }
        return errors;
    }
}

// A user profile that can also be saved and validated
public class UserProfile implements Saveable, Validatable {
    private String username;
    private String email;

    // Completely different implementation, same interfaces
    @Override
    public void save() {
        // Save to database
    }

    @Override
    public void load() {
        // Load from database
    }

    @Override
    public boolean isValid() {
        return username != null && email != null && email.contains("@");
    }

    @Override
    public List<String> getValidationErrors() {
        // Different validation logic
    }
}
```

---

## Putting It All Together: A Complete Example

Let's build a game character system using all four pillars:

```java
// Interface - defines what actions characters can perform
public interface Attackable {
    void attack(Character target);
    int getDamage();
}

public interface Healable {
    void heal(int amount);
    int getMaxHealth();
}

// Abstract class - base for all characters
public abstract class Character implements Attackable, Healable {
    // Encapsulation: private fields with controlled access
    private String name;
    private int health;
    private int maxHealth;
    private int level;

    // Protected for subclass access
    protected int baseDamage;

    public Character(String name, int maxHealth, int baseDamage) {
        this.name = name;
        this.maxHealth = maxHealth;
        this.health = maxHealth;
        this.level = 1;
        this.baseDamage = baseDamage;
    }

    // Encapsulation: controlled access to health
    public int getHealth() {
        return health;
    }

    public void takeDamage(int damage) {
        health = Math.max(0, health - damage);
        System.out.println(name + " takes " + damage + " damage! HP: " + health);

        if (health == 0) {
            System.out.println(name + " has been defeated!");
        }
    }

    @Override
    public void heal(int amount) {
        int oldHealth = health;
        health = Math.min(maxHealth, health + amount);
        System.out.println(name + " heals for " + (health - oldHealth) + "! HP: " + health);
    }

    @Override
    public int getMaxHealth() {
        return maxHealth;
    }

    // Abstraction: subclasses define their own attack behavior
    @Override
    public abstract void attack(Character target);

    // Abstraction: subclasses define their special ability
    public abstract void useSpecialAbility();

    // Common method for all characters
    public void displayStatus() {
        System.out.println("=== " + name + " (Level " + level + ") ===");
        System.out.println("HP: " + health + "/" + maxHealth);
        System.out.println("Base Damage: " + baseDamage);
    }

    public String getName() {
        return name;
    }

    public boolean isAlive() {
        return health > 0;
    }

    public void levelUp() {
        level++;
        maxHealth += 10;
        health = maxHealth;
        baseDamage += 2;
        System.out.println(name + " leveled up to " + level + "!");
    }
}
```

```java
// Concrete class: Warrior
public class Warrior extends Character {
    private int armor;

    public Warrior(String name) {
        super(name, 150, 20);  // High health, good damage
        this.armor = 5;
    }

    @Override
    public void attack(Character target) {
        System.out.println(getName() + " swings sword at " + target.getName() + "!");
        target.takeDamage(getDamage());
    }

    @Override
    public int getDamage() {
        return baseDamage + 5;  // Bonus sword damage
    }

    @Override
    public void useSpecialAbility() {
        armor += 3;
        System.out.println(getName() + " raises shield! Armor increased to " + armor);
    }

    @Override
    public void takeDamage(int damage) {
        int reducedDamage = Math.max(1, damage - armor);
        super.takeDamage(reducedDamage);
    }
}
```

```java
// Concrete class: Mage
public class Mage extends Character {
    private int mana;
    private int maxMana;

    public Mage(String name) {
        super(name, 80, 10);  // Low health, low base damage
        this.maxMana = 100;
        this.mana = maxMana;
    }

    @Override
    public void attack(Character target) {
        if (mana >= 10) {
            mana -= 10;
            System.out.println(getName() + " casts fireball at " + target.getName() + "!");
            target.takeDamage(getDamage());
        } else {
            System.out.println(getName() + " is out of mana! Weak staff attack.");
            target.takeDamage(5);
        }
    }

    @Override
    public int getDamage() {
        return baseDamage + 25;  // Magic is powerful!
    }

    @Override
    public void useSpecialAbility() {
        mana = maxMana;
        System.out.println(getName() + " meditates and restores mana to " + mana);
    }

    @Override
    public void displayStatus() {
        super.displayStatus();
        System.out.println("Mana: " + mana + "/" + maxMana);
    }
}
```

```java
// Concrete class: Archer
public class Archer extends Character {
    private int arrows;

    public Archer(String name) {
        super(name, 100, 15);
        this.arrows = 20;
    }

    @Override
    public void attack(Character target) {
        if (arrows > 0) {
            arrows--;
            System.out.println(getName() + " shoots arrow at " + target.getName() + "! (" + arrows + " arrows left)");
            target.takeDamage(getDamage());
        } else {
            System.out.println(getName() + " has no arrows! Weak dagger attack.");
            target.takeDamage(5);
        }
    }

    @Override
    public int getDamage() {
        return baseDamage + 10;
    }

    @Override
    public void useSpecialAbility() {
        System.out.println(getName() + " fires triple shot!");
        // Would deal 3x damage to target
        arrows -= 2;  // Uses extra arrows
    }

    public void restockArrows(int count) {
        arrows += count;
        System.out.println(getName() + " restocked " + count + " arrows. Total: " + arrows);
    }
}
```

```java
// Game class demonstrating polymorphism
public class Game {
    public static void main(String[] args) {
        // Polymorphism: array of base type holds different subtypes
        Character[] party = {
            new Warrior("Conan"),
            new Mage("Gandalf"),
            new Archer("Legolas")
        };

        Character enemy = new Warrior("Orc Chief");

        System.out.println("=== Battle Start ===\n");

        // Display all characters (polymorphic call to displayStatus)
        for (Character c : party) {
            c.displayStatus();
            System.out.println();
        }

        enemy.displayStatus();
        System.out.println();

        // Battle round (polymorphic calls to attack)
        System.out.println("=== Round 1 ===\n");
        for (Character c : party) {
            if (enemy.isAlive()) {
                c.attack(enemy);  // Each character attacks differently!
            }
        }

        System.out.println();

        // Special abilities (polymorphic calls)
        System.out.println("=== Special Abilities ===\n");
        for (Character c : party) {
            c.useSpecialAbility();  // Each has unique ability!
        }

        // Healing (through interface)
        System.out.println("\n=== Healing Phase ===\n");
        for (Character c : party) {
            c.heal(20);
        }
    }
}
```

**This example demonstrates all four pillars:**
- **Encapsulation:** Private fields, controlled access through methods
- **Inheritance:** Warrior/Mage/Archer extend Character
- **Polymorphism:** Same method calls, different behaviors
- **Abstraction:** Abstract class and interfaces hide complexity

---

## AI Integration: Evaluating OOP Code

When AI generates class hierarchies, check for:

### Common AI Mistakes

**1. Breaking Encapsulation:**
```java
// AI often generates public fields
public class User {
    public String password;  // Terrible!
}
```

**2. Incorrect Inheritance (IS-A vs HAS-A):**
```java
// AI might suggest this
public class Car extends Engine { }  // Wrong! Car HAS-A Engine

// Correct
public class Car {
    private Engine engine;  // Composition
}
```

**3. Missing @Override:**
```java
// AI often forgets this
public void tostring() {  // Typo! Should be toString()
    return "...";
}

// With @Override, compiler catches the error
@Override
public String toString() {
    return "...";
}
```

**4. Violating Liskov Substitution:**
```java
// If Square extends Rectangle, can you always use Square where Rectangle is expected?
public class Rectangle {
    public void setWidth(int w) { ... }
    public void setHeight(int h) { ... }
}

public class Square extends Rectangle {
    @Override
    public void setWidth(int w) {
        // Must also set height - breaks expectations!
    }
}
```

### Review Checklist

When reviewing AI-generated OOP code:
- [ ] Are fields private with appropriate accessors?
- [ ] Is inheritance used correctly (IS-A relationship)?
- [ ] Are abstract methods implemented in all concrete classes?
- [ ] Is `@Override` used for all overridden methods?
- [ ] Can objects of subclasses replace parent class objects safely?
- [ ] Are interfaces used for shared behavior across unrelated classes?

---

## Exercises

### Exercise 6.1: Vehicle Hierarchy
Create a vehicle system:
- Abstract class `Vehicle` with make, model, year
- Subclasses: `Car`, `Motorcycle`, `Truck`
- Interface `Electric` for electric vehicles
- Interface `Loadable` for vehicles that can carry cargo
- Demonstrate polymorphism with an array of vehicles

### Exercise 6.2: Employee System
Build an employee management system:
- Abstract class `Employee` with name, id, baseSalary
- Subclasses: `FullTimeEmployee`, `PartTimeEmployee`, `Contractor`
- Abstract method `calculatePay()` implemented differently by each
- Interface `Taxable` with `calculateTax()` method

### Exercise 6.3: Media Player
Create a media player:
- Interface `Playable` with play(), pause(), stop()
- Abstract class `Media` with title, duration
- Classes: `Song`, `Video`, `Podcast`
- Each has unique properties and play behavior

### Exercise 6.4: Fix AI Code
The following AI-generated code has OOP problems. Identify and fix them:

```java
public class Animal {
    public String name;
    public int age;
}

public class Dog extends Animal {
    public void bark() {
        System.out.println("Woof!");
    }
}

public class Cat extends Animal {
    public void meow() {
        System.out.println("Meow!");
    }
}

// Usage that requires improvement
public class Shelter {
    public void makeSound(Object animal) {
        if (animal instanceof Dog) {
            ((Dog) animal).bark();
        } else if (animal instanceof Cat) {
            ((Cat) animal).meow();
        }
    }
}
```

### Exercise 6.5: Design Challenge
Design a class hierarchy for a library system:
- Different types of items: Book, Magazine, DVD
- All items can be borrowed and returned
- Some items have different loan periods
- Some items can be renewed, others cannot
- Consider what should be abstract classes vs interfaces

---

## Key Takeaways

### Encapsulation
- Hide data with `private`
- Control access with getters/setters
- Validate data in setters
- Protect invariants

### Inheritance
- Use for IS-A relationships
- Call parent constructor with `super()`
- Override methods with `@Override`
- Prefer composition for HAS-A

### Polymorphism
- Same method call, different behavior
- Program to interfaces, not implementations
- Enables extensible, maintainable code
- Reduces type checking and casting

### Abstraction
- Abstract classes for shared code + contract
- Interfaces for contract only
- Hide implementation details
- Simplify complex systems

---

## What's Next

You now understand the four pillars of OOP - the foundation of all Java development. Take time to practice these concepts. They're essential for:

- Understanding existing codebases
- Evaluating AI-generated code
- Building maintainable software
- Succeeding in technical interviews

In the remaining chapters (7-17), we'll build on this foundation with:
- Exception handling
- Collections and generics
- Streams and lambdas
- Concurrency
- Testing
- And finally, effective AI integration

**Review these first six chapters thoroughly.** They are the bedrock of everything that follows.

---

*"Good object-oriented design is not about inheritance hierarchies - it's about defining clean contracts and hiding complexity behind simple interfaces."*
