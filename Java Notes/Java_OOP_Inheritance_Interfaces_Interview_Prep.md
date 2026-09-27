# Java OOP: Inheritance, Polymorphism, Interfaces & Object Model
## Interview Preparation Edition

> **Goal:** Build a clean mental model of Java's object-oriented type system, then use that model to answer interview questions, predict code behavior, and design better class hierarchies.
>
> **Current Java note:** JDK 27 was released on **September 15, 2026**. JDK 25 is the current LTS release. The material below focuses on stable language features; newer preview features are not treated as production features. citeturn158071search0turn158071search3

---

# 1. The Big Picture: Why Inheritance Exists

Inheritance lets one class reuse and specialize another class.

A simple example:

```java
class User {
    void bookmark() {
        System.out.println("Bookmarking...");
    }
}

class Staff extends User {
    void approveReview() {
        System.out.println("Approving review...");
    }
}
```

`Staff` is a `User`, so a `Staff` object can be used wherever a `User` is expected.

```java
Staff staff = new Staff();
staff.bookmark();
staff.approveReview();
```

## The important idea

Inheritance is not primarily about copying code. It creates a **type relationship**:

```text
User
  |
  +-- Staff
       |
       +-- Editor
```

That relationship gives us substitution and polymorphism.

## Interview question: What is inheritance?

**Answer:** Inheritance is a mechanism through which a class derives from another class, gaining the inherited accessible behavior and becoming a subtype of the superclass. In Java, a class uses `extends` for class inheritance.

## Tricky question: Does a subclass inherit everything from its parent?

No.

Private members are not inherited by a subclass. They remain part of the superclass's own implementation. A subclass may interact with superclass state through accessible methods or other accessible members.

Static and instance members also behave differently, and constructors are **not inherited**.

## Tricky question: Can a subclass access a private field directly?

No.

```java
class User {
    private int id = 10;
}

class Staff extends User {
    void print() {
        // System.out.println(id); // compile error
    }
}
```

A superclass can expose controlled access through methods:

```java
class User {
    private int id = 10;

    public int getId() {
        return id;
    }
}
```

## Key takeaway

Think of inheritance as:

> **“Staff can be treated as a User.”**

That sentence is more useful than thinking:

> “Staff gets User's code.”

---

# 2. Is-A vs Has-A: Choosing Inheritance Correctly

The most useful design question is:

> **Is the new type genuinely a specialized form of the old type?**

Examples:

- `Staff` **is a** `User` → reasonable inheritance.
- `Editor` **is a** `Staff` → potentially reasonable.
- `Triangle` **is a** `Shape` → reasonable.
- `Engine` **is a** `Car` → wrong.
- `Car` **has an** `Engine` → composition.

## Composition

```java
class Engine {
    void start() {
        System.out.println("Engine started");
    }
}

class Car {
    private final Engine engine = new Engine();

    void start() {
        engine.start();
    }
}
```

`Car` does not need to extend `Engine`. It **contains** an `Engine`.

## Interview question: When should you prefer composition?

When the relationship is “has-a” rather than “is-a”, or when inheritance would tightly couple a class to a superclass implementation.

Composition often makes behavior easier to change independently.

## Tricky question: Is inheritance always better because it reduces duplicate code?

No. Reusing code is not enough to justify inheritance. A false inheritance relationship can create fragile designs, expose unwanted behavior, and make future changes harder.

A class hierarchy should represent a meaningful subtype relationship.

## Good interview answer

> “I use inheritance when substitutability is meaningful. If the relationship is mainly code reuse, I first consider composition.”

---

# 3. Access Modifiers and Inheritance

Java has four main access levels:

| Modifier | Same class | Same package | Subclass outside package | Other packages |
|---|---|---|---|---|
| `private` | yes | no | no | no |
| package-private | yes | yes | no* | no |
| `protected` | yes | yes | yes, through subclass access | no |
| `public` | yes | yes | yes | yes |

`*` A package-private member is not inherited/accessible by a subclass in another package as a normal inherited member.

## Protected is the tricky one

Consider:

```java
package p1;

public class Parent {
    protected int value = 10;
}
```

and:

```java
package p2;

import p1.Parent;

public class Child extends Parent {
    void test() {
        System.out.println(value); // allowed
    }
}
```

But an unrelated class in package `p2` cannot simply do:

```java
new Parent().value; // not allowed
```

The outside-package protected rule is tied to subclass access, not simply “anyone in the family”.

## Interview question: What is the difference between package-private and protected?

`protected` gives same-package access plus special access to subclasses in other packages. Package-private gives only same-package access.

## Tricky question: Is `protected` “less restrictive than public and the same as default”?

No. It is its own access level. It is more accessible than package-private because subclasses in other packages can access it under the protected access rules.

---

# 4. Polymorphism: The Most Important OOP Idea

Polymorphism means that one supertype reference can refer to objects of different subtypes.

```java
class User {
    void postReview() {
        System.out.println("User review");
    }
}

class Staff extends User {
    @Override
    void postReview() {
        System.out.println("Staff review");
    }
}
```

Now:

```java
User u = new Staff();
u.postReview();
```

Output:

```text
Staff review
```

The **reference type** is `User`.
The **runtime object type** is `Staff`.

For an overridden instance method, runtime dispatch selects the implementation from the actual object.

