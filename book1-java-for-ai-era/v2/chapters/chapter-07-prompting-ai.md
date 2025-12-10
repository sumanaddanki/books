# Chapter 7: Prompting AI for Code

> "AI gives you what you ask for. The skill is learning to ask for what you actually need."

---

## Why Prompting Matters

You've learned the concepts. You understand types, logic, structure, errors, and collections.

Now you can:
- Ask AI for exactly what you need
- Provide context that matters
- Spot when AI misunderstood
- Iterate to improve results

The difference between frustrating AI interactions and productive ones isn't the AI—it's how you communicate with it.

---

## The Anatomy of a Good Prompt

### Bad Prompt:
> "Write a function to process users"

**What AI doesn't know:**
- Process how? Filter? Transform? Validate?
- What user fields exist?
- What should happen with errors?
- What's the expected output?
- How many users? Performance concerns?

### Good Prompt:
> "Write a Java method that:
> - Takes a List of User objects
> - Filters out users who haven't verified their email
> - Returns their email addresses as a List<String>
> - Handle null input by returning empty list
> - User class has: email (String), isVerified (boolean), name (String)"

**What AI now knows:**
- Input type and output type
- The transformation to perform
- Edge case handling
- Relevant fields

---

## The Five Components of Effective Prompts

### 1. Context - What Are You Building?

Tell AI what you're working on. This helps it make appropriate choices.

**Without context:**
> "Create a login function"

**With context:**
> "I'm building a financial app. Create a login function that needs to be secure. Users log in with email and password."

Context changes recommendations. A blog login ≠ a bank login.

### 2. Input/Output - What Goes In, What Comes Out?

Be explicit about data types and structures.

**Without I/O:**
> "Convert temperature"

**With I/O:**
> "Create a method that takes a temperature in Fahrenheit (double) and returns Celsius (double).
> Example: input 32.0 → output 0.0"

### 3. Constraints - What Rules Must Be Followed?

Tell AI about requirements and limitations.

**Without constraints:**
> "Create a password validator"

**With constraints:**
> "Create a password validator with these rules:
> - Minimum 8 characters
> - At least one uppercase letter
> - At least one lowercase letter
> - At least one number
> - No spaces allowed
> - Return a list of which rules failed (empty if valid)"

### 4. Edge Cases - What Could Go Wrong?

Explicitly mention cases that need handling.

**Without edge cases:**
> "Calculate average of numbers"

**With edge cases:**
> "Calculate average of a list of integers.
> - Handle empty list (return 0 or throw exception?)
> - Handle null input
> - Handle potential integer overflow for large lists
> - Return as double for precision"

### 5. Examples - Show What You Mean

Examples remove ambiguity.

**Without examples:**
> "Format a phone number"

**With examples:**
> "Format phone numbers to (XXX) XXX-XXXX format.
> Examples:
> - 5551234567 → (555) 123-4567
> - 555-123-4567 → (555) 123-4567
> - (555) 123-4567 → (555) 123-4567 (already formatted)
> - Invalid input → throw exception"

---

## Prompting Patterns That Work

### Pattern 1: The Setup + Requirements

**Structure:**
> "I'm building [context]. I need [what].
> Requirements:
> - [requirement 1]
> - [requirement 2]
> - [requirement 3]"

**Example:**
> "I'm building an e-commerce checkout. I need a method to calculate order total.
> Requirements:
> - Sum all item prices × quantities
> - Apply discount code if provided (percentage off)
> - Add tax (8.5%)
> - Return breakdown: subtotal, discount, tax, total
> - All money values in cents (integers) to avoid floating point issues"

### Pattern 2: The Existing Code Context

**Structure:**
> "Here's my existing code: [code]
> I need to add: [what]
> It should: [requirements]"

**Example:**
> "Here's my User class:
> ```java
> public class User {
>     private String email;
>     private String passwordHash;
>     private boolean isVerified;
> }
> ```
> I need to add a method to change the password.
> It should:
> - Require the old password (verify it matches)
> - Validate new password (min 8 chars, has number)
> - Hash the new password before storing
> - Return success/failure with reason"

### Pattern 3: The Error/Bug Fix

**Structure:**
> "This code has a problem: [describe problem]
> Here's the code: [code]
> Expected behavior: [what should happen]
> Actual behavior: [what actually happens]"

**Example:**
> "This code sometimes processes the same order twice:
> ```java
> public void processOrder(Order order) {
>     if (!processedOrders.contains(order.getId())) {
>         chargeCustomer(order);
>         processedOrders.add(order.getId());
>     }
> }
> ```
> Expected: Each order processed exactly once
> Actual: Under high load, same order is processed twice
> Please fix the race condition"

