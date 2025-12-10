# Chapter 10: Reviewing AI Output - The Final Checklist

> "AI wrote the code in seconds. Your review determines if those seconds saved hours or cost days."

---

## The Review Mindset

```
AI-generated code arrives
         |
         v
+-------------------+
| Assume it's wrong |  ← Start here
| until verified    |
+-------------------+
         |
         v
+-------------------+
| Check against     |
| your knowledge    |
+-------------------+
         |
         v
+-------------------+
| Test edge cases   |
+-------------------+
         |
         v
+-------------------+
| Integrate or      |
| iterate           |
+-------------------+
```

Don't trust. Verify.

---

## The Master Checklist

### Level 1: Quick Scan (30 seconds)

```
[ ] Does it compile?
[ ] Does the structure look reasonable?
[ ] Are there obvious red flags (catch Exception, public fields)?
[ ] Is it using your existing classes or creating duplicates?
```

### Level 2: Type Safety (1 minute)

```
[ ] Money uses BigDecimal, not double
[ ] Large counts use long, not int
[ ] String comparisons use .equals()
[ ] Wrapper types checked for null before unboxing
[ ] Collections have correct generic types
[ ] No raw types (List vs List<String>)
```

### Level 3: Null Safety (1 minute)

```
[ ] Null checks before method calls on objects
[ ] Optional used for return values, not fields
[ ] orElse/orElseThrow used, not .get()
[ ] Defensive null checks on method parameters
[ ] No assumption that collections/arrays are non-null
```

### Level 4: Resource Safety (1 minute)

```
[ ] try-with-resources for closeable resources
[ ] Database connections properly closed
[ ] File handles properly closed
[ ] No resource leaks in exception paths
```

### Level 5: Exception Handling (1 minute)

```
[ ] Specific exceptions caught, not Exception
[ ] No empty catch blocks
[ ] Exception causes chained when wrapping
[ ] Appropriate exception types thrown
[ ] Errors (OutOfMemoryError) not caught
```

### Level 6: Collections (1 minute)

```
[ ] Right collection for the job (HashSet for contains)
[ ] Thread-safe collections if concurrent access
[ ] No modification during iteration
[ ] Proper equals/hashCode for objects in hash collections
[ ] Immutable collections where appropriate
```

### Level 7: OOP & Design (2 minutes)

```
[ ] Uses existing base classes (extends BaseEntity?)
[ ] Fields are private with controlled access
[ ] Static used appropriately (not for instance data)
[ ] Depends on abstractions, not concretions
[ ] No instanceof chains (use polymorphism)
```

### Level 8: Modern Java (1 minute)

```
[ ] Streams don't have side effects
[ ] Method references where lambda is single method call
[ ] No stream reuse
[ ] Modern switch syntax (Java 14+)
[ ] Records for simple data classes (Java 14+)
```

### Level 9: Security (1 minute)

```
[ ] No SQL built with string concatenation
[ ] No logging of sensitive data (passwords, tokens)
[ ] Input validation present
[ ] No hardcoded credentials
[ ] Proper access modifiers (not everything public)
```

### Level 10: Integration (1 minute)

```
[ ] Follows team's code style
[ ] Uses project's existing utilities
[ ] Consistent naming conventions
[ ] Appropriate package location
[ ] Required annotations present (@Service, @Entity, etc.)
```

---

## Visual Review Flow

```
                    AI Code Arrives
                          |
                          v
                 +--------+--------+
                 | Quick Scan Pass |
                 +--------+--------+
                          |
           +--------------+--------------+
           |              |              |
        Compiles?     Red Flags?     Duplicates?
           |              |              |
           No             Yes            Yes
           |              |              |
           v              v              v
        Fix syntax    Flag for      Ask AI to
        errors        investigation  use existing
           |              |              |
           +------+-------+------+-------+
                  |
                  v
         +--------+--------+
         | Deep Review     |
         | (Levels 2-10)   |
         +--------+--------+
                  |
           +------+------+
           |             |
        Issues?       Clean?
           |             |
           v             v
        Iterate      Integrate
        with AI
```

---

## Common AI Mistakes by Category

### Category 1: Type Mistakes

```java
// WRONG: double for money
double price = 19.99;

// RIGHT
BigDecimal price = new BigDecimal("19.99");
```

```java
// WRONG: int for large counters
int viewCount = 0;

// RIGHT
long viewCount = 0L;
```

```java
// WRONG: == for strings
if (status == "active")

// RIGHT
if ("active".equals(status))
```

### Category 2: Null Mistakes

```java
// WRONG: No null check
user.getProfile().getAddress().getCity()

// RIGHT
Optional.ofNullable(user)
    .map(User::getProfile)
    .map(Profile::getAddress)
    .map(Address::getCity)
    .orElse("Unknown")
```

