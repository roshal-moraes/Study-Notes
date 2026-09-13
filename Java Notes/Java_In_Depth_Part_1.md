# Java In-Depth — Rewritten Notes

A practical, easy-to-follow guide to Java fundamentals, from the JVM and first program through object-oriented programming, control flow, strings, packages, and common Java APIs.

---

## Chapter 1 — What Java Is and Why It Works

### 1.1 Java in one idea

Java is a general-purpose, statically typed programming language designed to make software portable, maintainable, and suitable for large applications.

Three ideas explain most of Java:

- **Object-oriented:** programs are organized around classes and objects.
- **Platform-independent:** Java source is compiled to bytecode that can run on any compatible JVM.
- **Managed runtime:** the JVM handles tasks such as memory management and runtime execution.

### 1.2 Why “write once, run anywhere” works

Java source code is not normally compiled directly into one operating system's executable format. Instead:

```text
Hello.java
   |
   | javac
   v
Hello.class   <- Java bytecode
   |
   | JVM
   v
Windows / Linux / macOS / ...
```

The `.class` file is portable, while the JVM implementation is platform-specific.

### 1.3 Strengths and trade-offs

Java gives you automatic memory management, a large standard library, strong tooling, concurrency support, and mature ecosystems. The trade-off is that the JVM and type system add some complexity compared with very small scripting environments.

### 1.4 A better way to think about Java

Do not think of Java as “just an interpreted language.” Modern Java uses a **mixed execution model**: bytecode may initially be interpreted and frequently executed code may be compiled and optimized at runtime by the JIT compiler.

---

## Chapter 2 — Where Java Came From

Java grew out of Sun Microsystems' Green Project in the early 1990s. James Gosling and the team designed a language called **Oak** for software that needed to work across different devices.

The language was later renamed **Java**. The early work emphasized portability, safety, compact programs, and concurrency. The famous Star7 prototype demonstrated the team's vision of interactive consumer devices.

The history matters mainly because it explains Java's design goals:

> A Java program should not have to be rewritten from scratch just because the underlying machine is different.

Sun Microsystems was acquired by Oracle in 2010, and Java development continues through the Java ecosystem and the OpenJDK project.

---

## Chapter 3 — Source Code, Compilation, and Platform Dependency

### 3.1 Three levels to distinguish

It helps to separate these ideas:

1. **Source code** — what a programmer writes.
2. **Bytecode or native code** — code produced by a compiler.
3. **Machine execution** — what the processor ultimately executes.

For Java:

```text
Source code -> javac -> bytecode -> JVM -> native execution
```

For a conventional native C program:

```text
Source code -> C compiler/linker -> native executable -> operating system/CPU
```

### 3.2 Why native executables are platform-dependent

A native executable depends on details such as the operating system, CPU architecture, executable format, and ABI/system interfaces.

For example, on Windows a program may be a PE executable such as `Hello.exe`. On Linux, GCC can produce an ELF executable named simply `Hello`.

The source can remain the same, but the resulting executable is normally built separately for each target.

### 3.3 Incremental compilation

It is too strong to say that every source change forces a complete rebuild. Modern build tools can recompile only affected source files and dependencies.

---

## Chapter 4 — The JVM and Java Bytecode

### 4.1 What `javac` does

Consider:

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

Compile it:

```bash
javac Hello.java
```

This produces `Hello.class`, which contains Java bytecode.

Run it:

```bash
java Hello
```

Notice that `java` takes the **class name**, not `Hello.class`.

### 4.2 What the JVM does

The JVM is the runtime defined by the Java Virtual Machine Specification. A particular JVM implementation is built for a particular platform.

At a high level it:

- loads classes,
- verifies and links them,
- manages runtime memory,
- executes bytecode,
- may JIT-compile frequently executed code,
- works with the garbage collector to reclaim unreachable objects.

### 4.3 Why Java can be fast

The JVM can observe the program while it runs. If a method becomes “hot,” the JIT compiler can optimize it for the current machine.

A simplified picture is:

```text
Bytecode
  |
  +--> Interpreter ---------
  |                         |
  +--> JIT compiler --> optimized native code
                            |
                            v
                           CPU
```

This does not guarantee that every Java program is as fast as C or C++, but it explains why Java can achieve high performance.

---

## Chapter 5 — The Java Platform, JDK, and Releases

