# Chapter 6: Working with Collections

> "The difference between a slow app and a fast app often comes down to one decision: which collection did you use?"

---

## The Core Problem

You have multiple things. Users. Products. Messages. Orders.

How do you store them?

This simple decision affects:
- How fast you can find things
- How fast you can add/remove things
- Whether duplicates are allowed
- Whether order is preserved
- How much memory you use

---

## The Three Mental Models

There are only three types of collections you need to understand:

### 1. List - Ordered Sequence

**Mental model:** A playlist.

- Songs in order
- Same song can appear twice
- You can access by position: "What's song #5?"
- Order matters: "What's next?"

**Use when:**
- Order matters
- Duplicates are fine
- You access by position
- You process things in sequence

**Real examples:**
- Shopping cart items (order of adding)
- Steps in a process (must be in order)
- Search results (ranked by relevance)
- Transaction history (chronological)

---

### 2. Set - Unique Items Only

**Mental model:** A guest list.

- Each name appears once
- Can't have duplicate guests
- "Is John invited?" → Fast to check
- No particular order (usually)

**Use when:**
- No duplicates allowed
- You need to check "is this in there?"
- Order doesn't matter
- Uniqueness is the point

**Real examples:**
- Email subscribers (each email once)
- Tags on a post (no duplicate tags)
- Unique visitors to a page
- Permissions a user has

---

### 3. Map - Key-Value Pairs

**Mental model:** A phone book.

- Name → Phone Number
- Look up by name, get the number
- Each name appears once (keys are unique)
- Fast lookup: "What's John's number?"

**Use when:**
- You need to look things up by a key
- You're associating one thing with another
- Fast lookup is important

**Real examples:**
- User ID → User object
- Product code → Product details
- Word → Definition
- Configuration name → Value

---

## The Decision Flowchart

```
What do you need?
        │
        ▼
Do you look things up by a key?
        │
    ┌───┴───┐
    ▼       ▼
   YES      NO
    │       │
   MAP      Are duplicates allowed?
            │
        ┌───┴───┐
        ▼       ▼
       YES      NO
        │       │
      LIST     SET
```

That's it. Three choices.

---

## Real Disasters: Collection Mistakes

### Disaster 1: The Slow Search

**The scenario:**
Social media app. Check if username is taken during signup.

**The AI approach:**
Stored all usernames in a List. To check if taken:
```
For each username in all_usernames:
    If username matches → It's taken
```

**With 100 users:** Instant.
**With 100,000 users:** Takes 2 seconds per signup.
**With 10,000,000 users:** Takes forever.

**The fix:**
Use a Set. Checking "is this in the set?" is instant, regardless of size.

**The lesson:**
Lists are slow for "is this in there?" checks. Sets are built for exactly that.

---

### Disaster 2: The Duplicate Orders

**The scenario:**
E-commerce system. Process incoming orders.

**The AI approach:**
Store processed order IDs in a List. Before processing:
```
If order_id in processed_list:
    Skip (already processed)
Else:
    Process order
    Add order_id to processed_list
```

**The disaster:**
Retry logic sent the same order twice quickly. The "is it in the list?" check was slow. Both requests passed the check before either finished processing. Customer charged twice.

**The fix:**
Use a Set. The check-and-add is atomic and fast.

**The lesson:**
When preventing duplicates matters, use a Set.

---

### Disaster 3: The N+1 Query

**The scenario:**
Display users with their department names.

**The AI approach:**
```
users = get_all_users()           // 1 query
For each user:
    dept = get_department(user.dept_id)  // 1 query each!
    display(user, dept)
```

**With 1,000 users:** 1,001 database queries. Slow.

**The fix:**
Load all needed departments into a Map first:
```
users = get_all_users()                      // 1 query
depts = get_departments_by_ids(all_dept_ids) // 1 query, into Map
For each user:
    dept = depts[user.dept_id]               // Instant lookup
    display(user, dept)
```

**Total:** 2 queries instead of 1,001.

**The lesson:**
When you'll look up the same things repeatedly, load them into a Map first.

---

### Disaster 4: The Lost Order

**The scenario:**
Display items in the order user added them.

**The AI approach:**
Used a Set for "unique items."

