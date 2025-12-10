# Chapter 13: File I/O - Reading and Writing Data

> "Programs without I/O are islands - they can think, but they can't communicate. File handling connects your code to the real world."

---

## What You'll Learn

- Modern file handling with java.nio
- Reading and writing text files
- Working with binary files
- Path manipulation
- Directory operations
- Handling different file formats (CSV, JSON, Properties)
- Common file handling mistakes AI makes

---

## Old vs New: java.io vs java.nio

Java has two file APIs:

| java.io (old) | java.nio (modern) |
|---------------|-------------------|
| `File` | `Path` |
| Stream-based | Buffer-based |
| Blocking | Can be non-blocking |
| Less convenient | More convenient methods |

**Use java.nio for new code.** It's more powerful and has better methods.

---

## Working with Paths

### Creating Paths

```java
import java.nio.file.Path;
import java.nio.file.Paths;

// Different ways to create paths
Path path1 = Path.of("data", "users.txt");           // Java 11+
Path path2 = Paths.get("data", "users.txt");         // Java 7+
Path path3 = Path.of("/home/user/documents/file.txt");

// From string
Path path4 = Path.of("./relative/path/file.txt");
Path path5 = Path.of("C:\\Windows\\System32");       // Windows

// Current directory
Path current = Path.of(".");
Path absolute = current.toAbsolutePath();
```

### Path Operations

```java
Path path = Path.of("/home/user/documents/report.pdf");

// Get parts of path
Path fileName = path.getFileName();      // report.pdf
Path parent = path.getParent();          // /home/user/documents
Path root = path.getRoot();              // /
int nameCount = path.getNameCount();     // 4

// Get specific element
Path element = path.getName(1);          // user

// Resolve paths (combine)
Path base = Path.of("/home/user");
Path full = base.resolve("documents/file.txt");  // /home/user/documents/file.txt

// Relativize (opposite of resolve)
Path p1 = Path.of("/home/user/docs");
Path p2 = Path.of("/home/user/photos/vacation.jpg");
Path relative = p1.relativize(p2);       // ../photos/vacation.jpg

// Normalize (remove . and ..)
Path messy = Path.of("/home/user/../user/./documents");
Path clean = messy.normalize();          // /home/user/documents
```

### Checking Path Properties

```java
import java.nio.file.Files;

Path path = Path.of("myfile.txt");

// Check existence
boolean exists = Files.exists(path);
boolean notExists = Files.notExists(path);

// Check type
boolean isFile = Files.isRegularFile(path);
boolean isDir = Files.isDirectory(path);
boolean isLink = Files.isSymbolicLink(path);

// Check permissions
boolean canRead = Files.isReadable(path);
boolean canWrite = Files.isWritable(path);
boolean canExecute = Files.isExecutable(path);

// Check if hidden
boolean hidden = Files.isHidden(path);

// Get file attributes
long size = Files.size(path);
FileTime modified = Files.getLastModifiedTime(path);
```

---

## Reading Files

### Reading Entire File

```java
import java.nio.file.Files;
import java.nio.file.Path;

// Read all content as String (Java 11+)
String content = Files.readString(Path.of("data.txt"));

// Read all lines as List
List<String> lines = Files.readAllLines(Path.of("data.txt"));

// Read as bytes
byte[] bytes = Files.readAllBytes(Path.of("image.png"));

// With charset
String content = Files.readString(Path.of("data.txt"), StandardCharsets.UTF_8);
```

### Reading Large Files (Streaming)

For large files, don't load everything into memory:

```java
// Stream lines (lazy, memory efficient)
try (Stream<String> stream = Files.lines(Path.of("huge-file.txt"))) {
    stream
        .filter(line -> line.contains("ERROR"))
        .forEach(System.out::println);
}

// BufferedReader for more control
try (BufferedReader reader = Files.newBufferedReader(Path.of("data.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        process(line);
    }
}
```

### Reading Binary Files

