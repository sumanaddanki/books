# Chapter 12: Practical QUAD Workflows — From Laptop to Production

> "Setup isn't just installing tools. It's building a system where you can code with AI, review effectively, and ship confidently."

---

## The Journey

This chapter walks through **real workflows** using QUAD principles:

1. **Brand New Laptop Setup** — IDE, AI tools, dev environment
2. **First Day on a New Team** — Understanding the codebase
3. **Your First Feature** — End-to-end using QUAD stages
4. **Code Review Workflow** — Reviewing AI-generated code
5. **Production Incident** — Debugging when AI code fails

Each workflow shows **exactly what to do** and **what to prompt AI**.

---

## Workflow 1: Brand New Laptop Setup

**Scenario:** You just got a new MacBook. Need to set up for Java AI development.

### Step 1: Essential Tools (30 min)

```bash
# Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Java 21 (LTS)
brew install openjdk@21

# Set JAVA_HOME
echo 'export JAVA_HOME=$(/usr/libexec/java_home -v 21)' >> ~/.zshrc
source ~/.zshrc

# Verify
java -version
```

### Step 2: IDE + AI (15 min)

**IntelliJ IDEA Ultimate** (recommended for Java):
```bash
brew install --cask intellij-idea
```

**AI Plugins:**
1. Open IntelliJ → Settings → Plugins
2. Install:
   - **GitHub Copilot** — AI code completion
   - **Anthropic Claude** (if available) — In-IDE chat
   - **JetBrains AI Assistant** — Built-in AI

**Claude Code CLI** (for terminal):
```bash
# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Login
claude login
```

### Step 3: Git + GitHub (10 min)

```bash
# Configure Git
git config --global user.name "Your Name"
git config --global user.email "your.email@company.com"

# SSH key for GitHub
ssh-keygen -t ed25519 -C "your.email@company.com"
cat ~/.ssh/id_ed25519.pub  # Copy to GitHub Settings → SSH Keys
```

### Step 4: Build Tools (10 min)

```bash
# Maven
brew install maven

# Gradle (if needed)
brew install gradle

# Verify
mvn -version
gradle -version
```

### Step 5: Database (15 min)

```bash
# PostgreSQL (most common for enterprise)
brew install postgresql@15
brew services start postgresql@15

# Create dev database
createdb myapp_dev
psql myapp_dev -c "CREATE USER dev_user WITH PASSWORD 'dev_pass';"
psql myapp_dev -c "GRANT ALL PRIVILEGES ON DATABASE myapp_dev TO dev_user;"
```

### Step 6: Docker (10 min)

```bash
# Docker Desktop
brew install --cask docker

# Start Docker Desktop, then verify
docker run hello-world
```

### Step 7: Verification Prompt

**Ask AI to verify your setup:**

```
I just set up a new Mac for Java development. Verify my setup:
- Java 21 via Homebrew
- IntelliJ IDEA with GitHub Copilot
- Maven and Gradle
- PostgreSQL 15
- Docker Desktop

Generate a checklist to confirm everything works, including a sample
Spring Boot app that connects to PostgreSQL.
```

---

## Workflow 2: First Day on a New Team

**Scenario:** You joined a team. Need to understand their codebase and QUAD setup.

### Step 1: Get Access (Day 1 Morning)

- [ ] GitHub/GitLab repository access
- [ ] Slack/Teams channel access
- [ ] Jira/Linear board access
- [ ] VPN/internal network access
- [ ] AWS/GCP console access (read-only)

### Step 2: Clone and Run (Day 1 Afternoon)

```bash
# Clone the repo
git clone git@github.com:company/main-app.git
cd main-app

# Read the README first!
cat README.md

# Install dependencies
./mvnw install

# Run locally
./mvnw spring-boot:run
```

### Step 3: Understand the Codebase with AI

**Prompt 1 — Overall Structure:**
```
I just joined a team. Here's our project structure:
[paste output of: tree -L 3 src/]

Explain the architecture. What patterns do you see?
Is this a standard Spring Boot layout or something custom?
```

**Prompt 2 — Key Files:**
```
Here are our main configuration files:
[paste application.yml]
[paste pom.xml dependencies section]

What frameworks and libraries are we using?
What should I learn about before contributing?
```

**Prompt 3 — Domain Model:**
```
Here are our JPA entities:
[paste key entity classes]

Draw me a mental model of the domain.
What are the core business concepts?
```

### Step 4: Understand QUAD Setup

Ask your tech lead:

1. **"What's my Adoption Matrix level?"**
   - Skill level: 1, 2, or 3?
   - Trust level: 1, 2, or 3?
   - What zone am I in? (Restricted, Assisted, Balanced, etc.)

2. **"What's my role-stage participation?"**
   - Am I PRIMARY, SUPPORT, REVIEW, or INFORM for each stage?
   - Who reviews my code?
   - Who do I go to for architecture questions?

