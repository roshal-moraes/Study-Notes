# Java In-Depth — Rewritten Notes

A practical, easy-to-follow guide to Java fundamentals, from the JVM and first program through object-oriented programming, control flow, strings, packages, and common Java APIs.

---

## Chapter 1 — What Java Is and Why It Works

### Interview Prep

**Core concepts to be ready to explain**

- Java is statically typed, object-oriented, garbage-collected, and designed around a JVM runtime. “Platform independent” applies to Java bytecode, provided a compatible JVM exists on the target platform.

**Interview questions**

**1. Why is Java called platform-independent?**
**Answer:** Because `javac` normally produces platform-independent bytecode, while a platform-specific JVM executes that bytecode on the target OS/architecture.

**2. Is Java purely object-oriented?**
**Answer:** No. Java has primitive types such as `int` and `boolean`, so it is object-oriented but not purely object-oriented.

**Tricky question**

**Question:** Does JIT compilation make Java a compiled language or an interpreted language?
**Answer:** Neither label alone is sufficient for modern Java. Java source is compiled to bytecode ahead of time, and the JVM may interpret and/or JIT-compile bytecode at runtime.

**Mini code check**

**Question:** What is printed?

```java
int x = 10;
int y = 20;
System.out.println(x + y);
```

**Answer:** `30`. The important interview point is that Java is statically typed, so `x` and `y` have declared types before runtime.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Java’s early design goals explain portability, compact programs, safety, and concurrency. The history is useful as context, but interview questions are usually about the resulting design rather than trivia.

**Interview questions**

**1. What was Java originally called?**
**Answer:** Oak.

**2. Why is Java’s history relevant to its design?**
**Answer:** The early Green Project targeted heterogeneous devices, which helped motivate portability, safety, and a managed runtime model.

**Tricky question**

**Question:** Was Java created primarily as a web language?
**Answer:** No. Java later became strongly associated with the web, especially through applets and server-side development, but it originated in the Green Project for consumer-device software.

**Mini code check**

**Question:** Which design goal is this demonstrating?

```java
System.out.println("Same bytecode, different JVMs");
```

**Answer:** Portability. The source code does not contain OS-specific system calls; the JVM provides the platform-specific runtime layer.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Java grew out of Sun Microsystems' Green Project in the early 1990s. James Gosling and the team designed a language called **Oak** for software that needed to work across different devices.

The language was later renamed **Java**. The early work emphasized portability, safety, compact programs, and concurrency. The famous Star7 prototype demonstrated the team's vision of interactive consumer devices.

The history matters mainly because it explains Java's design goals:

> A Java program should not have to be rewritten from scratch just because the underlying machine is different.

Sun Microsystems was acquired by Oracle in 2010, and Java development continues through the Java ecosystem and the OpenJDK project.

---

## Chapter 3 — Source Code, Compilation, and Platform Dependency

### Interview Prep

**Core concepts to be ready to explain**

- Keep source code, bytecode/native code, and machine execution conceptually separate. Java’s target after `javac` is bytecode, not a Windows or Linux executable.

**Interview questions**

**1. What does `javac` produce?**
**Answer:** Usually one or more `.class` files containing Java bytecode.

**2. Why does native C code normally need a separate build per platform?**
**Answer:** The produced executable is tied to the target OS, architecture, ABI, executable format, and native libraries/system interfaces.

**Tricky question**

**Question:** Does every source change require a complete rebuild?
**Answer:** No. A compiler can recompile an individual source file, and build tools can perform incremental compilation of affected sources and dependencies.

**Mini code check**

**Question:** What command compiles this Java source?

```text
javac Hello.java
```

**Answer:** `javac` compiles source to bytecode. Running is a separate step with `java Hello`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- The JVM is the execution environment defined by the JVM specification. A concrete JVM implementation is platform-specific. Modern JVMs can interpret bytecode and JIT-compile hot code.

**Interview questions**

**1. What is the difference between the JVM specification and a JVM implementation?**
**Answer:** The specification defines the required execution model; an implementation (such as HotSpot) provides that model for a real platform.

**2. What is JIT compilation?**
**Answer:** JIT compilation turns frequently executed bytecode into optimized native machine code at runtime.

**Tricky question**