```java
// Read all bytes
byte[] data = Files.readAllBytes(Path.of("image.png"));

// Read with InputStream
try (InputStream is = Files.newInputStream(Path.of("data.bin"))) {
    byte[] buffer = new byte[8192];
    int bytesRead;
    while ((bytesRead = is.read(buffer)) != -1) {
        process(buffer, bytesRead);
    }
}
```

---

## Writing Files

### Writing Text Files

```java
// Write string (overwrites existing)
Files.writeString(Path.of("output.txt"), "Hello, World!");

// Write with options
Files.writeString(
    Path.of("output.txt"),
    "Appended text",
    StandardOpenOption.APPEND,
    StandardOpenOption.CREATE
);

// Write lines
List<String> lines = Arrays.asList("Line 1", "Line 2", "Line 3");
Files.write(Path.of("output.txt"), lines);

// With charset
Files.writeString(
    Path.of("output.txt"),
    "Content",
    StandardCharsets.UTF_8
);
```

### Standard Open Options

| Option | Description |
|--------|-------------|
| `CREATE` | Create if doesn't exist |
| `CREATE_NEW` | Create, fail if exists |
| `APPEND` | Append to existing |
| `TRUNCATE_EXISTING` | Clear existing content |
| `WRITE` | Open for writing |
| `READ` | Open for reading |

### Writing with BufferedWriter

```java
try (BufferedWriter writer = Files.newBufferedWriter(Path.of("output.txt"))) {
    writer.write("First line");
    writer.newLine();
    writer.write("Second line");
}

// With options
try (BufferedWriter writer = Files.newBufferedWriter(
        Path.of("output.txt"),
        StandardOpenOption.APPEND,
        StandardOpenOption.CREATE)) {
    writer.write("Appended content");
}
```

### Writing Binary Files

```java
// Write bytes
byte[] data = {0x48, 0x65, 0x6C, 0x6C, 0x6F};
Files.write(Path.of("output.bin"), data);

// Write with OutputStream
try (OutputStream os = Files.newOutputStream(Path.of("output.bin"))) {
    os.write(data);
}
```

---

## Directory Operations

### Creating Directories

```java
// Create single directory
Files.createDirectory(Path.of("newdir"));

// Create nested directories (like mkdir -p)
Files.createDirectories(Path.of("path/to/nested/dir"));
```

### Listing Directory Contents

```java
// List immediate children
try (Stream<Path> stream = Files.list(Path.of("."))) {
    stream.forEach(System.out::println);
}

// Filter by type
try (Stream<Path> stream = Files.list(Path.of("."))) {
    List<Path> files = stream
        .filter(Files::isRegularFile)
        .toList();
}

// Walk directory tree (recursive)
try (Stream<Path> stream = Files.walk(Path.of("."))) {
    stream
        .filter(p -> p.toString().endsWith(".java"))
        .forEach(System.out::println);
}

// Walk with depth limit
try (Stream<Path> stream = Files.walk(Path.of("."), 2)) {
    stream.forEach(System.out::println);
}

// Find files matching pattern
try (Stream<Path> stream = Files.find(
        Path.of("."),
        10,
        (path, attrs) -> path.toString().endsWith(".txt"))) {
    stream.forEach(System.out::println);
}
```

### Directory Traversal with Visitor

```java
Files.walkFileTree(Path.of("."), new SimpleFileVisitor<Path>() {
    @Override
    public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) {
        System.out.println("File: " + file);
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) {
        System.out.println("Dir: " + dir);
        return FileVisitResult.CONTINUE;
    }

    @Override
    public FileVisitResult visitFileFailed(Path file, IOException exc) {
        System.err.println("Failed: " + file);
        return FileVisitResult.CONTINUE;
    }
});
```

---

## File Operations

### Copy

