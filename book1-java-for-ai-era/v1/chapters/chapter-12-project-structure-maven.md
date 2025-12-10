# Chapter 12: Project Structure and Maven - Building Real Projects

> "A well-structured project is like a well-organized house - everything has its place, and you can find what you need without searching."

---

## What You'll Learn

- Standard Java project structure
- What Maven is and why it matters
- The pom.xml file explained
- Managing dependencies
- Build lifecycle: compile, test, package
- Creating multi-module projects
- What AI often assumes incorrectly about project setup

---

## Why Project Structure Matters

Small programs fit in one file. Real projects have:
- Hundreds of source files
- External libraries (dependencies)
- Tests
- Configuration files
- Resources (images, properties, etc.)

Without structure:
- Can't find anything
- Can't share code with others
- Can't automate builds
- Can't manage dependencies

---

## Standard Java Project Structure

```
my-project/
├── pom.xml                 # Maven configuration
├── src/
│   ├── main/
│   │   ├── java/           # Source code
│   │   │   └── com/
│   │   │       └── mycompany/
│   │   │           └── myapp/
│   │   │               ├── Application.java
│   │   │               ├── model/
│   │   │               ├── service/
│   │   │               └── controller/
│   │   └── resources/      # Non-code files
│   │       ├── application.properties
│   │       └── data/
│   └── test/
│       ├── java/           # Test code
│       │   └── com/
│       │       └── mycompany/
│       │           └── myapp/
│       │               └── service/
│       │                   └── UserServiceTest.java
│       └── resources/      # Test resources
├── target/                 # Build output (generated)
└── README.md
```

### Key Directories

| Directory | Purpose |
|-----------|---------|
| `src/main/java` | Application source code |
| `src/main/resources` | Config files, static data |
| `src/test/java` | Test source code |
| `src/test/resources` | Test-specific resources |
| `target` | Compiled classes, JARs (generated) |

---

## Introduction to Maven

Maven is a **build automation tool** that:
- Manages project dependencies
- Compiles code
- Runs tests
- Packages applications
- Enforces project structure

### Installing Maven

```bash
# macOS (Homebrew)
brew install maven

# Ubuntu/Debian
sudo apt install maven

# Windows (Chocolatey)
choco install maven

# Verify installation
mvn --version
```

### Creating a Maven Project

```bash
# Interactive mode
mvn archetype:generate

# Quick start with defaults
mvn archetype:generate \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.4 \
  -DinteractiveMode=false
```

---

## The pom.xml File

POM = Project Object Model. This file defines everything about your project.

### Minimal pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.mycompany</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <name>My Application</name>
    <description>A sample Maven project</description>
</project>
```

### Project Coordinates

Every Maven artifact is identified by three coordinates:

```xml
<groupId>com.mycompany</groupId>     <!-- Organization (like package name) -->
<artifactId>my-app</artifactId>       <!-- Project name -->
<version>1.0.0</version>              <!-- Version number -->
```

### Version Numbering

Follow Semantic Versioning (SemVer):

| Version | Meaning |
|---------|---------|
| 1.0.0 | Major.Minor.Patch |
| 1.0.0-SNAPSHOT | Development version |
| 1.0.0-alpha | Pre-release |
| 1.0.0-beta | Beta version |
| 1.0.0-RC1 | Release candidate |

---

## Managing Dependencies

### Adding Dependencies

```xml
<dependencies>
    <!-- JUnit for testing -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>

    <!-- JSON processing -->
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.10.1</version>
    </dependency>

    <!-- HTTP client -->
    <dependency>
        <groupId>com.squareup.okhttp3</groupId>
        <artifactId>okhttp</artifactId>
        <version>4.12.0</version>
    </dependency>
</dependencies>
```

### Dependency Scopes

| Scope | Description |
|-------|-------------|
| `compile` | Default. Available everywhere |
| `test` | Only for tests |
| `provided` | Available at compile, not packaged |
| `runtime` | Not needed for compile, needed at runtime |

```xml
<!-- Only available during testing -->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>

<!-- Servlet API - provided by container -->
<dependency>
    <groupId>jakarta.servlet</groupId>
    <artifactId>jakarta.servlet-api</artifactId>
    <version>6.0.0</version>
    <scope>provided</scope>
