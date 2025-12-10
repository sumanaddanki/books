# Chapter 9: Prompting AI for Java - Getting Better Code

> "The quality of AI's output depends on the quality of your input. Vague prompts get vague code."

---

## The Prompting Framework

```
Bad Prompt:                    Good Prompt:
+------------------+          +-------------------------+
| "Make a user     |          | Context:                |
|  class"          |          |   - Extends BaseEntity  |
|                  |          |   - Uses Lombok         |
|                  |          | Requirements:           |
|                  |          |   - email unique        |
|                  |          |   - password hashed     |
|                  |          | Constraints:            |
|                  |          |   - JPA entity          |
|                  |          |   - Java 17             |
+------------------+          +-------------------------+
        |                              |
        v                              v
Generic code that              Code that fits your
needs heavy modification       project immediately
```

---

## The CONTEXT-REQUIREMENTS-CONSTRAINTS Model

Every good prompt has three parts:

### 1. Context (What Exists)

Tell AI about your codebase:
- Existing classes to extend/use
- Patterns your team follows
- Libraries already in project

### 2. Requirements (What You Need)

Be specific about functionality:
- What the code should do
- Expected inputs/outputs
- Edge cases to handle

### 3. Constraints (Technical Limits)

Specify technical boundaries:
- Java version
- Frameworks (Spring, Jakarta)
- Style preferences

---

## Prompt Patterns for Common Tasks

### Pattern 1: Creating a New Class

**Bad:**
```
Create a User class
```

**Good:**
```
Create a User entity class for Spring Boot.

Context:
- Extend our BaseEntity (has id, createdAt, updatedAt)
- Project uses Lombok
- We have UserRole enum: ADMIN, USER, GUEST

Requirements:
- Fields: email (unique), passwordHash, name, role
- Email must be validated format
- Password should never be exposed (no getter)
- Include a method to check password: boolean checkPassword(String raw)

Constraints:
- Java 17, use records where appropriate
- JPA annotations for MySQL
- Follow builder pattern for construction
```

### Pattern 2: Implementing a Feature

**Bad:**
```
Add user authentication
```

**Good:**
```
Implement password reset functionality.

Context:
- UserService already has findByEmail(String)
- EmailService has sendEmail(String to, String subject, String body)
- We use JWT for tokens (JwtService exists)

Requirements:
- User requests reset with email
- Generate time-limited token (1 hour)
- Send email with reset link
- Validate token and update password
- Invalidate token after use

Constraints:
- Spring Boot 3.x
- Store tokens in reset_tokens table
- Use BCrypt for password hashing
- Return appropriate HTTP status codes
```

### Pattern 3: Fixing a Bug

**Bad:**
```
Fix the null pointer exception
```

**Good:**
```
Fix NullPointerException in OrderService.calculateTotal()

Current code:
[paste the problematic code]

Error:
NullPointerException at line 45 when order.getItems() returns null

Requirements:
- Handle case where items list is null or empty
- Return BigDecimal.ZERO for empty orders
- Add validation to prevent null items in future

Constraints:
- Don't change method signature
- Keep existing behavior for valid orders
- Add appropriate logging
```

### Pattern 4: Writing Tests

**Bad:**
```
Write tests for UserService
```

**Good:**
```
Write unit tests for UserService.createUser()

Method signature:
public User createUser(CreateUserRequest request) throws DuplicateEmailException

Test scenarios needed:
1. Successfully creates user with valid data
2. Throws DuplicateEmailException for existing email
3. Hashes password before saving
4. Sets default role to USER
5. Validates email format

Context:
- Use JUnit 5 + Mockito
- UserRepository is a dependency (mock it)
- PasswordEncoder is a dependency (mock it)

Constraints:
- Follow AAA pattern (Arrange-Act-Assert)
- Use @DisplayName for readable test names
- One assertion per test where practical
```

---

## Prompt Modifiers for Better Results

### Specify the Style

```
"Use defensive programming - check all inputs"
"Use fail-fast approach - throw early"
"Use fluent API style for the builder"
"Use functional style with streams"
"Use traditional loops for clarity"
```

### Specify What to Avoid

```
"Don't use Optional as method parameter"
"Don't catch generic Exception"
"Don't use static mutable state"
"Don't use raw types - always specify generics"
```

### Request Specific Patterns

```
"Use the factory method pattern"
"Use dependency injection, not new"
"Use the template method pattern"
"Return Optional instead of null"
```

---

## Domain-Specific Prompts

### For REST APIs

