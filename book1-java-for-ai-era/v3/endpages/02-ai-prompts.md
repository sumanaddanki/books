# Appendix B: AI Prompt Templates

## Template: New Class

```
Create [ClassName] for [framework].

Context:
- Extend: [BaseClass if any]
- Libraries: [Lombok, etc.]
- Related: [existing classes]

Requirements:
- Fields: [list with types]
- Methods: [list with purpose]
- Validation: [rules]

Constraints:
- Java version: [X]
- Annotations: [JPA, Spring, etc.]
- Pattern: [Builder, Factory, etc.]
```

## Template: Service Method

```
Create [methodName] in [ServiceClass].

Context:
- Dependencies: [repos, services]
- DTOs: [input/output types]

Requirements:
- Input: [parameters]
- Output: [return type]
- Steps: [1. 2. 3.]
- Edge cases: [null, empty, etc.]

Constraints:
- Transaction: [required/readonly]
- Exceptions: [what to throw]
```

## Template: Bug Fix

```
Fix [brief description] in [Class.method()].

Current behavior: [what happens]
Expected behavior: [what should happen]

Code:
[paste code]

Error:
[paste error/stack trace]

Constraints:
- Don't change: [API, signature]
- Must handle: [specific cases]
```

## Template: Unit Test

```
Write tests for [Class.method()].

Method signature:
[paste signature]

Scenarios:
1. [happy path]
2. [edge case]
3. [error case]

Context:
- Framework: [JUnit 5, Mockito]
- Mock: [dependencies to mock]
- Pattern: [AAA, given-when-then]
```

## Template: Code Review

```
Review this code for:
- Thread safety
- Null handling
- Resource leaks
- Performance
- Best practices

[paste code]

Context:
- Environment: [single/multi-threaded]
- Frequency: [how often called]
- Data size: [typical volume]
```

## Quick Modifiers

Add these to any prompt:

- "Use BigDecimal for money calculations"
- "Use try-with-resources for all closeable resources"
- "Return Optional instead of null"
- "Extend BaseEntity for the entity class"
- "Use constructor injection, not field injection"
- "Add null checks for all parameters"
- "Use HashSet instead of ArrayList for lookups"
- "Chain exception causes when wrapping"
