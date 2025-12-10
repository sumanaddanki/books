# Chapter 2: Thinking in Types - Why "What Kind of Data?" Matters

> "Every bug I've fixed started with someone not thinking clearly about their data."

---

## The First Question

Before any code exists, before you prompt any AI, before you design anything, there's one question:

**"What kind of data am I working with?"**

This sounds simple. It's not. Getting this wrong causes:
- Calculations that are silently incorrect
- Apps that crash on large numbers
- Security vulnerabilities
- Performance problems
- Debugging nightmares

---

## The Mental Model: Data Has Shape

Think of data like physical objects that need containers.

**You wouldn't put:**
- Soup in a paper bag
- A couch in a shoebox
- Water in a basket

**Same with data:**
- Money amounts need precise decimal containers
- User counts need whole number containers
- Yes/no answers need boolean containers
- Names need text containers

**The wrong container = problems.**

---

## The Four Data Shapes You Need to Know

### 1. Whole Numbers (Integers)

**What they are:** Numbers without fractions. 1, 42, -17, 1000000.

**Use for:**
- Counting things (users, items, attempts)
- IDs and identifiers
- Ages (usually)
- Quantities

**Real-world example:**
```
Users in database: 1,542
Items in cart: 3
Login attempts: 5
Order ID: 789456
```

**The trap:** Whole numbers have limits. Regular integers max out around 2.1 billion.

---

### 2. Decimal Numbers (Floating-Point)

**What they are:** Numbers with fractions. 3.14, 19.99, -0.001.

**Use for:**
- Measurements (distance, weight, temperature)
- Percentages
- Scientific calculations
- Coordinates