```
Create REST endpoint for user registration.

Context:
- Spring Boot 3.x with Spring Security
- DTOs: RegisterRequest, UserResponse
- Service: UserService.register(RegisterRequest)

Requirements:
- POST /api/users/register
- Validate: email format, password strength (8+ chars, 1 number)
- Return 201 on success with UserResponse
- Return 400 with validation errors
- Return 409 if email exists

Constraints:
- Use @Valid for input validation
- Use ResponseEntity for response
- Document with OpenAPI annotations
- No sensitive data in response
```

### For Database Operations

```
Create repository method to find active users by department.

Context:
- Spring Data JPA
- User has: isActive (boolean), department (String)
- Need pagination support

Requirements:
- Find users where isActive=true and department matches
- Support pagination (page, size)
- Sort by name ascending by default

Constraints:
- Use method naming convention if possible
- Otherwise use @Query with JPQL
- Return Page<User>
```

### For Error Handling

```
Add error handling to PaymentService.processPayment()

Current flow:
1. Validate payment details
2. Call payment gateway
3. Update order status
4. Send confirmation email

Requirements:
- Custom exceptions: InvalidPaymentException, GatewayException
- Log all errors with transaction ID
- Rollback on gateway failure
- Don't fail if email fails (log warning)

Constraints:
- Use @Transactional appropriately
- Chain exception causes
- Return PaymentResult with success/failure info
```

---

## Anti-Patterns: What NOT to Prompt

### Too Vague

```
"Make it work"
"Fix it"
"Add error handling"
"Make it better"
```

### Too Broad

```
"Build a complete e-commerce system"
"Create all the APIs for user management"
"Write all the tests"
```

### Missing Context

```
"Add authentication"
(What framework? What method? What exists?)

"Create User class"
(Extends what? What fields? What patterns?)
```

### Contradictory

```
"Use immutable objects with setters"
"Make it thread-safe but use HashMap"
"Use Optional but return null for not found"
```

---

## The Iteration Pattern

Don't expect perfect code on first try:

```
Round 1: Generate initial code
   |
   v
Round 2: "Add null checks for X and Y"
   |
   v
Round 3: "Use HashSet instead of ArrayList for lookupTable"
   |
   v
Round 4: "Add logging at entry and exit"
   |
   v
Final: Review and integrate
```

### Example Iteration

**Round 1:**
```
Create a service method to calculate order total
```

**AI outputs basic method**

**Round 2:**
```
Update to:
- Use BigDecimal for money (not double)
- Handle null items list
- Apply discount if present
```

**AI updates**

**Round 3:**
```
Add:
- Logging with order ID
- Metric for calculation time
- Cache result in Order object
```

**Continue until satisfied**

---

## Prompt Templates

### Template: New Service Method

```
Create [method name] in [ServiceClass].

Context:
- Dependencies: [list injected services/repos]
- Related classes: [DTOs, entities involved]
- Existing methods: [related methods that exist]

Requirements:
- Input: [parameters with types]
- Output: [return type and meaning]
- Steps: [1. 2. 3. ...]
- Edge cases: [null, empty, duplicates, etc.]

Constraints:
- Java version: [X]
- Framework: [Spring/Jakarta/etc]
- Transaction: [required/not/readonly]
- Exceptions: [what to throw when]
```

### Template: Bug Fix

```
Fix [brief description] in [Class.method()]

Current behavior:
[what happens now]

Expected behavior:
[what should happen]

Code:
[paste relevant code]

Error/Stack trace:
[paste if available]

Constraints:
- Don't change: [method signature/public API/etc]
- Must handle: [specific cases]
```

### Template: Code Review Request

```
Review this code for:
- Thread safety issues
- Null pointer risks
- Resource leaks
- Performance problems
- Java best practices

[paste code]

Context:
- This runs in [single-threaded/multi-threaded] environment
- Called approximately [frequency]
- Data size typically [small/medium/large]
```

---

## What to Tell AI

**Bad:** "Create a class"

**Good:** "Create a JPA entity extending BaseEntity with these fields... using Lombok... for Java 17"

**Bad:** "Add validation"

**Good:** "Add bean validation: email must be valid format, name must be 2-50 chars, age must be 18-120"

**Bad:** "Handle errors"

**Good:** "Catch SQLException and wrap in DataAccessException with original cause. Log error with user ID context."

---

## Red Flags in Your Own Prompts

🚩 **Single sentence prompts**
```
"Create user service"  // Too vague
```

🚩 **No context about existing code**
```
"Add login"  // What exists? What patterns?
```

🚩 **Asking for too much at once**
```
"Build the entire authentication system"  // Break it down
```