**Question:** Does the JVM compile the entire program to machine code before execution?
**Answer:** Not necessarily. Execution is typically mixed: some code may be interpreted while hot code is compiled and optimized by the JIT.

**Mini code check**

**Question:** Why does `java Hello`, not `java Hello.class`, run the class?

```text
java Hello
```

**Answer:** The launcher expects a class name by default. `Hello.class` is a class-file path, not the normal launcher argument form.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Know the difference between Java SE, the JDK, the JVM, the JLS, the JVMS, and library/API documentation. Do not use the obsolete “JDK contains a separate JRE” mental model for modern JDKs.

**Interview questions**

**1. JDK vs JVM?**
**Answer:** The JDK is a development kit containing tools and runtime components; the JVM is the virtual machine that executes Java bytecode.

**2. JLS vs JVMS?**
**Answer:** JLS specifies Java language rules; JVMS specifies the JVM and class-file execution model.

**Tricky question**

**Question:** Is Jakarta EE the same thing as Java SE?
**Answer:** No. Java SE is the core Java platform; Jakarta EE is an enterprise platform built around Java technologies and specifications.

**Mini code check**

**Question:** Which commands verify a JDK installation?

```bash
java --version
javac --version
```

**Answer:** Both should report a usable JDK/JVM installation. `javac` is the key check that the compiler is available.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

Java moved to a six-month feature-release cadence starting with **Java 10**. Long-term-support releases are provided at intervals.

For interviews, the most useful language milestones are:

- **Java 8:** lambdas, method references, streams, and default methods.
- **Java 9:** modules and convenient collection factories such as `List.of`.
- **Java 10:** local-variable type inference with `var` and the six-month release model.
- **Java 15:** text blocks became permanent.
- **Java 16:** records and pattern matching for `instanceof` became permanent.
- **Java 17:** sealed classes became permanent.
- **Java 21:** record patterns, pattern matching for `switch`, and sequenced collections became important modern features.
- **Java 25:** compact source files/instance `main` methods, module import declarations, and flexible constructor bodies became permanent.

For notes intended to age well, prefer documented language/API guarantees instead of memorizing vendor-specific installation details.

---

## Chapter 6 — Installing Java and Understanding `PATH`

### Interview Prep

**Core concepts to be ready to explain**

- Use a JDK for development. `PATH` lets the shell locate commands; `JAVA_HOME` is a conventional variable used by build tools to locate the JDK installation.

**Interview questions**

**1. Why can `java` work while `javac` fails?**
**Answer:** The shell may be finding a runtime/launcher from another installation or an incorrectly configured environment, while the compiler is not on the effective `PATH`.

**2. What is `JAVA_HOME`?**
**Answer:** A conventional environment variable pointing to the JDK installation directory.

**Tricky question**

**Question:** Should `JAVA_HOME` point to `bin`?
**Answer:** Usually no. It should point to the JDK home directory; `PATH` can then include `$JAVA_HOME/bin` (or the Windows equivalent).

**Mini code check**

**Question:** What does this command test?

```bash
java --version
```

**Answer:** It reports the Java launcher/runtime version visible through the current `PATH`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Classpath is a search path for classes/resources. `PATH` finds executables; classpath finds Java classes and libraries. Modules provide a stronger namespace/dependency model than a flat classpath.

**Interview questions**

**1. What does `-cp .` mean?**
**Answer:** Use the current directory as a classpath entry.

**2. Why prefer explicit `-cp` over a global `CLASSPATH`?**
**Answer:** It makes the command reproducible and avoids hidden environment-dependent dependencies.

**Tricky question**

**Question:** Is a package name a filesystem path?
**Answer:** Not by itself. A package name maps conventionally to directories in classpath-based layouts, but the JVM resolves types through classpath/module rules rather than treating package names as arbitrary OS paths.

**Mini code check**

**Question:** What does this run?

```bash
java -cp . com.example.Main
```

**Answer:** It asks the launcher to find `com.example.Main` using the current directory as a classpath root.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Know the structure of `public static void main(String[] args)`, file/class naming rules for public top-level classes, and the separation between compile and run.

**Interview questions**

**1. Why must `HelloWorld.java` normally match `public class HelloWorld`?**
**Answer:** A public top-level class’s canonical source-file name must match the class name.

