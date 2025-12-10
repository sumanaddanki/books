# Chapter 5: When Things Go Wrong

> "The question isn't whether your code will fail. It's whether you'll know about it when it does."

---

## The Uncomfortable Truth

Your code WILL encounter problems:
- Networks time out
- Files don't exist
- Users enter garbage
- Databases go down
- APIs return errors
- Memory runs out
- Services restart

The difference between amateur and professional code isn't whether it fails—it's how it handles failure.

---

## The Two Types of Problems

### Expected Problems (Handle Gracefully)

Things that might reasonably happen in normal operation:
- User enters invalid email format
- File they're looking for doesn't exist
- Network request times out
- Payment is declined

**Response:** Handle it. Give useful feedback. Let the user try again.

### Unexpected Problems (Fail Loudly)

Things that shouldn't happen and indicate something is really wrong:
- Out of memory
- Configuration is missing
- Database schema doesn't match code
- Critical service is completely down

**Response:** Fail clearly. Log everything. Alert someone. Don't pretend things are fine.

---

## The Disaster Patterns

### Disaster 1: The Silent Failure

**The scenario:**
Notification system sends emails to users.

**The AI code pattern:**
```
try:
    send_email(user)
except:
    pass  # Ignore errors
```

**What happened:**
Email service was down for 2 days. No emails sent. No errors logged. No alerts. Users complained: "I never got my order confirmation."

Support: "System shows it was sent successfully."

It wasn't. The error was swallowed. Nobody knew.

**The lesson:**
NEVER ignore errors silently. At minimum, log them.

---

### Disaster 2: The Swallowed Exception

**The scenario:**
Payment processing system.

**The AI code pattern:**
```
try:
    charge_customer()
    fulfill_order()
except:
    log("Something went wrong")
```

**What happened:**
Customer was charged but order wasn't fulfilled. Error was logged but nobody monitored the logs. Customer waited 3 weeks for order that was never processed.

**The lesson:**
Logging isn't handling. If money is involved, you need real error handling and alerts.

---

### Disaster 3: The Crash Loop

**The scenario:**
Background job processes items from a queue.

**The AI code pattern:**
```
while true:
    item = get_next_item()
    process(item)
```

**What happened:**
One malformed item caused `process()` to crash. Job restarted. Picked up same item. Crashed again. Restart. Crash. Restart. Crash.

10,000 restarts in one hour. Logs flooded. Alerts flooded. Real problems got buried.

**The lesson:**
Handle bad data gracefully. After repeated failures, skip the item or move it aside.

---

### Disaster 4: The Overly Broad Catch

**The scenario:**
User registration system.

**The AI code pattern:**
```
try:
    user = create_user(data)
    return user
except Exception:
    return "Registration failed"
```

**What happened:**
A programming bug caused `create_user` to crash due to a typo in the code. But the exception was caught. User saw "Registration failed."

Nobody knew the real cause. Bug lived in production for weeks. Thousands of users couldn't register.

**The lesson:**
Catching too broadly hides real bugs. Only catch what you can actually handle.

---

### Disaster 5: The Null Surprise

**The scenario:**
User profile display.

**The AI code pattern:**
```
display(user.getProfile().getAvatar().getUrl())
```

**What happened:**
New users had no profile. Crashed with NullPointerException.
Users with profile but no avatar. Crashed.
Users with avatar but no URL. Crashed.

**The lesson:**
Every dot is a potential null. Handle the cases where things don't exist.

---

## The Mental Model: Error Handling Flow

```
Something risky happens
        │
        ▼
    Did it work?
        │
    ┌───┴───┐
    ▼       ▼
   YES      NO
    │       │
    │   What went wrong?
    │       │
    │   ┌───┴───────────┐
    │   ▼               ▼
    │ Expected?      Unexpected?
    │   │               │
    │   ▼               ▼
    │ Handle it:     Fail clearly:
    │ - Log it       - Log everything
    │ - Maybe retry  - Alert someone
    │ - User message - Don't hide it
    │ - Continue     - Maybe stop
    │       │               │
    ▼       ▼               ▼
        Continue or Stop
```

---

## The Key Questions

Before writing (or accepting) any code that could fail:

### 1. "What could go wrong here?"

List every failure mode:
- Network fails
- Resource not found
- Permission denied
- Invalid data
- Service unavailable
- Timeout
- Rate limited

### 2. "If it fails, will I know about it?"

- Is there logging?
- Is there alerting for critical failures?
- Will I find out before users complain?

### 3. "Can the user recover?"

- Can they retry?
- Do they see a useful message?
- Is there an alternative path?

### 4. "Am I catching only what I can handle?"

- Don't catch everything
- Let unexpected errors bubble up
- Programming bugs should crash, not be caught