```java
// WRONG: Optional.get() direct
return optional.get();

// RIGHT
return optional.orElseThrow(() -> new NotFoundException());
```

### Category 3: Resource Mistakes

```java
// WRONG: Manual close
FileReader reader = new FileReader(path);
String content = read(reader);
reader.close();  // Never reached if read() throws

// RIGHT
try (FileReader reader = new FileReader(path)) {
    return read(reader);
}
```

### Category 4: Exception Mistakes

```java
// WRONG: Empty catch
try { ... } catch (Exception e) { }

// RIGHT
try { ... } catch (SpecificException e) {
    log.error("Context: {}", id, e);
    throw new ServiceException("Failed", e);
}
```

```java
// WRONG: Lost cause
catch (SQLException e) {
    throw new DataException(e.getMessage());
}

// RIGHT
catch (SQLException e) {
    throw new DataException("DB error", e);  // Cause preserved
}
```

### Category 5: Collection Mistakes

```java
// WRONG: ArrayList for frequent contains
List<Long> processedIds = new ArrayList<>();
if (!processedIds.contains(id)) ...  // O(n)

// RIGHT
Set<Long> processedIds = new HashSet<>();
if (processedIds.add(id)) ...  // O(1)
```

```java
// WRONG: Modification during iteration
for (Item item : items) {
    if (condition) items.remove(item);
}

// RIGHT
items.removeIf(item -> condition);
```

### Category 6: OOP Mistakes

```java
// WRONG: Ignoring existing base class
public class User {
    private Long id;
    private LocalDateTime createdAt;
    // ... duplicating BaseEntity
}

// RIGHT
public class User extends BaseEntity {
    // id, createdAt inherited
}
```

```java
// WRONG: Hardcoded dependency
private StripeProcessor processor = new StripeProcessor();

// RIGHT
private PaymentProcessor processor;  // Injected interface
```

---

## Red Flag Quick Reference

| Red Flag | Why It's Bad | Fix |
|----------|--------------|-----|
| `catch (Exception e) {}` | Hides all errors | Catch specific, log, handle |
| `double` for money | Precision errors | Use BigDecimal |
| `== ` for String | Object comparison | Use .equals() |
| `public` fields | No encapsulation | Private + getters |
| `new Dependency()` | Can't test/swap | Inject dependency |
| `Optional.get()` | NPE risk | Use orElse/orElseThrow |
| `ArrayList.contains()` loops | O(n) per check | Use HashSet |
| No try-with-resources | Resource leak | Use try-with-resources |
| `instanceof` chains | Not extensible | Use polymorphism |
| `static` for state | Thread unsafe | Use instance fields |

---

## Review Questions to Ask

For each piece of AI code, ask:

### About Types
- "Is this the right type for this data?"
- "Could this overflow or lose precision?"
- "Is null possible here?"

### About Structure
- "Does this duplicate existing code?"
- "Should this extend something?"
- "Is this the right collection type?"

### About Safety
- "What happens if this is null?"
- "What happens if this throws?"
- "Are resources properly closed?"

### About Design
- "Can I test this in isolation?"
- "Can I extend this without modification?"
- "Does this follow our patterns?"

---

## The 5-Minute Full Review

```
Minute 1: Quick Scan
  - Compiles?
  - Obvious red flags?
  - Using existing classes?

Minute 2: Types & Nulls
  - BigDecimal for money?
  - Null checks present?
  - String .equals()?

Minute 3: Resources & Exceptions
  - try-with-resources?
  - Specific catches?
  - Causes chained?

Minute 4: Collections & OOP
  - Right collection?
  - Private fields?
  - Uses existing base classes?

Minute 5: Integration
  - Follows team style?
  - Uses project utilities?
  - Correct annotations?
```

---

## When to Reject AI Code

**Reject immediately if:**
- Creates security vulnerabilities (SQL injection, exposed credentials)
- Ignores critical requirements
- Would require complete rewrite to fix

**Request iteration if:**
- Uses wrong types (double for money)
- Missing null handling
- Wrong collection choice
- Doesn't use existing base classes

**Accept with minor edits if:**
- Style issues only
- Missing comments/docs
- Minor naming adjustments

---

## What to Tell AI When Rejecting

**Bad:**
```
"This is wrong, fix it"
```

**Good:**
```
"Update the code:
1. Change price from double to BigDecimal
2. Add null check before user.getProfile()
3. Extend BaseEntity instead of creating id field
4. Use HashSet instead of ArrayList for processedIds"
```

Be specific. AI can't read your mind about what's wrong.

---

## Chapter 10 Quiz

