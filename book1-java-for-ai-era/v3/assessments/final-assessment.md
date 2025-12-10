# Final Assessment

**50 Questions | All Chapters | Test Your Java + AI Readiness**

---

## Section 1: Types & Data (Q1-8)

**Q1:** AI generated `double total = price * quantity;` for an invoice calculation. What's the issue?
- a) Should use float
- b) Should use BigDecimal for money
- c) Should use int
- d) No issue

<details><summary>Answer</summary>

**b)** Floating-point has precision errors. Money must use BigDecimal.
</details>

---

**Q2:** What's the output of `System.out.println(0.1 + 0.2 == 0.3);`?
- a) true
- b) false
- c) Compilation error
- d) Runtime exception

<details><summary>Answer</summary>

**b)** Floating-point math: 0.1 + 0.2 = 0.30000000000000004
</details>

---

**Q3:** AI used `int` for YouTube view counter. Video gets 3 billion views. What happens?
- a) Shows 3,000,000,000
- b) Throws overflow exception
- c) Shows large negative number
- d) Shows 0

<details><summary>Answer</summary>

**c)** Integer overflow wraps around to negative. Use `long` for large counts.
</details>

---

**Q4:** What's wrong with `if (name == "admin")`?
- a) Syntax error
- b) Compares object references, not content
- c) name can't equal string
- d) Nothing wrong

<details><summary>Answer</summary>

**b)** Use `.equals()` for String content comparison.
</details>

---

**Q5:** What happens: `Integer x = null; int y = x;`?
- a) y becomes 0
- b) y becomes null
- c) NullPointerException
- d) Compilation error

<details><summary>Answer</summary>

**c)** Unboxing null throws NPE.
</details>

---

**Q6:** Which is null-safe for String comparison?
- a) `str.equals("value")`
- b) `"value".equals(str)`
- c) `str == "value"`
- d) `str.compareTo("value")`

<details><summary>Answer</summary>

**b)** Putting literal first handles null str without NPE.
</details>

---

**Q7:** What does `String` immutability mean?
- a) Strings can't be created
- b) String content can't be changed after creation
- c) Strings use less memory
- d) Strings are thread-unsafe

<details><summary>Answer</summary>

**b)** Every "modification" creates a new String object.
</details>

---

**Q8:** AI wrote `double percentage = 7 / 10;`. Result?
- a) 0.7
- b) 0.0
- c) 1.0
- d) Compilation error

<details><summary>Answer</summary>

**b)** Integer division (7/10=0) then converted to double (0.0).
</details>

---

## Section 2: Control Flow (Q9-14)

**Q9:** What does `users.removeIf(User::isInactive)` do?
- a) Throws exception
- b) Removes inactive users safely without ConcurrentModificationException
- c) Returns inactive users
- d) Counts inactive users

<details><summary>Answer</summary>

**b)** Safe way to remove elements during iteration.
</details>

---

**Q10:** When should you use traditional loop over stream?
- a) Always
- b) When you need break/early return or complex error handling
- c) Never
- d) When filtering

<details><summary>Answer</summary>

**b)** Streams don't support break. Use loops for complex control flow.
</details>

---

**Q11:** What's wrong with `for(Item i : items) { items.remove(i); }`?
- a) Syntax error
- b) ConcurrentModificationException
- c) NullPointerException
- d) Nothing wrong

<details><summary>Answer</summary>

**b)** Can't modify collection during foreach.
</details>

---

**Q12:** Modern Java 14+ switch syntax uses:
- a) `case X: break;`
- b) `case X -> result;`
- c) `when X: result;`
- d) `match X => result`

<details><summary>Answer</summary>

**b)** Arrow syntax, no break needed.
</details>

---

**Q13:** `users.stream().anyMatch(User::isAdmin)` returns:
- a) List of admins
- b) First admin
- c) true/false if any admin exists
- d) Count of admins

<details><summary>Answer</summary>

**c)** Boolean - whether any element matches.
</details>

---

**Q14:** Why does this loop never end?
```java
int i = 0;
while (i < 5) {
    if (i == 2) continue;
    i++;
}
```
- a) Wrong condition
- b) `continue` skips `i++` when i=2
- c) While loops can't use continue
- d) It does end

<details><summary>Answer</summary>

**b)** When i=2, continue skips i++, so i stays 2 forever.
</details>

---

## Section 3: OOP (Q15-26)

**Q15:** AI created User class with id, createdAt. You have BaseEntity with these. Action?
- a) Keep both
- b) Make User extend BaseEntity
- c) Delete BaseEntity
- d) Use interface instead

<details><summary>Answer</summary>

**b)** Don't duplicate. Extend existing base class.
</details>

---

**Q16:** What does `this` keyword refer to?
- a) The class
- b) The current object instance
- c) The parent class
- d) Static context

<details><summary>Answer</summary>

**b)** Reference to the specific object.
</details>

---