### Pattern 4: The Improvement Request

**Structure:**
> "Here's working code: [code]
> Problem: [issue]
> Please improve it to: [goal]"

**Example:**
> "Here's working code:
> ```java
> for (User user : users) {
>     if (user.isActive() && user.getAge() > 18 && user.hasVerifiedEmail()) {
>         result.add(user.getEmail());
>     }
> }
> ```
> Problem: Hard to read, not using modern Java features
> Please improve it to use streams while keeping the same logic"

### Pattern 5: The Comparison Request

**Structure:**
> "I need to [goal]. What are the options and trade-offs?
> Context: [relevant constraints]"

**Example:**
> "I need to cache API responses. What are the options and trade-offs?
> Context:
> - About 10,000 unique requests per hour
> - Response data is 1-5KB each
> - Data can be stale for up to 5 minutes
> - Running on a single server (not distributed)"

---

## What to Include by Category

### When Asking for Data Processing:
- Input format/type
- Output format/type
- Transformation rules
- Edge cases (empty, null, invalid)
- Performance concerns (how many items?)

### When Asking for Validation:
- All rules that must pass
- What to return (boolean? list of errors?)
- Examples of valid and invalid input
- How strict? (trim whitespace? case-sensitive?)

### When Asking for API/Database Code:
- What operation (read/write/update/delete)?
- Error handling expectations
- Transaction requirements
- Security considerations

### When Asking for Error Handling:
- Which errors to catch
- Retry logic if any
- Logging requirements
- User-facing messages

---

## Iterating on AI Responses

First response not quite right? Don't start over. Refine.

### Refinement Prompts:

**Make it simpler:**
> "This is more complex than needed. Can you simplify it?"

**Make it more robust:**
> "What happens if the input is null? Add handling for that."

**Change the approach:**
> "Instead of using a loop, can you use streams?"

**Add error handling:**
> "Add proper exception handling. Log errors and throw custom exception."

**Explain the code:**
> "Can you explain what this part does? [paste specific section]"

**Ask about trade-offs:**
> "Are there performance concerns with this approach? What's the time complexity?"

---

## Red Flags in AI Responses

When you get code back, watch for:

**🚩 No input validation**
Ask: "What if the input is null or empty?"

**🚩 Generic exception handling**
Ask: "Can you catch specific exceptions instead of Exception?"

**🚩 No error handling at all**
Ask: "What could fail here and how should we handle it?"

**🚩 Magic numbers**
Ask: "What does the number 5 mean here? Should it be a named constant?"

**🚩 Missing edge cases**
Ask: "What happens with empty list? Zero? Negative numbers?"

**🚩 Overly complex solution**
Ask: "Is there a simpler way to do this?"

---

## Prompts for Common Tasks

### Validating Input:
> "Create a validation method for [data type].
> Rules: [list all rules]
> Return: [specific error messages for each rule that fails]
> Handle: null input, empty input"

### Processing Collections:
> "Process a list of [items].
> For each item: [what to do]
> Filter: [conditions to include/exclude]
> Transform: [how to modify]
> Handle: empty list, null items in list
> Return: [output type]"

### Error Handling:
> "Add error handling to this code: [code]
> Handle: [specific error types]
> On failure: [retry? log? throw?]
> Cleanup: [resources to close]"

### Database Operations:
> "Create a method to [operation] in database.
> Input: [what data]
> Return: [what to return]
> Errors: [what could fail, how to handle]
> Transaction: [yes/no, rollback conditions]"

---

## Quick Reference: Prompt Checklist

Before submitting a prompt, check:

- [ ] Did I explain the context?
- [ ] Did I specify input types?
- [ ] Did I specify output types?
- [ ] Did I mention edge cases?
- [ ] Did I include examples (if complex)?
- [ ] Did I state constraints/requirements?
- [ ] Did I mention error handling needs?
- [ ] Is there anything ambiguous?

---

## Key Takeaways

- Good prompts have: context, input/output, constraints, edge cases, examples
- Be specific—AI can't read your mind
- Iterate on responses rather than starting over
- Ask about edge cases and error handling
- Request explanations when code is unclear
- The better your prompt, the less debugging later

---

## What's Next

**Chapter 8: Reviewing AI Output**

You know how to ask AI for code. Now: how do you review what it gives you?

In the final chapter, we'll cover the systematic review process that catches problems before they ship.

---

*"A vague question gets a vague answer. A precise question gets precise code."*
