# Chapter 16: Code Review Skills - Reading Code Like a Pro

> "Code is read far more than it is written. The ability to read and evaluate code quickly is a superpower."

---

## What You'll Learn

- Why code review matters
- What to look for in a code review
- Common patterns and anti-patterns
- How to give constructive feedback
- Reviewing AI-generated code
- Building your code review checklist

---

## Why Code Review Matters

Code review catches:
- **Bugs** before they reach production
- **Security vulnerabilities** before they're exploited
- **Performance issues** before they cause problems
- **Design problems** while they're still fixable
- **Knowledge gaps** that training can address

In the AI era, code review is even more critical because:
- AI generates plausible but potentially flawed code
- AI doesn't understand your specific business context
- AI makes consistent mistakes that humans can spot

---

## The Code Review Mindset

### What You're NOT Doing

- Finding fault with the author
- Proving you're smarter
- Enforcing your personal style preferences
- Blocking progress unnecessarily

### What You ARE Doing

- Ensuring code quality
- Sharing knowledge
- Catching what others missed
- Improving the codebase together

---

## What to Look For

### 1. Correctness

**Does it do what it's supposed to do?**

```java
// Bug: Off-by-one error
public List<Integer> getFirstN(List<Integer> list, int n) {
    List<Integer> result = new ArrayList<>();
    for (int i = 0; i <= n; i++) {  // Should be i < n
        result.add(list.get(i));
    }
    return result;
}
```

**Questions to ask:**
- What happens with edge cases (null, empty, max values)?
- Are all code paths correct?
- Does it handle errors appropriately?

### 2. Security

**Is it secure?**

```java
// SQL Injection vulnerability
String query = "SELECT * FROM users WHERE id = " + userId;

// Fixed
PreparedStatement stmt = conn.prepareStatement(
    "SELECT * FROM users WHERE id = ?");
stmt.setLong(1, userId);
```

**Common vulnerabilities:**
- SQL injection
- XSS (cross-site scripting)
- Path traversal
- Hardcoded secrets
- Insufficient input validation

### 3. Performance

**Is it efficient?**

```java
// Bad: O(n*m) when O(n) is possible
public boolean containsAny(List<String> haystack, List<String> needles) {
    for (String needle : needles) {
        for (String hay : haystack) {  // Linear search each time
            if (hay.equals(needle)) return true;
        }
    }
    return false;
}

// Good: O(n + m)
public boolean containsAny(List<String> haystack, List<String> needles) {
    Set<String> haySet = new HashSet<>(haystack);  // O(n)
    for (String needle : needles) {
        if (haySet.contains(needle)) return true;  // O(1)
    }
    return false;
}
```

**Questions to ask:**
- Is the right data structure used?
- Are there unnecessary loops or operations?
- Could this cause memory issues?
- Does it scale?

### 4. Readability

**Is it easy to understand?**

```java
// Bad: What does this do?
public int f(int[] a) {
    int r = 0;
    for (int i = 0; i < a.length; i++) {
        if (a[i] % 2 == 0) r++;
    }
    return r;
}

// Good: Clear intent
public int countEvenNumbers(int[] numbers) {
    int count = 0;
    for (int number : numbers) {
        if (isEven(number)) {
            count++;
        }
    }
    return count;
}

private boolean isEven(int number) {
    return number % 2 == 0;
}
```

**Questions to ask:**
- Are names meaningful?
- Is the code self-documenting?
- Are complex parts explained?
- Is the flow easy to follow?

### 5. Design

**Is it well-designed?**

```java
// Bad: God class doing everything
public class UserManager {
    public void createUser() { }
    public void deleteUser() { }
    public void sendEmail() { }
    public void generateReport() { }
    public void validateInput() { }
    public void formatOutput() { }
    public void logActivity() { }
    public void calculateStatistics() { }
}

// Good: Single responsibility
public class UserService { }
public class EmailService { }
public class ReportGenerator { }
public class InputValidator { }
```

**Questions to ask:**
- Does each class have one responsibility?
- Is the code modular and reusable?
- Are dependencies appropriate?
- Is there unnecessary coupling?

### 6. Testability

**Can it be tested?**

```java
// Bad: Hard to test (static call, hidden dependency)
public class OrderProcessor {
    public void processOrder(Order order) {
        if (PaymentService.charge(order.getTotal())) {  // Static - can't mock
            Database.save(order);  // Direct database access
            EmailSender.send(order.getCustomerEmail(), "Order confirmed");
        }
    }
}

// Good: Testable (injectable dependencies)
public class OrderProcessor {
    private final PaymentService paymentService;
    private final OrderRepository repository;
    private final EmailService emailService;

    public OrderProcessor(PaymentService ps, OrderRepository repo, EmailService es) {
        this.paymentService = ps;
        this.repository = repo;
        this.emailService = es;
    }

    public void processOrder(Order order) {
        if (paymentService.charge(order.getTotal())) {
            repository.save(order);
            emailService.sendConfirmation(order);
        }
    }
}
```

---

## Common Anti-Patterns

### Anti-Pattern 1: Empty Catch Blocks

