# Chapter 8: Reviewing AI Output

> "AI code isn't right or wrong. It's a first draft that needs your expertise."

---

## The Review Mindset

AI-generated code is impressive. It's also:
- A first draft, not finished code
- Optimized for looking correct, not being correct
- Missing context about your specific needs
- Potentially hiding subtle bugs

Your job: Find what's missing, what's wrong, what's risky—before it ships.

---

## The 5-Minute Review

You don't need to spend hours reviewing. A systematic 5-minute check catches most problems.

### Minute 1: Does It Do What I Asked?

Read the code. Trace through it mentally with sample input.

- Does the happy path work?
- Did AI miss any requirements?
- Does the output match what I expected?

**Red flag:** You can't follow what the code does.
**Action:** Ask AI to explain it. If you still can't follow, it's probably too complex.

---

### Minute 2: Edge Cases

Walk through problematic inputs:

| Input Type | Check |
|------------|-------|
| Null | What if any input is null? |
| Empty | Empty string? Empty list? |
| One | List with one item? |
| Many | List with millions of items? |
| Zero | What if number is 0? |
| Negative | What if number is negative? |
| Boundary | What about max/min values? |

**Red flag:** No null checks, no empty handling.
**Action:** Ask AI to add specific edge case handling.

---

### Minute 3: Error Handling

Look at how failures are handled:

- Are errors caught?
- Are they logged with enough context?
- Are they swallowed (bad) or propagated (usually good)?
- Is cleanup done even on error?

**Red flags:**
```java
catch (Exception e) { }           // Empty - terrible
catch (Exception e) { return null; }  // Hides problems
catch (Exception e) { log.error("Error"); }  // No context
```

**Good:**
```java
catch (SpecificException e) {
    log.error("Failed to process order {}: {}", orderId, e.getMessage());
    throw new OrderProcessingException("Could not process order", e);
}
```

---

### Minute 4: Security Scan

Quick check for common vulnerabilities:

**SQL Injection:**
```java
// BAD
String query = "SELECT * FROM users WHERE id = " + userId;

// GOOD
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
ps.setString(1, userId);
```

**Exposed Sensitive Data:**
```java
// BAD
log.info("User login: " + user.toString());  // Might include password

// GOOD
log.info("User login: {}", user.getEmail());
```

**Missing Authorization:**
```java
// BAD - Any user can access any data
public Order getOrder(Long orderId) {
    return orderRepository.findById(orderId);
}

// GOOD - Check ownership
public Order getOrder(Long orderId, User currentUser) {
    Order order = orderRepository.findById(orderId);
    if (!order.getUserId().equals(currentUser.getId())) {
        throw new UnauthorizedException();
    }
    return order;
}
```

---

### Minute 5: Performance Smell

Look for common performance problems:

**Nested loops (O(n²)):**
```java
// BAD - For 10,000 items, this is 100,000,000 operations
for (User user : users) {
    for (Order order : allOrders) {
        if (order.getUserId().equals(user.getId())) { ... }
    }
}

// GOOD - Build a Map first
Map<Long, List<Order>> ordersByUser = // group orders by userId
for (User user : users) {
    List<Order> userOrders = ordersByUser.get(user.getId());
}
```

**Database calls in loops:**
```java
// BAD - N+1 problem
for (Order order : orders) {
    User user = userRepository.findById(order.getUserId());  // Query each time!
}

// GOOD - Batch fetch
Set<Long> userIds = orders.stream().map(Order::getUserId).collect(toSet());
Map<Long, User> users = userRepository.findAllById(userIds);  // One query
```

**Loading everything:**
```java
// BAD - Loads entire table
List<User> allUsers = userRepository.findAll();
User target = allUsers.stream().filter(u -> u.getEmail().equals(email)).findFirst();

// GOOD - Let database do the work
User target = userRepository.findByEmail(email);
```

---

## The Detailed Review Checklist

For important code, go deeper:

### Logic
- [ ] Does it handle all cases from requirements?
- [ ] Are conditions correct? (< vs <=, && vs ||)
- [ ] Is the order of operations right?
- [ ] Are there any off-by-one errors?

### Data
- [ ] Are the right types used? (int vs long, float vs BigDecimal)
- [ ] Is data validated before use?
- [ ] Are conversions handled correctly?
- [ ] Is null handled appropriately?

### Errors
- [ ] Are specific exceptions caught (not just Exception)?
- [ ] Is error context logged?
- [ ] Are resources cleaned up on error?
- [ ] Does the user see helpful messages?

### Security
- [ ] Is user input sanitized?
- [ ] Are queries parameterized?
- [ ] Is authorization checked?
- [ ] Is sensitive data protected?

### Performance
- [ ] Are there nested loops that could be optimized?
- [ ] Are database calls batched appropriately?
- [ ] Is memory usage reasonable for expected data size?
- [ ] Are the right collections used?

