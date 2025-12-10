# Chapter 17: Final Project - Building a Complete Application

> "Theory without practice is empty. Practice without theory is blind. This project combines everything you've learned."

---

## What You'll Build

A **Task Management CLI Application** that demonstrates:
- Object-oriented design
- Collections and generics
- File I/O for persistence
- Exception handling
- Streams and lambdas
- Unit testing
- Professional project structure

This is your graduation project. Build it yourself, using AI as an assistant (not as the author).

---

## Project Overview

### Features

The Task Manager will support:
- Create, view, update, delete tasks
- Mark tasks complete/incomplete
- Filter tasks by status, priority, due date
- Search tasks by keyword
- Persist data to JSON file
- Statistics and reporting

### Skills Applied

| Feature | Concepts Used |
|---------|---------------|
| Task class | OOP, encapsulation, records |
| Task storage | Collections, generics |
| Save/load | File I/O, JSON |
| Filtering | Streams, lambdas, predicates |
| CLI interface | Control flow, exceptions |
| Tests | JUnit, assertions |

---

## Project Structure

```
task-manager/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/taskmanager/
│   │   │       ├── App.java              # Entry point
│   │   │       ├── model/
│   │   │       │   ├── Task.java
│   │   │       │   ├── Priority.java
│   │   │       │   └── TaskStatus.java
│   │   │       ├── service/
│   │   │       │   └── TaskService.java
│   │   │       ├── repository/
│   │   │       │   └── TaskRepository.java
│   │   │       └── cli/
│   │   │           └── CommandHandler.java
│   │   └── resources/
│   │       └── data/
│   └── test/
│       └── java/
│           └── com/taskmanager/
│               ├── model/
│               │   └── TaskTest.java
│               └── service/
│                   └── TaskServiceTest.java
├── tasks.json                            # Data file
└── README.md
```

---

## Phase 1: Domain Model

### Task.java

Create the core Task class:

```java
package com.taskmanager.model;

import java.time.LocalDateTime;
import java.util.UUID;

public class Task {
    private final String id;
    private String title;
    private String description;
    private Priority priority;
    private TaskStatus status;
    private LocalDateTime dueDate;
    private LocalDateTime createdAt;
    private LocalDateTime completedAt;

    // TODO: Implement
    // - Constructor(s)
    // - Getters
    // - Setters for mutable fields
    // - equals() and hashCode() based on id
    // - toString()
    // - markComplete() and markIncomplete() methods
}
```

**Requirements:**
- `id` should be auto-generated UUID
- `createdAt` should be set automatically
- `completedAt` should be set when marked complete
- Validate that title is not null or empty

### Priority.java

```java
package com.taskmanager.model;

public enum Priority {
    LOW(1),
    MEDIUM(2),
    HIGH(3),
    URGENT(4);

    private final int level;

    Priority(int level) {
        this.level = level;
    }

    public int getLevel() {
        return level;
    }
}
```

### TaskStatus.java

```java
package com.taskmanager.model;

public enum TaskStatus {
    TODO,
    IN_PROGRESS,
    COMPLETED,
    CANCELLED
}
```

### Exercise: Complete Phase 1

1. Implement the Task class fully
2. Write unit tests for Task:
   - Test creation with valid data
   - Test creation with invalid data (null title)
   - Test markComplete() sets completedAt
   - Test markIncomplete() clears completedAt
   - Test equals() and hashCode()

---

## Phase 2: Repository Layer

### TaskRepository.java

```java
package com.taskmanager.repository;

import com.taskmanager.model.Task;
import java.util.*;
import java.nio.file.*;

public class TaskRepository {
    private final Map<String, Task> tasks = new HashMap<>();
    private final Path dataFile;

    public TaskRepository(Path dataFile) {
        this.dataFile = dataFile;
        load();
    }

    // CRUD operations
    public void save(Task task) {
        // TODO: Add task to map, persist to file
    }

    public Optional<Task> findById(String id) {
        // TODO: Return Optional of task
    }

    public List<Task> findAll() {
        // TODO: Return all tasks
    }

    public void update(Task task) {
        // TODO: Update existing task, persist
    }

    public void delete(String id) {
        // TODO: Remove task, persist
    }

    // Persistence
    private void load() {
        // TODO: Load tasks from JSON file
    }

    private void persist() {
        // TODO: Save all tasks to JSON file
    }
}
```

### JSON Persistence

Use Gson for JSON serialization:

```xml
<!-- Add to pom.xml -->
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>
```

**Tips:**
- Register type adapters for LocalDateTime
- Handle file not existing on first run
- Make persistence atomic (write to temp, then rename)