## The classic interview sentence

> **Compile time decides what can be called; runtime decides which overridden instance implementation runs.**

That sentence is worth memorizing.

---

# 5. Reference Type vs Object Type

This distinction explains many interview questions.

```java
class User {
    void common() {
        System.out.println("User");
    }
}

class Editor extends User {
    void approve() {
        System.out.println("Approve");
    }
}
```

```java
User x = new Editor();
x.common();
// x.approve(); // compile error
```

Why?

The compiler sees `x` as a `User`, and `User` does not declare `approve()`.

The actual object is still an `Editor`, but the reference type limits what the compiler lets you call directly.

To call `approve()`:

```java
((Editor) x).approve();
```

That cast is safe only if the runtime object really is an `Editor`.

---

# 6. Object Casting: Upcasting and Downcasting

Primitive casting and reference casting are different concepts.

## Upcasting

A subtype can be assigned to a supertype without an explicit cast.

```java
Editor editor = new Editor();
User user = editor;
```

This is a **widening reference conversion**.

## Downcasting

Going from a supertype reference to a subtype reference requires an explicit cast.

```java
User user = new Editor();
Editor editor = (Editor) user;
```

At runtime, the JVM checks the object type.

## ClassCastException

This is dangerous:

```java
User user = new User();
Editor editor = (Editor) user; // ClassCastException
```

The reference type `User` is compatible syntactically with the cast, but the actual object is only a `User`.

## Safer modern form

Since **Java 16**, pattern matching for `instanceof` became a permanent feature. citeturn918864search0turn918864search5

```java
if (user instanceof Editor editor) {
    editor.approve();
}
```

This combines the type test and cast.

## Important interview question

What does this print?

```java
User user = null;
System.out.println(user instanceof Editor);
```

Answer:

```text
false
```

`instanceof` is false for `null`.

## Tricky question: Does `instanceof` check the reference type or object type?

It checks whether the referenced runtime value can be matched to the tested reference type.

---

# 7. Overloading vs Overriding

This is one of the most frequently tested Java topics.

## Overloading

Same method name, different parameter list.

```java
class Printer {
    void print(int value) {
        System.out.println("int");
    }

    void print(String value) {
        System.out.println("String");
    }
}
```

Which method is selected is a **compile-time** decision.

## Overriding

A subclass provides a new implementation for an inherited instance method with a compatible signature.

```java
class Parent {
    void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    @Override
    void show() {
        System.out.println("Child");
    }
}
```

Which implementation runs for an instance call is determined at runtime.

## Interview shortcut

| Feature | Overloading | Overriding |
|---|---|---|
| Where? | Same class or inheritance hierarchy | Subclass/superclass relationship |
| Parameters | Must differ | Same signature/subsignature |
| Decision | Compile time | Runtime for instance methods |
| Purpose | Multiple ways to call | Specialized behavior |

## Tricky example

```java
class Parent {
    void go(double x) {
        System.out.println("Parent double");
    }
}

class Child extends Parent {
    void go(int x) {
        System.out.println("Child int");
    }
}

Parent p = new Child();
p.go(10);
```

Output:

```text
Parent double
```

Why?

`Child.go(int)` is **not an override** of `Parent.go(double)`. It is an overload introduced by `Child`.

The compiler selects the method using the reference type `Parent`, finds `go(double)`, and runtime dispatch cannot turn that into `go(int)` because the signatures are different.

---

# 8. Rules for Method Overriding

For an overriding instance method:

1. The method signature must match the inherited method's signature/subsignature.
2. The return type must be the same or covariant for reference types.
3. The overriding method cannot reduce access.
4. Checked exceptions cannot be broader than those permitted by the overridden method.
5. A `final` method cannot be overridden.
6. Static methods are hidden, not overridden.

The JLS specifies the inheritance, overriding, and hiding rules; Java's tutorials summarize the visibility and static/instance distinctions. citeturn936174search43turn936174search9

## Covariant return type

This is valid:

```java
class User {
    User copy() {
        return new User();
    }
}

class Staff extends User {
    @Override
    Staff copy() {
        return new Staff();
    }
}
```

`Staff` is a subtype of `User`, so the return type is covariant.

Primitive return types do not work this way:

```java
class A {
    int value() { return 1; }
}

class B extends A {
    // short value() { return 1; } // invalid
}
```

## Visibility trap

The source notes incorrectly suggested that an overriding method may become less accessible. It cannot.

This is invalid:

```java
class Parent {
    public void go() {}
}

class Child extends Parent {
    // protected void go() {} // invalid
}
```

But widening access is allowed:

```java
class Parent {
    protected void go() {}
}

class Child extends Parent {
    @Override
    public void go() {}
}
```

---

# 9. `super`: Calling the Parent Part of the Object

`super` refers to the immediate superclass from the current instance context.

## Calling an overridden method

```java
class User {
    void postReview() {
        System.out.println("Base review");
    }
}

class Staff extends User {
    @Override
    void postReview() {
        super.postReview();
        System.out.println("Staff-specific review");
    }
}
```

Output:

```text
Base review
Staff-specific review
```

## Calling a superclass constructor

```java
class User {
    User(int id) {
        System.out.println("User: " + id);
    }
}

class Staff extends User {
    Staff(int id) {
        super(id);
    }
}
```

## Accessing a hidden field