**Q17:** When should you use `static`?
- a) For all methods
- b) For utility methods and constants shared across instances
- c) For instance data
- d) Never

<details><summary>Answer</summary>

**b)** Static is for class-level shared things, not instance data.
</details>

---

**Q18:** Why does `static` method can't access instance field?
- a) Performance
- b) Static belongs to class, has no `this` reference
- c) Syntax rule
- d) It can access instance fields

<details><summary>Answer</summary>

**b)** No object context in static methods.
</details>

---

**Q19:** `public class Stack extends ArrayList` - what's wrong?
- a) Nothing
- b) Stack isn't an ArrayList - should use composition (HAS-A not IS-A)
- c) ArrayList is deprecated
- d) Missing constructor

<details><summary>Answer</summary>

**b)** Wrong relationship. Stack should contain a List, not be one.
</details>

---

**Q20:** AI wrote `if (emp instanceof Manager) {...} else if (emp instanceof Engineer)...`. Better approach?
- a) Add more instanceof
- b) Use polymorphism - override method in each subclass
- c) Use switch
- d) Keep as-is

<details><summary>Answer</summary>

**b)** Polymorphism eliminates instanceof chains.
</details>

---

**Q21:** What does encapsulation prevent?
- a) Inheritance
- b) Direct access to internal state, enabling validation
- c) Method calls
- d) Object creation

<details><summary>Answer</summary>

**b)** Private fields with controlled access methods.
</details>

---

**Q22:** AI hardcoded `new StripeProcessor()`. Problem?
- a) Stripe is bad
- b) Can't switch implementations or mock for tests
- c) Memory issue
- d) No problem

<details><summary>Answer</summary>

**b)** Tight coupling. Should inject interface.
</details>

---

**Q23:** When to use abstract class vs interface?
- a) Always abstract class
- b) Abstract for partial implementation with state; interface for pure contract
- c) Always interface
- d) They're the same

<details><summary>Answer</summary>

**b)** Abstract classes have state and partial implementation.
</details>

---

**Q24:** `@Override` annotation purpose?
- a) Makes method faster
- b) Compiler verifies method actually overrides parent
- c) Makes method public
- d) Optional decoration

<details><summary>Answer</summary>

**b)** Catches typos and signature mismatches.
</details>

---

**Q25:** Records in Java 14+ provide:
- a) Database access
- b) Immutable data class with auto-generated constructor, getters, equals, hashCode
- c) Logging
- d) Threading

<details><summary>Answer</summary>

**b)** Compact syntax for simple data holders.
</details>

---

**Q26:** Return `Collections.unmodifiableList(items)` instead of `items` because:
- a) Performance
- b) Prevents callers from modifying internal list
- c) Memory savings
- d) Style preference

<details><summary>Answer</summary>

**b)** Protects encapsulation.
</details>

---

## Section 4: Error Handling (Q27-32)

**Q27:** What's wrong with `catch (Exception e) {}`?
- a) Syntax error
- b) Silently swallows all errors including bugs
- c) Too specific
- d) Nothing wrong

<details><summary>Answer</summary>

**b)** Empty catch hides all problems. Always do something.
</details>

---

**Q28:** try-with-resources guarantees:
- a) No exceptions
- b) Resources closed even if exception occurs
- c) Faster execution
- d) Automatic retry

<details><summary>Answer</summary>

**b)** Automatic cleanup, no leaks.
</details>

---

**Q29:** How to preserve original exception when wrapping?
- a) `throw new MyException(e.getMessage())`
- b) `throw new MyException("msg", e)`
- c) `throw e`
- d) `throw new MyException()`

<details><summary>Answer</summary>

**b)** Pass exception as cause parameter.
</details>

---

**Q30:** Checked vs unchecked exceptions: when use checked?
- a) Always
- b) For recoverable conditions caller should handle
- c) For programming bugs
- d) Never

<details><summary>Answer</summary>

**b)** Checked = recoverable (IOException). Unchecked = bugs (NPE).
</details>

---

**Q31:** Should you catch `OutOfMemoryError`?
- a) Yes, always
- b) No, Errors indicate JVM problems beyond recovery
- c) Only in production
- d) Only with logging

<details><summary>Answer</summary>

**b)** Errors are unrecoverable JVM issues.
</details>

---

**Q32:** AI code: `BufferedReader r = new BufferedReader(...); String s = r.readLine(); r.close();`. Issue?
- a) Nothing
- b) If readLine() throws, close() never called - resource leak
- c) Wrong order
- d) Missing try

<details><summary>Answer</summary>

**b)** Use try-with-resources for guaranteed cleanup.
</details>

---

## Section 5: Collections (Q33-40)

**Q33:** ArrayList.contains() is O(n). For frequent lookups use:
- a) LinkedList
- b) HashSet
- c) TreeSet
- d) Vector

<details><summary>Answer</summary>

**b)** HashSet provides O(1) contains().
</details>

---