### 5.1 Java SE and Jakarta EE

**Java SE** is the core Java platform: the language, standard APIs, and JVM-related specifications used to build general Java programs.

**Jakarta EE** is the modern enterprise platform that evolved from Java EE.

Java ME targets specific resource-constrained environments.

### 5.2 JLS, JVM Specification, and APIs

Three references are especially useful:

- **JLS:** defines the Java language.
- **JVMS:** defines the JVM and class-file execution model.
- **Java API documentation:** explains standard library classes and methods.

### 5.3 JDK and runtime

The **JDK (Java Development Kit)** contains the tools needed for development, including the compiler and runtime components.

For everyday learning, the most important commands are:

```bash
java --version
javac --version
```

### 5.4 Release cycle

Java moved to a six-month feature-release cadence starting with Java 10. Long-term-support releases are provided at intervals, making them common choices for production environments.

For notes intended to age well, prefer commands and concepts that remain stable instead of memorizing vendor-specific installation details.

---

## Chapter 6 — Installing Java and Understanding `PATH`

### 6.1 Install a JDK, not just a runtime

To compile Java programs you need a JDK.

After installation, verify:

```bash
java --version
javac --version
```

### 6.2 `JAVA_HOME` and `PATH`

`JAVA_HOME` is commonly used by tools to point to the JDK directory.

`PATH` tells the operating system where to look for commands such as `java` and `javac`.

A useful mental model is:

```text
JAVA_HOME -> where the JDK is
PATH      -> where the shell looks for commands
```

### 6.3 Avoid hard-coding old assumptions

The layout of Java installations and whether a separate JRE package exists varies by Java generation and distribution. Modern JDKs should be treated as complete development/runtime installations rather than relying on old `rt.jar` or standalone-JRE explanations.

---

## Chapter 7 — Classpath, Modules, and Running Code

### 7.1 What the classpath means

The **classpath** is a search path used by Java tools and the runtime to locate classes and resources.

For a simple program in the current directory:

```bash
java -cp . Hello
```

Multiple locations can be separated with the platform's path separator.

- Windows: `;`
- Linux/macOS: `:`

### 7.2 Do not confuse `PATH` and `CLASSPATH`

- `PATH` helps the shell find commands such as `java`.
- `CLASSPATH` helps Java tools find classes and libraries.

For small programs, it is often clearer to specify `-cp` explicitly than to maintain a global `CLASSPATH` environment variable.

### 7.3 IDEs

IDEs such as IntelliJ IDEA, Eclipse, or VS Code configure classpaths and build tasks for you. You still need to understand the underlying idea because build failures often become easier to diagnose from the command-line model.

---

## Chapter 8 — Your First Java Program

Start with the smallest useful program:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

Save it as `HelloWorld.java`.

Compile:

```bash
javac HelloWorld.java
```

Run:

```bash
java HelloWorld
```

### 8.1 Reading the `main` method

```java
public static void main(String[] args)
```

- `public` — the launcher can access the method.
- `static` — the method belongs to the class, so no object is required to call it.
- `void` — it returns no value.
- `String[] args` — receives command-line arguments.

### 8.2 A more useful example

```java
public class Greeting {
    public static void main(String[] args) {
        String name = "Maya";
        System.out.println("Hello, " + name + "!");
    }
}
```

Now the example introduces a variable and string concatenation without introducing unnecessary syntax.

---

## Chapter 9 — Classes and Objects

Java programs are built from classes, but a class and an object are not the same thing.

A **class** describes what objects of that type can contain and do.

An **object** is an instance created from that class.

Example:

```java
class Student {
    String name;
    int age;

    void introduce() {
        System.out.println("I am " + name + ", age " + age);
    }
}
```

Create and use an object:

```java
Student s = new Student();
s.name = "Aisha";
s.age = 20;
s.introduce();
```

Here:

- `Student` is the type.
- `s` is a reference variable.
- `new Student()` creates an object.

A class can define **state** (fields) and **behavior** (methods).

---

## Chapter 10 — Java Syntax Basics

### 10.1 Identifiers

Identifiers may contain letters, digits, `_`, and `$`, but they cannot begin with a digit and cannot be keywords.

Prefer readable names:

```java
int studentCount;
String firstName;
```

Avoid:

```java
int x1;
String a;
```

