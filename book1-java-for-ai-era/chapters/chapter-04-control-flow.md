# Chapter 4: Control Flow - Making Decisions and Repeating Actions

> "Programs don't just calculate - they decide, adapt, and repeat."

---

## What You'll Learn

- Making decisions with `if`, `else if`, and `else`
- Using `switch` statements (traditional and modern)
- The ternary operator for concise conditions
- Repeating with `for`, `while`, and `do-while` loops
- The enhanced `for` loop for collections
- Controlling loop execution with `break` and `continue`
- Nested loops and labeled statements
- Common patterns and pitfalls

---

## The Flow of a Program

So far, our programs execute from top to bottom, one line at a time. But real programs need to:

- **Make decisions:** "If the user is logged in, show the dashboard"
- **Repeat actions:** "Process each item in the shopping cart"
- **Skip actions:** "Skip this item if it's out of stock"

Control flow structures let us direct the execution path of our programs.

---

## Conditional Statements: If-Else

### Basic If Statement

```java
int age = 18;

if (age >= 18) {
    System.out.println("You can vote!");
}
```

**Structure:**
```
if (condition) {
    // code to execute if condition is true
}
```

The condition must evaluate to a `boolean` (`true` or `false`).

### If-Else Statement

```java
int temperature = 15;

if (temperature > 25) {
    System.out.println("It's hot!");
} else {
    System.out.println("It's not hot.");
}
```

### If-Else-If Chain

```java
int score = 85;
char grade;

if (score >= 90) {
    grade = 'A';
} else if (score >= 80) {
    grade = 'B';
} else if (score >= 70) {
    grade = 'C';
} else if (score >= 60) {
    grade = 'D';
} else {
    grade = 'F';
}

System.out.println("Your grade: " + grade);  // Your grade: B
```

**Important:** Conditions are evaluated in order. Once one is true, the rest are skipped.

### Nested If Statements

```java
boolean hasAccount = true;
boolean isVerified = true;
double balance = 100.0;

if (hasAccount) {
    if (isVerified) {
        if (balance > 0) {
            System.out.println("Transaction allowed");
        } else {
            System.out.println("Insufficient funds");
        }
    } else {
        System.out.println("Please verify your account");
    }
} else {
    System.out.println("Please create an account");
}
```

**Better approach using logical operators:**
```java
if (hasAccount && isVerified && balance > 0) {
    System.out.println("Transaction allowed");
} else if (!hasAccount) {
    System.out.println("Please create an account");
} else if (!isVerified) {
    System.out.println("Please verify your account");
} else {
    System.out.println("Insufficient funds");
}
```

### Common If-Else Pitfalls

**Pitfall 1: Dangling Else**
```java
// Confusing
if (condition1)
    if (condition2)
        doSomething();
else  // This else belongs to inner if, not outer!
    doSomethingElse();

// Always use braces:
if (condition1) {
    if (condition2) {
        doSomething();
    }
} else {
    doSomethingElse();
}
```

**Pitfall 2: Comparing Strings with ==**
```java
String name = "Alice";

// WRONG - compares references
if (name == "Alice") { }

// CORRECT - compares content
if (name.equals("Alice")) { }

// SAFER - handles null
if ("Alice".equals(name)) { }
```

**Pitfall 3: Empty Condition Bodies**
```java
// Bug: semicolon creates empty body
if (score > 100);  // This semicolon ends the if!
{
    System.out.println("This ALWAYS runs!");
}
```

---

## Switch Statement

When you have many conditions checking the same variable, `switch` is cleaner than if-else chains.

### Traditional Switch

```java
int day = 3;
String dayName;

switch (day) {
    case 1:
        dayName = "Monday";
        break;
    case 2:
        dayName = "Tuesday";
        break;
    case 3:
        dayName = "Wednesday";
        break;
    case 4:
        dayName = "Thursday";
        break;
    case 5:
        dayName = "Friday";
        break;
    case 6:
        dayName = "Saturday";
        break;
    case 7:
        dayName = "Sunday";
        break;
    default:
        dayName = "Invalid day";
}

System.out.println(dayName);  // Wednesday
```