</dependency>
```

### Finding Dependencies

Search on [Maven Central](https://search.maven.org/):

1. Search for library name
2. Copy the dependency XML
3. Paste into your pom.xml

### Transitive Dependencies

Maven automatically downloads dependencies of your dependencies:

```
Your App → Gson → (no dependencies)
Your App → Spring Boot → Spring Core → Commons Logging
```

View dependency tree:
```bash
mvn dependency:tree
```

### Excluding Transitive Dependencies

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>6.1.0</version>
    <exclusions>
        <exclusion>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

---

## Maven Build Lifecycle

### Default Lifecycle Phases

```
validate → compile → test → package → verify → install → deploy
```

| Phase | Description |
|-------|-------------|
| validate | Check project is correct |
| compile | Compile source code |
| test | Run unit tests |
| package | Create JAR/WAR |
| verify | Run integration tests |
| install | Install to local repository |
| deploy | Deploy to remote repository |

### Running Maven Commands

```bash
# Compile source code
mvn compile

# Run tests
mvn test

# Create package (JAR)
mvn package

# Skip tests (not recommended, but useful sometimes)
mvn package -DskipTests

# Clean and package
mvn clean package

# Install to local repository
mvn install

# Run specific goal
mvn compiler:compile
```

### Clean Lifecycle

```bash
# Remove target directory
mvn clean

# Clean and rebuild
mvn clean install
```

---

## Properties and Variables

### Defining Properties

```xml
<properties>
    <!-- Java version -->
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

    <!-- Dependency versions -->
    <junit.version>5.10.0</junit.version>
    <gson.version>2.10.1</gson.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <version>${junit.version}</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Built-in Properties

```xml
<!-- Project properties -->
${project.version}
${project.groupId}
${project.artifactId}
${project.basedir}

<!-- Environment variables -->
${env.JAVA_HOME}
${env.PATH}

<!-- System properties -->
${java.version}
${os.name}
```

---

## Plugins

Plugins add functionality to Maven.

### Common Plugins

```xml
<build>
    <plugins>
        <!-- Compiler plugin -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.11.0</version>
            <configuration>
                <source>21</source>
                <target>21</target>
            </configuration>
        </plugin>

        <!-- Create executable JAR -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>3.3.0</version>
            <configuration>
                <archive>
                    <manifest>
                        <mainClass>com.mycompany.App</mainClass>
                    </manifest>
                </archive>
            </configuration>
        </plugin>

        <!-- Run application -->
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>3.1.0</version>
            <configuration>
                <mainClass>com.mycompany.App</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### Creating Fat JAR (with dependencies)

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>3.5.0</version>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
            <configuration>
                <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>com.mycompany.App</mainClass>
                    </transformer>
                </transformers>
            </configuration>
        </execution>
    </executions>
</plugin>
```

---