when the meaning is not obvious.

Java is case-sensitive:

```java
age
Age
AGE
```

are different identifiers.

### 10.2 Output

```java
System.out.print("Hello ");
System.out.println("Java");
```

`println` prints and then moves to a new line. `print` does not.

### 10.3 Comments

```java
// One line

/*
   Multiple lines
*/
```

Use comments to explain **why**, not merely to restate obvious code.

### 10.4 Arithmetic

```java
int total = 10 + 5;
int remainder = 17 % 5;
```

The `%` operator gives the remainder. It is useful for problems such as checking whether a number is even:

```java
boolean even = number % 2 == 0;
```

---

## Chapter 11 — Variables and the Primitive Types

### 11.1 Declaration and assignment

```java
int age;
age = 21;
```

Or combine them:

```java
int age = 21;
```

### 11.2 Java's eight primitive types

| Type | Purpose | Typical size |
|---|---|---:|
| `byte` | small integer | 8 bits |
| `short` | small integer | 16 bits |
| `int` | general integer | 32 bits |
| `long` | large integer | 64 bits |
| `float` | approximate decimal | 32 bits |
| `double` | higher-precision decimal | 64 bits |
| `char` | UTF-16 code unit | 16 bits |
| `boolean` | `true` / `false` | JVM-dependent representation |

For ordinary integer calculations, `int` is usually the best default. For decimal calculations, `double` is the usual default.

### 11.3 Local variables are special

Fields get default values; local variables do not.

This works:

```java
class Example {
    int count; // defaults to 0
}
```

This does not:

```java
public static void main(String[] args) {
    int count;
    System.out.println(count); // compile-time error
}
```

Initialize a local variable before using it.

### 11.4 `final`

Use `final` when a variable should not be assigned again:

```java
final double TAX_RATE = 0.20;
```

For constants, uppercase names with underscores are conventional.

---

## Chapter 12 — Integer, Floating-Point, `char`, and `boolean`

### 12.1 Integer literals

```java
int million = 1_000_000;
long population = 8_000_000_000L;
```

The `L` suffix marks a `long` literal.

Java also supports hexadecimal and other integer literal forms:

```java
int mask = 0xFF;
```

### 12.2 Floating-point numbers

```java
double price = 19.99;
float ratio = 3.5F;
```

Floating-point values are approximate. Do not use `double` for money when exact decimal arithmetic is required; use `BigDecimal` or another domain-appropriate representation.

### 12.3 `char`

A `char` stores a UTF-16 code unit:

```java
char grade = 'A';
```

Remember the difference:

```java
char letter = 'A';
String word = "Apple";
```

A `char` uses single quotes; a `String` uses double quotes.

### 12.4 `boolean`

```java
boolean loggedIn = true;
```

Java does not treat integers such as `0` and `1` as booleans.

---

## Chapter 13 — Type Casting and Numeric Conversion

Java is statically typed, so values cannot be freely mixed between unrelated types.

### 13.1 Widening conversion

A smaller numeric type can usually be converted to a larger compatible type automatically:

```java
int count = 10;
long total = count;
double value = total;
```

### 13.2 Narrowing conversion

A narrowing conversion usually needs an explicit cast:

```java
long big = 1000L;
int small = (int) big;
```

If the value does not fit, information can be lost.

```java
long big = 3_000_000_000L;
int small = (int) big; // value changes
```

### 13.3 Integer division

This is an important beginner trap:

```java
System.out.println(5 / 2);       // 2
System.out.println(5.0 / 2);     // 2.5
```

The first expression performs integer division. The second has a floating-point operand.

### 13.4 A useful cast example

```java
int a = 5;
int b = 2;
double result = (double) a / b;
```

The cast changes the arithmetic to floating-point division.

---

## Chapter 14 — References, Objects, `null`, and the Heap

Primitive variables store primitive values. Reference variables store references to objects.

```java
Student s = new Student();
```

A useful conceptual picture is:

```text
s  ---->  Student object
```

The variable `s` is not the object itself.

### 14.1 `null`

A reference can hold `null`:

```java
Student s = null;
```

Calling an instance method through a null reference causes a `NullPointerException`:

```java
s.introduce(); // throws NullPointerException
```

### 14.2 Garbage collection

