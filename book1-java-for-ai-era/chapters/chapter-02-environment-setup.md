# Chapter 2: Setting Up Your Java Environment

> "A craftsman is only as good as their tools - and their understanding of those tools."

---

## What You'll Learn

- The difference between JDK, JRE, and JVM
- How to install Java on Windows, Mac, and Linux
- Setting up environment variables correctly
- Choosing and configuring your IDE
- Writing and running your first Java program
- Understanding the compilation process

---

## Why Setup Matters

You might be thinking: "Can't I just use an online compiler?"

Sure, you could. But here's why we're doing this properly:

1. **Professional development happens locally** - Real jobs require local setups
2. **Understanding the toolchain** - Knowing what happens behind "Run" button
3. **Debugging capability** - Online compilers hide important information
4. **No AI shortcuts** - Setup is where many developers first reach for AI help

This chapter might feel slow. That's intentional. We're building understanding, not just a working setup.

---

## The Java Ecosystem: JVM, JRE, and JDK

Before installing anything, let's understand what we're installing.

### The Java Virtual Machine (JVM)

The JVM is Java's secret sauce. It's why Java code can run on any operating system.

**The Traditional Way (C/C++):**
```
Source Code → Compile → Machine Code (specific to Windows/Mac/Linux)
```
You need different compiled versions for each operating system.

**The Java Way:**
```
Source Code → Compile → Bytecode → JVM → Machine Code
```

Java compiles to "bytecode" - an intermediate format. The JVM then translates bytecode to machine code for whatever system it's running on.

**Analogy:** Think of bytecode as a universal recipe. The JVM is a chef who can cook that recipe in any kitchen (operating system), adapting to whatever equipment is available.

### The Java Runtime Environment (JRE)

The JRE = JVM + Standard Libraries

It's everything needed to **run** Java programs:
- The JVM itself
- Core libraries (collections, I/O, networking, etc.)
- Supporting files

If someone just wants to run Java applications (not develop them), they only need the JRE.

### The Java Development Kit (JDK)

The JDK = JRE + Development Tools

It's everything needed to **develop** Java programs:
- Everything in JRE
- Compiler (`javac`)
- Debugger
- Documentation generator
- Other development tools

**As developers, we need the JDK.**

### Visual Summary

```
┌─────────────────────────────────────────────┐
│                    JDK                       │
│  ┌───────────────────────────────────────┐  │
│  │                JRE                     │  │
│  │  ┌─────────────────────────────────┐  │  │
│  │  │              JVM                 │  │  │
│  │  │   (Runs bytecode on any OS)     │  │  │
│  │  └─────────────────────────────────┘  │  │
│  │  + Standard Libraries                 │  │
│  └───────────────────────────────────────┘  │
│  + Compiler (javac)                          │
│  + Debugger, Documentation tools             │
└─────────────────────────────────────────────┘
```

---

## Choosing a JDK: OpenJDK vs Oracle JDK

You'll encounter different JDK distributions. Here's what you need to know:

### OpenJDK
- **Open source** - Free to use anywhere
- **Community maintained** - With Oracle's involvement
- **The reference implementation** - What "Java" officially is
- **Recommended for learning and most development**

### Oracle JDK
- **Commercial** - Free for development, licensing for production
- **Oracle maintained** - With additional tooling
- **Identical codebase** - Same code as OpenJDK (since Java 11)

### Other Distributions
- **Amazon Corretto** - Amazon's OpenJDK build
- **Adoptium (Eclipse Temurin)** - Community builds
- **Azul Zulu** - Another popular distribution

**For this book: Use OpenJDK or Adoptium.** They're free, reliable, and what most companies use.

---

## Java Versions: Which One?

Java has a 6-month release cycle. As of this writing:

| Version | Type | Recommendation |
|---------|------|----------------|
| Java 8 | LTS | Legacy, avoid for new projects |
| Java 11 | LTS | Minimum for modern development |
| Java 17 | LTS | **Recommended for this book** |
| Java 21 | LTS | Latest LTS, also excellent choice |

**LTS = Long Term Support** - These versions get security updates for years.

**We'll use Java 17 or 21** - Modern features, long-term support, widely used.

---

## Installation: Windows

### Step 1: Download the JDK

1. Go to https://adoptium.net/
2. Select your operating system (Windows)
3. Choose version (17 or 21 LTS)
4. Download the `.msi` installer

### Step 2: Run the Installer

1. Double-click the downloaded `.msi` file
2. Follow the installation wizard
3. **Important:** Check "Set JAVA_HOME variable" if offered
4. Note the installation path (usually `C:\Program Files\Eclipse Adoptium\jdk-17...`)