**2. Can a Java application have more than one method named `main`?**
**Answer:** Yes, you can overload methods named `main`, but the launcher looks for a recognized launchable `main` method signature/protocol.

**Tricky question**

**Question:** Does `static` mean there is only one copy of every variable in the class?
**Answer:** No. `static` applies to the member being declared; a static field is associated with the class, while instance fields belong to objects.

**Mini code check**

**Question:** What is the output?

```java
public class Demo {
    public static void main(String[] args) {
        System.out.println(args.length);
    }
}
```

**Answer:** If launched as `java Demo a b`, the output is `2`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- A class defines a type; an object is an instance. A reference variable such as `Student s` refers to an object rather than being the object itself.

**Interview questions**

**1. Class vs object?**
**Answer:** A class describes structure/behavior; an object is a runtime instance of that class.

**2. What does `new Student()` do?**
**Answer:** It creates a new `Student` object and returns a reference to it.

**Tricky question**

**Question:** Is the variable `s` in `Student s = new Student();` the object?
**Answer:** No. `s` is a reference variable. The object is created by `new Student()`.

**Mini code check**

**Question:** What prints?

```java
class Student { String name = "Ana"; }
Student a = new Student();
Student b = a;
b.name = "Mina";
System.out.println(a.name);
```

**Answer:** `Mina`, because `a` and `b` refer to the same object.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Java is case-sensitive. Identifiers follow lexical rules; keywords cannot be identifiers. Prefer semantic names and consistent conventions.

**Interview questions**

**1. Are `count`, `Count`, and `COUNT` the same variable?**
**Answer:** No. Java is case-sensitive.

**2. Can `$` appear in a Java identifier?**
**Answer:** Yes, although normal application code usually avoids it in favor of conventional names.

**Tricky question**

**Question:** Is `_` a valid standalone identifier?
**Answer:** No. Since Java 9, a single underscore is not a valid identifier.

**Mini code check**

**Question:** Which declaration is valid?

```java
int student2 = 10;
// int 2student = 10; // invalid
```

**Answer:** `student2` is valid; an identifier cannot start with a digit.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Local variables must be definitely assigned before use. Fields and array elements receive default values. Primitive types store primitive values; reference variables store references.

**Interview questions**

**1. Do local variables get default values?**
**Answer:** No. A local variable must be definitely assigned before it is read.

**2. Which primitive type is normally used for general integer arithmetic?**
**Answer:** `int`, unless a different range or type is required.

**Tricky question**

**Question:** Why does this compile: `int x; if (true) x = 1; System.out.println(x);`?
**Answer:** Because the compiler can prove the assignment always executes. Definite-assignment analysis is compile-time reasoning, not a runtime default.

**Mini code check**

**Question:** Does this compile?

```java
int x;
System.out.println(x);
```

**Answer:** No. Local variable `x` is not definitely assigned.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Know default literal types, exact integer ranges, floating-point approximation, `char` as an unsigned 16-bit UTF-16 code unit, and `boolean` as a distinct primitive type.

**Interview questions**

**1. What is the default type of `3.14`?**
**Answer:** `double`.

**2. Why is `long x = 8_000_000_000L;` suffixed with `L`?**
**Answer:** Without the suffix, the integer literal would need to fit the `int` literal rules; `L` makes it a `long` literal.

**Tricky question**

**Question:** Is `char` an integer type or a character type?
**Answer:** It is a distinct primitive type representing a UTF-16 code unit, but it participates in numeric conversions and can be used in integer arithmetic.

**Mini code check**

**Question:** What prints?

```java
System.out.println(5 / 2);
System.out.println(5 / 2.0);
```

**Answer:** `2` and `2.5`. Integer division happens when both operands are integral.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Widening numeric conversions are generally implicit; narrowing conversions generally require an explicit cast. Conversions can lose precision or magnitude.

**Interview questions**

**1. Why does `int` to `long` usually need no cast?**
**Answer:** It is a widening primitive conversion that preserves the integer value.

**2. What happens when a `double` is cast to `int`?**
**Answer:** The fractional part is discarded; the result is subject to the defined narrowing conversion rules.

**Tricky question**

**Question:** Does casting a value make it fit without information loss?
**Answer:** No. A cast can change the value by truncating, overflowing/wrapping according to the target type’s conversion rules, or losing floating-point precision.

