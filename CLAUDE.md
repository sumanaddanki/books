# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a book authoring repository for "Java Fundamentals for the AI Era" - a GenZ-focused guide to learning Java with the philosophy that understanding fundamentals is essential before leveraging AI coding tools effectively.

**Core Philosophy:** "You can't effectively use AI to write code you don't understand."

## Project Structure

```
books/
├── book1-java-for-ai-era/         # Main book content
│   ├── JAVA_FOR_AI_ERA_BOOK.md    # Complete book manuscript
│   ├── BOOK_OUTLINE.md            # Chapter breakdown
│   ├── SUMMARY.md                 # Book overview and metadata
│   ├── chapters/                  # Individual chapter files (17 chapters + 4 appendices)
│   ├── code-examples/             # Runnable Java examples organized by chapter
│   ├── youtube-scripts/           # Companion video series scripts
│   ├── exercises/                 # Practice problems (to be populated)
│   └── resources/                 # Additional materials (to be populated)
└── AI_4_GENZ/                     # Placeholder for future content
```

## Content Structure

The book is organized into 4 parts:
- **Part 1 (Ch 1-2):** Foundation - Why understanding matters, environment setup
- **Part 2 (Ch 3-11):** Core Java - Variables, control flow, OOP, collections, generics, streams, concurrency
- **Part 3 (Ch 12-14):** Modern Development - Project structure, file I/O, unit testing
- **Part 4 (Ch 15-17):** AI Integration - Prompting, code review, final project

## Writing Guidelines

When working on book content:
- Target audience is GenZ developers (ages 18-28) who use AI coding tools
- Chapters follow a consistent pattern: concept explanation → hands-on coding → AI integration/comparison
- Code examples should be typed manually by readers (no copy-paste philosophy)
- Use real-world analogies to explain concepts (e.g., classes as blueprints, variables as labeled boxes)
- Include "AI pitfall" sections highlighting common mistakes in AI-generated code
- Tone should be energetic and relatable without being condescending

## Code Examples

Java code examples in `code-examples/` follow this structure:
- Each chapter has its own directory
- Single-file examples can be compiled with: `javac FileName.java && java FileName`
- Maven projects use: `mvn compile && mvn exec:java`
- Tests use JUnit 5 and run with: `mvn test`

## YouTube Companion Series

The `youtube-scripts/VIDEO_SERIES_OVERVIEW.md` contains prompts and outlines for 17 companion videos (~7-8 hours total). Each video corresponds to a book chapter with:
- Google Vids prompts for script generation
- Video structure with timestamps
- Thumbnail and description templates

## File Formats

- Book content: Markdown (`.md`) with HTML for page breaks and styling
- Code: Java 17+ (`.java`)
- Build: Maven (`pom.xml`) for multi-file projects
- Images: PNG for book cover and diagrams