3. **"Which Circle am I in?"**
   - Development Circle most likely
   - Who's the circle lead?

### Step 5: First Contribution

**Start small.** A good first task:
- Fix a typo in documentation
- Add a missing unit test
- Update an outdated dependency

**Prompt to understand existing code:**
```
I'm new to this codebase. Before I modify [file], explain:
1. What this class does
2. How it fits into the larger system
3. What tests exist for it
4. What could break if I change it
```

---

## Workflow 3: Your First Feature (End-to-End QUAD)

**Scenario:** Assigned feature: "Add email notification when user signs up"

### Q Stage — Understand Requirements

**Ask PM/Manager:**
- What triggers the email? (After signup? After email verification?)
- What's in the email? (Welcome message? Action required?)
- What email service? (AWS SES? SendGrid? Company SMTP?)

**AI Prompt:**
```
I need to add email notification on user signup for a Spring Boot app.
Current setup: Spring Boot 3.2, PostgreSQL, no email service configured.
AWS SES is available.

What questions should I clarify with the product manager before starting?
```

### U Stage — Design Solution

**AI Prompt:**
```
Design email notification for user signup.
Requirements:
- Trigger after successful registration (not before email verification)
- Use AWS SES
- Store email status in database (sent/failed/pending)
- Allow retry for failed emails

Generate:
1. New database tables needed
2. Service class design
3. Integration with existing UserService
```