**The `break` is crucial!** Without it, execution "falls through" to the next case:

```java
// Fall-through example (sometimes intentional)
int month = 2;
String season;

switch (month) {
    case 12:
    case 1:
    case 2:
        season = "Winter";
        break;
    case 3:
    case 4:
    case 5:
        season = "Spring";
        break;
    case 6:
    case 7:
    case 8:
        season = "Summer";
        break;
    case 9:
    case 10:
    case 11:
        season = "Fall";
        break;
    default:
        season = "Unknown";
}
```

### Modern Switch Expression (Java 14+)

Much cleaner syntax without `break` statements:

```java
int day = 3;

String dayName = switch (day) {
    case 1 -> "Monday";
    case 2 -> "Tuesday";
    case 3 -> "Wednesday";
    case 4 -> "Thursday";
    case 5 -> "Friday";
    case 6 -> "Saturday";
    case 7 -> "Sunday";
    default -> "Invalid day";
};

System.out.println(dayName);  // Wednesday
```

**Multiple labels:**
```java
String dayType = switch (day) {
    case 1, 2, 3, 4, 5 -> "Weekday";
    case 6, 7 -> "Weekend";
    default -> "Invalid";
};
```

**With code blocks:**
```java
String description = switch (day) {
    case 1 -> {
        System.out.println("Start of the week");
        yield "Monday";  // 'yield' returns value from block
    }
    case 5 -> {
        System.out.println("Almost weekend!");
        yield "Friday";
    }
    default -> "Regular day";
};
```

### Switch with Strings (Java 7+)

```java
String command = "start";

switch (command.toLowerCase()) {
    case "start":
        System.out.println("Starting...");
        break;
    case "stop":
        System.out.println("Stopping...");
        break;
    case "pause":
        System.out.println("Pausing...");
        break;
    default:
        System.out.println("Unknown command");
}
```

### Switch Limitations

Switch works with:
- `byte`, `short`, `char`, `int` (and their wrappers)
- `String` (Java 7+)
- `enum` types

Switch does NOT work with:
- `long`, `float`, `double`
- `boolean`
- Objects (except String)

---

## The Ternary Operator

A concise way to write simple if-else:

```java
// If-else version
String status;
if (age >= 18) {
    status = "Adult";
} else {
    status = "Minor";
}

// Ternary version
String status = (age >= 18) ? "Adult" : "Minor";
```

**Syntax:** `condition ? valueIfTrue : valueIfFalse`

**Use sparingly!** Complex ternaries are hard to read:

```java
// Too complex - don't do this
String result = a > b ? (c > d ? "A" : "B") : (e > f ? "C" : "D");

// Use if-else instead for complex logic
```

---

## Loops: Repeating Actions

### The For Loop

Best when you know how many times to iterate:

```java
// Print numbers 1 to 5
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

**Structure:**
```
for (initialization; condition; update) {
    // body
}
```

**Execution order:**
1. Initialization (once, at start)
2. Check condition
3. If true, execute body
4. Execute update
5. Go to step 2

**Common patterns:**

```java
// Counting up
for (int i = 0; i < 10; i++) { }

// Counting down
for (int i = 10; i > 0; i--) { }

// Skip by 2
for (int i = 0; i < 20; i += 2) { }

// Multiple variables
for (int i = 0, j = 10; i < j; i++, j--) { }
```

### The While Loop

Best when you don't know how many iterations:

```java
int count = 0;
while (count < 5) {
    System.out.println("Count: " + count);
    count++;
}
```

**Structure:**
```
while (condition) {
    // body
}
```

**Example: Reading until valid input**
```java
Scanner scanner = new Scanner(System.in);
int number = -1;