### Step 3: Set Environment Variables (if not automatic)

1. Open Start Menu, search "Environment Variables"
2. Click "Edit the system environment variables"
3. Click "Environment Variables" button
4. Under "System variables":

**Create JAVA_HOME:**
- Click "New"
- Variable name: `JAVA_HOME`
- Variable value: `C:\Program Files\Eclipse Adoptium\jdk-17.0.x` (your actual path)

**Update PATH:**
- Find "Path" in system variables, click "Edit"
- Click "New"
- Add: `%JAVA_HOME%\bin`

### Step 4: Verify Installation

Open Command Prompt (cmd) and run:

```cmd
java -version
```

You should see:
```
openjdk version "17.0.x" 2023-xx-xx
OpenJDK Runtime Environment Temurin-17.0.x+x (build 17.0.x+x)
OpenJDK 64-Bit Server VM Temurin-17.0.x+x (build 17.0.x+x, mixed mode, sharing)
```

Also verify the compiler:
```cmd
javac -version
```

Output: `javac 17.0.x`

**If you see "not recognized as a command":** Your PATH isn't set correctly. Go back to Step 3.

---

## Installation: macOS

### Option 1: Using Homebrew (Recommended)

If you don't have Homebrew, install it first:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then install Java:
```bash
brew install openjdk@17
```

Create a symlink so the system finds it:
```bash
sudo ln -sfn $(brew --prefix)/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

### Option 2: Manual Installation

1. Go to https://adoptium.net/
2. Download the `.pkg` installer for macOS
3. Run the installer
4. Follow the prompts

### Set JAVA_HOME

Add to your `~/.zshrc` (or `~/.bash_profile` for older Macs):

```bash
export JAVA_HOME=$(/usr/libexec/java_home -v 17)
export PATH=$JAVA_HOME/bin:$PATH
```

Reload your shell:
```bash
source ~/.zshrc
```

### Verify Installation

```bash
java -version
javac -version
```

---

## Installation: Linux (Ubuntu/Debian)

### Using APT

```bash
# Update package list
sudo apt update

# Install OpenJDK 17
sudo apt install openjdk-17-jdk

# Verify
java -version
javac -version
```

### Set JAVA_HOME

Find where Java is installed:
```bash
sudo update-alternatives --config java
```

Note the path (like `/usr/lib/jvm/java-17-openjdk-amd64`)

Add to `~/.bashrc`:
```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$JAVA_HOME/bin:$PATH
```

Reload:
```bash
source ~/.bashrc
```

---

## Understanding JAVA_HOME

Why do we set `JAVA_HOME`?

1. **Build tools need it** - Maven, Gradle look for JAVA_HOME
2. **IDEs use it** - To find the correct JDK
3. **Scripts reference it** - Standard way to locate Java
4. **Multiple Java versions** - Easily switch by changing JAVA_HOME

**Test it's set correctly:**

Windows:
```cmd
echo %JAVA_HOME%
```

Mac/Linux:
```bash
echo $JAVA_HOME
```

Should print your JDK installation path.

---

## Your First Java Program: The Traditional Way

Before using any IDE, let's understand what happens underneath.

### Step 1: Create the Source File

Open any text editor (Notepad, TextEdit, nano - not Word!).

Type exactly this:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

**Important rules:**
- File name MUST match class name: `HelloWorld.java`
- Java is case-sensitive: `HelloWorld` ≠ `helloworld`
- Save with `.java` extension

Save the file as `HelloWorld.java` in a folder you can easily find (e.g., `C:\java-practice` or `~/java-practice`).

### Step 2: Compile

Open terminal/command prompt. Navigate to your folder:

```bash
cd ~/java-practice    # Mac/Linux
cd C:\java-practice   # Windows
```

Compile:
```bash
javac HelloWorld.java
```

If successful, you'll see... nothing! No output means no errors.

But look in your folder - there's a new file: `HelloWorld.class`

This `.class` file contains **bytecode** - the compiled version of your program.

### Step 3: Run

```bash
java HelloWorld
```

Note: No `.class` extension when running!

Output:
```
Hello, World!
```

### What Just Happened?

```
HelloWorld.java  →  javac  →  HelloWorld.class  →  java  →  "Hello, World!"
   (source)       (compile)     (bytecode)        (run)      (output)