```java
// Copy file
Files.copy(
    Path.of("source.txt"),
    Path.of("dest.txt"),
    StandardCopyOption.REPLACE_EXISTING
);

// Copy with all attributes
Files.copy(
    Path.of("source.txt"),
    Path.of("dest.txt"),
    StandardCopyOption.COPY_ATTRIBUTES
);

// Copy to stream
try (OutputStream os = new FileOutputStream("dest.txt")) {
    Files.copy(Path.of("source.txt"), os);
}

// Copy from stream
try (InputStream is = new FileInputStream("source.txt")) {
    Files.copy(is, Path.of("dest.txt"));
}
```

### Move

```java
// Move/rename file
Files.move(
    Path.of("old-name.txt"),
    Path.of("new-name.txt"),
    StandardCopyOption.REPLACE_EXISTING
);

// Atomic move (if supported)
Files.move(
    Path.of("source.txt"),
    Path.of("dest.txt"),
    StandardCopyOption.ATOMIC_MOVE
);
```

### Delete

```java
// Delete file (throws if doesn't exist)
Files.delete(Path.of("file.txt"));

// Delete if exists (no exception)
boolean deleted = Files.deleteIfExists(Path.of("file.txt"));

// Delete directory (must be empty!)
Files.delete(Path.of("emptyDir"));

// Delete directory with contents
Files.walk(Path.of("dirToDelete"))
    .sorted(Comparator.reverseOrder())  // Files before directories
    .forEach(path -> {
        try {
            Files.delete(path);
        } catch (IOException e) {
            e.printStackTrace();
        }
    });
```

---

## Working with Common File Formats

### Properties Files

```java
import java.util.Properties;

// Read properties
Properties props = new Properties();
try (InputStream is = Files.newInputStream(Path.of("config.properties"))) {
    props.load(is);
}

String dbHost = props.getProperty("db.host", "localhost");
int dbPort = Integer.parseInt(props.getProperty("db.port", "5432"));

// Write properties
props.setProperty("app.name", "MyApp");
props.setProperty("app.version", "1.0");

try (OutputStream os = Files.newOutputStream(Path.of("config.properties"))) {
    props.store(os, "Application Configuration");
}
```

### CSV Files

```java
// Simple CSV reading
List<String[]> records = new ArrayList<>();
try (BufferedReader reader = Files.newBufferedReader(Path.of("data.csv"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        String[] fields = line.split(",");
        records.add(fields);
    }
}

// With proper CSV parsing (handles quotes, commas in fields)
// Better to use a library like OpenCSV or Apache Commons CSV
```

### JSON Files

```java
// Using Gson library
import com.google.gson.Gson;

Gson gson = new Gson();

// Read JSON
String json = Files.readString(Path.of("data.json"));
User user = gson.fromJson(json, User.class);

// Read JSON array
Type listType = new TypeToken<List<User>>(){}.getType();
List<User> users = gson.fromJson(json, listType);

// Write JSON
String output = gson.toJson(user);
Files.writeString(Path.of("output.json"), output);

// Pretty print
Gson prettyGson = new GsonBuilder().setPrettyPrinting().create();
String pretty = prettyGson.toJson(user);
```

---

## Temporary Files and Directories

```java
// Create temp file
Path tempFile = Files.createTempFile("prefix-", ".txt");
System.out.println(tempFile);  // /tmp/prefix-123456.txt

// Create temp file in specific directory
Path tempFile2 = Files.createTempFile(Path.of("/custom/path"), "data-", ".tmp");

// Create temp directory
Path tempDir = Files.createTempDirectory("myapp-");

// Delete on exit (JVM shutdown)
tempFile.toFile().deleteOnExit();
```

---

## Resource Files (in JAR)

Reading files packaged in your JAR:

```java
// Read resource from classpath
InputStream is = getClass().getResourceAsStream("/data/config.properties");
// or
InputStream is = MyClass.class.getResourceAsStream("/data/config.properties");

// Read as string
String content = new String(is.readAllBytes(), StandardCharsets.UTF_8);

// Get resource URL
URL url = getClass().getResource("/data/config.properties");
```

---

## Hands-On Project: File Processor

