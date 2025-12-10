# Chapter 15: AI Prompting for Java - Using AI as Your Coding Partner

> "AI is a powerful tool, but a tool wielded by someone who doesn't understand what it does is dangerous. Learn to drive before you speed."

---

## What You'll Learn

- How to effectively prompt AI for Java code
- When to use AI vs when to code yourself
- Prompt engineering patterns
- Reviewing and improving AI output
- Common AI mistakes and how to catch them
- Building a workflow that combines human and AI strengths

---

## The Right Mindset

By now you've learned Java fundamentals. You understand:
- How variables and types work
- Object-oriented design
- Collections and streams
- Exception handling
- Testing

**Now** you can use AI effectively because you can:
1. Understand what AI generates
2. Spot mistakes and bugs
3. Ask intelligent follow-up questions
4. Know when AI is wrong

Without this foundation, you'd be **"vibe coding"** - accepting code you don't understand and hoping it works.

---

## When to Use AI

### Good Uses for AI

| Task | Why AI Helps |
|------|--------------|
| Boilerplate code | Repetitive, time-consuming |
| Standard patterns | Well-documented, common |
| Code conversion | Syntax translation |
| Documentation | Writing explanations |
| Test generation | Covering edge cases |
| Debugging help | Fresh perspective |
| Learning new APIs | Quick examples |

### When to Code Yourself

| Task | Why You Should Code |
|------|---------------------|
| Core business logic | Needs deep understanding |
| Security-critical code | AI makes security mistakes |
| Performance-critical code | Needs optimization expertise |
| Novel algorithms | AI regurgitates existing solutions |
| Complex integrations | Context-dependent |

---

## Prompt Engineering Basics

### Be Specific

**Bad prompt:**
```
Write Java code for a user class
```

**Good prompt:**
```
Write a Java class called User with:
- Private fields: id (Long), name (String), email (String), createdAt (LocalDateTime)
- All-args constructor
- Getters for all fields
- Setters for name and email only (id and createdAt are immutable)
- equals() and hashCode() based on id
- toString() that returns "User[id=X, name=Y]"
```

### Provide Context

**Bad prompt:**
```
Fix this bug
[code snippet]
```

**Good prompt:**
```
This method should return the sum of all positive numbers in the list,
but it's returning the wrong result for lists containing negative numbers.

Expected behavior:
- Input: [1, -2, 3, -4, 5] → Output: 9
- Input: [-1, -2, -3] → Output: 0
- Input: [] → Output: 0

Current code:
[code snippet]

What's wrong and how do I fix it?
```

### Specify Constraints

**Good prompt:**
```
Write a method to find the most frequent element in a list.

Constraints:
- Use Java 17 features
- Time complexity should be O(n)
- Handle null and empty lists
- If there's a tie, return any of the most frequent elements
- Don't use external libraries

Method signature: public static <T> Optional<T> mostFrequent(List<T> list)
```

---

## Prompt Patterns for Java

### Pattern 1: Implementation Request

```
Implement a [data structure/algorithm/feature] in Java that:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

Use these specific approaches:
- [Approach/library/pattern to use]

Avoid:
- [Anti-patterns or approaches to avoid]

Include:
- [X] Unit tests
- [X] JavaDoc comments
- [ ] Exception handling (I'll add my own)
```

### Pattern 2: Code Review Request

```
Review this Java code for:
1. Correctness - Does it do what it's supposed to?
2. Performance - Any inefficiencies?
3. Security - Any vulnerabilities?
4. Best practices - Following Java conventions?
5. Readability - Easy to understand?

Code:
[your code]

Focus especially on [specific concern].
```

### Pattern 3: Debugging Request

```
This code throws [ExceptionType] when [condition].

Expected behavior: [what should happen]
Actual behavior: [what happens]

Stack trace:
[stack trace]

Code:
[code]

I've already tried:
- [Thing 1]
- [Thing 2]

What's causing this and how do I fix it?
```

### Pattern 4: Explanation Request

