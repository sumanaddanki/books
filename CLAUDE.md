# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a book authoring repository for educational content targeting GenZ developers who use AI coding tools. The core philosophy is **concepts over syntax**—AI handles syntax, humans handle understanding and judgment.

**Core Philosophy:** "You don't need to memorize syntax. You need to understand WHY you're doing something, WHAT could go wrong, and HOW to evaluate AI output."

## Project Structure

```
books/
├── book1-java-for-ai-era/           # Java book
│   ├── v1/                          # Original 17-chapter syntax-focused version
│   │   ├── chapters/                # Detailed syntax tutorials
│   │   ├── code-examples/           # Runnable Java examples
│   │   └── youtube-scripts/         # Companion video scripts
│   ├── v2/                          # Current 8-chapter concept-focused version
│   │   ├── chapters/                # Mental models, real disasters, checklists
│   │   ├── BOOK_OUTLINE.md          # Chapter breakdown
│   │   └── SUMMARY.md               # Version overview
│   └── VERSION_HISTORY.md           # Why versions exist, what changed
└── AI_4_GENZ/                       # Placeholder for future books
```

## Version Philosophy

- **v1:** Traditional approach - teach syntax deeply, add AI at the end
- **v2:** Modern approach - teach concepts, use real disaster stories, AI integrated throughout

We keep both versions to compare approaches and potentially offer different formats.

## Book Series Plan

This repository will eventually contain multiple books in the same style:
1. **Java for the AI Era** (current)
2. **Database Engineering for the AI Era** (planned)
3. **UI/Frontend for the AI Era** (planned)

All follow the same philosophy: mental models over syntax, real-world disasters, AI as tool not replacement.

## Writing Guidelines (v2 Style)

When working on book content:
- **Concepts, not syntax** - Explain WHY and WHAT, AI handles HOW
- **Real disaster stories** - Every concept illustrated with "what went wrong"
- **Mental checklists** - Practical frameworks readers can apply
- **What to tell AI** - Show how to prompt effectively for each concept
- **Red flags** - What to watch for in AI-generated code
- Target audience: GenZ developers (ages 18-28) who use AI tools daily
- Tone: Direct, practical, no fluff, respect reader's time

## Chapter Structure (v2)

Each chapter follows this pattern:
1. **Opening quote** - Sets the tone
2. **The concept** - Simple explanation with real-world analogy
3. **Real disasters** - Stories of what goes wrong without understanding
4. **Mental model/framework** - How to think about it
5. **What to tell AI** - Effective prompting for this concept
6. **Red flags** - What to watch for in AI output
7. **Quick checklist** - Practical review framework
8. **Key takeaways** - Summary points

## File Formats

- Book content: Markdown (`.md`)
- No code examples in v2 (concept-focused, AI generates code)
- v1 retains Java examples for reference