```java
import java.nio.file.*;
import java.io.*;
import java.util.*;
import java.util.stream.*;

public class FileProcessor {

    public static void main(String[] args) throws IOException {
        FileProcessor processor = new FileProcessor();

        // 1. Count lines in all Java files
        long totalLines = processor.countLinesInJavaFiles(Path.of("."));
        System.out.println("Total lines in Java files: " + totalLines);

        // 2. Find large files
        List<Path> largeFiles = processor.findLargeFiles(Path.of("."), 1024 * 1024);
        System.out.println("Files over 1MB: " + largeFiles);

        // 3. Backup directory
        processor.backupDirectory(Path.of("src"), Path.of("backup"));

        // 4. Generate report
        processor.generateReport(Path.of("."), Path.of("report.txt"));
    }

    public long countLinesInJavaFiles(Path directory) throws IOException {
        try (Stream<Path> stream = Files.walk(directory)) {
            return stream
                .filter(p -> p.toString().endsWith(".java"))
                .mapToLong(this::countLines)
                .sum();
        }
    }

    private long countLines(Path file) {
        try (Stream<String> lines = Files.lines(file)) {
            return lines.count();
        } catch (IOException e) {
            return 0;
        }
    }

    public List<Path> findLargeFiles(Path directory, long minSize) throws IOException {
        try (Stream<Path> stream = Files.walk(directory)) {
            return stream
                .filter(Files::isRegularFile)
                .filter(p -> {
                    try {
                        return Files.size(p) > minSize;
                    } catch (IOException e) {
                        return false;
                    }
                })
                .collect(Collectors.toList());
        }
    }

    public void backupDirectory(Path source, Path target) throws IOException {
        if (!Files.exists(source)) {
            throw new IllegalArgumentException("Source doesn't exist: " + source);
        }

        Files.walk(source).forEach(sourcePath -> {
            try {
                Path targetPath = target.resolve(source.relativize(sourcePath));

                if (Files.isDirectory(sourcePath)) {
                    Files.createDirectories(targetPath);
                } else {
                    Files.createDirectories(targetPath.getParent());
                    Files.copy(sourcePath, targetPath, StandardCopyOption.REPLACE_EXISTING);
                }
            } catch (IOException e) {
                throw new RuntimeException("Backup failed: " + e.getMessage(), e);
            }
        });

        System.out.println("Backup completed: " + source + " → " + target);
    }

    public void generateReport(Path directory, Path reportFile) throws IOException {
        try (BufferedWriter writer = Files.newBufferedWriter(reportFile)) {
            writer.write("Directory Report: " + directory.toAbsolutePath());
            writer.newLine();
            writer.write("Generated: " + java.time.LocalDateTime.now());
            writer.newLine();
            writer.write("=".repeat(50));
            writer.newLine();
            writer.newLine();

            // Count by extension
            Map<String, Long> countByExtension = new HashMap<>();
            Map<String, Long> sizeByExtension = new HashMap<>();

            Files.walk(directory)
                .filter(Files::isRegularFile)
                .forEach(path -> {
                    String ext = getExtension(path);
                    countByExtension.merge(ext, 1L, Long::sum);
                    try {
                        sizeByExtension.merge(ext, Files.size(path), Long::sum);
                    } catch (IOException ignored) {}
                });

            writer.write("Files by Extension:");
            writer.newLine();
            countByExtension.entrySet().stream()
                .sorted(Map.Entry.<String, Long>comparingByValue().reversed())
                .forEach(entry -> {
                    try {
                        writer.write(String.format("  %s: %d files, %s bytes%n",
                            entry.getKey(),
                            entry.getValue(),
                            formatSize(sizeByExtension.getOrDefault(entry.getKey(), 0L))));
                    } catch (IOException ignored) {}
                });
        }

        System.out.println("Report generated: " + reportFile);
    }

    private String getExtension(Path path) {
        String name = path.getFileName().toString();
        int dot = name.lastIndexOf('.');
        return dot > 0 ? name.substring(dot) : "(no extension)";
    }

    private String formatSize(long bytes) {
        if (bytes < 1024) return bytes + " B";
        if (bytes < 1024 * 1024) return (bytes / 1024) + " KB";
        return (bytes / (1024 * 1024)) + " MB";
    }
}
```