```java
try {
    riskyOperation();
} catch (Exception e) {
    // Silently swallowed - BAD!
}
```

**Fix:** At minimum, log the error. Better: handle it properly.

### Anti-Pattern 2: Magic Numbers

```java
if (age >= 18) { }  // What's 18? Legal age? Drinking age?
if (retries < 3) { }  // Why 3?
Thread.sleep(5000);  // Why 5 seconds?
```

**Fix:** Use named constants.

```java
private static final int LEGAL_ADULT_AGE = 18;
private static final int MAX_RETRY_ATTEMPTS = 3;
private static final long RETRY_DELAY_MS = 5000;
```

### Anti-Pattern 3: Boolean Blindness

```java
// What does true mean here?
createUser("Alice", true, false, true);
```

**Fix:** Use enum or builder pattern.

```java
createUser(UserBuilder.name("Alice")
    .admin(true)
    .active(true)
    .verified(false));
```

### Anti-Pattern 4: Deep Nesting

```java
// Hard to follow
if (user != null) {
    if (user.isActive()) {
        if (user.hasPermission("admin")) {
            if (resource.isAvailable()) {
                // finally do something
            }
        }
    }
}
```

**Fix:** Early returns / guard clauses.

```java
if (user == null) return;
if (!user.isActive()) return;
if (!user.hasPermission("admin")) return;
if (!resource.isAvailable()) return;

// Do something
```

### Anti-Pattern 5: God Method

```java
// 200+ lines doing everything
public void processOrder() {
    // validate
    // calculate
    // save
    // send email
    // update inventory
    // generate report
    // log
}
```

**Fix:** Break into focused methods.

```java
public void processOrder(Order order) {
    validateOrder(order);
    BigDecimal total = calculateTotal(order);
    save(order);
    notifyCustomer(order);
    updateInventory(order);
    generateReport(order);
}
```

### Anti-Pattern 6: Premature Optimization

```java
// Over-engineered for "performance"
// when simple code would be fine
StringBuilder sb = new StringBuilder(256);
sb.append("Hello");
sb.append(" ");
sb.append("World");
return sb.toString();

// For simple cases, this is fine:
return "Hello" + " " + "World";
```

**Fix:** Write clear code first. Optimize when you have evidence of a problem.

---

## Reviewing AI-Generated Code

AI code needs special attention. Look for:

### 1. Outdated Patterns

```java
// AI often generates old-style Java
Date date = new Date();  // Use java.time instead
Vector<String> list = new Vector<>();  // Use ArrayList
StringBuffer sb = new StringBuffer();  // Use StringBuilder
```

### 2. Overly Complex Solutions

```java
// AI might overcomplicate
public static String reverseString(String input) {
    char[] chars = input.toCharArray();
    int left = 0;
    int right = chars.length - 1;
    while (left < right) {
        char temp = chars[left];
        chars[left] = chars[right];
        chars[right] = temp;
        left++;
        right--;
    }
    return new String(chars);
}

// Simpler Java approach
public static String reverseString(String input) {
    return new StringBuilder(input).reverse().toString();
}
```

### 3. Missing Edge Cases

```java
// AI often forgets null checks
public int getLength(String s) {
    return s.length();  // NPE if s is null
}
```

### 4. Incorrect Algorithm Complexity

```java
// AI might use O(n²) when O(n) is possible
// Often uses nested loops when not needed
```

### 5. Security Blind Spots

```java
// AI might not consider security implications
public void executeCommand(String userInput) {
    Runtime.getRuntime().exec(userInput);  // Command injection!
}
```

### 6. Generic Comments

```java
// AI adds unhelpful comments
// This method adds two numbers
public int add(int a, int b) {
    return a + b;
}
```

---

## Giving Constructive Feedback

### Good Feedback Principles

1. **Be specific** - Point to exact code
2. **Explain why** - Not just what's wrong
3. **Suggest alternatives** - Don't just criticize
4. **Be kind** - Critique code, not people
5. **Ask questions** - "Have you considered...?"

### Bad vs Good Feedback

**Bad:**
```
This code is wrong.
```

**Good:**
```
The loop at line 15 has an off-by-one error. It should be
`i < n` instead of `i <= n`. This causes an
ArrayIndexOutOfBoundsException when the array has exactly n elements.
```

**Bad:**
```
Why would you do it this way?
```

**Good:**
```
I see you used a LinkedList here. Since we're doing frequent
lookups by index (line 23, 45), an ArrayList would be more
efficient - O(1) vs O(n). What do you think?
```

**Bad:**
```
This is inefficient.
```

**Good:**
```
This nested loop makes the complexity O(n*m). We could improve
this to O(n+m) by using a HashSet for the lookup:

Set<String> set = new HashSet<>(list1);
for (String s : list2) {
    if (set.contains(s)) { ... }
}

This matters because this method is called frequently with
large lists in our production traffic.
```

---

## Code Review Checklist

Use this for every review:

### Functionality
- [ ] Code does what it's supposed to do
- [ ] Edge cases handled (null, empty, boundary values)
- [ ] Error cases handled appropriately
- [ ] No obvious bugs