**Mini code check**

**Question:** What are the outputs?

```java
double d = 9.8;
int x = (int) d;
System.out.println(x);
```

**Answer:** `9`. Casting from `double` to `int` discards the fractional part.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- A reference variable can refer to an object or to `null`. `null` is not an object. Dereferencing `null` causes `NullPointerException`.

**Interview questions**

**1. What is the default value of an object reference field?**
**Answer:** `null`.

**2. What happens when you call an instance method through `null`?**
**Answer:** A `NullPointerException` occurs when the expression is dereferenced.

**Tricky question**

**Question:** Does `null == null` throw an exception?
**Answer:** No. Comparing references with `==` does not dereference them, so `null == null` is `true`.

**Mini code check**

**Question:** What happens?

```java
String s = null;
System.out.println(s == null);
System.out.println(s.length());
```

**Answer:** First prints `true`; the second statement throws `NullPointerException`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- An expression produces a value; a statement performs an action or controls execution. Scope determines where a declared name can be used.

**Interview questions**

**1. Is `a + b` a statement?**
**Answer:** No. It is an expression. `int c = a + b;` is a statement containing an expression.

**2. What does block scope mean?**
**Answer:** A local variable declared inside `{ ... }` is generally visible only within that block and nested blocks.

**Tricky question**

**Question:** Can an inner block declare a local variable with the same name as an outer local variable?
**Answer:** No, not when the scopes overlap in a way that would make the declarations illegal; Java does not allow arbitrary local-variable shadowing in overlapping local scopes.

**Mini code check**

**Question:** Will this compile?

```java
int x = 1;
{
    int y = 2;
    System.out.println(x + y);
}
// y is not visible here
```

**Answer:** Yes. `y` is visible only inside the inner block.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Know arithmetic, relational, logical, assignment, conditional, increment/decrement, and bitwise operators. Pay special attention to precedence, integer division, and short-circuit evaluation.

**Interview questions**

**1. `&&` vs `&` for booleans?**
**Answer:** `&&` short-circuits; `&` evaluates both operands and performs logical AND on booleans.

**2. `==` vs `.equals()` for objects?**
**Answer:** `==` compares reference identity (or primitive values); `.equals()` can compare logical equality when the class overrides it.

**Tricky question**

**Question:** What happens here?
**Answer:** It evaluates left-to-right: `x++` yields 5 then x becomes 6; `++x` makes x 7 and yields 7; output is `12`. Avoid writing code like this in production; interview questions use it to test evaluation order.

**Mini code check**

**Question:** What does `false && method()` demonstrate?

```java
boolean result = false && expensiveCheck();
```

**Answer:** `expensiveCheck()` is not called because `&&` short-circuits.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Use `if`/`else` for general conditions. Modern `switch` can be used as a statement or expression; arrow labels avoid accidental fall-through.

**Interview questions**

**1. What is the difference between `switch` statement and switch expression?**
**Answer:** A switch expression produces a value and can use `yield` from a block; a switch statement primarily controls execution.

**2. What is fall-through?**
**Answer:** In traditional colon-style switch labels, control can continue into the next case unless `break`, `return`, or another control transfer exits.

**Tricky question**

**Question:** Does `case A, B ->` fall through to the next case?
**Answer:** No. Arrow-style switch rules do not fall through.

**Mini code check**

**Question:** What does this expression return for `day = 6`?

```java
String type = switch (day) {
    case 6, 7 -> "weekend";
    default -> "weekday";
};
```

**Answer:** `weekend`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Choose the loop that best expresses the intent. `for` is common for counted iteration, `while` for condition-driven repetition, and `do-while` when one execution must happen before the test.

**Interview questions**

**1. When is `do-while` useful?**
**Answer:** When the loop body must execute at least once, such as reading input and validating it afterward.

**2. What does `break` do?**
**Answer:** Exits the nearest enclosing loop or switch, or a labeled statement when a label is supplied.

**Tricky question**

**Question:** What is wrong with modifying an `ArrayList` in a for-each loop?
**Answer:** Structural modification can trigger a fail-fast iterator, often resulting in `ConcurrentModificationException`.

**Mini code check**

**Question:** How many times does this print?