**Q34:** HashMap vs TreeMap - when TreeMap?
- a) Always
- b) When you need keys in sorted order
- c) For faster access
- d) Never

<details><summary>Answer</summary>

**b)** TreeMap keeps keys sorted. HashMap is faster but unordered.
</details>

---

**Q35:** For thread-safe map with high concurrency:
- a) HashMap
- b) Hashtable
- c) ConcurrentHashMap
- d) TreeMap

<details><summary>Answer</summary>

**c)** ConcurrentHashMap uses segment locking for better concurrency.
</details>

---

**Q36:** What must a class implement to work in HashSet?
- a) Comparable
- b) equals() and hashCode()
- c) Serializable
- d) Cloneable

<details><summary>Answer</summary>

**b)** HashSet uses both for storage and lookup.
</details>

---

**Q37:** Using mutable object as HashMap key causes:
- a) Performance issue
- b) Object unfindable after mutation (hash changes)
- c) Memory leak
- d) No issue

<details><summary>Answer</summary>

**b)** Hash changes, object stays in old bucket.
</details>

---

**Q38:** LinkedHashMap vs HashMap:
- a) LinkedHashMap is faster
- b) LinkedHashMap maintains insertion order
- c) HashMap maintains insertion order
- d) No difference

<details><summary>Answer</summary>

**b)** LinkedHashMap preserves insertion order.
</details>

---

**Q39:** `list.remove(0)` in while loop for 100K items is:
- a) O(n)
- b) O(n²) because each remove shifts all elements
- c) O(1)
- d) O(log n)

<details><summary>Answer</summary>

**b)** Each remove is O(n). Total: O(n²). Use Queue.poll().
</details>

---

**Q40:** Which maintains unique elements in insertion order?
- a) HashSet
- b) TreeSet
- c) LinkedHashSet
- d) ArrayList

<details><summary>Answer</summary>

**c)** LinkedHashSet: unique + insertion order.
</details>

---

## Section 6: Modern Java (Q41-46)

**Q41:** `optional.get()` directly is bad because:
- a) Performance
- b) Throws NoSuchElementException if empty
- c) Returns null
- d) Deprecated

<details><summary>Answer</summary>

**b)** Use orElse/orElseThrow instead.
</details>

---

**Q42:** Method reference for `u -> u.getName()`:
- a) `User.getName()`
- b) `User::getName`
- c) `getName::User`
- d) `User->getName`

<details><summary>Answer</summary>

**b)** Double colon syntax.
</details>

---

**Q43:** Stream operations are lazy until:
- a) filter() is called
- b) map() is called
- c) Terminal operation (collect, forEach) is called
- d) They're never lazy

<details><summary>Answer</summary>

**c)** Terminal operations trigger the pipeline.
</details>

---

**Q44:** Can you use a stream twice?
- a) Yes
- b) No, IllegalStateException
- c) Only with parallel()
- d) Only with sequential()

<details><summary>Answer</summary>

**b)** Streams are single-use. Collect if you need to reprocess.
</details>

---

**Q45:** Optional should NOT be used as:
- a) Return type
- b) Class field
- c) In map() operation
- d) With orElse()

<details><summary>Answer</summary>

**b)** Optional is for return values, not field storage.
</details>

---

**Q46:** `flatMap` vs `map`:
- a) flatMap is faster
- b) flatMap transforms and flattens nested structures
- c) map flattens
- d) No difference

<details><summary>Answer</summary>

**b)** flatMap unwraps nested Optional/Stream.
</details>

---

## Section 7: AI Integration (Q47-50)

**Q47:** Good AI prompt includes:
- a) Just "fix it"
- b) Context (existing code), requirements, constraints
- c) Only the error message
- d) Just the class name

<details><summary>Answer</summary>

**b)** CONTEXT-REQUIREMENTS-CONSTRAINTS model.
</details>

---

**Q48:** When reviewing AI code, first check:
- a) Performance
- b) Does it compile, any obvious red flags
- c) Documentation
- d) Test coverage

<details><summary>Answer</summary>

**b)** Quick scan before deep review.
</details>

---

**Q49:** AI creates classes without knowing your codebase. Main risk:
- a) Syntax errors
- b) Duplicating existing classes/patterns
- c) Using wrong language
- d) No risk

<details><summary>Answer</summary>

**b)** Always provide context about existing code.
</details>

---

**Q50:** "AI is a multiplier" means:
- a) AI writes more code
- b) AI amplifies your understanding - good or bad
- c) AI needs multiple prompts
- d) AI costs multiply

<details><summary>Answer</summary>

**b)** Strong understanding + AI = great code. Weak understanding + AI = fast bugs.
</details>

---

## Scoring

- **45-50:** Excellent - Ready to lead AI-assisted development
- **38-44:** Good - Solid foundation, review weak areas
- **30-37:** Fair - Revisit chapters where you struggled
- **Below 30:** Review all chapters before relying on AI

---

*Completing this assessment means you have the knowledge to work safely with AI. Now apply it.*
