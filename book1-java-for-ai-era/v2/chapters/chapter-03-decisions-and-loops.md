# Chapter 3: Making Decisions & Repeating Things

> "Every program, no matter how complex, does exactly two things: makes decisions and repeats actions. Master these concepts, and you understand all code."

---

## The Two Superpowers

Strip away all the fancy frameworks, libraries, and patterns. Every program does two things:

1. **Makes decisions** - "If this, do that"
2. **Repeats actions** - "Do this until done"

That's it. Everything else is built on these two ideas.

AI can write the syntax. But YOU need to understand:
- What decisions need to be made
- What should happen in each case
- What needs to repeat
- When repetition should stop
- What could go wrong

---

## Part 1: Making Decisions

### The Concept

Every decision in code follows this pattern:

```
Is something true?
├── YES → Do this
└── NO  → Do that (or nothing)
```

That's all branching logic is. Everything else is just combinations of this.

### Real-World Thinking

**Login system:**
```
Does password match?
├── YES → Let them in
└── NO  → Show error
         └── Too many failures?
             ├── YES → Lock account
             └── NO  → Let them try again
```

**E-commerce checkout:**
```
Is cart empty?
├── YES → Show "cart is empty" message
└── NO  → Is user logged in?
          ├── YES → Show payment options
          └── NO  → Is guest checkout allowed?
                    ├── YES → Show guest form
                    └── NO  → Redirect to login
```

You don't need to know syntax. You need to know:
- What are ALL the possible situations?
- What should happen in EACH situation?
- Did I miss any situations?

### The Questions to Ask

Before writing (or accepting) any decision logic:

1. **What are all the possible cases?**
   - Not just success and failure
   - What about "unknown" or "pending"?
   - What about invalid input?

2. **Is there a default case?**
   - What happens if none of the conditions match?
   - Is that situation even possible?

3. **Are conditions mutually exclusive?**
   - Can multiple conditions be true at once?
   - Does the order matter?

4. **What are the edge cases?**
   - Empty input?
   - Null values?
   - Boundary values (0, -1, max)?

---

### Decision Disaster: The Missing Case

**The scenario:**
Subscription system. Three tiers: Free, Pro, Enterprise.

**The AI code logic:**
```
If tier is "Free" → Show basic features
If tier is "Pro" → Show pro features
If tier is "Enterprise" → Show all features
```

**The disaster:**
New "Trial" tier added. Code doesn't handle it. Trial users see nothing—completely blank screen.

**What was needed:**
A default case: "If tier is anything else → Show basic features (or error)"

---

### Decision Disaster: The Order Problem

**The scenario:**
Discount calculation. Multiple discounts can apply.

**The AI code logic:**
```
If order > $100 → Apply 10% discount
If user is VIP → Apply 20% discount
```

**The question:**
VIP user orders $150. Do they get 10%, 20%, or 30%?

**The disaster:**
Nobody knew. Different parts of the code assumed different answers. Some users got 30% (both discounts). Some got 20% (VIP only). Angry customers. Refund nightmare.

**What was needed:**
Clear understanding of: Are these additive? Does one override the other? What's the business rule?

---

### What to Tell AI

**Bad:** "Add logic for user permissions"

**Good:** "Add permission checking with these rules:
- Admins can do everything
- Editors can create and edit, but not delete
- Viewers can only read
- If role is unknown, deny access and log warning"

**Bad:** "Handle the payment status"

**Good:** "Handle payment status:
- 'completed' → Show success, send receipt
- 'pending' → Show waiting message, check again in 30 seconds
- 'failed' → Show error with reason, offer retry
- 'refunded' → Show refund confirmation
- Any other status → Log error, show generic message, alert support"

---

## Part 2: Repeating Things

### The Concept

Repetition means: do something multiple times until a condition is met.

```
Keep doing something
    └── Until → Condition is met
```

Every loop, no matter how it's written, has:
1. **An action** - What happens each time
2. **A continuation condition** - Why keep going
3. **A termination condition** - What makes it stop

### Real-World Thinking

**Processing orders:**
```
For each order in today's orders:
    Calculate tax
    Update inventory
    Send confirmation
    Move to next order
(Stop when: all orders processed)
```

**Waiting for response:**
```
Send request
While no response received:
    Wait a moment
    Check for response
    If timeout reached: give up
(Stop when: response received OR timeout)
```

**Searching for something:**
```
Look through items one by one
    If this item matches: stop, found it!
    If not: keep looking
(Stop when: found it OR checked everything)
```

### The Critical Questions

Before writing (or accepting) any loop:

1. **What makes this stop?**
   - Is there a guaranteed end?
   - What if the end condition is never met?

2. **What if there's nothing to process?**
   - Empty list?
   - Zero items?

3. **What if there's one item?**
   - Does the logic still work?

4. **What if there are millions of items?**
   - Will this take forever?
   - Will it use too much memory?

5. **Am I changing what I'm looping over?**
   - Adding to a list while reading it?
   - Removing items while iterating?

---

### Loop Disaster: The Infinite Email