Fields do not participate in runtime overriding.

```java
class Parent {
    int value = 10;
}

class Child extends Parent {
    int value = 20;

    void print() {
        System.out.println(value);       // 20
        System.out.println(super.value); // 10
    }
}
```

## Tricky question

Why is `super` unavailable in a static method?

Because `super` refers to the current object's superclass state/behavior. A static method belongs to the class, not to a particular object instance.

---

# 10. Fields Are Hidden, Not Overridden

Methods can be overridden.
Fields cannot.

```java
class Parent {
    int value = 1;
}

class Child extends Parent {
    int value = 2;
}

Parent p = new Child();
System.out.println(p.value);
```

Output:

```text
1
```

Field access is based on the **reference type**, not runtime polymorphic dispatch.

That is why fields should generally be kept private and exposed through methods when polymorphic behavior is needed.

---

# 11. Static Methods: Hiding, Not Overriding

```java
class Parent {
    static void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    static void show() {
        System.out.println("Child");
    }
}
```

Now:

```java
Parent p = new Child();
p.show();
```

prints:

```text
Parent
```

The static method is selected using the **reference type**.

## Interview question: Can a static method be overridden?

No.

It can be **hidden** by a static method declared in a subclass.

Java's own tutorial distinguishes static method hiding from instance method overriding. citeturn936174search9

## Tricky question

Can a subclass change an inherited instance method into static?

No. That produces a compile-time error.

Likewise, changing a superclass static method into an instance method is invalid.

---

# 12. Method Binding: Compile Time vs Runtime

Interviewers often use the word **binding** to describe when Java decides which method to use.

## Compile-time binding

Typical examples:

- method overloading
- static method selection
- field access

## Runtime dispatch

Overridden instance methods are dynamically dispatched.

```java
class Parent {
    void go() { System.out.println("Parent"); }
}

class Child extends Parent {
    @Override
    void go() { System.out.println("Child"); }
}

Parent p = new Child();
p.go(); // Child
```

## Bytecode detail

For advanced interviews, know the broad JVM distinction:

- `invokestatic` → static calls
- `invokevirtual` → typical virtual instance calls
- `invokeinterface` → interface calls
- `invokespecial` → constructors, private methods, and explicit superclass/special calls

You do not normally need to memorize bytecode instructions unless the interview is JVM-focused.

## Classic trap: overload + override together

```java
class Parent {
    void test(Object o) {
        System.out.println("Parent Object");
    }
}

class Child extends Parent {
    @Override
    void test(Object o) {
        System.out.println("Child Object");
    }

    void test(String s) {
        System.out.println("Child String");
    }
}

Parent p = new Child();
p.test("Java");
```

Output:

```text
Child Object
```

Why?

1. Compile time sees `Parent`.
2. It selects `test(Object)` because that's what `Parent` exposes.
3. Runtime dispatch selects `Child.test(Object)`.
4. `Child.test(String)` is never considered by runtime dispatch as an overload selected after the fact.

---

# 13. The `final` Keyword

`final` can apply to variables, methods, and classes.

## Final variable

A final variable cannot be assigned a new value after initialization.

```java
final int MAX = 100;
```

For reference variables, `final` prevents changing the reference, not necessarily mutating the object:

```java
final List<String> names = new ArrayList<>();
names.add("Alice"); // allowed

// names = new ArrayList<>(); // not allowed
```

## Final method

A final instance method cannot be overridden.

```java
class User {
    final void audit() {
        System.out.println("Audit");
    }
}
```

## Final class

A final class cannot be extended.

```java
final class Utility {
}

// class Child extends Utility {} // invalid
```

## Modern alternative: sealed classes

Java 17 finalized sealed classes/interfaces. A sealed type restricts direct inheritance/implementation to explicitly permitted types. citeturn918864search2turn918864search4

```java
sealed interface Payment permits CardPayment, CashPayment {}

final class CardPayment implements Payment {}
final class CashPayment implements Payment {}
```

This is different from `final`:

- `final` → no subclass at all.
- `sealed` → only approved subclasses.

This is an important modern interview topic.

---

# 14. Constructors and Constructor Chaining

A constructor initializes a newly created object.

```java
class User {
    private final int id;

    User(int id) {
        this.id = id;
    }
}
```

## Constructors are not inherited

This is a common interview question.

If:

```java
class Parent {
    Parent(int id) {}
}
```

then:

```java
class Child extends Parent {
}
```

does **not** inherit `Parent(int)`.

The compiler would need a constructor for `Child`, and if no constructor is written, it tries to supply a no-arg constructor that invokes `super()`. Since `Parent()` does not exist, compilation fails.

## Constructor chaining

```java
class Parent {
    Parent() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    Child() {
        super();
        System.out.println("Child");
    }
}
```

Creating `new Child()` initializes the superclass portion first.

Conceptually:

```text
Object constructor
      ↓
Parent constructor
      ↓
Child constructor
```

## `this()` vs `super()`

```java
class User {
    User() {
        this(0);
    }

    User(int id) {
        // initialize
    }
}
```

`this(...)` calls another constructor in the same class.

`super(...)` calls the immediate superclass constructor.

Only one constructor-invocation statement can appear, and it must be the first statement in the constructor.

## Tricky question

Can a constructor call both `this()` and `super()`?