### Security
- [ ] No SQL/command injection
- [ ] Input validation present
- [ ] Sensitive data protected
- [ ] No hardcoded secrets

### Performance
- [ ] Appropriate data structures
- [ ] No unnecessary work
- [ ] Resources properly managed
- [ ] Will scale appropriately

### Readability
- [ ] Clear naming
- [ ] Appropriate comments (not excessive)
- [ ] Consistent style
- [ ] Easy to follow logic

### Design
- [ ] Single responsibility
- [ ] Appropriate abstraction level
- [ ] Minimal coupling
- [ ] Easy to test

### Testing
- [ ] Tests included
- [ ] Tests are meaningful
- [ ] Edge cases tested
- [ ] Tests are maintainable

---

## Self-Review

Before submitting code for review, review it yourself:

1. **Take a break** - Fresh eyes catch more
2. **Read it line by line** - Don't skim
3. **Run through the checklist** - Be thorough
4. **Test edge cases** - Even if you think they work
5. **Check for patterns** - Repeated mistakes

---

## Hands-On: Review Practice

### Code Sample 1

Review this code. What issues do you find?

```java
public class UserService {
    public void updateUser(String id, String name, String email) {
        try {
            Connection conn = DriverManager.getConnection(DB_URL);
            String sql = "UPDATE users SET name='" + name + "', email='" + email + "' WHERE id=" + id;
            Statement stmt = conn.createStatement();
            stmt.executeUpdate(sql);
        } catch (Exception e) {
        }
    }
}
```

<details>
<summary>Issues (click to reveal)</summary>

1. **SQL Injection** - String concatenation in SQL
2. **Resource leak** - Connection and Statement not closed
3. **Empty catch block** - Errors are silently swallowed
4. **No validation** - Input not validated
5. **Overly broad exception** - Catches all exceptions

**Fixed version:**
```java
public class UserService {
    public void updateUser(String id, String name, String email) throws ServiceException {
        validateInput(id, name, email);

        String sql = "UPDATE users SET name=?, email=? WHERE id=?";

        try (Connection conn = dataSource.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, name);
            stmt.setString(2, email);
            stmt.setString(3, id);
            stmt.executeUpdate();

        } catch (SQLException e) {
            throw new ServiceException("Failed to update user: " + id, e);
        }
    }
}
```
</details>

### Code Sample 2

Review this AI-generated code:

```java
public static List<Integer> removeDuplicates(List<Integer> numbers) {
    List<Integer> result = new ArrayList<>();
    for (int i = 0; i < numbers.size(); i++) {
        boolean isDuplicate = false;
        for (int j = 0; j < result.size(); j++) {
            if (numbers.get(i).equals(result.get(j))) {
                isDuplicate = true;
                break;
            }
        }
        if (!isDuplicate) {
            result.add(numbers.get(i));
        }
    }
    return result;
}
```

<details>
<summary>Issues (click to reveal)</summary>

1. **Performance** - O(n²) when O(n) is possible using Set
2. **Null handling** - No null check on input list
3. **NPE risk** - numbers.get(i) could be null, equals() would fail
4. **Overcomplicated** - Java has simpler built-in solutions

**Better version:**
```java
public static List<Integer> removeDuplicates(List<Integer> numbers) {
    if (numbers == null) return Collections.emptyList();
    return new ArrayList<>(new LinkedHashSet<>(numbers));  // Preserves order
}
```
</details>

---

## Exercises

### Exercise 16.1: Find the Bugs
Review this code and list all issues:

```java
public class Calculator {
    public double divide(int a, int b) {
        return a / b;
    }

    public double average(List<Integer> numbers) {
        int sum = 0;
        for (Integer n : numbers) {
            sum += n;
        }
        return sum / numbers.size();
    }
}
```

### Exercise 16.2: Review AI Output
Get AI to generate a "merge two sorted arrays" function, then review it thoroughly.

### Exercise 16.3: Write Better Code
Take a piece of code you wrote last week. Review it against the checklist. What would you change?

### Exercise 16.4: Practice Feedback
Write constructive feedback for this code:

```java
public boolean check(String s) {
    if (s.length() < 8) return false;
    boolean n = false, u = false, l = false;
    for (int i = 0; i < s.length(); i++) {
        if (Character.isDigit(s.charAt(i))) n = true;
        if (Character.isUpperCase(s.charAt(i))) u = true;
        if (Character.isLowerCase(s.charAt(i))) l = true;
    }
    return n && u && l;
}
```

---

## Key Takeaways

- **Code review is a skill** - Practice makes perfect
- **Look beyond syntax** - Focus on correctness, security, performance
- **AI code needs extra scrutiny** - It makes consistent mistakes
- **Be constructive** - Help, don't criticize
- **Use a checklist** - Don't rely on memory
- **Review your own code** - Before others see it
- **Learn from reviews** - Both giving and receiving

---

## What's Next

You've learned to read and review code professionally. In **Chapter 17**, we'll bring everything together with a Final Project - building a complete application using all the skills from this book.

---

*"The best developers aren't those who write the most code - they're those who can read any code."*