while (number < 0 || number > 100) {
    System.out.print("Enter a number between 0 and 100: ");
    number = scanner.nextInt();
}

System.out.println("You entered: " + number);
```

### The Do-While Loop

Executes at least once, then checks condition:

```java
int count = 0;
do {
    System.out.println("Count: " + count);
    count++;
} while (count < 5);
```

**When to use do-while:**
- Menu systems (show menu at least once)
- Input validation (get input at least once)
- Games (play at least one round)

```java
Scanner scanner = new Scanner(System.in);
String choice;

do {
    System.out.println("\n1. Play Game");
    System.out.println("2. View Scores");
    System.out.println("3. Exit");
    System.out.print("Choose: ");
    choice = scanner.nextLine();

    switch (choice) {
        case "1" -> playGame();
        case "2" -> viewScores();
        case "3" -> System.out.println("Goodbye!");
        default -> System.out.println("Invalid choice");
    }
} while (!choice.equals("3"));
```

### Enhanced For Loop (For-Each)

For iterating over arrays and collections:

```java
int[] numbers = {1, 2, 3, 4, 5};

// Traditional for
for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}

// Enhanced for (cleaner)
for (int num : numbers) {
    System.out.println(num);
}
```

**Read as:** "For each `num` in `numbers`"

**Works with:**
- Arrays
- Any class implementing `Iterable` (List, Set, etc.)

**Limitations:**
- Can't modify the array/collection
- Can't access the index
- Can't iterate backwards

```java
// When you need the index, use traditional for
String[] names = {"Alice", "Bob", "Charlie"};
for (int i = 0; i < names.length; i++) {
    System.out.println((i + 1) + ". " + names[i]);
}
```

---

## Loop Control: Break and Continue

### Break: Exit the Loop

```java
// Find first even number
int[] numbers = {1, 3, 5, 4, 7, 9};

for (int num : numbers) {
    if (num % 2 == 0) {
        System.out.println("Found even: " + num);
        break;  // Exit loop immediately
    }
}
// Output: Found even: 4
```

### Continue: Skip to Next Iteration

```java
// Print only odd numbers
for (int i = 1; i <= 10; i++) {
    if (i % 2 == 0) {
        continue;  // Skip even numbers
    }
    System.out.println(i);
}
// Output: 1, 3, 5, 7, 9
```

### Labeled Break and Continue

For nested loops:

```java
outer:
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
        if (i == 1 && j == 1) {
            break outer;  // Exits BOTH loops
        }
        System.out.println("i=" + i + ", j=" + j);
    }
}
```

Without label, `break` only exits the inner loop.

---

## Infinite Loops

Loops that never end (sometimes intentional):

```java
// Intentional infinite loop (server listening)
while (true) {
    // Wait for connection
    // Handle request
}

// Accidental infinite loop (bug!)
int i = 0;
while (i < 10) {
    System.out.println(i);
    // Forgot i++; - i never changes!
}
```

**Always ensure your loop has a way to exit!**

---

## Nested Loops

Loops inside loops:

```java
// Multiplication table
for (int i = 1; i <= 5; i++) {
    for (int j = 1; j <= 5; j++) {
        System.out.print(i * j + "\t");
    }
    System.out.println();  // New line after each row
}
```

Output:
```
1	2	3	4	5
2	4	6	8	10
3	6	9	12	15
4	8	12	16	20
5	10	15	20	25
```

**Performance consideration:** Nested loops multiply! Two loops of 1000 iterations = 1,000,000 operations.

---

## Common Patterns

### Pattern 1: Search

```java
int[] numbers = {5, 8, 12, 3, 9};
int target = 12;
int index = -1;

for (int i = 0; i < numbers.length; i++) {
    if (numbers[i] == target) {
        index = i;
        break;
    }
}

if (index != -1) {
    System.out.println("Found at index: " + index);
} else {
    System.out.println("Not found");
}
```

### Pattern 2: Accumulator

```java
int[] numbers = {1, 2, 3, 4, 5};
int sum = 0;

