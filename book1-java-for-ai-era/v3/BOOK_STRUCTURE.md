# Book Structure & Compilation Guide

## Folder Structure

```
v3/
├── BOOK_STRUCTURE.md          # This file - compilation guide
├── OUTLINE.md                 # Chapter overview
│
├── cover/
│   ├── front-cover.md         # Title, subtitle, author
│   └── back-cover.md          # Book summary, author bio, quotes
│
├── preface/
│   ├── 01-about-this-book.md  # Who it's for, how to use
│   ├── 02-about-author.md     # Author background
│   └── 03-acknowledgments.md  # Thanks
│
├── chapters/
│   ├── chapter-01-ai-paradox.md
│   ├── chapter-02-java-types.md
│   ├── chapter-03-control-flow.md
│   ├── chapter-04-oop-foundations.md
│   ├── chapter-05-oop-pillars.md
│   ├── chapter-06-error-handling.md
│   ├── chapter-07-collections.md
│   ├── chapter-08-modern-java.md
│   ├── chapter-09-prompting-ai.md
│   └── chapter-10-reviewing-output.md
│
├── assessments/
│   └── final-assessment.md    # 50 comprehensive questions
│
└── endpages/
    ├── 01-appendix-quick-reference.md   # Cheat sheets
    ├── 02-appendix-ai-prompts.md        # Prompt templates
    ├── 03-resources.md                  # Further learning
    └── 04-index.md                      # Index (if needed)
```

---

## Compilation Order (for DOCX generation)

When combining all MD files into final book:

```
1.  cover/front-cover.md
2.  preface/01-about-this-book.md
3.  preface/02-about-author.md
4.  preface/03-acknowledgments.md
5.  [Table of Contents - auto-generated]
6.  chapters/chapter-01-ai-paradox.md
7.  chapters/chapter-02-java-types.md
8.  chapters/chapter-03-control-flow.md
9.  chapters/chapter-04-oop-foundations.md
10. chapters/chapter-05-oop-pillars.md
11. chapters/chapter-06-error-handling.md
12. chapters/chapter-07-collections.md
13. chapters/chapter-08-modern-java.md
14. chapters/chapter-09-prompting-ai.md
15. chapters/chapter-10-reviewing-output.md
16. assessments/final-assessment.md
17. endpages/01-appendix-quick-reference.md
18. endpages/02-appendix-ai-prompts.md
19. endpages/03-resources.md
20. endpages/04-index.md
21. cover/back-cover.md
```

---

## Chapter Structure (Each Chapter)

Every chapter file includes:

1. **Title & Opening Quote**
2. **Core Concept** - Java-specific explanation
3. **Real Disasters** - What goes wrong without understanding
4. **Mental Model** - How to think about it
5. **What to Tell AI** - Prompting for this topic
6. **Red Flags** - What to watch in AI output
7. **Quick Checklist** - Review framework
8. **Key Takeaways** - Summary points
9. **Quiz (10 Questions)** - Self-assessment
10. **What's Next** - Transition to next chapter

---

## Quiz Format (End of Each Chapter)

```markdown
---

## Chapter Quiz

Test your understanding with these 10 questions.

**Q1:** [Scenario or concept question]
- a) Option A
- b) Option B
- c) Option C
- d) Option D

<details>
<summary>Answer</summary>
**b) Option B** - Explanation of why this is correct.
</details>

... (10 questions total)
```

---

## Final Assessment Format

```markdown
# Final Assessment - 50 Questions

Comprehensive test covering all chapters.

## Section 1: Types & Data (Questions 1-8)
## Section 2: Control Flow (Questions 9-14)
## Section 3: OOP (Questions 15-26)
## Section 4: Error Handling (Questions 27-32)
## Section 5: Collections (Questions 33-40)
## Section 6: Modern Java (Questions 41-46)
## Section 7: AI Integration (Questions 47-50)

[Questions with answers in collapsible sections]
```

---

## Compilation Command (Example)

Using pandoc to generate DOCX:

```bash
pandoc \
  cover/front-cover.md \
  preface/*.md \
  chapters/*.md \
  assessments/*.md \
  endpages/*.md \
  cover/back-cover.md \
  -o "Java_for_AI_Era_v3.docx" \
  --toc \
  --toc-depth=2
```

---

## Status Tracking

| Section | Files | Status |
|---------|-------|--------|
| Cover | 2 | ⬜ Not started |
| Preface | 3 | ⬜ Not started |
| Chapter 1 | 1 | 🟡 In progress |
| Chapter 2-10 | 9 | ⬜ Not started |
| Final Assessment | 1 | ⬜ Not started |
| End Pages | 4 | ⬜ Not started |