When an object is no longer reachable, it becomes eligible for garbage collection. You should not think of `System.gc()` as a command that guarantees immediate collection.

### 14.3 Avoid pretending Java references are raw addresses

A JVM reference is an implementation-level reference. Its exact representation and size are JVM-specific. For beginner explanations, “reference to an object” is more accurate and safer than equating every reference with a C-style memory address.

---

## Chapter 15 — Statements, Expressions, and Scope

An **expression** produces a value:

```java
2 + 3
age > 18
name + "!"
```

A **statement** performs an action:

```java
age = 21;
System.out.println(age);
```

### 15.1 Blocks and scope

Variables declared inside a block are normally visible only inside that block:

```java
if (true) {
    int score = 100;
    System.out.println(score);
}

// score is not visible here
```

Understanding scope prevents many “variable cannot be found” errors.

---

## Chapter 16 — Operators

### 16.1 Arithmetic

```java
+  -  *  /  %
```

### 16.2 Relational

```java
==  !=  >  <  >=  <=
```

### 16.3 Logical

```java
&&  ||  !
```

Example:

```java
if (age >= 18 && hasId) {
    System.out.println("Allowed");
}
```

### 16.4 Assignment

```java
x = 10;
x += 5;
x *= 2;
```

### 16.5 Increment and decrement

```java
count++;
count--;
```

Prefer simple expressions while learning. Compact expressions can hide evaluation order and make bugs harder to see.

### 16.6 `instanceof`

Use `instanceof` to test whether a reference is compatible with a type:

```java
if (value instanceof String s) {
    System.out.println(s.length());
}
```

---

## Chapter 17 — Conditions and `switch`

### 17.1 `if` / `else`

```java
int score = 82;

if (score >= 90) {
    System.out.println("A");
} else if (score >= 80) {
    System.out.println("B");
} else {
    System.out.println("C or below");
}
```

Write conditions so that the reader can follow the decision from top to bottom.

### 17.2 Ternary operator

For a short value choice:

```java
String status = age >= 18 ? "adult" : "minor";
```

Avoid deeply nested ternary expressions.

### 17.3 `switch`

For a small set of discrete choices:

```java
int day = 2;

switch (day) {
    case 1 -> System.out.println("Monday");
    case 2 -> System.out.println("Tuesday");
    default -> System.out.println("Other day");
}
```

Modern `switch` can also be an expression:

```java
String label = switch (day) {
    case 1 -> "Mon";
    case 2 -> "Tue";
    default -> "Other";
};
```

This form is often clearer because every branch produces a value.

---

## Chapter 18 — Loops: `for`, `while`, and `do-while`

### 18.1 `for`

Use `for` when the loop has a clear counter or iteration structure:

```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}
```

### 18.2 Enhanced `for`

For arrays and collections:

```java
int[] scores = {90, 82, 77};

for (int score : scores) {
    System.out.println(score);
}
```

### 18.3 `while`

Use `while` when the number of iterations is not known in advance:

```java
while (balance > 0) {
    balance -= 10;
}
```

### 18.4 `do-while`

Use `do-while` when the body must execute at least once:

```java
do {
    System.out.println("Menu");
} while (choice != 0);
```

### 18.5 `break` and `continue`

```java
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;
    }
}
```

`break` exits the loop. `continue` skips to the next iteration.

Use them when they make the control flow clearer, not as a replacement for a well-structured loop.

---

## Chapter 19 — Arrays

An array stores a fixed number of values of one type.

```java
int[] scores = {90, 82, 77};
```

Indexes start at zero:

```java
System.out.println(scores[0]); // 90
```

### 19.1 Creating an array with a fixed size

```java
int[] scores = new int[5];
```

For an array of `int`, elements initially contain `0`.

### 19.2 Common array operations

```java
scores[2] = 95;
System.out.println(scores.length);
```

The `length` field gives the number of elements.

### 19.3 Common mistake

This causes an exception when the array has length 3:

```java
scores[3] = 100; // ArrayIndexOutOfBoundsException
```

Valid indexes are `0` through `length - 1`.