```

1. `javac` (Java Compiler) read your source code
2. Checked for syntax errors
3. Generated bytecode in `.class` file
4. `java` command started the JVM
5. JVM loaded and executed the bytecode
6. Output appeared on screen

**This is what IDEs do automatically** - but now you understand what's happening.

---

## Anatomy of Hello World

Let's break down every part:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

### `public class HelloWorld`
- `public` - Accessible from anywhere
- `class` - Everything in Java lives in a class
- `HelloWorld` - The name (must match filename)

### `public static void main(String[] args)`
This is the **entry point** - where your program starts.

- `public` - JVM needs to access this from outside
- `static` - Can be called without creating an object
- `void` - Returns nothing
- `main` - Special name the JVM looks for
- `String[] args` - Command-line arguments (we'll use later)

### `System.out.println("Hello, World!");`
- `System` - A built-in class
- `out` - The standard output stream
- `println` - Print line (adds newline at end)
- `"Hello, World!"` - A String (text in double quotes)
- `;` - Statements end with semicolon

---

## Choosing an IDE

Now that you understand what happens behind the scenes, let's set up a proper development environment.

### IntelliJ IDEA (Recommended)

**Why IntelliJ:**
- Most popular Java IDE
- Excellent code completion
- Great debugging tools
- Free Community Edition is sufficient

**Installation:**
1. Go to https://www.jetbrains.com/idea/download/
2. Download **Community Edition** (free)
3. Run installer, follow prompts
4. On first launch, select "Do not import settings"

**First Project:**
1. Click "New Project"
2. Select "Java" on the left
3. Ensure JDK is detected (should show your Java 17/21)
4. Click Next, Next
5. Name: `java-practice`, click Finish
6. Right-click `src` → New → Java Class → `HelloWorld`
7. Type the code, right-click → Run

### VS Code Alternative

If you prefer VS Code:

1. Install VS Code
2. Install "Extension Pack for Java" extension
3. Create a folder, open in VS Code
4. Create `HelloWorld.java`
5. Click "Run" above the main method

VS Code is lighter but IntelliJ has better Java-specific features.

---

## IDE vs Command Line: Know Both

Many developers only know IDE buttons. When something breaks, they're lost.

**Always understand the command line:**
- IDEs are running `javac` and `java` for you
- Build servers don't have IDEs
- Debugging often requires command-line knowledge
- Job interviews may test this

**Practice both:** Use IDE for productivity, but periodically compile from command line to stay sharp.

---

## Troubleshooting Common Issues

### "java is not recognized as a command"
- JAVA_HOME not set, or not in PATH
- Solution: Revisit environment variable setup

### "javac is not recognized"
- You might have JRE instead of JDK
- Solution: Install JDK, not just JRE

### "Could not find or load main class"
- Wrong directory
- Class name doesn't match filename
- Solution: Check you're in the right folder, names match exactly

### "Class names only accepted if annotation processing is explicitly requested"
- You included `.java` when running: `java HelloWorld.java`
- Solution: Run without extension: `java HelloWorld`

### IDE doesn't find JDK
- JAVA_HOME not set
- Solution: In IDE settings, manually point to JDK location

---

## Exercises

### Exercise 2.1: Verify Your Setup
Run these commands and confirm the output:
```bash
java -version
javac -version
echo $JAVA_HOME   # or %JAVA_HOME% on Windows
```

### Exercise 2.2: Command Line Compilation
Without using an IDE:
1. Create `Greeting.java` with a class that prints your name
2. Compile with `javac`
3. Run with `java`
4. Verify the `.class` file exists

### Exercise 2.3: Intentional Errors
Create a file with each of these errors (one at a time). Try to compile and observe the error messages:

1. Missing semicolon
2. Class name doesn't match filename
3. Missing closing brace
4. `Main` instead of `main`
5. `string` instead of `String`

Understanding error messages is a crucial skill!

### Exercise 2.4: Command Line Arguments
Modify HelloWorld to use command-line arguments:

```java
public class Greeter {
    public static void main(String[] args) {
        if (args.length > 0) {
            System.out.println("Hello, " + args[0] + "!");
        } else {
            System.out.println("Hello, stranger!");
        }
    }
}
```

Compile and run:
```bash
javac Greeter.java
java Greeter Alice
java Greeter
```

---

## Key Takeaways

- **JDK = JRE + Development Tools** - We need JDK for development
- **JVM enables portability** - Write once, run anywhere
- **JAVA_HOME is important** - Build tools and IDEs rely on it
- **Compilation creates bytecode** - `.java` → `javac` → `.class` → `java`
- **Know command line AND IDE** - Both skills are essential
- **Error messages are your friends** - Learn to read them

---

## What's Next

Your environment is ready. You understand what happens when you click "Run."

In **Chapter 3**, we'll dive into the building blocks of Java: variables, data types, and operators. You'll learn why Java's type system is actually a superpower, not a limitation.

No more setup. Time to code.

---

*"The person who knows HOW will always have a job. The person who knows WHY will always be their boss."*