### Exercise: Complete Phase 2

1. Implement TaskRepository
2. Write tests:
   - Test save and find
   - Test update
   - Test delete
   - Test persistence (save, create new repo, verify data loaded)

---

## Phase 3: Service Layer

### TaskService.java

```java
package com.taskmanager.service;

import com.taskmanager.model.*;
import com.taskmanager.repository.TaskRepository;
import java.util.*;
import java.util.function.Predicate;
import java.util.stream.*;

public class TaskService {
    private final TaskRepository repository;

    public TaskService(TaskRepository repository) {
        this.repository = repository;
    }

    // Task operations
    public Task createTask(String title, String description, Priority priority, LocalDateTime dueDate) {
        // TODO: Create and save task
    }

    public Optional<Task> getTask(String id) {
        return repository.findById(id);
    }

    public void updateTask(String id, String title, String description, Priority priority, LocalDateTime dueDate) {
        // TODO: Update task fields
    }

    public void deleteTask(String id) {
        repository.delete(id);
    }

    public void markComplete(String id) {
        // TODO: Mark task as complete
    }

    public void markIncomplete(String id) {
        // TODO: Mark task as incomplete
    }

    // Query operations
    public List<Task> getAllTasks() {
        return repository.findAll();
    }

    public List<Task> getTasksByStatus(TaskStatus status) {
        // TODO: Filter by status using streams
    }

    public List<Task> getTasksByPriority(Priority priority) {
        // TODO: Filter by priority
    }

    public List<Task> getOverdueTasks() {
        // TODO: Find tasks past due date and not completed
    }

    public List<Task> searchTasks(String keyword) {
        // TODO: Search title and description
    }

    public List<Task> getTasksSorted(Comparator<Task> comparator) {
        // TODO: Return sorted list
    }

    // Statistics
    public Map<TaskStatus, Long> getTaskCountByStatus() {
        // TODO: Use Collectors.groupingBy
    }

    public Map<Priority, Long> getTaskCountByPriority() {
        // TODO: Group by priority
    }

    public TaskStatistics getStatistics() {
        // TODO: Return statistics object
    }
}

// Helper class for statistics
record TaskStatistics(
    long total,
    long completed,
    long overdue,
    double completionRate
) {}
```

### Exercise: Complete Phase 3

1. Implement all TaskService methods
2. Use streams and lambdas for filtering/sorting
3. Write comprehensive tests:
   - Test each filter method
   - Test search functionality
   - Test statistics calculations
   - Test edge cases (empty repository, no matches)

---

## Phase 4: CLI Interface

### CommandHandler.java