**Q1: What's the first thing to check in AI-generated code?**
- a) Performance
- b) Does it compile and any obvious red flags
- c) Documentation
- d) Test coverage

<details>
<summary>Answer</summary>

**b) Compiles and red flags** - Quick scan first: does it compile? Any obvious issues like empty catch blocks or public fields?
</details>

---

**Q2: AI used double for a bank balance. What's your action?**
- a) Accept - double is fine for decimals
- b) Reject and request BigDecimal
- c) Add a comment saying it's approximate
- d) Convert to cents as integer

<details>
<summary>Answer</summary>

**b) Request BigDecimal** - Money must use BigDecimal for precision. Double has floating-point errors that cause real financial bugs.
</details>

---

**Q3: AI code has `catch (Exception e) { return null; }`. Problem?**
- a) Should return empty string
- b) Hides all errors including bugs, returns dangerous null
- c) Exception is deprecated
- d) No problem

<details>
<summary>Answer</summary>

**b) Hides errors, dangerous null** - Catches everything (including bugs), silently fails, and returns null which will cause NPE elsewhere.
</details>

---

**Q4: AI created a User class with id, createdAt fields. You have BaseEntity with these. Action?**
- a) Keep both - more explicit
- b) Ask AI to extend BaseEntity
- c) Delete BaseEntity
- d) Merge manually

<details>
<summary>Answer</summary>

**b) Extend BaseEntity** - Duplicating common fields causes maintenance issues. Tell AI about existing base classes.
</details>

---

**Q5: AI uses `optional.get()` directly. What should replace it?**
- a) `optional.value()`
- b) `optional.orElseThrow(() -> new NotFoundException())`
- c) `optional.getOrNull()`
- d) Keep it - it's correct

<details>
<summary>Answer</summary>

**b) orElseThrow** - Direct get() throws NoSuchElementException if empty. orElseThrow gives meaningful exception.
</details>

---

**Q6: When should you reject AI code entirely vs request iteration?**
- a) Reject everything always
- b) Reject security issues; iterate on type/collection mistakes
- c) Never reject - fix it yourself
- d) Reject style issues only

<details>
<summary>Answer</summary>

**b) Reject security, iterate others** - Security vulnerabilities warrant rejection. Wrong types or collections can be fixed through iteration.
</details>

---

**Q7: AI wrote `for(Item i : items) items.remove(i)`. Issue?**
- a) Syntax error
- b) ConcurrentModificationException
- c) Performance issue
- d) No issue

<details>
<summary>Answer</summary>

**b) ConcurrentModificationException** - Can't modify collection during foreach. Use `items.removeIf()` or iterator.
</details>

---

**Q8: What should you always check about exceptions in AI code?**
- a) Only that they're caught
- b) Specific exceptions caught, causes chained, no empty blocks
- c) Total exception count
- d) Exception class names

<details>
<summary>Answer</summary>

**b) Specific, chained, not empty** - Generic catches hide bugs. Lost causes make debugging hard. Empty blocks are silent failures.
</details>

---

**Q9: AI uses `new PaymentService()` inside a controller. Problem?**
- a) Syntax error
- b) Can't mock for testing, not following DI pattern
- c) Performance issue
- d) No problem

<details>
<summary>Answer</summary>

**b) Can't test, violates DI** - Direct instantiation means you can't mock dependencies for testing. Should be injected.
</details>

---

**Q10: What's the 5-minute review order?**
- a) Security → Performance → Types → Tests
- b) Scan → Types/Nulls → Resources/Exceptions → Collections/OOP → Integration
- c) Tests → Code → Deploy
- d) Random order is fine

<details>
<summary>Answer</summary>

**b) Structured order** - Quick scan first, then progressively deeper checks: types, resources, design, integration. Systematic review catches more issues.
</details>

---

## Key Takeaways

- **Assume AI code is wrong** until you verify it
- **Use the checklist** - systematic review catches more bugs
- **Types matter** - BigDecimal for money, long for large counts
- **Check for existing code** - AI doesn't know your codebase
- **Be specific when rejecting** - tell AI exactly what to fix
- **5 minutes of review** can save days of debugging

---

## Final Thoughts

You now have the knowledge to:

1. **Understand Java concepts** at the level needed to evaluate AI output
2. **Write effective prompts** that get better code from AI
3. **Review AI output systematically** using checklists
4. **Catch common mistakes** before they reach production

AI is a powerful tool. Your Java knowledge makes it a **safe** powerful tool.

The developers who thrive in the AI era aren't those who write the most code. They're the ones who **understand** code well enough to guide AI and catch its mistakes.

You're now equipped to be one of them.

---

*"The best code review is the one that happens before the bug reaches production. With AI, that review is your job."*