Not directly in the same constructor, because only one explicit constructor invocation can appear first. A `this()` chain may eventually reach a constructor that invokes `super()`.

---

# 15. Abstract Classes

An abstract class represents a partial abstraction.

It may contain:

- abstract methods
- concrete methods
- instance fields
- static fields
- constructors
- nested types

But it **cannot be instantiated directly**.

```java
abstract class Payment {
    abstract void pay();

    void receipt() {
        System.out.println("Receipt created");
    }
}
```

A subclass completes the abstract behavior:

```java
class CardPayment extends Payment {
    @Override
    void pay() {
        System.out.println("Paid by card");
    }
}
```

## Abstract class can have constructors

```java
abstract class User {
    protected final int id;

    protected User(int id) {
        this.id = id;
    }
}
```

The constructor runs as part of constructing a concrete subclass.

## Can an abstract class contain no abstract methods?

Yes.

For example, a class may be abstract simply to prevent direct instantiation and force use through subclasses.

## Can an abstract method be static?

No.

A static method is not overridden, while an abstract method requires a concrete implementation in a subclass.

Likewise, an abstract method cannot be `final` or `private`.

---

# 16. Abstract Class vs Interface

This is one of the most common design questions.

| Abstract class | Interface |
|---|---|
| Can have instance state | Cannot have instance fields |
| Can have constructors | No constructors |
| Can have concrete methods | Can have default/static/private methods |
| Can have abstract methods | Can have abstract methods |
| Class can extend only one class | Class can implement multiple interfaces |
| Represents shared base identity/implementation | Usually represents a contract/capability |

## Good rule of thumb

Use an abstract class when related types share **state and implementation**.

Use an interface when you want to express a **contract, capability, or role** that unrelated classes can implement.

Example:

```java
interface Printable {
    void print();
}

abstract class Document {
    protected String title;

    protected Document(String title) {
        this.title = title;
    }

    abstract void save();
}
```

A PDF document might be a `Document` and also be `Printable`.

---

# 17. Interfaces: Contracts and Capabilities

An interface defines a type that classes can implement.

```java
interface Shareable {
    void share();
}
```

Multiple unrelated classes can implement it:

```java
class Book implements Shareable {
    public void share() {
        System.out.println("Sharing book");
    }
}

class Photo implements Shareable {
    public void share() {
        System.out.println("Sharing photo");
    }
}
```

Then:

```java
void shareIt(Shareable item) {
    item.share();
}
```

This gives polymorphism without forcing `Book` and `Photo` into the same class hierarchy.

---

# 18. Interface Evolution Across Java Releases

The original “interfaces only contain abstract methods” model is outdated.

### Java 8
Default and static methods were introduced in interfaces. This enabled APIs to evolve while providing implementations for new methods. Java 8 also introduced lambdas, making functional interfaces especially important. citeturn918864search7

### Java 9
Interfaces gained private methods, allowing shared implementation to be factored inside the interface. Oracle's language-change documentation lists private interface methods as a Java 9 change. citeturn609565search7

## Modern interface example

```java
interface Logger {
    default void info(String message) {
        write("INFO", message);
    }

    default void warn(String message) {
        write("WARN", message);
    }

    private void write(String level, String message) {
        System.out.println(level + ": " + message);
    }
}
```

The private method cannot be called by implementing classes.

---

# 19. Interface Fields

Variables declared in an interface are implicitly:

```java
public static final
```

So:

```java
interface Limits {
    int MAX = 100;
}
```

is effectively:

```java
public static final int MAX = 100;
```

You cannot use an interface instance to hold separate field state because interfaces are not instantiated.

## Interview question

Can an interface have an instance variable?

No. Interface fields are constants: `public static final`.

---

# 20. Multiple Interface Inheritance

A class can implement multiple interfaces:

```java
class SmartPrinter implements Printable, Scannable {
}
```

An interface can extend multiple interfaces:

```java
interface SmartDevice extends Printable, Scannable {
}
```

This gives Java a form of multiple inheritance of **types**, while class inheritance remains single inheritance.

---

# 21. The Diamond Problem and Default Methods

Two interfaces can provide conflicting default implementations.

```java
interface A {
    default void go() {
        System.out.println("A");
    }
}

interface B {
    default void go() {
        System.out.println("B");
    }
}

class C implements A, B {
    @Override
    public void go() {
        System.out.println("C");
    }
}
```

The class must resolve the conflict.

## Explicitly calling a default

If needed, a class can select a direct superinterface's default implementation:

```java
interface A {
    default void go() {
        System.out.println("A");
    }
}

interface B {
    default void go() {
        System.out.println("B");
    }
}

class C implements A, B {
    @Override
    public void go() {
        A.super.go();
    }
}
```

## Default-method precedence

A useful interview hierarchy is:

1. A concrete superclass method takes priority over a default method.
2. A more specific subinterface default takes priority over a less-specific parent-interface default.
3. If unrelated interfaces conflict, the class must resolve the conflict.

## Can an interface default method override `Object.equals()`?

No. Interfaces cannot provide a default implementation that overrides a public method of `Object` such as `equals`, `hashCode`, or `toString`.

---

# 22. Static Methods in Interfaces

Interfaces may define static methods.

```java
interface MathUtil {
    static int square(int x) {
        return x * x;
    }
}
```