### 19.4 Multidimensional arrays

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6}
};
```

Java's multidimensional arrays are arrays of arrays, so rows do not have to be the same length.

---

## Chapter 20 — Methods: Make Code Reusable

A method packages a piece of behavior behind a name.

```java
static int add(int a, int b) {
    return a + b;
}
```

Call it:

```java
int total = add(10, 20);
```

### 20.1 Method structure

```java
[modifiers] returnType methodName(parameters) {
    // body
}
```

### 20.2 `void`

A method that returns nothing can use `void`:

```java
static void greet(String name) {
    System.out.println("Hello, " + name);
}
```

### 20.3 Java is pass-by-value

Java always passes arguments **by value**. For object references, the value being copied is the reference.

```java
static void rename(Student s) {
    s.name = "Nora";
}
```

The method receives a copy of the reference, but both references can refer to the same object. That is why the object's field can change.

However, reassigning the parameter does not replace the caller's reference:

```java
static void replace(Student s) {
    s = new Student();
}
```

The caller's variable is unchanged.

### 20.4 Method overloading

Methods can share a name when their parameter lists differ:

```java
static int max(int a, int b) {
    return a > b ? a : b;
}

static double max(double a, double b) {
    return a > b ? a : b;
}
```

The return type alone is not enough to overload a method.

---

## Chapter 21 — Constructors and Object Initialization

A constructor runs when an object is created.

```java
class Student {
    String name;
    int age;

    Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

Create an object:

```java
Student s = new Student("Maya", 21);
```

### 21.1 `this`

Inside an instance method or constructor, `this` refers to the current object.

```java
this.name = name;
```

The left side is the field; the right side is the parameter.

### 21.2 Constructor overloading

```java
Student() {
    this("Unknown", 0);
}

Student(String name, int age) {
    this.name = name;
    this.age = age;
}
```

Use `this(...)` to delegate from one constructor to another.

### 21.3 A common misconception

Constructors do not have a return type, not even `void`.

---

## Chapter 22 — Initialization Order

When creating an object, initialization follows a defined order involving superclass initialization, instance field initializers, instance initializer blocks, and the constructor.

For a simple class:

```java
class Counter {
    int value = 10;

    {
        System.out.println("initializer");
    }

    Counter() {
        System.out.println("constructor");
    }
}
```

The initializer block runs as part of object construction before the constructor body.

Initializer blocks are legal but less common than explicit constructors and field initializers. Prefer the simplest form that communicates the intent.

---

## Chapter 23 — Encapsulation and Access Control

Encapsulation means that a class controls how its internal state is accessed and changed.

Instead of exposing a mutable field directly:

```java
class BankAccount {
    private double balance;

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    public double getBalance() {
        return balance;
    }
}
```

This allows the class to protect its invariants.

### 23.1 Access levels

- `private` — accessible only within the class.
- package-private — accessible within the same package.
- `protected` — accessible in the same package and, with additional inheritance rules, in subclasses.
- `public` — accessible wherever the type/member is accessible.

For most fields, `private` is a strong default.

---

## Chapter 24 — Packages and Imports

Packages organize related types and help avoid naming conflicts.

```java
package com.example.billing;

public class Invoice {
}
```

Another class can use it through an import:

```java
import com.example.billing.Invoice;
```

Then:

```java
Invoice invoice = new Invoice();
```

### 24.1 Directory layout

A package such as:

```text
com.example.billing
```

normally maps to directories such as:

```text
com/example/billing
```

Build tools and IDEs handle much of this automatically, but understanding the mapping helps when diagnosing classpath errors.

---

## Chapter 25 — Strings

`String` is a class, not a primitive type.

```java
String name = "Maya";
```

### 25.1 Strings are immutable

This:

```java
String text = "Hello";
text = text + " Java";
```

creates a new string value rather than changing the original string object.

### 25.2 Comparing strings

Do not use `==` to compare string contents.

Use:

```java
String a = "hello";
String b = new String("hello");

System.out.println(a.equals(b)); // true
```

`==` compares references; `equals` compares contents for `String`.

### 25.3 Useful methods

```java
String text = "  Java Programming  ";

text.length();
text.trim();
text.toLowerCase();
text.contains("Java");
text.substring(2, 6);
```

### 25.4 `StringBuilder`

When repeatedly building a string in a loop, `StringBuilder` is often clearer and more efficient:

```java
StringBuilder sb = new StringBuilder();

for (int i = 1; i <= 3; i++) {
    sb.append(i).append(' ');
}

System.out.println(sb);
```

---

## Chapter 26 — Escape Sequences and Text Formatting

Common escapes include:

```text
\n  newline
\t  tab
\\  backslash
\"  double quote
\'  single quote
```

Example:

```java
System.out.println("Name:\tMaya\nAge:\t21");
```

Java also supports text blocks in modern releases:

```java
String message = """
    Hello,
    Java!
    """;
```

Use text blocks when preserving multi-line text is clearer than a long string containing escape sequences.

---

## Chapter 27 — The Java API

The Java API is a large collection of classes and interfaces that you can reuse instead of writing common functionality from scratch.

A few important examples:

- `String` — text
- `Math` — common mathematical operations
- `Arrays` — array utilities
- `List` / `Set` / `Map` — collections
- `Objects` — common reference utilities

### 27.1 Read documentation by asking four questions

When learning an unfamiliar class, look for:

1. What does it represent?
2. How do I create or obtain an instance?
3. Which methods solve my problem?
4. What exceptions or special cases should I expect?

Example:

```java
int larger = Math.max(10, 25);
double root = Math.sqrt(81);
```

Good Java development means knowing how to use the library, not memorizing every method.

---

## Chapter 28 — The `Math` Class

Common methods include:

```java
Math.abs(-5);      // 5
Math.max(10, 20);  // 20
Math.min(10, 20);  // 10
Math.sqrt(25);     // 5.0
Math.pow(2, 3);    // 8.0
```

### 28.1 Random numbers

For basic examples, `Math.random()` returns a value in the interval `[0.0, 1.0)`.

```java
int die = (int) (Math.random() * 6) + 1;
```

For serious applications, use the appropriate API such as `Random`, `ThreadLocalRandom`, or a security-oriented random source depending on the requirement.

---

## Chapter 29 — Boxing, Unboxing, and Wrapper Types

Every primitive type has a corresponding wrapper class:

```text
int     -> Integer
long    -> Long
double  -> Double
boolean -> Boolean
char    -> Character
```

### 29.1 Boxing

```java
Integer count = 10;
```

The primitive `10` is boxed into an `Integer` object.

### 29.2 Unboxing

```java
Integer count = 10;
int value = count;
```

The wrapper is unboxed to a primitive.

### 29.3 Why wrappers matter

Generics work with reference types, not primitives:

```java
List<Integer> numbers = new ArrayList<>();
numbers.add(10);
```

Autoboxing makes this convenient, but remember that wrapper values can be `null`:

```java
Integer x = null;
int y = x; // NullPointerException
```

---

## Chapter 30 — Coding Conventions and Writing Better Java

Correct code is only the first step. Good Java code should also be easy to read.

### 30.1 Naming

Use descriptive names:

```java
int retryCount;
String customerName;
```

Common conventions:

- classes: `PascalCase`
- methods/variables: `camelCase`
- constants: `UPPER_SNAKE_CASE`

### 30.2 Prefer simple methods

A method should ideally do one clear job.

Instead of:

```java
processEverything();
```

prefer focused methods whose names reveal what they do:

```java
validateOrder();
calculateTotal();
saveOrder();
```

### 30.3 Prefer clarity over cleverness

A short but cryptic expression is not necessarily better than three readable lines.

### 30.4 Comments should add information

Weak comment:

```java
// increment i by 1
i++;
```

Better comment:

```java
// Retry at most three times because the service is occasionally unavailable.
```

---

# Quick Reference — The Java Learning Path

When learning Java, follow this order:

1. **Write and run a program.**
2. **Understand variables and types.**
3. **Learn expressions and operators.**
4. **Use `if` and `switch` for decisions.**
5. **Use loops and arrays for repetition and data.**
6. **Create methods to reuse logic.**
7. **Create classes, objects, and constructors.**
8. **Understand references, `null`, and object state.**
9. **Use packages and access control to organize code.**
10. **Use the Java API instead of reinventing common functionality.**

A compact mental model is:

```text
Java source
    |
    v
   javac
    |
    v
 Java bytecode
    |
    v
    JVM
    |
    +--> interpreter
    |
    +--> JIT compiler
    |
    v
  running program
```

And for object-oriented programming:

```text
Class
  |
  | new
  v
Object
  |
  +--> state (fields)
  +--> behavior (methods)
```

The goal is not to memorize isolated rules. The goal is to understand how the pieces fit together so that each new Java feature has a place in the overall model.