```java
package com.taskmanager.cli;

import com.taskmanager.model.*;
import com.taskmanager.service.TaskService;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.*;

public class CommandHandler {
    private final TaskService taskService;
    private final Scanner scanner;
    private static final DateTimeFormatter DATE_FORMAT =
        DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");

    public CommandHandler(TaskService taskService) {
        this.taskService = taskService;
        this.scanner = new Scanner(System.in);
    }

    public void run() {
        System.out.println("Task Manager - Type 'help' for commands");

        while (true) {
            System.out.print("\n> ");
            String input = scanner.nextLine().trim();

            if (input.isEmpty()) continue;

            try {
                if (!handleCommand(input)) {
                    break;  // Exit
                }
            } catch (Exception e) {
                System.out.println("Error: " + e.getMessage());
            }
        }

        System.out.println("Goodbye!");
    }

    private boolean handleCommand(String input) {
        String[] parts = input.split("\\s+", 2);
        String command = parts[0].toLowerCase();
        String args = parts.length > 1 ? parts[1] : "";

        switch (command) {
            case "help" -> showHelp();
            case "add" -> addTask();
            case "list" -> listTasks(args);
            case "view" -> viewTask(args);
            case "edit" -> editTask(args);
            case "delete" -> deleteTask(args);
            case "complete" -> completeTask(args);
            case "search" -> searchTasks(args);
            case "stats" -> showStatistics();
            case "exit", "quit" -> { return false; }
            default -> System.out.println("Unknown command. Type 'help' for commands.");
        }

        return true;
    }

    private void showHelp() {
        System.out.println("""
            Commands:
              add              - Add a new task (interactive)
              list [filter]    - List tasks (all/todo/done/overdue)
              view <id>        - View task details
              edit <id>        - Edit a task
              delete <id>      - Delete a task
              complete <id>    - Mark task as complete
              search <keyword> - Search tasks
              stats            - Show statistics
              exit             - Exit the application
            """);
    }

    private void addTask() {
        System.out.print("Title: ");
        String title = scanner.nextLine().trim();
        if (title.isEmpty()) {
            System.out.println("Title cannot be empty");
            return;
        }

        System.out.print("Description (optional): ");
        String description = scanner.nextLine().trim();

        System.out.print("Priority (LOW/MEDIUM/HIGH/URGENT) [MEDIUM]: ");
        String priorityStr = scanner.nextLine().trim().toUpperCase();
        Priority priority = priorityStr.isEmpty() ?
            Priority.MEDIUM : Priority.valueOf(priorityStr);

        System.out.print("Due date (yyyy-MM-dd HH:mm) [none]: ");
        String dueDateStr = scanner.nextLine().trim();
        LocalDateTime dueDate = dueDateStr.isEmpty() ?
            null : LocalDateTime.parse(dueDateStr, DATE_FORMAT);

        Task task = taskService.createTask(title, description, priority, dueDate);
        System.out.println("Created task: " + task.getId());
    }

    // TODO: Implement remaining methods
    private void listTasks(String filter) { }
    private void viewTask(String id) { }
    private void editTask(String id) { }
    private void deleteTask(String id) { }
    private void completeTask(String id) { }
    private void searchTasks(String keyword) { }
    private void showStatistics() { }

    // Helper methods
    private void printTask(Task task) {
        String status = task.getStatus() == TaskStatus.COMPLETED ? "[✓]" : "[ ]";
        System.out.printf("%s %s - %s (%s)%n",
            status, task.getId().substring(0, 8), task.getTitle(), task.getPriority());
    }

    private void printTaskDetails(Task task) {
        System.out.println("ID: " + task.getId());
        System.out.println("Title: " + task.getTitle());
        System.out.println("Description: " + task.getDescription());
        System.out.println("Priority: " + task.getPriority());
        System.out.println("Status: " + task.getStatus());
        System.out.println("Due: " + (task.getDueDate() != null ?
            task.getDueDate().format(DATE_FORMAT) : "Not set"));
        System.out.println("Created: " + task.getCreatedAt().format(DATE_FORMAT));
        if (task.getCompletedAt() != null) {
            System.out.println("Completed: " + task.getCompletedAt().format(DATE_FORMAT));
        }
    }
}
```

### App.java (Entry Point)

```java
package com.taskmanager;

import com.taskmanager.cli.CommandHandler;
import com.taskmanager.repository.TaskRepository;
import com.taskmanager.service.TaskService;
import java.nio.file.Path;

public class App {
    public static void main(String[] args) {
        Path dataFile = Path.of("tasks.json");

        TaskRepository repository = new TaskRepository(dataFile);
        TaskService service = new TaskService(repository);
        CommandHandler cli = new CommandHandler(service);

        cli.run();
    }
}
```

### Exercise: Complete Phase 4

1. Implement all CLI command methods
2. Add input validation
3. Handle errors gracefully
4. Test manually with various scenarios

---

## Phase 5: Testing

### TaskServiceTest.java

```java
package com.taskmanager.service;

import com.taskmanager.model.*;
import com.taskmanager.repository.TaskRepository;
import org.junit.jupiter.api.*;
import org.junit.jupiter.api.io.TempDir;
import java.nio.file.Path;
import java.time.LocalDateTime;
import java.util.*;

import static org.junit.jupiter.api.Assertions.*;

class TaskServiceTest {

    private TaskService service;
    private TaskRepository repository;

    @TempDir
    Path tempDir;

    @BeforeEach
    void setUp() {
        Path dataFile = tempDir.resolve("test-tasks.json");
        repository = new TaskRepository(dataFile);
        service = new TaskService(repository);
    }

    @Nested
    @DisplayName("Task Creation")
    class TaskCreationTests {

        @Test
        @DisplayName("creates task with all fields")
        void createsTaskWithAllFields() {
            LocalDateTime dueDate = LocalDateTime.now().plusDays(7);

            Task task = service.createTask(
                "Test Task",
                "Description",
                Priority.HIGH,
                dueDate
            );

            assertNotNull(task.getId());
            assertEquals("Test Task", task.getTitle());
            assertEquals("Description", task.getDescription());
            assertEquals(Priority.HIGH, task.getPriority());
            assertEquals(TaskStatus.TODO, task.getStatus());
            assertEquals(dueDate, task.getDueDate());
        }

        @Test
        @DisplayName("throws on null title")
        void throwsOnNullTitle() {
            assertThrows(IllegalArgumentException.class, () -> {
                service.createTask(null, "desc", Priority.LOW, null);
            });
        }
    }

    @Nested
    @DisplayName("Task Filtering")
    class TaskFilteringTests {

        @BeforeEach
        void createSampleTasks() {
            service.createTask("Task 1", "", Priority.LOW, null);
            service.createTask("Task 2", "", Priority.HIGH, null);
            Task completed = service.createTask("Task 3", "", Priority.MEDIUM, null);
            service.markComplete(completed.getId());
        }

        @Test
        @DisplayName("filters by status")
        void filtersByStatus() {
            List<Task> todo = service.getTasksByStatus(TaskStatus.TODO);
            List<Task> completed = service.getTasksByStatus(TaskStatus.COMPLETED);

            assertEquals(2, todo.size());
            assertEquals(1, completed.size());
        }

        @Test
        @DisplayName("filters by priority")
        void filtersByPriority() {
            List<Task> highPriority = service.getTasksByPriority(Priority.HIGH);
            assertEquals(1, highPriority.size());
        }
    }

    // TODO: Add more test classes for:
    // - Search functionality
    // - Statistics
    // - Edge cases
}
```