### 5. "What about cleanup?"

- Are files closed?
- Are connections released?
- Are transactions rolled back?
- Even when errors happen?

---

## Error Handling Patterns

### Pattern 1: Validate Early, Fail Fast

**Concept:** Check for problems at the start, before doing any work.

```
Process order:
    If order is null → Error: "No order provided"
    If order.items is empty → Error: "Order has no items"
    If order.total < 0 → Error: "Invalid order total"

    // Now we know the data is valid
    Actually process the order...
```

**Why it works:** You catch bad data before it causes confusing errors deep in your logic.

---

### Pattern 2: The Retry with Backoff

**Concept:** For temporary failures, try again with increasing delays.

```
Attempt 1 → Failed → Wait 1 second
Attempt 2 → Failed → Wait 2 seconds
Attempt 3 → Failed → Wait 4 seconds
Attempt 4 → Failed → Give up, report error
```

**Why it works:** Many failures are temporary (network blip, service restart). Retrying often succeeds. Increasing delays prevent overwhelming the system.

---

### Pattern 3: The Circuit Breaker

**Concept:** If something keeps failing, stop trying for a while.

```
Track failures
If failures > threshold in time window:
    Stop trying
    Return fallback or error immediately
    After cooldown period → Try again
```

**Why it works:** Prevents cascade failures. If a service is down, don't keep hammering it (and waiting for timeouts).

---

### Pattern 4: Fallback Behavior

**Concept:** If the preferred path fails, have a backup.

```
Try to get user's personalized recommendations:
    If fails → Return popular items instead

Try to send email notification:
    If fails → Queue for later retry

Try to load high-res image:
    If fails → Load low-res version
```

**Why it works:** Some functionality is better than no functionality. Degraded experience beats complete failure.

---

### Pattern 5: Transaction / All-or-Nothing

**Concept:** When multiple steps must all succeed, make sure partial failure doesn't leave things in a broken state.

```
Start transaction
    Charge customer
    Create order
    Update inventory
    Send confirmation
If all succeeded → Commit
If any failed → Rollback everything
```

**Why it works:** You don't end up with charged customers but no order, or orders but no inventory update.

---

## What to Tell AI

**Bad:** "Add error handling"

**Good:** "Handle errors for this API call:
- If 404: Return empty result (item not found is OK)
- If 429: Wait and retry up to 3 times
- If 500: Log the error, retry once, then fail
- If network timeout: Retry up to 3 times with 1-second delays
- For any other error: Log full details and re-throw"

**Bad:** "Make sure it doesn't crash"

**Good:** "Validate the input at the start:
- email must be non-null and match email format
- age must be between 0 and 150
- Return specific error messages for each validation failure
- Don't proceed if validation fails"

**Bad:** "Handle the database connection"

**Good:** "Ensure the database connection is closed even if an error occurs. Use try-finally or try-with-resources pattern."

---

## Red Flags in AI Error Handling

**🚩 Empty catch blocks**
```
try { ... } catch (Exception e) { }
```
Errors vanish. You'll never know when things fail.

**🚩 Catching Exception (too broad)**
```
catch (Exception e) { return null; }
```
Catches bugs you should see. Hides real problems.

**🚩 No logging in catch**
```
catch (Exception e) { return defaultValue; }
```
Something failed. Nobody will know what.

**🚩 Swallowing and continuing**
```
catch (Exception e) { log.warn("Error"); }
// Continues anyway
```
State might be corrupted. Continuing might make it worse.

**🚩 Generic error messages**
```
catch (Exception e) { throw new Error("Something went wrong"); }
```
Lost all details. Debugging will be nightmare.

---

## Quick Mental Checklist

- [ ] What could fail here?
- [ ] Am I logging enough to debug later?
- [ ] Am I catching only what I can actually handle?
- [ ] Is the user message helpful (not just "Error")?
- [ ] Are resources cleaned up even on failure?
- [ ] Will I find out about failures before users complain?
- [ ] Does partial failure leave things in a broken state?

---

## Key Takeaways

- Things WILL fail—the question is how you handle it
- Silent failures are worse than crashes (at least crashes are obvious)
- Only catch exceptions you can actually handle
- Log enough context to debug later
- Clean up resources even when errors occur
- Validate early, fail fast with clear messages
- Consider retry logic for temporary failures
- Don't hide programming bugs by catching too broadly

---

## What's Next

**Chapter 6: Working with Collections**

You have one user. But what about 1,000 users? A million? How do you store them? Search them? Process them?

The choice of collection (list, set, map) has huge implications for your code's correctness and performance.

---

*"The goal isn't to prevent all errors. It's to detect them quickly, handle them gracefully, and fix them easily."*