```java
for (int i = 0; i < 3; i++) {
    System.out.println(i);
}
```

**Answer:** Three times: `0`, `1`, `2`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Arrays have fixed length and zero-based indexing. Array elements receive default values. Multidimensional arrays are arrays of arrays.

**Interview questions**

**1. Can an array change size after creation?**
**Answer:** No. Its length is fixed.

**2. What exception results from an invalid index?**
**Answer:** `ArrayIndexOutOfBoundsException`.

**Tricky question**

**Question:** Are `int[3][4]` arrays guaranteed to be rectangular?
**Answer:** No. Java uses arrays of arrays, so rows can have different lengths.

**Mini code check**

**Question:** What prints?

```java
int[][] a = { {1, 2}, {3} };
System.out.println(a.length);
System.out.println(a[1].length);
```

**Answer:** `2` and `1`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Methods define reusable behavior. Know parameters vs arguments, return types, overloading, varargs, and Java’s pass-by-value semantics.

**Interview questions**

**1. Is Java pass-by-reference?**
**Answer:** No. Java is pass-by-value. For object arguments, the value copied is the reference to the object.

**2. What is method overloading?**
**Answer:** Defining multiple methods with the same name but different parameter lists.

**Tricky question**

**Question:** Can methods be overloaded only by changing the return type?
**Answer:** No. Return type alone is not enough to distinguish overloaded methods.

**Mini code check**

**Question:** What prints?

```java
static void change(int x) { x = 99; }
int n = 10;
change(n);
System.out.println(n);
```

**Answer:** `10`. The primitive value was passed by value.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Constructors initialize newly created objects. If no constructor is declared, the compiler may provide a default constructor with no parameters. Constructor chaining uses `this(...)` or `super(...)`.

**Interview questions**

**1. Does a constructor have a return type?**
**Answer:** No. Not even `void`.

**2. Can constructors be inherited?**
**Answer:** No. Constructors are not inherited, though a subclass constructor must ultimately initialize its superclass.

**Tricky question**

**Question:** Can `this(...)` and `super(...)` both appear as explicit constructor invocations in the same constructor?
**Answer:** No. A constructor can have only one explicit constructor invocation, and it must be the first constructor-invocation statement subject to the language rules.

**Mini code check**

**Question:** Which constructor runs first?

```java
class A { A() { System.out.println("A"); } }
class B extends A { B() { System.out.println("B"); } }
new B();
```

**Answer:** `A` prints before `B`, because superclass initialization occurs before the subclass constructor body.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- For object creation, superclass initialization happens before subclass initialization. Within a class, instance field initializers and instance initializer blocks run in source order before the constructor body.

**Interview questions**

**1. What is the order between field initializer and instance initializer block?**
**Answer:** They run in the order they appear in the class body, after the superclass constructor has completed for the current class level.

**2. Why should initialization order matter in interviews?**
**Answer:** Because overridden methods or field references during construction can observe partially initialized state.

**Tricky question**

**Question:** Is it safe to call an overridable method from a constructor?
**Answer:** It is legal, but risky because the subclass part of the object may not yet be initialized.

**Mini code check**

**Question:** What prints?

```java
class X {
    int a = print("field");
    { print("block"); }
    X() { print("ctor"); }
    static int print(String s) { System.out.println(s); return 1; }
}
new X();
```

**Answer:** `field`, then `block`, then `ctor`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Encapsulation means controlling access to object state and preserving invariants. `private` fields plus validated methods are a common pattern.

**Interview questions**

**1. Why make fields private?**
**Answer:** To prevent uncontrolled external modification and centralize validation and invariants.

**2. `protected` means what exactly?**
**Answer:** Accessible to the same package and, subject to Java’s protected-access rules, by subclasses in other packages.

**Tricky question**

**Question:** Does encapsulation mean “always use getters and setters for every field”?
**Answer:** No. Encapsulation is about controlling invariants and API design; exposing a trivial getter/setter for everything is not automatically good encapsulation.

**Mini code check**

**Question:** What invariant is protected?

```java
class Account {
    private double balance;
    void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException();
        balance += amount;
    }
}
```

**Answer:** The class prevents non-positive deposits from directly corrupting the state.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Packages organize types, avoid naming collisions, and interact with access control. An import is compile-time source convenience; it does not copy classes or change runtime identity.