### Maintainability
- [ ] Can I understand this code?
- [ ] Are variable names clear?
- [ ] Is the logic too complex?
- [ ] Are magic numbers explained?

---

## Common AI Mistakes by Category

### Logic Mistakes

**Off-by-one errors:**
```java
// Processing 10 items: 0,1,2,...,9 or 0,1,2,...,10?
for (int i = 0; i <= items.size(); i++)  // Bug! Should be <
```

**Wrong comparisons:**
```java
if (user.getRole() == "admin")  // Bug! Should be .equals()
```

**Inverted conditions:**
```java
if (!user.isActive() && !user.isVerified())  // Is this what you wanted?
// vs
if (user.isActive() && user.isVerified())
```

### Security Mistakes

**SQL Injection:**
```java
query = "SELECT * FROM users WHERE name = '" + name + "'"
```

**Command Injection:**
```java
Runtime.exec("convert " + userFilename + " output.png")
```

**Path Traversal:**
```java
File file = new File("/uploads/" + userProvidedFilename);
// User could provide: "../../../etc/passwd"
```

### Performance Mistakes

**Wrong collection:**
```java
// Checking if item exists - should be Set, not List
List<String> processedIds = new ArrayList<>();
if (!processedIds.contains(id)) { ... }  // O(n) each check!
```

**Unnecessary work in loops:**
```java
for (User user : users) {
    Pattern pattern = Pattern.compile(regex);  // Compiles every iteration!
    if (pattern.matcher(user.getName()).matches()) { ... }
}
```

### Reliability Mistakes

**Resource leaks:**
```java
FileInputStream fis = new FileInputStream(file);
// Process file...
// What if exception occurs? fis never closed!
```

**Missing null checks:**
```java
return user.getProfile().getSettings().getTheme();
// What if profile is null? Settings is null?
```

---

## The "Explain It" Test

If you can't explain what the code does, don't ship it.

**The test:**
1. Read the code
2. Explain each section out loud (or to a rubber duck)
3. If you get stuck, that's a red flag

**Stuck? Ask AI:**
> "Explain what this code does step by step: [paste code]"

If the explanation doesn't match your expectations, something's wrong.

---

## When to Reject AI Code

**Reject when:**
- You don't understand it
- It doesn't handle obvious edge cases
- It has security red flags
- It's way more complex than needed
- It doesn't match your requirements

**Don't just regenerate.** Give feedback:
> "This doesn't handle null input. Also, the nested loops will be slow with large lists. Please fix these issues."

---

## Building Your Review Habit

### For Small Changes:
Quick mental trace + edge case check. ~2 minutes.

### For New Features:
Full 5-minute review. Check logic, errors, security, performance.

### For Critical Code (payments, auth, data):
Detailed review with checklist. Take your time. Get another person to look.

### Always:
If something feels wrong, investigate. Trust your instincts.

---

## Quick Reference: Red Flags

| Red Flag | What to Ask |
|----------|-------------|
| Empty catch block | "What happens when this fails?" |
| Catching Exception | "Can we catch something more specific?" |
| String concatenation in SQL | "Is this vulnerable to injection?" |
| No null checks | "What if this is null?" |
| Nested loops | "Can this be optimized with a Map/Set?" |
| Database call in loop | "Can we batch these queries?" |
| Magic numbers | "What does 5 mean? Should it be a constant?" |
| Everything is public | "Should some of this be private?" |
| No error messages | "How will we debug when this fails?" |
| Overly complex | "Is there a simpler way?" |

---

## Key Takeaways

- AI code is a first draft, not finished code
- 5-minute review catches most problems: logic, edges, errors, security, performance
- If you can't explain it, don't ship it
- Don't just regenerate—give AI specific feedback
- Trust your instincts when something feels wrong
- Critical code deserves extra review time

---

## What You've Learned

Congratulations. You now have:

1. **Understanding of why this matters** - The AI paradox, real disasters
2. **Types** - What kind of data, what containers
3. **Logic** - Decisions and repetition, what could go wrong
4. **Structure** - Why code is organized, the four concepts
5. **Errors** - What fails, how to handle it
6. **Collections** - List, Set, Map, when to use what
7. **Prompting** - How to ask AI effectively
8. **Reviewing** - How to catch problems before they ship

You're not an AI-dependent developer who accepts whatever appears.

You're an **informed developer** who directs AI effectively and catches problems before they become disasters.

---

## Where to Go From Here

This book gave you mental models. Now:

**Practice:**
- Use AI to build things
- Apply the review checklist
- Notice when you catch bugs the old you would have missed

**Keep learning:**
- Deep dive into areas relevant to your work
- Learn the specific tools of your domain
- Build increasingly complex projects

**Remember:**
- AI is a tool, not a replacement
- Understanding compounds over time
- Every bug you catch makes you better

---

*"The goal was never to memorize syntax. It was to think clearly about code. AI handles the typing. You handle the thinking. Together, you're unstoppable."*