for (int num : numbers) {
    sum += num;
}

System.out.println("Sum: " + sum);  // 15
```

### Pattern 3: Counter

```java
String text = "Hello World";
int vowelCount = 0;

for (char c : text.toLowerCase().toCharArray()) {
    if (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u') {
        vowelCount++;
    }
}

System.out.println("Vowels: " + vowelCount);  // 3
```

### Pattern 4: Max/Min Finding

```java
int[] numbers = {5, 8, 2, 12, 3};
int max = numbers[0];  // Start with first element

for (int i = 1; i < numbers.length; i++) {
    if (numbers[i] > max) {
        max = numbers[i];
    }
}

System.out.println("Maximum: " + max);  // 12
```

---

## Hands-On Project: Number Guessing Game

Let's build a complete game that uses everything we've learned:

```java
import java.util.Random;
import java.util.Scanner;

public class GuessingGame {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Random random = new Random();

        System.out.println("=== NUMBER GUESSING GAME ===");
        System.out.println("I'm thinking of a number between 1 and 100.");
        System.out.println("Can you guess it?\n");

        boolean playAgain = true;

        while (playAgain) {
            // Generate random number between 1 and 100
            int secretNumber = random.nextInt(100) + 1;
            int attempts = 0;
            int maxAttempts = 7;
            boolean guessedCorrectly = false;

            System.out.println("You have " + maxAttempts + " attempts.");

            // Game loop
            while (attempts < maxAttempts && !guessedCorrectly) {
                System.out.print("\nAttempt " + (attempts + 1) + "/" + maxAttempts + " - Your guess: ");

                // Input validation
                if (!scanner.hasNextInt()) {
                    System.out.println("Please enter a valid number!");
                    scanner.next();  // Clear invalid input
                    continue;
                }

                int guess = scanner.nextInt();
                attempts++;

                // Validate range
                if (guess < 1 || guess > 100) {
                    System.out.println("Please guess between 1 and 100!");
                    continue;
                }

                // Check guess
                if (guess == secretNumber) {
                    guessedCorrectly = true;
                    System.out.println("\n*** CONGRATULATIONS! ***");
                    System.out.println("You guessed it in " + attempts + " attempts!");

                    // Rating based on attempts
                    String rating = switch (attempts) {
                        case 1 -> "INCREDIBLE! Are you psychic?";
                        case 2, 3 -> "Excellent! You're a natural!";
                        case 4, 5 -> "Good job! Nice guessing!";
                        default -> "You got it! Persistence pays off!";
                    };
                    System.out.println(rating);

                } else if (guess < secretNumber) {
                    System.out.println("Too LOW! Guess higher.");
                    if (secretNumber - guess <= 10) {
                        System.out.println("(You're close!)");
                    }
                } else {
                    System.out.println("Too HIGH! Guess lower.");
                    if (guess - secretNumber <= 10) {
                        System.out.println("(You're close!)");
                    }
                }
            }

            // Lost the game
            if (!guessedCorrectly) {
                System.out.println("\n*** GAME OVER ***");
                System.out.println("The number was: " + secretNumber);
            }

            // Play again?
            System.out.print("\nPlay again? (yes/no): ");
            scanner.nextLine();  // Clear buffer
            String response = scanner.nextLine().toLowerCase();

            playAgain = response.equals("yes") || response.equals("y");
        }

        System.out.println("\nThanks for playing! Goodbye!");
        scanner.close();
    }
}
```

**What this demonstrates:**
- `while` loops (game loop, play again)
- `if-else` chains (comparing guesses)
- `switch` expression (rating)
- `break` implicit in switch
- `continue` (input validation)
- Nested conditions
- Boolean flags

---

## AI Integration: Compare Your Code

Now let's see what AI generates for the same task.

**Prompt:** "Write a number guessing game in Java with 7 attempts"

**AI might generate something simpler:**

```java
import java.util.Random;
import java.util.Scanner;