### Exercise: Complete Phase 5

1. Achieve at least 80% code coverage
2. Test all edge cases
3. Test persistence (data survives restart)

---

## Phase 6: Polish and Document

### README.md

```markdown
# Task Manager CLI

A command-line task management application built in Java.

## Features

- Create, view, update, delete tasks
- Filter by status, priority, due date
- Search tasks
- Statistics and reporting
- Persistent storage (JSON)

## Requirements

- Java 21+
- Maven 3.8+

## Building

```bash
mvn clean package
```

## Running

```bash
java -jar target/task-manager-1.0.0.jar
```

## Commands

| Command | Description |
|---------|-------------|
| add | Add a new task |
| list [filter] | List tasks |
| view <id> | View task details |
| complete <id> | Mark complete |
| search <term> | Search tasks |
| stats | Show statistics |
| exit | Exit application |

## Project Structure

[Describe your project structure]

## Design Decisions

[Explain key design choices]
```

### Exercise: Complete Phase 6

1. Add JavaDoc to public methods
2. Write a comprehensive README
3. Add input validation messages
4. Polish the user experience

---

## Self-Assessment Checklist

Use this to evaluate your completed project:

### Code Quality
- [ ] Clean, readable code
- [ ] Meaningful variable/method names
- [ ] Appropriate comments (not excessive)
- [ ] Consistent formatting

### OOP Principles
- [ ] Encapsulation (private fields, public methods)
- [ ] Single responsibility principle
- [ ] Loose coupling between layers
- [ ] Appropriate use of inheritance/interfaces

### Java Features
- [ ] Proper use of generics
- [ ] Streams for collection processing
- [ ] Lambda expressions
- [ ] Modern Java features (records, text blocks, etc.)

### Error Handling
- [ ] Appropriate exceptions
- [ ] Meaningful error messages
- [ ] No empty catch blocks
- [ ] Resources properly closed

### Testing
- [ ] Comprehensive test coverage
- [ ] Edge cases tested
- [ ] Tests are readable and maintainable
- [ ] Tests pass consistently

### Project Structure
- [ ] Standard Maven structure
- [ ] Logical package organization
- [ ] Dependencies managed in pom.xml
- [ ] Clean separation of concerns

---

## Extension Ideas

Once you've completed the basic project, try these extensions:

### 1. Categories/Tags
Add the ability to categorize tasks with tags and filter by them.

### 2. Recurring Tasks
Support tasks that repeat daily, weekly, or monthly.

### 3. Subtasks
Allow tasks to have subtasks/checklists.

### 4. Export/Import
Export to CSV or import from another format.

### 5. Web Interface
Add a simple REST API using Spring Boot.

### 6. Notifications
Add reminders for due tasks.

---

## What You've Accomplished

By completing this project, you've demonstrated:

1. **Java Fundamentals** - Variables, types, control flow
2. **OOP Design** - Classes, inheritance, encapsulation
3. **Collections** - Lists, Maps, Sets
4. **Generics** - Type-safe code
5. **Streams** - Functional processing
6. **File I/O** - Persistence
7. **Exception Handling** - Error management
8. **Testing** - JUnit, assertions
9. **Project Structure** - Maven, packages
10. **AI Collaboration** - Using AI effectively

You're now ready to build real Java applications!

---

## Final Words

Congratulations on completing this book!

Remember:
- **Understanding before automation** - You can now use AI effectively because you understand what it produces
- **Continuous learning** - Java evolves; keep learning
- **Practice makes permanent** - Keep building projects
- **Read code** - Learn from open source projects
- **Share knowledge** - Teach others what you've learned

The journey continues. Good luck!

---

*"The best way to learn programming is to program. Now go build something amazing."*