**Interview questions**

**1. Does an import make a class available at runtime?**
**Answer:** The compiler uses imports to resolve source names. Runtime class loading still follows classpath/module rules.

**2. Can two classes with the same simple name be used in one source file?**
**Answer:** Yes, but at least one may need a fully qualified name if imports create ambiguity.

**Tricky question**

**Question:** Does `import java.util.*` import subpackages such as `java.util.concurrent`?
**Answer:** No. A wildcard import covers types directly in that package, not subpackages.

**Mini code check**

**Question:** What is the difference?

```java
import java.util.List;
java.util.ArrayList<String> xs = new java.util.ArrayList<>();
```

**Answer:** Imports affect name resolution only; fully qualified names can be used without imports.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- `String` is immutable. String literals are interned in the string pool. Repeated concatenation in loops should generally use `StringBuilder` when building mutable text.

**Interview questions**

**1. Why is `String` immutable?**
**Answer:** Immutability simplifies sharing, caching, security-sensitive use, and thread safety, and allows string pooling.

**2. `==` vs `.equals()` for strings?**
**Answer:** `==` checks whether references are identical; `.equals()` checks character content.

**Tricky question**

**Question:** What prints?
**Answer:** `false`, then `true`.

```java
```java
String a = "java";
String b = new String("java");
System.out.println(a == b);
System.out.println(a.equals(b));
```
```

**Mini code check**

**Question:** Why is this usually preferred in a loop?

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 100; i++) sb.append(i);
String s = sb.toString();
```

**Answer:** `StringBuilder` avoids repeatedly creating intermediate immutable `String` objects for mutable accumulation.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Know common escapes such as `\n`, `\t`, `\"`, `\\`, and Unicode escapes. Text blocks (standard since Java 15) help with multiline text.

**Interview questions**

**1. What does `\n` represent?**
**Answer:** A newline character in a Java string literal.

**2. Why use text blocks?**
**Answer:** They make multiline string literals easier to read and maintain.

**Tricky question**