Call it using the interface name:

```java
int result = MathUtil.square(5);
```

Static interface methods are **not inherited** by implementing classes.

So this is invalid:

```java
class Foo implements MathUtil {
}

// Foo.square(5); // not inherited
```

## Interview question

Why use a static method in an interface?

To keep utility behavior conceptually associated with the interface without requiring an implementing object.

---

# 23. Private Methods in Interfaces

Introduced in **Java 9**, private interface methods allow shared implementation among interface methods. citeturn609565search7

```java
interface Audit {
    default void info(String message) {
        write("INFO", message);
    }

    default void error(String message) {
        write("ERROR", message);
    }

    private void write(String level, String message) {
        System.out.println(level + ": " + message);
    }
}
```

This is useful for avoiding duplication inside an interface.

---

# 24. Functional Interfaces and Default Methods

A functional interface has exactly **one abstract method**.

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}
```

It can be used with a lambda:

```java
Calculator add = (a, b) -> a + b;
```

Adding a default method does not remove its functional-interface status:

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);

    default void description() {
        System.out.println("Calculator");
    }
}
```

The interface still has one abstract method.

## Tricky question

What happens if another abstract method is added?

It is no longer a functional interface, and existing lambda assignments targeting it will stop compiling.

---

# 25. Representative Interfaces vs Mixin Interfaces

A useful conceptual distinction from the source is:

## Representative interface

The interface represents the main type/protocol.

Example:

```java
interface ListLike<E> {
    E get(int index);
    int size();
}
```

The interface describes the core behavior expected from its implementations.

## Mixin/capability interface

The interface adds a capability.

```java
interface Shareable {
    void share();
}
```

A class can have its own primary identity and optionally gain the capability.

Examples from the Java API include concepts such as `Comparable` and `Runnable`.

A good interview phrasing is:

> “A mixin-like interface represents a capability or role rather than the object's entire identity.”

---

# 26. Refer to Objects by Their Interfaces

A strong design habit is to use the least specific useful type.

Prefer:

```java
List<String> names = new ArrayList<>();
```

over:

```java
ArrayList<String> names = new ArrayList<>();
```

Then method parameters can also stay general:

```java
void sortNames(List<String> names) {
    names.sort(String::compareToIgnoreCase);
}
```

This allows callers to pass an `ArrayList`, `LinkedList`, or another `List` implementation.

## When not to use the interface type

Use a concrete type when the caller genuinely needs concrete-specific behavior.

The principle is not:

> “Always use interfaces.”

It is:

> “Expose the least specific type that provides everything the client needs.”

---

# 27. `Object`: The Root of Class Inheritance

Every class in Java ultimately derives from `java.lang.Object`, directly or indirectly.

```java
class User {
}
```

is effectively based on:

```java
class User extends Object {
}
```

The `Object` class provides fundamental methods including:

- `equals`
- `hashCode`
- `toString`
- `getClass`
- `clone`
- monitor/wait-notify methods

The Java 27 API documents these methods directly on `Object`. citeturn936174search0turn609565search1

---

# 28. `toString()`

`toString()` provides a string representation of an object.

Default implementations are usually not useful for domain objects, so classes commonly override it.

```java
class User {
    private final int id;
    private final String name;

    User(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "'}";
    }
}
```

Now:

```java
System.out.println(new User(10, "Alice"));
```

is much more useful when debugging.

## Tricky question: Does default `toString()` return the memory address?

Do not say “memory address.” The default representation is based on the runtime class name and a hexadecimal form of a hash code; it should not be described as a raw memory address.

---

# 29. `equals()` and `==`

For reference types:

```java
==
```
checks whether two references refer to the same object.

`equals()` asks whether two objects are logically equal according to the class's implementation.

The default implementation in `Object` uses identity equality. citeturn936174search7

Example:

```java
User a = new User(1, "Alice");
User b = new User(1, "Alice");
```

Unless `User.equals()` is overridden appropriately:

```java
a == b       // false
```

and likely:

```java
a.equals(b)  // false
```

## Interview question

What is the difference between `==` and `equals()`?

**Answer:** For object references, `==` compares identity; `equals()` compares according to the object's equality contract.

---

# 30. The `equals()` Contract

When implementing `equals()`, remember the standard properties:

- reflexive: `x.equals(x)` is true
- symmetric: `x.equals(y)` matches `y.equals(x)`
- transitive
- consistent while relevant state does not change
- false for `null`

A broken `equals()` implementation can cause bugs in `HashSet`, `HashMap`, testing, and caching.

## Better modern example

```java
final class User {
    private final int id;

    User(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof User other)) return false;
        return id == other.id;
    }

    @Override
    public int hashCode() {
        return Integer.hashCode(id);
    }
}
```

Pattern matching for `instanceof` in the example is a Java 16 feature. citeturn918864search5

---

# 31. `hashCode()` and the Critical Contract

The source notes incorrectly described `hashCode()` as being based on the memory address. Java does **not** make that guarantee.

The crucial contract is:

> If two objects are equal according to `equals()`, they must have the same hash code.

The reverse is not required.

```text
equals == true  → hashCode must match
hashCode matches → does NOT imply equals
```

This is why overriding `equals()` usually requires overriding `hashCode()`.

## Interview question

Why are `equals()` and `hashCode()` usually overridden together?