```
Explain this Java code step by step:

[code]

Specifically, I don't understand:
- Why does [specific line] work?
- What happens if [edge case]?
- Why use [specific construct] instead of [alternative]?

Explain as if I understand basic Java but am unfamiliar with [specific topic].
```

### Pattern 5: Test Generation

```
Generate JUnit 5 tests for this method:

[method code]

Include tests for:
- Normal cases with typical inputs
- Edge cases (null, empty, boundary values)
- Error cases (what should throw exceptions)

Use @ParameterizedTest where appropriate.
Mock any dependencies.
Follow Arrange-Act-Assert pattern.
```

### Pattern 6: Refactoring Request

```
Refactor this code to:
- [Specific improvement 1]
- [Specific improvement 2]

Keep:
- [What to preserve]

Original code:
[code]

Explain each change you make.
```

---

## Iterative Prompting

Don't expect perfect code in one shot. Iterate.

### Round 1: Initial Request

```
Write a method to parse a CSV line into fields, handling quoted fields correctly.
```

*AI generates basic implementation*

### Round 2: Add Requirements

```
This looks good, but add:
- Handle escaped quotes inside quoted fields (e.g., "He said ""hello""")
- Handle different delimiters (not just comma)
- Return empty string for empty fields, not null
```

*AI updates implementation*

### Round 3: Edge Cases

```
What happens if:
- The line ends with a delimiter?
- A quoted field contains a newline?
- The input is null or empty?

Add handling for these cases.
```

*AI adds edge case handling*

### Round 4: Polish

```
Now:
- Add JavaDoc with examples
- Throw IllegalArgumentException for malformed CSV (unclosed quotes)
- Make it a static utility method in a CsvParser class
```

---

## Reviewing AI Output

Always review AI code before using it. Use this checklist:

### Correctness Checklist

- [ ] Does it compile?
- [ ] Does it do what you asked?
- [ ] Handle edge cases (null, empty, boundary)?
- [ ] Correct algorithm?
- [ ] Correct data types?

### Security Checklist

- [ ] No SQL injection vulnerabilities?
- [ ] No command injection?
- [ ] Proper input validation?
- [ ] Sensitive data handled correctly?
- [ ] No hardcoded credentials?

### Performance Checklist

- [ ] Appropriate data structures?
- [ ] No unnecessary loops?
- [ ] Resources closed properly?
- [ ] No memory leaks?
- [ ] Reasonable time/space complexity?

### Best Practices Checklist

- [ ] Follows naming conventions?
- [ ] Methods are appropriately sized?
- [ ] Proper exception handling?
- [ ] Thread-safe if needed?
- [ ] No deprecated APIs?

---

## Common AI Mistakes

### Mistake 1: Wrong Collection Type

**AI writes:**
```java
// Searching through LinkedList - O(n) for each lookup!
LinkedList<String> items = new LinkedList<>();
// ... fill list ...
if (items.contains("search")) { }
```

**You should catch this:** HashSet for O(1) contains, ArrayList if order matters.

### Mistake 2: Null Handling Issues

**AI writes:**
```java
public String processName(String name) {
    return name.trim().toUpperCase();  // NPE if name is null!
}
```

**You should catch this:** Add null check or use Optional.

### Mistake 3: Resource Leaks

**AI writes:**
```java
public String readFile(String path) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(path));
    return reader.readLine();  // Reader never closed!
}
```

**You should catch this:** Use try-with-resources.

### Mistake 4: Thread Safety Issues

**AI writes:**
```java
public class Counter {
    private int count = 0;
    public void increment() { count++; }  // Not thread-safe!
}
```

**You should catch this:** Use AtomicInteger or synchronized.

### Mistake 5: Security Vulnerabilities

**AI writes:**
```java
String query = "SELECT * FROM users WHERE name = '" + userName + "'";
// SQL injection vulnerability!
```

**You should catch this:** Use PreparedStatement.

### Mistake 6: Outdated APIs

**AI writes:**
```java
Date date = new Date();  // Old API
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");  // Not thread-safe
```

**You should catch this:** Use java.time (LocalDate, DateTimeFormatter).

---

## Building a Workflow

### Step 1: Define What You Need