---

## AI Integration: File I/O Mistakes

### Problem 1: Not Closing Resources

**AI generates:**
```java
BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
String line = reader.readLine();
// Reader never closed!
```

**Fix:**
```java
try (BufferedReader reader = Files.newBufferedReader(Path.of("file.txt"))) {
    String line = reader.readLine();
}
```

### Problem 2: Using Old API

**AI generates:**
```java
File file = new File("data.txt");
FileReader reader = new FileReader(file);
```

**Fix:**
```java
Path path = Path.of("data.txt");
String content = Files.readString(path);
```

### Problem 3: Loading Large Files into Memory

**AI generates:**
```java
// 10GB file loaded entirely into memory!
String content = Files.readString(Path.of("huge-file.txt"));
```

**Fix:**
```java
// Stream lines instead
try (Stream<String> lines = Files.lines(Path.of("huge-file.txt"))) {
    lines.filter(l -> l.contains("ERROR")).forEach(System.out::println);
}
```

### Problem 4: Not Handling File Not Found

**AI generates:**
```java
String content = Files.readString(Path.of("config.txt"));  // May throw!
```

**Fix:**
```java
Path configPath = Path.of("config.txt");
if (Files.exists(configPath)) {
    String content = Files.readString(configPath);
} else {
    // Use defaults or throw meaningful exception
}
```

### Problem 5: Platform-Dependent Paths

**AI generates:**
```java
Path path = Path.of("C:\\Users\\data\\file.txt");  // Windows only!
```

**Fix:**
```java
// Use Path.of() with segments
Path path = Path.of(System.getProperty("user.home"), "data", "file.txt");
// Or
Path path = Path.of("data", "file.txt");  // Relative, platform-independent
```

---

## Exercises

### Exercise 13.1: File Statistics
Write a program that reads a text file and reports:
- Total characters
- Total words
- Total lines
- Most frequent word

### Exercise 13.2: Directory Sync
Create a method that syncs two directories - copying new/modified files from source to target.

### Exercise 13.3: Log Parser
Parse a log file with format `[TIMESTAMP] [LEVEL] Message` and:
- Count entries by level (INFO, WARN, ERROR)
- Find all ERROR entries from today
- Write ERROR entries to separate file

### Exercise 13.4: Config Manager
Create a ConfigManager class that:
- Loads configuration from properties file
- Falls back to defaults if file missing
- Saves changes back to file
- Supports live reload

### Exercise 13.5: Fix AI Code
Fix the file handling issues in this AI-generated code:

```java
public class DataProcessor {
    public List<String> processFile(String filename) {
        List<String> results = new ArrayList<>();
        try {
            File file = new File(filename);
            Scanner scanner = new Scanner(file);
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                if (line.length() > 0) {
                    results.add(line.toUpperCase());
                }
            }
        } catch (Exception e) {
            System.out.println("Error");
        }
        return results;
    }

    public void writeResults(String filename, List<String> data) {
        FileWriter writer = new FileWriter(filename);
        for (String line : data) {
            writer.write(line + "\n");
        }
        writer.close();
    }
}
```

---

## Key Takeaways

- **Use java.nio.file** - Modern API with better methods
- **Use Path, not File** - More powerful path manipulation
- **Always use try-with-resources** - Automatic resource cleanup
- **Stream large files** - Don't load everything into memory
- **Use Files utility methods** - readString, writeString, lines
- **Handle missing files gracefully** - Check existence or catch exceptions
- **Use platform-independent paths** - Path.of() with segments

---

## What's Next

You can now read and write files professionally. In **Chapter 14**, we'll explore Unit Testing - how to write tests that ensure your code works correctly and keeps working.

---

*"Files are the memory of your application - handle them with care."*