## Complete pom.xml Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>task-manager</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>Task Manager</name>
    <description>A simple task management application</description>

    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <!-- Dependency versions -->
        <junit.version>5.10.0</junit.version>
        <gson.version>2.10.1</gson.version>
        <slf4j.version>2.0.9</slf4j.version>
    </properties>

    <dependencies>
        <!-- JSON Processing -->
        <dependency>
            <groupId>com.google.code.gson</groupId>
            <artifactId>gson</artifactId>
            <version>${gson.version}</version>
        </dependency>

        <!-- Logging -->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>${slf4j.version}</version>
        </dependency>

        <!-- Testing -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>${junit.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Compiler -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
            </plugin>

            <!-- Surefire for testing -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.1.2</version>
            </plugin>

            <!-- Create executable JAR -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.5.0</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>com.example.taskmanager.App</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

---

## Multi-Module Projects

For larger applications, split into modules:

```
parent-project/
├── pom.xml (parent)
├── common/
│   ├── pom.xml
│   └── src/
├── api/
│   ├── pom.xml
│   └── src/
└── web/
    ├── pom.xml
    └── src/
```

### Parent pom.xml

```xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>common</module>
        <module>api</module>
        <module>web</module>
    </modules>

    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!-- Centralize versions here -->
            <dependency>
                <groupId>org.junit.jupiter</groupId>
                <artifactId>junit-jupiter</artifactId>
                <version>5.10.0</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

### Child pom.xml

```xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0.0-SNAPSHOT</version>
    </parent>

    <artifactId>common</artifactId>

    <dependencies>
        <!-- Version inherited from parent -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

---

## Profiles

Customize builds for different environments:

```xml
<profiles>
    <profile>
        <id>development</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <env>dev</env>
        </properties>
    </profile>

    <profile>
        <id>production</id>
        <properties>
            <env>prod</env>
        </properties>
        <build>
            <plugins>
                <!-- Additional plugins for production -->
            </plugins>
        </build>
    </profile>
</profiles>
```

Activate profiles:
```bash
mvn package -P production
mvn package -P development,testing
```

---

## IDE Integration

### IntelliJ IDEA

1. File → Open → Select pom.xml
2. Open as Project
3. IntelliJ automatically imports dependencies

### VS Code

1. Install "Extension Pack for Java"
2. Open folder containing pom.xml
3. VS Code detects and configures automatically

### Eclipse

1. File → Import → Maven → Existing Maven Projects
2. Select directory containing pom.xml
3. Eclipse imports project

---

## Hands-On Project: Setting Up a Complete Project

Create a Task Manager application with proper Maven structure:

```bash
# Create project
mvn archetype:generate \
  -DgroupId=com.example \
  -DartifactId=task-manager \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DarchetypeVersion=1.4 \
  -DinteractiveMode=false

cd task-manager
```

Project structure after setup:

```
task-manager/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/taskmanager/
│   │   │       ├── App.java
│   │   │       ├── model/
│   │   │       │   └── Task.java
│   │   │       ├── service/
│   │   │       │   └── TaskService.java
│   │   │       └── repository/
│   │   │           └── TaskRepository.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/
│       └── java/
│           └── com/example/taskmanager/
│               └── service/
│                   └── TaskServiceTest.java
└── target/
```

---

## AI Integration: Build Configuration Mistakes

### Problem 1: Missing Java Version

**AI generates:**
```xml
<project>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>
    <!-- No Java version specified! -->
</project>
```

**Fix:**
```xml
<properties>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
</properties>
```

### Problem 2: Outdated Dependencies

**AI generates:**
```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>  <!-- Old JUnit 4! -->
</dependency>
```

**Fix:**
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>  <!-- Modern JUnit 5 -->
    <scope>test</scope>
</dependency>
```

### Problem 3: Wrong Scope

**AI generates:**
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <!-- Missing scope - included in production JAR! -->
</dependency>
```

**Fix:**
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>  <!-- Only for testing -->
</dependency>
```

### Problem 4: Hardcoded Versions Everywhere

**AI generates:**
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>6.1.0</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>6.1.0</version>  <!-- Duplicated! -->
    </dependency>
</dependencies>
```

**Fix:**
```xml
<properties>
    <spring.version>6.1.0</spring.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>${spring.version}</version>
    </dependency>
</dependencies>
```

---

## Exercises

### Exercise 12.1: Create Project
Create a Maven project from scratch for a "Library Management" application with proper structure.

### Exercise 12.2: Add Dependencies
Add these dependencies to your project:
- Gson for JSON
- SLF4J for logging
- JUnit 5 for testing

### Exercise 12.3: Create Fat JAR
Configure your pom.xml to create an executable JAR with all dependencies included.

### Exercise 12.4: Multi-Module
Split a project into `core`, `api`, and `app` modules with proper parent-child relationships.

### Exercise 12.5: Fix AI pom.xml
Fix the issues in this AI-generated pom.xml:

```xml
<project>
    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0</version>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>31.0-jre</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

---

## Key Takeaways

- **Follow standard structure** - src/main/java, src/test/java
- **Use Maven for builds** - Dependency management, compilation, testing
- **pom.xml is the heart** - Defines everything about your project
- **Manage versions centrally** - Use properties for versions
- **Scope dependencies correctly** - test for testing, provided for containers
- **Create executable JARs** - Use shade plugin for fat JARs
- **Check AI-generated pom.xml** - Often has outdated or wrong configurations

---

## What's Next

You can now structure and build professional Java projects. In **Chapter 13**, we'll explore File I/O - reading and writing files, working with paths, and handling different file formats.

---

*"Maven isn't just a build tool - it's a contract that tells everyone how to build your project."*