Before prompting:
- What does this code need to do?
- What are the inputs and outputs?
- What are the constraints?
- What are the edge cases?

### Step 2: Prompt AI

Use appropriate prompt pattern. Be specific.

### Step 3: Review Output

Use the checklists above. Ask yourself:
- Do I understand every line?
- Would I have written it this way?
- What could go wrong?

### Step 4: Test

Write tests for:
- Happy path
- Edge cases
- Error cases

If AI generated tests, add your own. AI tests often miss cases.

### Step 5: Refine

Ask AI to:
- Fix issues you found
- Improve specific aspects
- Add missing features

### Step 6: Document

Ensure you can explain the code. If you can't explain it, don't use it.

---

## Practical Examples

### Example 1: Generate a Builder Pattern

**Prompt:**
```
Create a builder pattern for a Person class with:
- Required fields: firstName, lastName
- Optional fields: middleName, age, email, phoneNumber

Requirements:
- Immutable Person instances
- Fluent API
- Validation: age must be positive if provided
- Use Java records if appropriate
```

**Review checklist:**
- Are required fields enforced?
- Is Person truly immutable?
- Is age validation correct?
- Does the fluent API work?

### Example 2: Debug a NullPointerException

**Prompt:**
```
I'm getting a NullPointerException at line 15:

1  public List<String> getActiveUserNames(List<User> users) {
2      return users.stream()
3          .filter(User::isActive)
4          .map(User::getName)
5          .collect(Collectors.toList());
6  }

Stack trace points to the filter line. Users list is not null.
Some users might have null values in the list.

How do I fix this to handle nulls gracefully?
```

**Review the fix:**
- Does it filter out nulls properly?
- Does it handle null names too?
- Is the solution efficient?

### Example 3: Generate Tests

**Prompt:**
```
Generate comprehensive JUnit 5 tests for:

public class StringUtils {
    public static String reverse(String input) {
        if (input == null) return null;
        return new StringBuilder(input).reverse().toString();
    }

    public static boolean isPalindrome(String input) {
        if (input == null) return false;
        String cleaned = input.toLowerCase().replaceAll("[^a-z0-9]", "");
        return cleaned.equals(new StringBuilder(cleaned).reverse().toString());
    }
}

Test edge cases thoroughly.
```

**Review generated tests:**
- Do they cover null inputs?
- Do they test empty strings?
- Do they test special characters?
- Are assertions specific enough?
- Add any missing cases.

---

## The 80/20 Rule

Use AI for the 80% that's routine:
- Boilerplate
- Standard patterns
- Common algorithms
- Documentation

Focus your brain on the 20% that matters:
- Business logic
- Edge cases
- Security considerations
- Performance optimization
- Architecture decisions

---

## Exercises

### Exercise 15.1: Prompt Writing
Write prompts to generate:
1. A thread-safe singleton class
2. A method to merge two sorted arrays
3. A custom exception class hierarchy

### Exercise 15.2: Code Review
Get AI to generate code for a shopping cart, then review it using the checklists. Document all issues you find.

### Exercise 15.3: Iterative Improvement
Start with a basic prompt for a cache class, then iterate through 3-4 rounds of improvements.

### Exercise 15.4: Bug Finding
Generate an AI solution for finding duplicates in a list, then deliberately try to break it with edge cases.

### Exercise 15.5: Compare Approaches
Ask AI for 3 different implementations of the same problem, then evaluate trade-offs yourself.

---

## Key Takeaways

- **AI is a tool, not a replacement** - You still need to understand code
- **Be specific in prompts** - Garbage in, garbage out
- **Always review AI output** - It makes mistakes
- **Test AI-generated code** - More thoroughly than hand-written code
- **Iterate** - Refine through multiple rounds
- **Know when not to use AI** - Critical code needs human attention
- **Document your understanding** - If you can't explain it, don't use it

---

## What's Next

You can now use AI effectively. In **Chapter 16**, we'll learn Code Review Skills - how to review both your own code and others' (including AI-generated code) like a professional.

---

*"AI makes you faster, but understanding makes you better. Combine both to become unstoppable."*
