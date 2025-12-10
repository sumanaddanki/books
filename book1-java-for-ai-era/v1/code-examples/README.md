# Code Examples - Java for the AI Era

## Repository Structure

```
code-examples/
├── chapter-02-setup/
│   └── HelloWorld.java
├── chapter-03-variables/
│   ├── DataTypesDemo.java
│   ├── TypeCasting.java
│   └── Calculator.java
├── chapter-04-control-flow/
│   ├── IfElseExamples.java
│   ├── SwitchDemo.java
│   ├── LoopsDemo.java
│   └── GuessingGame.java
├── chapter-05-oop-basics/
│   ├── Student.java
│   ├── StudentManager.java
│   └── StaticVsInstance.java
├── chapter-06-oop-pillars/
│   ├── encapsulation/
│   │   ├── BankAccount.java
│   │   └── BankAccountTest.java
│   ├── inheritance/
│   │   ├── Person.java
│   │   ├── Student.java
│   │   └── Teacher.java
│   ├── polymorphism/
│   │   ├── Animal.java
│   │   ├── Dog.java
│   │   ├── Cat.java
│   │   └── AnimalSounds.java
│   └── abstraction/
│       ├── Character.java
│       ├── Warrior.java
│       ├── Mage.java
│       └── GameDemo.java
├── chapter-07-exceptions/
│   ├── BasicExceptions.java
│   ├── CustomException.java
│   ├── TryWithResources.java
│   └── ExceptionBestPractices.java
├── chapter-08-collections/
│   ├── ListExamples.java
│   ├── SetExamples.java
│   ├── MapExamples.java
│   └── InventorySystem.java
├── chapter-09-generics/
│   ├── GenericClass.java
│   ├── GenericMethods.java
│   ├── BoundedTypes.java
│   └── WildcardDemo.java
├── chapter-10-streams/
│   ├── LambdaBasics.java
│   ├── StreamOperations.java
│   ├── Collectors.java
│   └── DataProcessor.java
├── chapter-11-concurrency/
│   ├── ThreadBasics.java
│   ├── RaceCondition.java
│   ├── SynchronizationDemo.java
│   └── ExecutorServiceDemo.java
├── chapter-12-project-structure/
│   └── maven-project-template/
│       ├── pom.xml
│       └── src/
├── chapter-13-file-io/
│   ├── FileReading.java
│   ├── FileWriting.java
│   ├── JsonProcessing.java
│   └── ConfigReader.java
├── chapter-14-testing/
│   ├── CalculatorTest.java
│   ├── StudentTest.java
│   └── ParameterizedTestDemo.java
├── chapter-15-ai-prompting/
│   └── prompt-templates.md
├── chapter-16-code-review/
│   ├── ai-generated-samples/
│   │   ├── sample1-with-bugs.java
│   │   ├── sample2-security-issue.java
│   │   └── sample3-performance.java
│   └── review-checklist.md
└── chapter-17-final-project/
    └── task-manager/
        ├── pom.xml
        └── src/
```

## How to Use This Repository

### For Readers:
1. Clone this repository
2. Each chapter folder contains runnable examples
3. Follow along with the book
4. Try the exercises before checking solutions

### For Video Viewers:
1. Code demonstrated in videos is tagged with video numbers
2. `git checkout video-XX` to see code at that point
3. Follow along and pause to type yourself

### Running Examples:

**Single file:**
```bash
cd chapter-XX-topic
javac FileName.java
java FileName
```

**Maven projects:**
```bash
cd project-folder
mvn compile
mvn exec:java
```

**Running tests:**
```bash
mvn test
```

## Exercises

Each chapter has exercises in a separate `exercises/` folder:
- `exercise-description.md` - What to build
- `starter-code/` - Template to start with
- `solution/` - Reference solution (try yourself first!)

## AI Comparison Samples

The `ai-comparison/` folder in each chapter contains:
- Human-written solution
- AI-generated solution
- Analysis of differences
- Discussion questions