public class SimpleGuess {
    public static void main(String[] args) {
        Random rand = new Random();
        Scanner scan = new Scanner(System.in);

        int number = rand.nextInt(100) + 1;

        for (int i = 0; i < 7; i++) {
            System.out.print("Guess: ");
            int guess = scan.nextInt();

            if (guess == number) {
                System.out.println("Correct!");
                return;
            } else if (guess < number) {
                System.out.println("Higher");
            } else {
                System.out.println("Lower");
            }
        }

        System.out.println("The number was " + number);
    }
}
```

**Compare:**

| Feature | Our Version | AI Version |
|---------|-------------|------------|
| Input validation | Yes | No |
| Range checking | Yes | No |
| "Close" hints | Yes | No |
| Performance rating | Yes | No |
| Play again option | Yes | No |
| Attempt counter display | Yes | No |
| Resource cleanup | Yes | No |

**The AI version works, but lacks robustness.** Your understanding lets you see what's missing and improve it.

---

## Common Loop Pitfalls

### Pitfall 1: Off-by-One Errors

```java
// Want to print 1-10
for (int i = 0; i <= 10; i++) {  // Prints 0-10 (11 numbers!)
    System.out.println(i);
}

// Correct versions:
for (int i = 1; i <= 10; i++) { }  // 1-10
for (int i = 0; i < 10; i++) { }   // 0-9 (10 numbers)
```

### Pitfall 2: Modifying Loop Variable

```java
for (int i = 0; i < 10; i++) {
    System.out.println(i);
    i++;  // Don't do this! Confusing behavior
}
// Prints: 0, 2, 4, 6, 8 (skips odd numbers)
```

### Pitfall 3: Comparing Floating-Point

```java
// This might never terminate!
for (double d = 0; d != 1.0; d += 0.1) {
    System.out.println(d);
}

// Safe version
for (double d = 0; d < 1.0; d += 0.1) {
    System.out.println(d);
}
```

### Pitfall 4: Forgetting Break in Switch

```java
switch (value) {
    case 1:
        System.out.println("One");
        // Missing break! Falls through to case 2
    case 2:
        System.out.println("Two");
        break;
}
// Input 1 prints both "One" AND "Two"!
```

---

## Exercises

### Exercise 4.1: FizzBuzz
Print numbers 1-100, but:
- Print "Fizz" for multiples of 3
- Print "Buzz" for multiples of 5
- Print "FizzBuzz" for multiples of both

### Exercise 4.2: Prime Checker
Write a program that:
- Asks for a number
- Determines if it's prime
- Uses efficient logic (only check up to square root)

### Exercise 4.3: Pattern Printing
Print this pattern:
```
*
**
***
****
*****
```

Then print it upside down.

### Exercise 4.4: Sum Calculator
Create a program that:
- Continuously asks for numbers
- Stops when user enters 0
- Prints the sum, count, and average

### Exercise 4.5: Password Validator
Implement a login system:
- User has 3 attempts
- Correct password: "secret123"
- Lock out after 3 failures
- Show remaining attempts

---

## Key Takeaways

- **if-else for decisions** - Use braces, even for one line
- **switch for multiple cases** - Prefer modern syntax when possible
- **for when you know iterations** - Watch for off-by-one errors
- **while when you don't** - Ensure exit condition is reachable
- **do-while for at-least-once** - Great for menus and validation
- **enhanced for for iteration** - Clean but limited
- **break exits, continue skips** - Use labeled versions for nested loops
- **Always validate input** - Don't trust user data

---

## What's Next

You can now make your programs intelligent - they decide and repeat. In **Chapter 5**, we enter the heart of Java: Object-Oriented Programming. You'll learn to organize code into classes and objects, building the foundation for larger programs.

This is where Java really shines.

---

*"A program is a spell that makes a computer do your bidding - if you choose your words carefully."*