Because hash-based collections use the hash code to locate candidate buckets and then use equality to establish logical equality. If equal objects have different hash codes, the collection contract is broken.

---

# 32. Mutable Keys: A Very Good Interview Trap

This is dangerous:

```java
Map<User, String> map = new HashMap<>();

User user = new User(10);
map.put(user, "Alice");

user.changeId(20);
```

If the fields used by `equals()` and `hashCode()` change after insertion, the entry may no longer be findable using the mutated key.

A common best practice is to use immutable objects as keys.

This concept connects directly to the Collections notes.

---

# 33. `getClass()`

`getClass()` returns the runtime class of an object.

```java
User user = new Staff();

System.out.println(user.getClass());
```

This is different from:

```java
User.class
```

which refers to the `Class` object representing the `User` type itself.

## Interview question

What does `getClass()` tell you?

It gives you the runtime class of the object on which it is invoked.

The method is final in `Object`, so it cannot be overridden. citeturn936174search1

---

# 34. `clone()`, `Cloneable`, and Why Cloning Is Tricky

This is an interview favorite because many candidates know the vocabulary but not the semantics.

`Object.clone()` is a protected method. It performs a field-by-field copy under the conventional cloning model. `Cloneable` is a marker interface; it does **not** declare a `clone()` method. citeturn936174search6

Typical form:

```java
class Person implements Cloneable {
    int age;

    @Override
    public Person clone() throws CloneNotSupportedException {
        return (Person) super.clone();
    }
}
```

## Shallow copy

Suppose:

```java
class Person implements Cloneable {
    String name;
    String[] hobbies;
}
```

A shallow clone copies the reference to `hobbies`, not the array itself.

So both objects can point to the same array.

## Deep copy

To make the array independent:

```java
@Override
public Person clone() throws CloneNotSupportedException {
    Person copy = (Person) super.clone();
    copy.hobbies = this.hobbies.clone();
    return copy;
}
```

The Java API documentation explicitly describes `Object.clone()` as a shallow field-level copy under its conventional semantics and notes that mutable referenced state may need separate copying for independence. citeturn936174search6

## Interview question: Does `Cloneable` contain `clone()`?

No. It is a marker interface. The `clone()` method comes from `Object`.

## Should you prefer `Cloneable` for normal application design?

Be cautious. Cloning has awkward API semantics and shallow/deep-copy concerns. Copy constructors or explicit copy/factory methods are often easier to reason about.

---

# 35. Shallow Copy vs Deep Copy

```text
Original
  |
  +---- field: int --------> copied value
  |
  +---- field: Object -----+---- same referenced object (shallow)
                            |
                            +---- new copied object (deep)
```

The important question is not merely “Was the object cloned?”

Ask:

> **Are the mutable objects inside it shared or copied?**

---

# 36. Class Hierarchies vs One Giant Class

A common modeling problem is a class with a type field and many conditionals:

```java
class Bookmark {
    String type;

    void open() {
        if (type.equals("BOOK")) {
            // book behavior
        } else if (type.equals("MOVIE")) {
            // movie behavior
        } else if (type.equals("WEBLINK")) {
            // web link behavior
        }
    }
}
```

As the number of types grows, conditional logic often becomes difficult to maintain.

A polymorphic hierarchy can move behavior to the types that own it:

```java
abstract class Bookmark {
    abstract void open();
}

class Book extends Bookmark {
    @Override
    void open() {
        System.out.println("Open book");
    }
}

class Movie extends Bookmark {
    @Override
    void open() {
        System.out.println("Play movie");
    }
}
```

## But do not overuse inheritance

The correct interview answer is not:

> “Always prefer inheritance to a giant class.”

It is:

> “When behavior varies by subtype and there is a real subtype relationship, polymorphism can remove type-based conditionals. Otherwise, composition or a data-oriented design may be better.”

---

# 37. Multiple Inheritance of Classes

Java does not allow:

```java
class C extends A, B { } // invalid
```

A class has one direct superclass.

This avoids ambiguities involving inherited state and implementation.

Java instead allows multiple interface inheritance/implementation.

```java
class C implements A, B { }
```

## Interview question: Does Java support multiple inheritance?

Best answer:

> “Java does not support multiple inheritance of classes, but a class can implement multiple interfaces, and an interface can extend multiple interfaces.”

That is more precise than simply saying “Java does not support multiple inheritance.”

---

# 38. Sealed Classes: Modern Controlled Inheritance

Java 17 introduced sealed classes and interfaces as a permanent language feature. citeturn918864search2turn918864search4

```java
sealed abstract class Shape
        permits Circle, Rectangle {
}

final class Circle extends Shape {
}

final class Rectangle extends Shape {
}
```

A permitted subclass must itself fit the permitted hierarchy rules, typically by being `final`, `sealed`, or `non-sealed`.

## Why use sealed classes?

When the domain has a controlled set of variants and you want the compiler and type system to know that set.

Examples:

```text
Payment
 ├── CardPayment
 ├── CashPayment
 └── TransferPayment
```

This becomes especially powerful when combined with pattern matching and exhaustive switches.

---

# 39. Pattern Matching Makes Casting Cleaner

Traditional code:

```java
if (shape instanceof Circle) {
    Circle c = (Circle) shape;
    c.radius();
}
```

Modern Java:

```java
if (shape instanceof Circle c) {
    System.out.println(c.radius());
}
```

Pattern matching for `instanceof` became permanent in Java 16. citeturn918864search5

## Interview question

What problem does pattern matching for `instanceof` solve?

It reduces duplicated type-checking and explicit casting, while keeping the variable's scope tied to a successful match.

---

# 40. A Better Design Mental Model

When designing an OOP hierarchy, ask these questions in order:

### 1. Is there a real subtype relationship?

If not, consider composition.

### 2. Does the parent define a meaningful common contract?

If not, inheritance may be unnecessary.

### 3. Is there shared implementation/state?

An abstract class may fit.

### 4. Is this a capability or role?

An interface may fit better.

### 5. Are there only a fixed number of allowed subtypes?

Consider a sealed hierarchy.

### 6. Do callers need the concrete implementation?

If not, expose the interface or least-specific useful type.

---

# 41. High-Value Interview Questions

## Q1. Can constructors be inherited?

**No.** Constructors are not inherited. A subclass has its own constructors, explicit or compiler-generated.

## Q2. Can an abstract class have a constructor?

**Yes.** It can initialize state needed by subclasses.

## Q3. Can an abstract class be instantiated?

**No.** A concrete subclass must be instantiated instead.

## Q4. Can an abstract class have no abstract methods?

**Yes.** `abstract` can be used to prevent direct instantiation and communicate design intent.

## Q5. Can an abstract method be `private`?

**No.** A private method cannot be overridden, while an abstract method requires implementation by a subclass.

## Q6. Can an abstract method be `static`?

**No.** Static methods are not overridden.

## Q7. Can a final class be abstract?

**No.** It would be contradictory: an abstract class is meant to be extended, while a final class cannot be extended.

## Q8. Can a static method be overridden?

**No.** It can be hidden.

## Q9. Can fields be overridden?

**No.** Fields are hidden, not overridden.

## Q10. What happens if `equals()` is overridden but `hashCode()` is not?

Hash-based collections may behave incorrectly because logically equal objects can have different hash codes.

## Q11. What is upcasting?

Treating a subtype object as a supertype reference:

```java
User u = new Staff();
```

## Q12. What is downcasting?

Converting a supertype reference back to a subtype reference:

```java
Staff s = (Staff) u;
```

It is only safe when the runtime object is actually compatible.

## Q13. Which is decided at compile time: overload or override?

Overload selection is compile-time. Overridden instance method dispatch occurs at runtime.

## Q14. Why use `@Override`?

It lets the compiler verify that the method really overrides an inherited method and catches mistakes such as incorrect signatures.

## Q15. Can an interface contain fields?

Yes, but they are implicitly `public static final` constants.

## Q16. Can interfaces contain implemented methods?

Yes. Since Java 8, they can contain `default` and `static` methods; since Java 9, they can also contain private methods. citeturn918864search7turn609565search7

## Q17. Can a class implement multiple interfaces?

Yes.

## Q18. Can an interface extend multiple interfaces?

Yes.

## Q19. Can a class extend multiple classes?

No.

## Q20. Why does Java not allow multiple class inheritance?

The language avoids ambiguity around inherited state and implementation and keeps class inheritance single-parent.

## Q21. What is the difference between `super.method()` and normal `method()`?

`method()` participates in normal dynamic dispatch. `super.method()` explicitly invokes the superclass implementation.

## Q22. What is the purpose of a sealed class?

To control exactly which types can directly extend a class/interface. This became a permanent feature in Java 17. citeturn918864search2

## Q23. What does `Cloneable` actually do?

It marks a class as permitting the `Object.clone()` mechanism under the conventional cloning contract. It does not declare `clone()` itself. citeturn936174search6

## Q24. Is `super.clone()` a deep copy?

No. It is normally a shallow field-by-field copy. Mutable referenced objects may still be shared.

## Q25. What is the difference between `getClass()` and `ClassName.class`?

`getClass()` obtains the runtime class of an object. `ClassName.class` refers to the class literal for that named type.

---

# 42. Tricky Code Questions

## Question 1

```java
class A {
    void go() {
        System.out.println("A");
    }
}

class B extends A {
    @Override
    void go() {
        System.out.println("B");
    }
}

A x = new B();
x.go();
```

**Answer:** `B`.

Reason: instance method overriding uses runtime dispatch.

---

## Question 2

```java
class A {
    static void go() {
        System.out.println("A");
    }
}

class B extends A {
    static void go() {
        System.out.println("B");
    }
}

A x = new B();
x.go();
```

**Answer:** `A`.

Reason: static method hiding uses the reference type.

---

## Question 3

```java
class A {
    int x = 1;
}

class B extends A {
    int x = 2;
}

A obj = new B();
System.out.println(obj.x);
```

**Answer:** `1`.

Fields are hidden, not polymorphically overridden.

---

## Question 4

```java
class A {
    void go(double x) {
        System.out.println("A double");
    }
}

class B extends A {
    void go(int x) {
        System.out.println("B int");
    }
}

A obj = new B();
obj.go(10);
```

**Answer:** `A double`.

This is overload selection plus inheritance, not overriding.

---

## Question 5

```java
class A {
    A() {
        System.out.println("A");
    }
}

class B extends A {
    B() {
        System.out.println("B");
    }
}

new B();
```