🚩 **No constraints specified**
```
"Create REST API"  // What framework? What version?
```

---

## Chapter 9 Quiz

**Q1: What's wrong with the prompt "Create a User class"?**
- a) Too long
- b) Missing context, requirements, and constraints
- c) Grammatically incorrect
- d) Nothing wrong

<details>
<summary>Answer</summary>

**b) Missing context, requirements, constraints** - AI doesn't know: what to extend, what fields, what patterns, what Java version, what framework.
</details>

---

**Q2: What should you include in "Context" part of a prompt?**
- a) What you want the code to do
- b) Existing classes, patterns, libraries in your project
- c) Java version only
- d) Your timeline

<details>
<summary>Answer</summary>

**b) Existing classes, patterns, libraries** - Context tells AI what already exists so it can integrate with your codebase.
</details>

---

**Q3: When fixing a bug via AI, what should you include?**
- a) Just say "fix the bug"
- b) Current code, error message, expected behavior, constraints
- c) Only the stack trace
- d) Only the file name

<details>
<summary>Answer</summary>

**b) All relevant information** - AI needs to see the code, understand what's wrong, know what's expected, and know what can't change.
</details>

---

**Q4: Why iterate on prompts rather than asking for everything at once?**
- a) AI has word limits
- b) Smaller requests are more accurate; you can refine based on output
- c) It's faster
- d) AI prefers short prompts

<details>
<summary>Answer</summary>

**b) Better accuracy and refinement** - Complex requests often miss details. Iterating lets you catch issues and guide AI toward exactly what you need.
</details>

---

**Q5: Which is a good "Constraint" for a prompt?**
- a) "Make it good"
- b) "Use Java 17, Spring Boot 3.x, return Optional for nullable results"
- c) "Be fast"
- d) "User should like it"

<details>
<summary>Answer</summary>

**b) Specific technical constraints** - Version numbers, frameworks, patterns, and technical requirements are constraints. Vague qualities aren't actionable.
</details>

---

**Q6: What's wrong with "Build complete e-commerce system"?**
- a) E-commerce is copyrighted
- b) Too broad - should be broken into smaller, specific requests
- c) AI can't do e-commerce
- d) Nothing wrong

<details>
<summary>Answer</summary>

**b) Too broad** - Break into: "Create Product entity", "Create Cart service", "Create checkout flow" etc. Smaller requests get better results.
</details>

---

**Q7: When asking AI to write tests, what should you specify?**
- a) Just "write tests"
- b) Test framework, scenarios to cover, mocking strategy, patterns
- c) Only the class name
- d) Only how many tests

<details>
<summary>Answer</summary>

**b) Framework, scenarios, mocking, patterns** - "Use JUnit 5 + Mockito, test these scenarios, mock these dependencies, use AAA pattern"
</details>

---

**Q8: What does "Context" help AI avoid?**
- a) Writing too much code
- b) Creating classes/patterns that duplicate what you already have
- c) Using wrong language
- d) Making syntax errors

<details>
<summary>Answer</summary>

**b) Duplicating existing code** - Without context, AI doesn't know you have BaseEntity, so it creates its own. Context prevents this.
</details>

---

**Q9: Which modifier helps get more defensive code?**
- a) "Make it fast"
- b) "Use defensive programming - validate all inputs, handle nulls"
- c) "Keep it simple"
- d) "Make it work"

<details>
<summary>Answer</summary>

**b) Explicit defensive programming request** - Telling AI your style preferences directly affects output. "Handle nulls, validate inputs" is actionable.
</details>

---

**Q10: After AI generates code, what's the next step?**
- a) Deploy immediately
- b) Review, iterate with refinement prompts, then integrate
- c) Delete and rewrite manually
- d) Accept without reading

<details>
<summary>Answer</summary>

**b) Review and iterate** - AI output is a starting point. Review for issues, refine with follow-up prompts, then integrate with your codebase.
</details>

---

## Key Takeaways

- **Use CONTEXT-REQUIREMENTS-CONSTRAINTS** for every significant prompt
- **Provide existing code context** - what classes/patterns exist
- **Be specific about requirements** - inputs, outputs, edge cases
- **Specify technical constraints** - version, framework, patterns
- **Iterate** - don't expect perfection on first try
- **Avoid vague prompts** - "make it work" gets poor results

---

## What's Next

**Chapter 10: Reviewing Java Output**

The checklist for evaluating AI-generated Java code before accepting it.

---

*"A well-crafted prompt is half the solution. A vague prompt is just wishful thinking."*