**Question:** Is `\n` one character or two?
**Answer:** In the resulting string it represents one newline character, although the source uses two characters (`\` and `n`).

**Mini code check**

**Question:** What prints on two lines?

```java
System.out.println("A\nB");
```

**Answer:** `A` on the first line and `B` on the second.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- The standard API is part of the language ecosystem. Strong developers look up API contracts rather than relying on memory for every method detail.

**Interview questions**

**1. Why should you learn to read API documentation?**
**Answer:** Because method contracts, exceptions, null handling, ordering guarantees, and performance characteristics are defined there.

**2. What is an API contract?**
**Answer:** The documented behavior clients can rely on, including accepted inputs, outputs, side effects, exceptions, and guarantees.

**Tricky question**

**Question:** Is an implementation detail automatically safe to depend on?
**Answer:** No. If the API does not guarantee it, a future implementation or release may change it.

**Mini code check**

**Question:** Which API class would you reach for to sort an array?

```java
Arrays.sort(numbers);
```

**Answer:** `java.util.Arrays` provides array utilities such as sorting and searching.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- `Math` provides common numeric operations. Some methods can involve floating-point edge cases; use precise domain-specific types when required.

**Interview questions**

**1. What does `Math.max(3, 7)` return?**
**Answer:** `7`.

**2. When might `Math.round` be inappropriate?**
**Answer:** When the application requires a precise, explicitly defined rounding policy such as financial calculations.

**Tricky question**

**Question:** Is `Math.pow(2, 3)` an integer operation?
**Answer:** No. It works with `double` and returns `double`.

**Mini code check**

**Question:** What is the result type?

```java
var x = Math.pow(2, 3);
```

**Answer:** `x` is inferred as `double`, and its value is `8.0`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Wrapper classes let primitive values participate in generic/object APIs. Autoboxing and unboxing are compiler-supported conversions, but unboxing `null` throws `NullPointerException`.

**Interview questions**

**1. Why can’t `List<int>` be used?**
**Answer:** Generics require reference types, so `List<Integer>` is used instead.

**2. What happens when `Integer x = null; int y = x;` executes?**
**Answer:** Unboxing `x` throws `NullPointerException`.

**Tricky question**

**Question:** Why can `Integer a = 127, b = 127; a == b` be `true`, but `128` may differ?
**Answer:** Wrapper caching is specified for some constant values such as the `int` range -128..127; identity comparisons on wrappers should not be used for numeric equality.

**Mini code check**

**Question:** What should you use?

```java
List<Integer> values = new ArrayList<>();
values.add(10);
```

**Answer:** `10` is autoboxed to `Integer` when inserted into the generic collection.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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

### Interview Prep

**Core concepts to be ready to explain**

- Interviewers care about correctness, readability, naming, cohesion, avoiding unnecessary cleverness, and understanding API contracts. “Works” is not the same as “good production code.”

**Interview questions**

**1. What makes code maintainable?**
**Answer:** Clear names, small coherent methods, explicit invariants, appropriate abstractions, predictable side effects, and tests.

**2. Why avoid premature optimization?**
**Answer:** It can make code harder to understand without addressing the actual bottleneck; measure first when performance matters.

**Tricky question**

**Question:** Is shorter code always better code?
**Answer:** No. The best code communicates intent clearly; a concise expression can be worse if it hides behavior or edge cases.

**Mini code check**

**Question:** Which is clearer?

```java
if (user != null && user.isActive()) {
    sendEmail(user);
}
```

**Answer:** This is generally clearer than deeply nested null checks and makes the guard condition explicit.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


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


## Current Java Release Snapshot — September 2026

JDK **27** was released on **September 15, 2026** and is the latest Java SE feature release. JDK **25** (released September 16, 2025) is the current LTS release. Java continues on a six-month feature-release cadence.

For interview preparation, treat these categories separately:

- **Stable features:** safe to describe as part of the standard language/API.
- **Preview features:** available for experimentation but not yet permanent; do not describe them as finalized language features.
- **Incubator features:** APIs under active development and not yet standard.

JDK 27 includes, among other changes, **G1 as the default garbage collector in all environments** and **compact object headers enabled by default** in HotSpot. It also continues preview/incubator work such as primitive types in patterns/`instanceof`/`switch`, lazy constants, structured concurrency, and the Vector API. For interviews, know the distinction between a stable feature and a preview/incubator feature rather than trying to memorize every JDK 27 JEP.

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

## Interview Drill — Java Fundamentals

Use these as rapid-fire questions after finishing the notes. Try answering aloud in 30–60 seconds before checking the answer.

### Rapid-fire questions

1. **Why is Java platform-independent but the JVM platform-specific?**  
   **Answer:** Bytecode has a platform-independent format; the JVM implementation contains the platform-specific code needed to execute it.

2. **Why is Java pass-by-value even for objects?**  
   **Answer:** The copied value is the object reference. Both caller and callee can refer to the same object, but assigning a new reference inside the method does not change the caller’s reference.

3. **Why can `String` be safely shared?**  
   **Answer:** Strings are immutable, so sharing cannot let one caller mutate another caller’s string contents.

4. **Why is `5 / 2` equal to `2`?**  
   **Answer:** Both operands are integers, so integer division is selected.

5. **Why does unboxing `Integer x = null` to `int` fail?**  
   **Answer:** The compiler inserts an unboxing conversion and dereferencing a null wrapper causes `NullPointerException`.

### Classic output questions

**Q1**
```java
String a = "hi";
String b = "hi";
String c = new String("hi");
System.out.println(a == b);
System.out.println(a == c);
System.out.println(a.equals(c));
```
**Answer:** `true`, `false`, `true`.

**Q2**
```java
int x = 1;
System.out.println(x++);
System.out.println(x);
```
**Answer:** `1`, then `2`.

**Q3**
```java
static void change(Student s) {
    s.name = "Bob";
    s = new Student();
    s.name = "Carol";
}
```
**Question:** Does the caller’s object name become `Carol`?  
**Answer:** No. The caller’s object becomes `Bob`; the reassignment of `s` affects only the local copy of the reference.

### Interview checklist

Before moving on, you should be able to explain without notes: JVM vs JDK, source vs bytecode, class vs object, stack/heap as conceptual runtime areas, primitive vs reference, pass-by-value, overload vs override, constructor vs method, `==` vs `.equals()`, checked vs runtime failures, and why immutability helps API design.