**Answer:**

```text
A
B
```

The superclass constructor runs first.

---

## Question 6

```java
class A {
    A(int x) {}
}

class B extends A {
    B() {}
}
```

Does this compile?

**No.** `B()` implicitly tries `super()`, but `A()` does not exist.

---

## Question 7

```java
interface A {
    default void go() {
        System.out.println("A");
    }
}

interface B {
    default void go() {
        System.out.println("B");
    }
}

class C implements A, B {
}
```

Does this compile?

**No.** `C` must resolve the conflicting defaults.

---

# 43. Common Incorrect Statements to Avoid in Interviews

Do not say:

> “Java is purely object oriented.”

Java has primitive types.

Do not say:

> “Static methods are overridden.”

They are hidden.

Do not say:

> “Fields are overridden.”

They are hidden.

Do not say:

> “`hashCode()` is the memory address.”

Java does not promise that.

Do not say:

> “`Cloneable` contains the `clone()` method.”

It does not.

Do not say:

> “Protected means accessible everywhere to subclasses.”

Protected access has specific rules, especially across packages.

Do not say:

> “Overriding can reduce visibility.”

It cannot.

Do not say:

> “Interfaces can only contain abstract methods.”

Modern Java interfaces can contain abstract, default, static, and private methods.

Do not say:

> “Java doesn't support multiple inheritance.”

More precisely: Java does not support multiple inheritance of classes, but it supports multiple interface implementation and multiple interface inheritance.

---

# 44. Interview Design Scenarios

## Scenario 1: Payment system

You have:

```text
CardPayment
CashPayment
BankTransfer
```

Question: abstract class or interface?

A possible design:

```java
interface Payment {
    void pay(double amount);
}
```

Use an interface if the main requirement is a common capability/protocol.

A sealed hierarchy is worth considering if the set of payment types is intentionally closed and known.

---

## Scenario 2: Shared state + shared implementation

Suppose every employee has:

- id
- name
- department

and all employees share common helper behavior.

An abstract base class may be useful:

```java
abstract class Employee {
    protected final int id;
    protected final String name;

    protected Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    abstract double calculatePay();
}
```

---

## Scenario 3: Multiple capabilities

A device may be:

- printable
- scannable
- network-connected

These are natural interface capabilities:

```java
interface Printable {}
interface Scannable {}
interface NetworkEnabled {}
```

One class can implement all three.

---

# 45. Recommended Mental Model for Interviews

When you see an OOP question, mentally separate four things:

### 1. Type

What does the compiler think this reference is?

### 2. Object

What object actually exists at runtime?

### 3. Member kind

Is this:

- instance method?
- static method?
- field?
- constructor?

### 4. Dispatch rule

Then apply:

```text
Overload      → compile time
Override      → runtime
Static        → compile-time selection / hiding
Field         → reference-type access
Constructor   → constructor chaining
```

This four-step mental model solves a large percentage of Java interview puzzles.

---

# 46. Modern Java Timeline for These Topics

| Java release | Relevant change |
|---|---|
| Java 8 | Interface `default` and `static` methods; lambdas/functional interfaces |
| Java 9 | Private interface methods |
| Java 16 | Pattern matching for `instanceof` finalized |
| Java 17 | Sealed classes and interfaces finalized |
| Java 21 | Record patterns and pattern matching for `switch` finalized; useful for sealed hierarchies |
| Java 25 | Current LTS release |
| Java 27 | Latest feature release as of September 2026 |

For the topics in this document, Java 8, 9, 16, 17, and 21 are the releases most worth remembering in interviews. Java 27 is the current feature release, but it does not replace the core inheritance/interface concepts covered here. citeturn158071search0turn158071search3turn158071search11

---

# 47. Final Revision Checklist

Before an interview, make sure you can explain these without notes:

- inheritance vs composition
- is-a vs has-a
- access modifiers
- protected across packages
- upcasting vs downcasting
- `instanceof`
- pattern matching for `instanceof`
- polymorphism
- overloading vs overriding
- covariant return types
- checked-exception rules for overriding
- static method hiding
- field hiding
- `super`
- constructor chaining
- constructors not being inherited
- final classes/methods/variables
- sealed classes
- abstract classes
- interfaces
- interface fields
- default/static/private interface methods
- interface conflict resolution
- functional interfaces
- class vs interface design
- representative vs capability/mixin interfaces
- coding to interfaces
- `Object` methods
- `equals()` vs `==`
- `equals()`/`hashCode()` contract
- `toString()`
- `getClass()`
- `clone()` and `Cloneable`
- shallow vs deep copying

If you can answer the tricky code questions by predicting the output **before running the program**, your mental model is in good shape.

---

# Sources and verification notes

The original notes were reorganized and corrected rather than copied verbatim. The main source material covered inheritance, casting, overriding, method binding, constructors, abstract classes, interfaces, marker interfaces, cloning, default methods, and interface static/private methods. fileciteturn5file0L1-L15

For current language/API details, this rewrite was checked against the Java SE/JLS and Oracle/OpenJDK documentation for Java 27, including overriding/hiding rules, `Object`, cloning, pattern matching for `instanceof`, sealed classes, interface evolution, and current JDK release status. citeturn936174search43turn936174search9turn936174search6turn918864search5turn918864search2turn609565search7turn158071search2