**Review AI output using Chapter 10 checklist:**
- [ ] Uses our existing patterns (check UserService)
- [ ] Proper exception handling for email failures
- [ ] Async processing (don't block signup)
- [ ] Proper logging

### A Stage — Plan Work

Break into tasks:
1. Add AWS SES dependency + configuration (1h)
2. Create EmailNotification entity + repository (1h)
3. Create EmailService with SES integration (2h)
4. Modify UserService to trigger email (1h)
5. Add retry mechanism for failures (2h)
6. Write unit tests (2h)
7. Write integration test with LocalStack (2h)

**Total estimate:** 11 hours (2 days)

### D Stage — Implement

**Task 1: Configuration**

```java
// application.yml
aws:
  ses:
    region: us-east-1
    sender: noreply@company.com
```

```xml
<!-- pom.xml -->
<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>ses</artifactId>
    <version>2.21.0</version>
</dependency>
```

**Task 2: Entity**

**AI Prompt:**
```
Create JPA entity EmailNotification for tracking sent emails.
Fields: id (UUID), userId, emailType (enum), status (PENDING/SENT/FAILED),
sentAt, errorMessage, retryCount.
Use our existing BaseEntity pattern (createdAt, updatedAt auto-managed).
```

**Review and adjust:**
```java
@Entity
@Table(name = "email_notifications")
public class EmailNotification extends BaseEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.UUID)
    private UUID id;

    @Column(nullable = false)
    private UUID userId;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private EmailType emailType;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private EmailStatus status = EmailStatus.PENDING;

    private Instant sentAt;

    @Column(length = 1000)
    private String errorMessage;

    @Column(nullable = false)
    private int retryCount = 0;

    // Getters, setters
}
```

**Task 3: Service**

**AI Prompt:**
```
Create EmailService for Spring Boot with AWS SES.
Requirements:
- Async sending (don't block caller)
- Save notification to database before sending
- Update status after send (success or failure)
- Handle SES rate limiting gracefully
- Use our existing pattern: @Slf4j, @RequiredArgsConstructor
```

**Review AI output:**
- [ ] Uses @Async correctly
- [ ] Transaction boundaries correct (save before async call)
- [ ] Exceptions logged, not swallowed
- [ ] Rate limiting handled

**Continue for remaining tasks...**

### Submit for Review

**Create PR with:**
```markdown
## Summary
Adds email notification on user signup using AWS SES.

## Changes
- EmailNotification entity + repository
- EmailService with async SES integration
- UserService triggers email after registration
- Retry mechanism for failed emails (max 3 retries)

## Testing
- Unit tests for EmailService
- Integration test with LocalStack SES

## QUAD Stage
Moving from D (Deliver) → Complete
Adoption Matrix: Skill 2, Trust 2 (Balanced AI zone)
AI tools used: GitHub Copilot for boilerplate, Claude for architecture review
```

---

## Workflow 4: Code Review (Reviewing AI-Generated Code)

**Scenario:** You're reviewing a PR from a junior developer who used AI.

### Step 1: Check Context

Before reviewing code, understand:
- What QUAD stage is this? (Should be D → Complete)
- What's reviewer's Adoption Matrix level?
- What AI tools were used?

### Step 2: Java-Specific Review Checklist

```markdown
## AI Code Review Checklist

### Types & Nullability
- [ ] No raw types (List<String> not List)
- [ ] Null checks present where needed
- [ ] Optional used for nullable returns
- [ ] BigDecimal for money (not double)

### OOP
- [ ] Extends existing classes (not duplicating)
- [ ] Uses existing interfaces
- [ ] Proper encapsulation (private fields)
- [ ] No god classes

### Collections
- [ ] Correct implementation (ArrayList vs LinkedList)
- [ ] Immutable collections for returns (List.copyOf)
- [ ] No ConcurrentModificationException risks

### Exceptions
- [ ] Checked exceptions handled
- [ ] Not catching generic Exception
- [ ] try-with-resources for AutoCloseable
- [ ] No swallowed exceptions

### Modern Java
- [ ] Streams used appropriately (not forced)
- [ ] Method references where cleaner
- [ ] No redundant lambda parameters

### Security
- [ ] No SQL injection (use parameterized queries)
- [ ] No hardcoded secrets
- [ ] Input validation present
- [ ] Proper authentication checks
```

### Step 3: Provide Constructive Feedback

**Instead of:**
> "This is wrong, AI doesn't know our patterns."

**Say:**
> "This ArrayList should be LinkedList for our use case (frequent insertions at head). See our CollectionGuidelines.md for when to use which."

**Instead of:**
> "Rewrite this."

**Say:**
> "The exception handling here catches too broadly. Prompt AI with: 'Refactor to catch specific exceptions: UserNotFoundException, EmailSendFailure. Log with context.'"

---

## Workflow 5: Production Incident (When AI Code Fails)

**Scenario:** 3 AM alert. Email service is failing. This was your feature.

### Step 1: Triage (5 min)

```bash
# Check logs
kubectl logs -f deployment/main-app | grep -i email

# Check error rate
curl -s http://localhost:8080/actuator/metrics/http.server.requests | jq
```

### Step 2: Identify Root Cause

**Common AI-generated code failures:**
1. **NullPointerException** — Missing null check
2. **Connection pool exhausted** — Not closing resources
3. **Rate limiting** — No backoff strategy
4. **Timeout** — Sync call where async needed

### Step 3: Quick Fix

```java
// Before (AI-generated, problematic)
public void sendEmail(Email email) {
    sesClient.sendEmail(email);  // Blocks, no timeout
}

// After (human fix)
public void sendEmail(Email email) {
    try {
        CompletableFuture.supplyAsync(() -> sesClient.sendEmail(email))
            .orTimeout(5, TimeUnit.SECONDS)
            .exceptionally(ex -> {
                log.error("Email failed: {}", email.getId(), ex);
                return null;
            });
    } catch (Exception e) {
        log.error("Failed to queue email: {}", email.getId(), e);
        // Save for retry
        saveForRetry(email);
    }
}
```

### Step 4: Post-Mortem

Document what AI got wrong:

```markdown
## Incident: Email Service Failure

### What Happened
AWS SES rate limit exceeded, causing all emails to fail.
AI-generated code had no rate limiting or retry logic.

### Root Cause
AI was prompted for "email service" but not told about:
- AWS SES rate limits (14 emails/second)
- Need for exponential backoff
- Retry queue for failures

### Fix
Added rate limiting with token bucket algorithm.
Added retry queue with exponential backoff.

### Lesson for Team
When prompting AI for external service integration, always specify:
- Rate limits
- Timeout requirements
- Retry strategy
- Failure handling

### Updated Prompt Template
"Create [service] integration with:
- Rate limiting: [X requests/second]
- Timeout: [Y seconds]
- Retry: [Z attempts with exponential backoff]
- Failure handling: [queue for retry / log and alert / etc.]"
```

---

## Key Takeaways

1. **Setup matters** — Right tools, right configuration, right AI access level
2. **First day focus** — Understand codebase AND QUAD setup (your role, your zone)
3. **Features flow through QUAD** — Q→U→A→D with clear handoffs
4. **Review is critical** — Use Chapter 10 checklist for AI-generated code
5. **Incidents teach** — Document what AI got wrong, improve prompts

---

## Quick Reference Card

**Laptop Setup:**
```
Java 21 + IntelliJ + Copilot + Maven + PostgreSQL + Docker
```

**First Day:**
```
1. Get access
2. Clone and run
3. Prompt AI to explain codebase
4. Learn your QUAD role
```

**Feature Flow:**
```
Q: Clarify requirements
U: Design with AI, review output
A: Break into tasks, estimate
D: Implement, test, PR
```

**Review Checklist:**
```
Types → OOP → Collections → Exceptions → Modern Java → Security
```

**Incident Response:**
```
Triage → Root cause → Quick fix → Post-mortem → Update prompts
```

---

**Congratulations!** You've completed "Java for the AI Era."

You now understand:
- Java concepts deeply enough to evaluate AI output
- When AI helps vs. when it misleads
- How to prompt effectively for Java code
- How to review AI-generated code
- How teams organize around AI with QUAD

**Go build something great. Let AI handle the syntax. You handle the thinking.**