**NOT for:**
- Money (we'll explain why)

**Real-world example:**
```
Temperature: 72.5°F
Distance: 5.7 miles
Completion: 73.2%
Latitude: 37.7749
```

**The trap:** Decimals are imprecise. `0.1 + 0.2` doesn't equal `0.3` in a computer.

---

### 3. Yes/No (Booleans)

**What they are:** Two possible values. True or false. Yes or no. On or off.

**Use for:**
- Flags and toggles
- Conditions
- Status checks

**Real-world example:**
```
Is user logged in? true
Is email verified? false
Has paid? true
Is admin? false
```

**The trap:** Sometimes things aren't binary. "Has user seen tutorial?" might need "yes/no/skipped/dismissed."

---

### 4. Text (Strings)

**What they are:** Sequences of characters. Words, sentences, any text.

**Use for:**
- Names, emails, addresses
- Messages and content
- User input
- Identifiers (sometimes)

**Real-world example:**
```
Name: "Alice Johnson"
Email: "alice@example.com"
Error message: "File not found"
```

**The trap:** Text can be empty (""), null, or contain unexpected characters.

---

## Real Disasters: Type Mistakes

### Disaster 1: The Money Bug

**The scenario:**
Online store. Customer buys item for $100. Gets 15% discount.

**The AI code:**
```
discount_percent = 15
price = 100
discount_amount = discount_percent / 100 * price
```

**The result:**
Discount amount = 0

**Why?**
Integer 15 divided by integer 100 = 0 (fractions are thrown away).
0 times 100 = 0.

**What was needed:**
Understanding that division with integers throws away fractions. For percentages, you need decimal numbers.

---

### Disaster 2: The View Counter Crash

**The scenario:**
Video platform. Counter tracks views. Working great for months.

**The crash:**
Popular video hits 2.1 billion views. Counter suddenly shows -2 billion.

**Why?**
Integer limit is about 2.1 billion. After that, it "overflows" and wraps to negative numbers.

**What was needed:**
Understanding that counters for popular things need bigger containers (long integers can hold 9 quintillion).

---

### Disaster 3: The Floating-Point Fiasco

**The scenario:**
Financial app. Sum up transaction amounts. Compare to expected total.

**The code:**
```
total = 0.0
total = total + 0.1  (ten times)
if total == 1.0:
    print("Balanced!")
```

**The result:**
Never prints "Balanced!" even though 0.1 × 10 = 1.0

**Why?**
In computers, 0.1 can't be represented exactly. It's actually 0.1000000000000000055511151231257827021181583404541015625.

Add that ten times, you get 0.9999999999999999, not 1.0.

**What was needed:**
Understanding that decimals can't be compared directly. Use tolerance ("is it close enough?") or use exact decimal types for money.

---

### Disaster 4: The Empty String Crash

**The scenario:**
User profile page. Display user's bio.

**The code:**
```
Display: bio.substring(0, 100) + "..."
```

**The crash:**
NullPointerException for users who never set a bio.

**Why?**
Bio was null (not set), not empty (""). You can't call methods on null.

**What was needed:**
Understanding the difference between "no value" (null), "empty value" (""), and "actual value" ("Hello, I'm Alice").

---

## The Decision Framework

When thinking about data, ask:

### Question 1: "Does this need fractions?"

```
YES → Use decimal type
NO  → Use whole number type
```

Examples:
- Price: $19.99 → Needs fractions → Decimal
- Quantity: 5 items → No fractions → Whole number
- Rating: 4.5 stars → Needs fractions → Decimal
- User count: 1542 → No fractions → Whole number

### Question 2: "How big can this get?"

```
Under 2 billion      → Regular integer is fine
Over 2 billion       → Need larger integer type
Global scale counts  → Definitely need larger type
```

Examples:
- Items in cart → Max maybe 100 → Regular integer
- Video views → Could be billions → Larger integer
- Database IDs → Could grow forever → Larger integer
- User's age → Max 150 → Regular integer

### Question 3: "Is this money?"

```
YES → Use exact decimal types, NEVER floating-point
NO  → Floating-point is usually fine
```

Why money is special:
- Legal and regulatory requirements for exactness
- Small errors compound into big problems
- Users notice when money is wrong

### Question 4: "Is it yes/no, or more complex?"

```
Truly binary (on/off, yes/no)  → Boolean
Multiple states               → Consider other types
Could be "unknown"            → Might need nullable boolean
```

Examples:
- Is active? → Boolean
- Status → Could be "pending", "approved", "rejected" → Not boolean
- Has agreed to terms? → Boolean
- Notification preference → Could be "all", "important", "none" → Not boolean

---

## What to Tell AI

Instead of accepting whatever AI gives you, be specific:

**Bad prompt:**
"Create a variable for price"

**Good prompt:**
"Create a price variable that handles money precisely without floating-point errors"

**Bad prompt:**
"Create a counter"

**Good prompt:**
"Create a counter that can safely count up to 10 billion"

**Bad prompt:**
"Store user status"

**Good prompt:**
"Store user status that can be 'active', 'suspended', 'pending_verification', or 'deleted'"

---

## Red Flags in AI-Generated Code

Watch for these:

**🚩 Integer division with decimals expected**
```java
int percentage = 15;
int discount = percentage / 100 * price;  // Always 0!
```

**🚩 Regular integers for large counts**
```java
int globalUserCount = 0;  // Will overflow!
```

**🚩 Floating-point for money**
```java
double accountBalance = 1000.00;  // Imprecise!
```

**🚩 Direct decimal comparison**
```java
if (total == expected) {  // May never be true!
```

**🚩 No null handling for optional values**
```java
return user.getBio().substring(0, 100);  // Crashes if bio is null!
```

---

## Quick Mental Checklist

Before accepting AI code with data:

- [ ] Could this number exceed 2 billion? → Need larger type
- [ ] Does this involve fractions? → Need decimal type
- [ ] Is this money? → Need exact decimal, not float
- [ ] Am I comparing decimals? → Need tolerance, not ==
- [ ] Could this value be missing? → Handle null/empty
- [ ] Am I dividing integers? → Might lose fractions

---

## Key Takeaways

- Every piece of data has a "shape" that determines what container it needs
- Wrong containers cause silent bugs, crashes, and security issues
- Money needs exact decimals, never floating-point
- Large counts need large containers to avoid overflow
- Decimal comparison needs tolerance, not direct equality
- Null and empty are different—handle both

---

## What's Next

**Chapter 3: Making Decisions & Repeating Things**

You know what data you're working with. Now: how does your code make decisions about that data? How does it process collections of data?

These two concepts—decisions and repetition—are the foundation of all program logic.

---

*"The type system isn't there to annoy you. It's there to catch mistakes before your users do."*