**The scenario:**
Notification system. Email users about unread messages.

**The AI code logic:**
```
For each unread notification:
    Send email
    Log "email sent" activity
```

**The disaster:**
The "email sent" activity created a new notification. Which triggered another email. Which created another notification. 10,000 emails in 20 minutes.

**What was needed:**
Asking: "Does my action inside the loop affect what the loop is processing?"

---

### Loop Disaster: The Off-By-One

**The scenario:**
Export 100 items to a report.

**The AI code logic:**
```
For position from 0 to 100:
    Add item at position to report
```

**The disaster:**
Report had 101 items. The last one was garbage data from beyond the array.

**Why:**
0 to 100 inclusive is 101 items (0,1,2,...,99,100). Should have been 0 to 99.

**What was needed:**
Asking: "Am I processing the right number of items? Let me count: 0 to 99 is 100 items. 0 to 100 is 101 items."

---

### Loop Disaster: The Memory Explosion

**The scenario:**
Process all users and generate a report.

**The AI code logic:**
```
Load all users into memory
For each user:
    Add their data to report
```

**The disaster:**
10 million users. Each user object: 1KB. Total: 10GB. Server crashed.

**What was needed:**
Asking: "How many items could there be? Can I process them in batches instead of all at once?"

---

### Loop Disaster: The Silent Skip

**The scenario:**
Validate all items in a shopping cart.

**The AI code logic:**
```
For each item in cart:
    If item is out of stock:
        Skip to next item
    Process item
```

**The disaster:**
Out-of-stock items were silently skipped. User thought they ordered 5 items, only got 3. No error, no warning.

**What was needed:**
Asking: "What should happen when we skip something? Should the user know? Should we track it?"

---

### What to Tell AI

**Bad:** "Loop through the users"

**Good:** "Process users in batches of 1000 to avoid memory issues. For each user:
- Validate their data
- If invalid, log the error and skip (don't stop everything)
- Track how many succeeded vs failed
- Report summary at the end"

**Bad:** "Keep trying until it works"

**Good:** "Retry the API call with these rules:
- Maximum 3 attempts
- Wait 1 second between attempts (exponential backoff)
- If all attempts fail, throw an error with details
- Log each retry attempt"

**Bad:** "Process the queue"

**Good:** "Process messages from the queue:
- Take one message at a time
- Process it completely before taking the next
- If processing fails, put it back for retry (max 3 times)
- If max retries exceeded, move to dead letter queue
- Stop processing if shutdown signal received"

---

## Combining Decisions and Loops

Real code combines both. A loop with decisions inside. Decisions that start loops.

**Real pattern: Search with criteria**
```
For each product in catalog:
    Does it match search term?
    ├── YES → Is it in stock?
    │         ├── YES → Add to results
    │         └── NO  → Skip (or add with "out of stock" label?)
    └── NO  → Skip
(Stop when: checked all products)
(But also: stop early if we have enough results?)
```

**Real pattern: Retry with conditions**
```
Attempt the operation
Did it succeed?
├── YES → Done!
└── NO  → What kind of failure?
          ├── Temporary → Should we retry?
          │              ├── Under retry limit → Wait, try again
          │              └── At retry limit → Give up, report error
          └── Permanent → Don't retry, report error immediately
```

---

## Red Flags in AI Loop Code

**🚩 No visible termination condition**
```
while (processing) {
    // What sets processing to false??
```

**🚩 Modifying collection while iterating**
```
for (item in items) {
    if (condition) {
        items.remove(item);  // Danger!
    }
}
```

**🚩 Loading everything into memory**
```
allRecords = database.getAll();  // How many is "all"??
for (record in allRecords) { ... }
```

**🚩 No handling of empty case**
```
// What if items is empty?
for (item in items) {
    firstItem = items[0];  // Crash if empty!
}
```

**🚩 Magic numbers in conditions**
```
while (retries < 5) {  // Why 5? What happens after 5?
```

---

## Quick Mental Checklist

### For Decisions:
- [ ] Have I covered all possible cases?
- [ ] Is there a default/else case?
- [ ] What happens with unexpected input?
- [ ] Does the order of conditions matter?

### For Loops:
- [ ] What guarantees this will stop?
- [ ] What if the collection is empty?
- [ ] What if there are millions of items?
- [ ] Am I modifying what I'm iterating over?
- [ ] What happens if something fails mid-loop?

---

## Key Takeaways

- All code logic boils down to decisions (if/then) and repetition (loops)
- Decisions need: all cases covered, default handling, edge cases considered
- Loops need: clear termination, empty handling, scale consideration
- The syntax doesn't matter—AI handles that
- The LOGIC matters—you handle that
- Always ask: "What makes this stop? What did I miss?"

---

## What's Next

**Chapter 4: Organizing Code**

You understand data types. You understand decisions and loops. Now: how do you organize code so it doesn't become an unmaintainable mess?

This is where objects, classes, and structure come in—not as syntax to memorize, but as concepts that make code manageable.

---

*"A loop without a clear termination condition is a bug waiting for the right moment to ruin your day."*