**The disaster:**
Sets don't preserve order (usually). Items displayed in random order. Users confused: "I added the shirt first, why is it showing last?"

**The fix:**
Use a List (if duplicates OK) or a special ordered Set (if uniqueness needed).

**The lesson:**
If order matters, make sure your collection preserves it.

---

### Disaster 5: The Memory Explosion

**The scenario:**
Cache API responses to avoid repeated calls.

**The AI approach:**
```
cache = Map()
For each request:
    If not in cache:
        response = call_api()
        cache[request] = response
    Return cache[request]
```

**The disaster:**
Cache grew forever. After a week: 10GB of memory. Server crashed.

**The fix:**
Limit cache size. Remove old entries. Use proper caching strategy (LRU, TTL).

**The lesson:**
Collections can grow without bound. Plan for limits.

---

## Choosing the Right Collection: The Questions

### Question 1: "How will I find things?"

```
By position (1st, 2nd, 3rd...)      → List
By a key (user ID, username...)     → Map
Just "is it in there?"              → Set
```

### Question 2: "Are duplicates allowed?"

```
Yes, same item can appear multiple times → List
No, each item only once                  → Set or Map
```

### Question 3: "Does order matter?"

```
Yes, must preserve insertion order   → List (or ordered variants)
Yes, must be sorted                  → Sorted variants
No, order doesn't matter             → Set or Map (faster)
```

### Question 4: "How big will this get?"

```
Small (under 100)     → Almost anything works
Medium (100-10,000)   → Right choice matters for speed
Large (10,000+)       → Wrong choice = disaster
```

### Question 5: "More reads or writes?"

```
Mostly reading/searching  → Optimize for lookup (Set/Map)
Mostly adding/removing    → Depends on where (List ends: fast, middle: slow)
Balanced mix              → Consider your most frequent operation
```

---

## What to Tell AI

**Bad:** "Store the users"

**Good:** "Store users where I can look them up by user ID quickly. There will be about 100,000 users."

**Bad:** "Track which items have been processed"

**Good:** "Track processed item IDs to avoid duplicates. Must be fast to check 'was this processed?' even with millions of IDs."

**Bad:** "Keep a list of tags"

**Good:** "Store tags for a post. No duplicate tags allowed. Order doesn't matter. Need to quickly check if a tag is already added."

**Bad:** "Cache the results"

**Good:** "Cache API responses by request URL. Limit to 1000 entries. When full, remove least recently used."

---

## Red Flags in AI Collection Code

**🚩 Searching in a List when Set would work**
```
For each item in list:
    If item == target: return found
```
With large lists, this is slow. Set lookup is instant.

**🚩 Nested loops over two lists**
```
For each a in list_a:
    For each b in list_b:
        If a.id == b.id: ...
```
O(n²) = slow. Convert one to a Map first.

**🚩 Using List when order doesn't matter**
If you don't care about order and need uniqueness, Set is better.

**🚩 Unbounded growth**
```
cache[key] = value  // Forever growing
```
No limit. Will eventually eat all memory.

**🚩 Wrong collection for the access pattern**
List for frequent lookups by key → Use Map.
Map for ordered processing → Use List or ordered Map.

---

## Quick Mental Checklist

- [ ] Will I look up by key? → Map
- [ ] Need to prevent duplicates? → Set
- [ ] Does order matter? → List (or ordered variant)
- [ ] Is "is this in there?" a frequent operation? → Set
- [ ] How large could this get? → Plan for scale
- [ ] Is there a limit on size? → Prevent unbounded growth

---

## Key Takeaways

- There are only three collection types to understand: List, Set, Map
- List: Ordered, duplicates OK, access by position
- Set: Unique items, fast "is it in there?" check
- Map: Key-value lookup, fast retrieval by key
- Wrong collection = slow code or bugs
- Always ask: How will I access this? How big will it get?

---

## What's Next

**Chapter 7: Prompting AI for Code**

You now understand the concepts: types, logic, structure, errors, collections. You're ready to direct AI effectively.

Let's learn how to ask AI for exactly what you need—and get better results.

---

*"Choosing the right collection isn't about knowing all the options. It's about knowing which question to ask: Do I need order? Uniqueness? Key lookup?"*
