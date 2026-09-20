# Java Collections, Generics, and Nested Classes — Rewritten Notes

> **Scope:** A practical rewrite of the original notes covering the Java Collections Framework, generics, and nested classes.
>
> **Approach:** Learn the common ideas first, then the implementations that solve specific problems. Examples are intentionally small so you can run them and experiment.

---

# Part 1 — The Collections Framework

## 1. The Big Picture

### Interview Prep

**Core concepts to be ready to explain**

- Program to collection interfaces and choose implementations based on required behavior: ordering, uniqueness, lookup, end operations, sorting, and concurrency.

**Interview questions**

**1. Why program to `List` instead of `ArrayList` in a variable declaration?**
**Answer:** It expresses the required abstraction and keeps the code independent of a specific implementation.

**2. Which collection is not a subtype of `Collection`?**
**Answer:** `Map`. `Map` has a separate hierarchy because it represents key/value mappings rather than a collection of elements.

**Tricky question**

**Question:** Is `Map` part of the `Collection` interface hierarchy?
**Answer:** No. `Map` is a separate top-level interface hierarchy in the Collections Framework.

**Mini code check**

**Question:** Which declaration communicates intent best?

```java
List<String> names = new ArrayList<>();
```

**Answer:** The variable depends on the `List` contract while the implementation is `ArrayList`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A **collection** is an object that groups other objects so we can store, retrieve, update, and process them.

Java gives us interfaces that describe *what a collection can do* and classes that provide *how it does it*.

The most useful mental model is:

```text
Iterable
   |
Collection
   |---- List
   |---- Set
   |---- Queue
          |
          Deque

Map is separate from Collection
```

`Map<K,V>` stores key/value mappings. It is part of the Collections Framework, but it does **not** extend `Collection`.

A good rule is:

- `List` → ordered sequence, duplicates allowed
- `Set` → unique elements
- `Queue` → process elements in a queue-like order
- `Deque` → work at both ends
- `Map` → look up a value using a key

---

## 2. `Collection`, `Iterable`, and the Enhanced `for` Loop

### Interview Prep

**Core concepts to be ready to explain**

- `Iterable` is what enables enhanced `for` iteration. `Collection` extends `Iterable` and adds collection-specific operations. A type can be iterable without being a `Collection`.

**Interview questions**

**1. Does enhanced `for` require `Collection`?**
**Answer:** No. It works with arrays and with any object implementing `Iterable` for which an iterator can be obtained.

**2. What does `iterator()` provide?**
**Answer:** An object used to traverse elements sequentially.

**Tricky question**

**Question:** Can a custom class support `for (x : obj)` without implementing `Collection`?
**Answer:** Yes. Implement `Iterable<T>` and provide an `iterator()`.

**Mini code check**

**Question:** What must a custom iterable provide?

```java
class Bag implements Iterable<String> {
    public Iterator<String> iterator() { ... }
}
```

**Answer:** An `Iterator<String>` returned by `iterator()` is enough for the enhanced loop.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`Collection<E>` is the common interface for most collections. It extends `Iterable<E>`.

That distinction matters:

> An object only needs to implement `Iterable` to work with the enhanced `for` loop. It does **not** have to implement `Collection`.

For example:

```java
class Countdown implements Iterable<Integer> {
    @Override
    public Iterator<Integer> iterator() {
        return List.of(3, 2, 1).iterator();
    }
}

for (int n : new Countdown()) {
    System.out.println(n);
}
```

### `AbstractCollection`

`AbstractCollection` is a **skeletal implementation** of `Collection`. It is not a superclass of the `Collection` interface.

It exists to make it easier to build your own collection implementation.

In normal application code, you will usually use a standard implementation such as `ArrayList` or `HashSet` instead of implementing `Collection` yourself.

### Common `Collection` methods

```java
Collection<String> names = new ArrayList<>();

names.add("Ana");
names.add("Ben");

System.out.println(names.contains("Ana")); // true
System.out.println(names.size());           // 2
System.out.println(names.isEmpty());        // false

names.remove("Ben");
names.clear();
```

Bulk operations include:

```java
a.addAll(b);
a.removeAll(b);
a.retainAll(b);
```

`retainAll` means: keep only elements that also occur in the other collection.

### Converting to an array

```java
Collection<String> names = List.of("Ana", "Ben");

String[] array = names.toArray(new String[0]);
```

The `new String[0]` idiom is concise and lets the collection create an appropriately sized array.

---

# Part 2 — Lists

## 3. The `List` Interface

### Interview Prep

**Core concepts to be ready to explain**

- `List` represents an ordered, indexable sequence and permits duplicates. `add(index, value)` returns `void`; `remove(index)` returns the removed element.

**Interview questions**

**1. Why does `List.add(int, E)` not return the old value?**
**Answer:** The indexed insertion method has a `void` return type; `set(index, value)` is the operation that replaces and returns the previous element.

**2. What does `subList(from, to)` return?**
**Answer:** A view over a range of the original list, not an independent copy.

**Tricky question**

**Question:** What is `list.remove(1)` for `List<Integer>`?
**Answer:** It removes by index and returns the removed `Integer`, not “the integer value 1” as an object-removal call.

**Mini code check**

**Question:** How do you remove the value `1` from `List<Integer>` rather than index 1?

```java
list.remove(Integer.valueOf(1));
```

**Answer:** The `Integer` overload selects `remove(Object)` rather than `remove(int)`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A `List` represents an ordered sequence.

It:

- preserves encounter order,
- allows duplicate elements,
- supports positional access by index.

Example:

```java
List<String> tasks = new ArrayList<>();

tasks.add("compile");
tasks.add("test");
tasks.add("deploy");
tasks.add("test");

System.out.println(tasks);
// [compile, test, deploy, test]
```

Useful methods:

```java
tasks.get(1);              // test
tasks.set(1, "verify");    // replaces test
tasks.add(1, "package");   // inserts
tasks.remove(0);           // removes by index
tasks.indexOf("test");     // first matching index
tasks.lastIndexOf("test");
```

A list index starts at `0`.

### Range views with `subList`

```java
List<String> tasks = new ArrayList<>(
    List.of("compile", "test", "package", "deploy")
);

List<String> middle = tasks.subList(1, 3);

System.out.println(middle);
// [test, package]
```

The upper bound is exclusive.

Important: `subList` is normally a **view backed by the original list**, not an independent copy.

So:

```java
middle.set(0, "verify");

System.out.println(tasks);
// [compile, verify, package, deploy]
```

Use `new ArrayList<>(tasks.subList(...))` when you need an independent list.

---

## 4. `ArrayList` — The Default List

### Interview Prep

**Core concepts to be ready to explain**

- `ArrayList` is a resizable-array `List`. `get` is constant time; insert/remove in the middle generally require shifting elements. Growth strategy is an implementation detail, not a percentage you should memorize.

**Interview questions**

**1. Why is `ArrayList.get(i)` fast?**
**Answer:** It can directly index the backing array.

**2. What happens when capacity is exhausted?**
**Answer:** The implementation grows its internal storage and copies/moves elements as needed.

**Tricky question**

**Question:** Is `size()` the same as capacity?
**Answer:** No. `size` is the number of elements. Capacity is internal storage available before another growth step.

**Mini code check**

**Question:** What is the complexity pattern?

```java
list.add(0, "new");
String x = list.get(list.size() - 1);
```

**Answer:** The insertion at index 0 is typically O(n) due to shifting; `get` is O(1).

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


For most everyday list work, `ArrayList` is the first implementation to consider.

```java
List<String> users = new ArrayList<>();

users.add("Alice");
users.add("Bob");
users.add("Charlie");

System.out.println(users.get(1)); // Bob
```

### Why is it fast?

`ArrayList` is backed by an array.

That makes indexed access efficient:

```java
users.get(2);
```

is constant-time in normal use.

Appending is **amortized constant time**. Occasionally the internal array must grow, but the growth policy is an implementation detail and should not be assumed to be “exactly 50% larger”.

You can request extra capacity when you know many elements are coming:

```java
ArrayList<String> users = new ArrayList<>();
users.ensureCapacity(10_000);
```

### Typical costs

| Operation | Typical complexity |
|---|---:|
| `get(index)` | O(1) |
| `set(index, value)` | O(1) |
| append | O(1) amortized |
| insert/remove in middle | O(n) |
| search (`contains`, `indexOf`) | O(n) |

Example:

```java
users.add(1, "David");
```

Elements after index `1` must shift right.

### When to choose it

Use `ArrayList` when:

- you want a general-purpose list,
- you often read by index,
- you mostly append,
- you iterate frequently.

---

## 5. `LinkedList` — Useful Mainly as a `Deque`

### Interview Prep

**Core concepts to be ready to explain**

- `LinkedList` is a doubly linked list and also implements `Deque`. It can add/remove at the ends efficiently, but indexed access is linear. In many queue/stack use cases, `ArrayDeque` is a better default.

**Interview questions**

**1. Why is `LinkedList.get(i)` slow?**
**Answer:** It must traverse from one end to reach the target node.

**2. Why might you use `LinkedList` as a `Deque`?**
**Answer:** It supports deque operations and can be useful when its particular semantics fit, although `ArrayDeque` is usually preferred for general queue/stack work.

**Tricky question**

**Question:** Does a linked list make arbitrary insertion “O(1)” in all cases?
**Answer:** Only after you already have the relevant node/position. Finding an indexed position can still take O(n).

**Mini code check**

**Question:** Which is a deque operation?

```java
LinkedList<String> q = new LinkedList<>();
q.addFirst("A");
q.addLast("B");
```

**Answer:** Both are constant-time end operations for a linked list.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`LinkedList` is a doubly-linked list and implements both `List` and `Deque`.

```java
LinkedList<String> list = new LinkedList<>();

list.addLast("A");
list.addLast("B");
list.addLast("C");

System.out.println(list.getFirst()); // A
System.out.println(list.getLast());  // C
```

A linked list does **not** make arbitrary indexed access fast:

```java
list.get(1000);
```

still requires traversal.

Insertion/removal can be efficient when the list position is already known, but finding that position may itself take linear time.

In practice:

> Prefer `ArrayList` for ordinary lists. Prefer `ArrayDeque` for queue/deque behavior.

`LinkedList` remains useful when you specifically need its API or semantics.

---

# Part 3 — Iteration and Queues

## 6. `Iterator` and Safe Removal

### Interview Prep

**Core concepts to be ready to explain**

- Use `Iterator.remove()` when you need to remove the last element returned by that iterator during iteration. Direct structural modification of many collections while iterating may trigger fail-fast behavior.

**Interview questions**

**1. Why call `iterator.remove()` instead of `list.remove(...)`?**
**Answer:** The iterator can update its own traversal state consistently while removing the element it just returned.

**2. Does fail-fast guarantee a `ConcurrentModificationException`?**
**Answer:** No. Fail-fast behavior is best-effort and should not be treated as a correctness mechanism.

**Tricky question**

**Question:** Can you call `iterator.remove()` twice in a row without another `next()`?
**Answer:** No. It is illegal to call `remove()` twice for the same returned element; the iterator state does not allow it.

**Mini code check**

**Question:** What remains?

```java
Iterator<String> it = names.iterator();
while (it.hasNext()) {
    if (it.next().isBlank()) it.remove();
}
```

**Answer:** Blank strings are safely removed through the iterator.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Every `Iterable` can provide an `Iterator`.

```java
List<String> names = new ArrayList<>(
    List.of("Alice", "Bob", "Cara")
);

Iterator<String> it = names.iterator();

while (it.hasNext()) {
    String name = it.next();
    System.out.println(name);
}
```

### Removing while iterating

Do not normally do this:

```java
for (String name : names) {
    if (name.startsWith("A")) {
        names.remove(name);   // unsafe pattern
    }
}
```

Structural modification while an iterator is active can cause `ConcurrentModificationException`, although fail-fast behavior is best-effort and must not be used as a program-correctness mechanism.

Use the iterator itself:

```java
Iterator<String> it = names.iterator();

while (it.hasNext()) {
    String name = it.next();

    if (name.startsWith("A")) {
        it.remove();
    }
}
```

Modern alternative:

```java
names.removeIf(name -> name.startsWith("A"));
```

For simple filtering, `removeIf` is usually easier to read.

### `Iterable.forEach`

```java
names.forEach(name -> System.out.println(name));
```

`Consumer<T>` is the functional interface used by this method.

---

## 7. `ListIterator`

### Interview Prep

**Core concepts to be ready to explain**

- `ListIterator` is for lists. It moves forward and backward and supports `add`, `remove`, and `set` on modifiable lists.

**Interview questions**

**1. Why can’t `ListIterator` be obtained from a `Set`?**
**Answer:** It is specifically defined for `List` and depends on positional ordering/index semantics.

**2. What does the cursor represent?**
**Answer:** A position between elements, not a “current element” reference.

**Tricky question**

**Question:** Why is `listIterator.add(x)` safe during iteration?
**Answer:** The iterator defines the cursor and updates its internal state as part of its specified modification operation.

**Mini code check**

**Question:** What list is produced?

```java
List<String> xs = new ArrayList<>(List.of("A", "C"));
ListIterator<String> it = xs.listIterator(1);
it.add("B");
```

**Answer:** `[A, B, C]`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`ListIterator` is an iterator specialized for lists.

It can move in both directions and can modify a modifiable list.

Use a modifiable list in examples:

```java
List<String> names = new ArrayList<>(List.of("A", "B", "C"));
ListIterator<String> it = names.listIterator();

while (it.hasNext()) {
    String name = it.next();

    if (name.equals("B")) {
        it.set("Beta");
    }
}

System.out.println(names);
// [A, Beta, C]
```

It also supports:

```java
it.add("X");
it.remove();
it.set("Y");
it.previous();
```

### Cursor mental model

A `ListIterator` cursor sits **between** elements:

```text
[A] [B] [C]
 ^
cursor
```

`next()` moves forward and `previous()` moves backward.

---

## 8. Queues and Deques

### Interview Prep

**Core concepts to be ready to explain**

- Know the four common Queue operation pairs: `add`/`offer`, `remove`/`poll`, `element`/`peek`. The first in each pair may throw where the second returns a sentinel.

**Interview questions**

**1. `poll()` vs `remove()`?**
**Answer:** Both remove the head; `poll()` returns `null` when empty, while `remove()` throws `NoSuchElementException`.

**2. `peek()` vs `element()`?**
**Answer:** Both inspect the head without removing; `peek()` returns `null` when empty, `element()` throws.

**Tricky question**

**Question:** Why are `null` elements discouraged in queues?
**Answer:** Because a `null` return from methods such as `poll` or `peek` may represent an empty queue, creating ambiguity.

**Mini code check**

**Question:** What happens on an empty queue?

```java
Queue<String> q = new ArrayDeque<>();
System.out.println(q.poll());
// q.remove(); // would throw
```

**Answer:** `poll()` prints `null`; `remove()` would throw.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A `Queue` normally represents a FIFO structure:

```text
First in → First out
A → B → C
^
remove here
```

Example:

```java
Queue<String> queue = new ArrayDeque<>();

queue.offer("Alice");
queue.offer("Bob");

System.out.println(queue.peek()); // Alice

System.out.println(queue.poll()); // Alice
System.out.println(queue.poll()); // Bob
```

The method pairs are important:

| Action | Throws if impossible/empty | Safer return |
|---|---|---|
| insert | `add` | `offer` |
| remove | `remove` | `poll` |
| inspect | `element` | `peek` |

For most application code, `offer`, `poll`, and `peek` are easy defaults.

---

## 9. `Deque` — Two Ends, Two Possible Behaviors

### Interview Prep

**Core concepts to be ready to explain**

- A deque supports both ends and can implement FIFO queue behavior or LIFO stack behavior. Prefer the deque interface and avoid the legacy `Stack` class for new stack code.

**Interview questions**

**1. How do you use a deque as a stack?**
**Answer:** Use `push`, `pop`, and `peek` (head-based operations).

**2. How do you use a deque as a queue?**
**Answer:** Use `offerLast`/`addLast` and `pollFirst`/`removeFirst`, or the corresponding Queue methods.

**Tricky question**

**Question:** Does `Deque` extend `Stack`?
**Answer:** No. `Deque` extends `Queue`; stack behavior is provided by methods such as `push` and `pop`.

**Mini code check**

**Question:** What prints?

```java
Deque<Integer> d = new ArrayDeque<>();
d.push(1);
d.push(2);
System.out.println(d.pop());
```

**Answer:** `2`, because the most recently pushed element is removed first.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A `Deque` (double-ended queue) allows operations at both ends.

```java
Deque<String> deque = new ArrayDeque<>();

deque.addLast("B");
deque.addFirst("A");
deque.addLast("C");

System.out.println(deque);
// [A, B, C]
```

You can use it as a queue:

```java
deque.offerLast("D");
String first = deque.pollFirst();
```

or as a stack:

```java
deque.push("X");
String value = deque.pop();
```

A `Deque` does **not** extend the old `Stack` class. In modern Java, prefer `Deque`/`ArrayDeque` for stack behavior.

---

## 10. `ArrayDeque` — Usually the Best General-Purpose Deque

### Interview Prep

**Core concepts to be ready to explain**

- `ArrayDeque` is a resizable-array implementation of `Deque` and generally a strong default for stack/queue use. It does not permit `null` elements.

**Interview questions**

**1. Why prefer `ArrayDeque` over `Stack`?**
**Answer:** `Stack` is a legacy class; `ArrayDeque` provides modern deque-based stack operations without the legacy design.

**2. Can `ArrayDeque` contain `null`?**
**Answer:** No.

**Tricky question**

**Question:** Does `ArrayDeque` implement `List`?
**Answer:** No. It implements `Deque`, which is a different abstraction.

**Mini code check**

**Question:** Queue mode or stack mode?

```java
Deque<String> d = new ArrayDeque<>();
d.offerLast("A");
d.offerLast("B");
System.out.println(d.pollFirst());
```

**Answer:** Queue mode: prints `A`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


For a queue or stack, `ArrayDeque` is generally the implementation to try first.

```java
Deque<Integer> numbers = new ArrayDeque<>();

numbers.addLast(10);
numbers.addLast(20);

System.out.println(numbers.pollFirst()); // 10
```

For stack behavior:

```java
Deque<Integer> stack = new ArrayDeque<>();

stack.push(10);
stack.push(20);

System.out.println(stack.pop()); // 20
```

Important properties:

- resizable,
- array-based,
- supports both ends,
- does not permit `null`.

This constructor:

```java
new ArrayDeque<>(100)
```

specifies an **initial capacity**, not a permanently fixed capacity.

---

# Part 4 — Sets

## 11. `Set` and `HashSet`

### Interview Prep

**Core concepts to be ready to explain**

- A set stores unique elements according to its equality semantics. `HashSet` is hash-based and generally offers fast membership operations without guaranteeing encounter order.

**Interview questions**

**1. How does `HashSet` decide whether an object is already present?**
**Answer:** Hashing narrows the candidate area and equality is used to determine logical equivalence.

**2. Does `HashSet` preserve insertion order?**
**Answer:** No guarantee of insertion order.

**Tricky question**

**Question:** Can a `HashSet` contain two objects with the same hash code?
**Answer:** Yes. Different unequal objects can collide; the hash code is not required to be unique.

**Mini code check**

**Question:** What is the size?

```java
Set<String> s = new HashSet<>();
s.add("A");
s.add("A");
System.out.println(s.size());
```

**Answer:** `1`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A `Set` represents unique elements.

```java
Set<String> tags = new HashSet<>();

tags.add("java");
tags.add("collections");
tags.add("java");

System.out.println(tags.size()); // 2
```

The second `"java"` is not added.

A `HashSet` is hash-table based. It does not promise a stable or insertion-based iteration order.

For expected constant-time basic operations, hash quality matters.

### `equals` and `hashCode`

When storing custom objects in a hash-based collection, the object's equality contract matters.

```java
record Book(String title, int year) {}
```

Records are convenient because Java generates `equals` and `hashCode` based on the record components.

```java
Set<Book> books = new HashSet<>();

books.add(new Book("Java Basics", 2026));
books.add(new Book("Java Basics", 2026));

System.out.println(books.size()); // 1
```

For a normal class, if you override `equals`, you should also override `hashCode`.

The contract is:

> If two objects are equal according to `equals`, they must have the same `hashCode`.

The reverse is not required: two unequal objects may have the same hash code.

---

## 12. `LinkedHashSet` and Encounter Order

### Interview Prep

**Core concepts to be ready to explain**

- `LinkedHashSet` combines set semantics with a predictable insertion-order encounter sequence. It uses additional links, so it has more overhead than `HashSet`.

**Interview questions**

**1. What does `LinkedHashSet` add over `HashSet`?**
**Answer:** Predictable encounter order, specifically insertion order for ordinary usage.

**2. Does it allow duplicates?**
**Answer:** No. It remains a `Set`.

**Tricky question**

**Question:** Does preserving insertion order mean the set is sorted?
**Answer:** No. Insertion order and sorted order are different concepts.

**Mini code check**

**Question:** What prints?

```java
var s = new LinkedHashSet<>(List.of("B", "A", "C"));
System.out.println(s);
```

**Answer:** `[B, A, C]`, preserving insertion order.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`LinkedHashSet` gives you set semantics plus a predictable encounter order: insertion order.

```java
Set<String> languages = new LinkedHashSet<>();

languages.add("Java");
languages.add("Python");
languages.add("Go");

System.out.println(languages);
// [Java, Python, Go]
```

Use it when:

- duplicates must be removed,
- but iteration order matters.

It usually has a little more overhead than `HashSet` because it maintains linking information.

---

## 13. `SequencedCollection` — Java 21 Upgrade

### Interview Prep

**Core concepts to be ready to explain**

- Java 21 introduced `SequencedCollection`, `SequencedSet`, and `SequencedMap` through JEP 431, giving ordered collections common first/last/reversed operations.

**Interview questions**

**1. When were sequenced collection interfaces introduced?**
**Answer:** Java 21.

**2. Why were they added?**
**Answer:** To provide a common abstraction for collections with a defined encounter order and consistent operations at both ends/reverse views.

**Tricky question**

**Question:** Does Java 21 make every collection ordered?
**Answer:** No. The interfaces model collections whose encounter order is defined; unordered collections such as ordinary `HashSet` remain unordered.

**Mini code check**

**Question:** What does this show?

```java
List<String> xs = new ArrayList<>(List.of("A", "B", "C"));
System.out.println(xs.getFirst());
System.out.println(xs.getLast());
System.out.println(xs.reversed());
```

**Answer:** `A`, `C`, then a reversed view/result with `C` first. These APIs are from the Java 21 sequenced-collection update.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


**Java 21** introduced:

- `SequencedCollection`
- `SequencedSet`
- `SequencedMap`

These APIs were added by **JEP 431**.

The goal was to give collections with a defined encounter order a common API for:

- first element,
- last element,
- adding/removing at either end where supported,
- reversed views.

For example, with Java 21+:

```java
List<String> names = new ArrayList<>(
    List.of("Alice", "Bob", "Cara")
);

System.out.println(names.getFirst()); // Alice
System.out.println(names.getLast());  // Cara
System.out.println(names.reversed()); // [Cara, Bob, Alice]
```

This is a useful modernization because older code often used implementation-specific methods such as `list.get(list.size() - 1)`.

`LinkedHashSet` became a `SequencedSet`, and `LinkedHashMap` became a `SequencedMap` in Java 21.

---

# Part 5 — Sorted Sets

## 14. `SortedSet` and `NavigableSet`

### Interview Prep

**Core concepts to be ready to explain**

- Focus on the contract of **14. SortedSet and NavigableSet**: what problem it solves, what guarantees it provides, and what it does *not* guarantee. For interviews, always connect the API to a concrete use case and one edge case.

**Interview questions**

**1. What is the main purpose of 14. SortedSet and NavigableSet?**
**Answer:** Use it for the specific behavior described in this section: understand the API contract first, then choose it when your application needs that behavior.

**2. What is a common interview mistake here?**
**Answer:** Memorizing an implementation detail as if it were a language/API guarantee. Prefer documented guarantees and distinguish typical behavior from specified behavior.

**Tricky question**

**Question:** What should you verify before relying on 14. SortedSet and NavigableSet?
**Answer:** Check mutability, ordering, null behavior, equality semantics, exceptions, and complexity guarantees in the API documentation rather than assuming them.

**Mini code check**

**Question:** What would you test first?

```java
// Pick one normal case and one boundary case for the API.
System.out.println("normal");
System.out.println("boundary");
```

**Answer:** In an interview, explain the expected contract before discussing implementation details.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A `SortedSet` keeps elements ordered.

```java
SortedSet<Integer> scores = new TreeSet<>();

scores.add(50);
scores.add(20);
scores.add(80);

System.out.println(scores);
// [20, 50, 80]
```

Useful methods:

```java
scores.first();
scores.last();

scores.headSet(50); // values < 50
scores.tailSet(50); // values >= 50
scores.subSet(20, 80); // 20 <= x < 80
```

`NavigableSet` adds "nearest match" operations:

```java
NavigableSet<Integer> scores = new TreeSet<>(
    List.of(10, 20, 40, 60)
);

System.out.println(scores.floor(35));   // 20
System.out.println(scores.ceiling(35)); // 40
System.out.println(scores.lower(40));   // 20
System.out.println(scores.higher(40));  // 60
```

---

## 15. `TreeSet`, `Comparable`, and `Comparator`

### Interview Prep

**Core concepts to be ready to explain**

- `TreeSet` uses ordering rather than hash equality to organize elements. `Comparable` defines a type’s natural order; `Comparator` supplies an external/custom order.

**Interview questions**

**1. Why can `TreeSet` treat two non-equal objects as duplicates?**
**Answer:** If their ordering comparison returns zero, the tree considers them equivalent for set membership.

**2. What is natural ordering?**
**Answer:** The order a class defines through `Comparable`.

**Tricky question**

**Question:** Should `compareTo` return only -1, 0, or 1?
**Answer:** No. Any negative value, zero, or positive value is sufficient; `Integer.compare` and similar helpers are safer than subtraction.

**Mini code check**

**Question:** What is the safer comparator?

```java
Comparator<Person> byAge =
    Comparator.comparingInt(Person::age);
```

**Answer:** It avoids overflow bugs that can happen with `a.age() - b.age()`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`TreeSet` is a sorted set backed by a tree structure and provides `O(log n)` basic operations.

### Natural ordering with `Comparable`

```java
record Student(String name, int age)
        implements Comparable<Student> {

    @Override
    public int compareTo(Student other) {
        return this.name.compareTo(other.name);
    }
}
```

Then:

```java
Set<Student> students = new TreeSet<>();

students.add(new Student("Zoe", 20));
students.add(new Student("Ana", 22));

System.out.println(students);
// sorted by name
```

### Custom ordering with `Comparator`

```java
Set<String> names = new TreeSet<>(
    Comparator.comparingInt(String::length)
              .thenComparing(String::compareTo)
);

names.add("Bob");
names.add("Alice");
names.add("Dan");

System.out.println(names);
// [Bob, Dan, Alice]
```

Be careful:

> In a `TreeSet`, the comparator determines whether two elements are considered equivalent for set purposes.

If `compare(a, b) == 0`, the tree may treat them as the same set element even when `a.equals(b)` is false.

For sorted collections, it is best for the ordering to be consistent with `equals`.

---

# Part 6 — Maps

## 16. The `Map` Interface

### Interview Prep

**Core concepts to be ready to explain**

- A map associates keys with values. Keys are unique within a map according to the map’s equality/order semantics. `Map` is not a subtype of `Collection`.

**Interview questions**

**1. Can a map contain duplicate keys?**
**Answer:** No. Inserting the same logical key replaces or updates the existing mapping.

**2. Can multiple keys map to the same value?**
**Answer:** Yes.

**Tricky question**

**Question:** What does `put(k, v)` return?
**Answer:** The previous value associated with `k`, or `null` if there was no mapping (subject to the ambiguity when null values are allowed).

**Mini code check**

**Question:** What prints?

```java
Map<String,Integer> m = new HashMap<>();
m.put("A", 1);
m.put("A", 2);
System.out.println(m.get("A"));
```

**Answer:** `2`; the second put replaces the first mapping.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A `Map<K,V>` stores key/value pairs.

```text
studentId → student
```

Example:

```java
Map<Integer, String> students = new HashMap<>();

students.put(101, "Alice");
students.put(102, "Bob");

System.out.println(students.get(101)); // Alice
```

A map:

- cannot have duplicate keys,
- can have duplicate values,
- gives access through keys.

Useful methods:

```java
students.containsKey(101);
students.get(101);
students.remove(101);
students.size();
```

When you want to distinguish "missing key" from "mapped to null", use `containsKey` as needed.

---

## 17. Modern `Map` Operations

### Interview Prep

**Core concepts to be ready to explain**

- Java 8 added useful default methods such as `getOrDefault`, `putIfAbsent`, `computeIfAbsent`, `compute`, `merge`, and `replaceAll`.

**Interview questions**

**1. Why is `merge` useful for counting?**
**Answer:** It lets you update an existing value or insert an initial value in one operation.

**2. When is `computeIfAbsent` useful?**
**Answer:** When a value should be created lazily only if a key is not already mapped.

**Tricky question**

**Question:** Does `getOrDefault` insert the default into the map?
**Answer:** No. It only returns the default when the key is absent; it does not modify the map.

**Mini code check**

**Question:** What is the final count?

```java
Map<String,Integer> c = new HashMap<>();
c.merge("java", 1, Integer::sum);
c.merge("java", 1, Integer::sum);
```

**Answer:** `java -> 2`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Java 8 added many useful default methods to `Map`.

### `getOrDefault`

```java
Map<String, Integer> counts = new HashMap<>();

int count = counts.getOrDefault("java", 0);
```

### `putIfAbsent`

```java
counts.putIfAbsent("java", 1);
```

### `computeIfAbsent`

A very useful pattern:

```java
Map<String, List<String>> courses = new HashMap<>();

courses.computeIfAbsent("Java", key -> new ArrayList<>())
       .add("Collections");
```

Now the list is created only when needed.

### `merge`

```java
Map<String, Integer> counts = new HashMap<>();

counts.merge("java", 1, Integer::sum);
counts.merge("java", 1, Integer::sum);

System.out.println(counts);
// {java=2}
```

---

## 18. `HashMap`

### Interview Prep

**Core concepts to be ready to explain**

- `HashMap` is the general-purpose hash-based map. It gives expected fast lookup/update under good hashing, but no insertion-order guarantee.

**Interview questions**

**1. Average complexity of lookup?**
**Answer:** Typically O(1) expected, with performance affected by hashing and collisions.

**2. Can `HashMap` have a null key?**
**Answer:** Yes; it permits one null key and may permit null values.

**Tricky question**

**Question:** Does `HashMap` guarantee that iteration order stays the same between runs?
**Answer:** No. Do not depend on encounter order.

**Mini code check**

**Question:** What should you use for key lookup?

```java
Map<Integer, String> users = new HashMap<>();
users.put(10, "Ana");
String name = users.get(10);
```

**Answer:** `HashMap` is appropriate for key-based lookup when no ordering requirement exists.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`HashMap` is the general-purpose hash-based map.

```java
Map<String, Integer> inventory = new HashMap<>();

inventory.put("book", 20);
inventory.put("pen", 100);

System.out.println(inventory.get("book")); // 20
```

Java's specification gives expected constant-time performance for basic operations under good hash distribution.

Do not describe that as "every operation is always O(1)".

Other important facts:

- `HashMap` permits one `null` key.
- It permits `null` values.
- It does not guarantee iteration order.
- It is not synchronized.

Its implementation details have evolved. Modern `HashMap` can use tree structures for heavily-colliding buckets; avoid teaching a `HashMap` as "an array of linked lists" as if that were the whole implementation.

---

## 19. Mutable Keys — A Common `Map` Bug

### Interview Prep

**Core concepts to be ready to explain**

- A key should remain stable with respect to `equals` and `hashCode` while stored in a hash-based map. Mutating key state can make an existing entry effectively unreachable.

**Interview questions**

**1. Why is a mutable key dangerous?**
**Answer:** If its hash code or equality-relevant fields change after insertion, lookup may search a different bucket/equality path.

**2. What is a safer key?**
**Answer:** An immutable type such as `String`, `Integer`, or a well-designed immutable value object/record.

**Tricky question**

**Question:** Does changing a value mapped by a key break a `HashMap` lookup?
**Answer:** No, changing the mapped value itself does not affect where the key is stored. Changing the key’s equality/hash behavior can.

**Mini code check**

**Question:** What bug is this?

```java
User u = new User("alice");
map.put(u, "data");
u.setName("bob");
map.get(u);
```

**Answer:** If `name` participates in `equals`/`hashCode`, the lookup may fail because the key’s hash/equality state changed.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A key should not be mutated in a way that changes its `equals` or `hashCode` behavior while it is being used as a key.

Bad idea:

```java
class User {
    String username;

    User(String username) {
        this.username = username;
    }

    @Override
    public int hashCode() {
        return username.hashCode();
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof User other)) return false;
        return username.equals(other.username);
    }
}
```

If:

```java
User user = new User("alice");
Map<User, String> map = new HashMap<>();

map.put(user, "Admin");

user.username = "bob";
```

the map's hashing assumptions have been broken.

### Safer approach

Use immutable key objects.

A record is convenient:

```java
record UserId(String username) {}
```

---

# Part 7 — Ordered Maps

## 20. `LinkedHashMap`

### Interview Prep

**Core concepts to be ready to explain**

- `LinkedHashMap` maintains a predictable encounter order. The default is insertion order; it can also be configured for access-order, which is useful for LRU-style caches.

**Interview questions**

**1. Insertion order vs access order?**
**Answer:** Insertion order reflects insertion sequence; access order moves recently accessed entries toward the end.

**2. Why is `LinkedHashMap` useful for caches?**
**Answer:** Access-order mode plus `removeEldestEntry` can implement simple LRU-like eviction.

**Tricky question**

**Question:** Does `get()` change iteration order in the default insertion-order mode?
**Answer:** No. In access-order mode it can affect encounter order.

**Mini code check**

**Question:** What order is shown?

```java
var m = new LinkedHashMap<Integer,String>(16, .75f, true);
m.put(1,"A"); m.put(2,"B");
m.get(1);
System.out.println(m.keySet());
```

**Answer:** `[2, 1]` in access-order mode.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`LinkedHashMap` maintains a predictable encounter order.

By default, that order is insertion order:

```java
Map<String, Integer> map = new LinkedHashMap<>();

map.put("A", 1);
map.put("B", 2);
map.put("C", 3);

System.out.println(map);
// {A=1, B=2, C=3}
```

It can also maintain **access order**, which is useful for cache-like structures:

```java
LinkedHashMap<String, String> cache =
    new LinkedHashMap<>(16, 0.75f, true);
```

In access-order mode, reading an entry moves it toward the end.

This is one of the classic building blocks for a simple LRU-style cache.

Modern Java 21+ also exposes `LinkedHashMap` through the new `SequencedMap` API.

---

## 21. `TreeMap` and `NavigableMap`

### Interview Prep

**Core concepts to be ready to explain**

- `TreeMap` keeps keys sorted according to natural ordering or a comparator and supports navigation/range operations from `NavigableMap`.

**Interview questions**

**1. When choose `TreeMap` over `HashMap`?**
**Answer:** When sorted keys or navigation/range queries are required.

**2. Typical basic-operation complexity?**
**Answer:** O(log n).

**Tricky question**

**Question:** What happens if a comparator returns zero for different keys?
**Answer:** The map treats them as equivalent for ordering and stores only one mapping for that ordering-equivalence class.

**Mini code check**

**Question:** What is `floorKey(15)` for `{10,20,30}`?

```java
map.floorKey(15);
```

**Answer:** `10`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`TreeMap` keeps keys sorted.

```java
NavigableMap<Integer, String> events = new TreeMap<>();

events.put(100, "Start");
events.put(200, "Checkpoint");
events.put(300, "Finish");

System.out.println(events.floorEntry(250));
// 200=Checkpoint
```

Typical basic operation cost:

- `get`
- `put`
- `remove`

are `O(log n)`.

Useful navigation methods include:

```java
floorKey(key)
ceilingKey(key)
lowerKey(key)
higherKey(key)
firstKey()
lastKey()
```

Java 21 also makes `TreeMap` a `SequencedMap`, but its encounter order is still determined by key ordering. Consequently, `putFirst` and `putLast` are unsupported for `TreeMap`.

---

# Part 8 — Utility APIs

## 22. `Arrays`

### Interview Prep

**Core concepts to be ready to explain**

- `Arrays` is a utility class for arrays: sorting, searching, copying, filling, equality, and converting to lists/strings.

**Interview questions**

**1. What does `Arrays.asList` return?**
**Answer:** A fixed-size list backed by the given array for the object-array overload; you can set elements but cannot structurally add/remove.

**2. Does `Arrays.sort` work in place?**
**Answer:** Yes; it sorts the supplied array.

**Tricky question**

**Question:** Can you call `add` on `Arrays.asList(...)`?
**Answer:** No. Structural modifications throw `UnsupportedOperationException`.

**Mini code check**

**Question:** How do you make it freely resizable?

```java
List<String> xs = new ArrayList<>(Arrays.asList("A", "B"));
xs.add("C");
```

**Answer:** Wrap/copy it into a modifiable collection such as `ArrayList`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`java.util.Arrays` contains utility methods for arrays.

Common operations:

```java
int[] numbers = {5, 2, 9, 1};

Arrays.sort(numbers);

System.out.println(Arrays.toString(numbers));
// [1, 2, 5, 9]
```

Other useful methods:

```java
Arrays.copyOf(...)
Arrays.copyOfRange(...)
Arrays.binarySearch(...)
Arrays.fill(...)
Arrays.equals(...)
```

For object arrays:

```java
Book[] books = ...;

Arrays.sort(
    books,
    Comparator.comparing(Book::title)
);
```

---

## 23. `Collections`

### Interview Prep

**Core concepts to be ready to explain**

- `Collections` is a utility class containing algorithms and wrappers for collection objects. Do not confuse it with the `Collection` interface.

**Interview questions**

**1. `Collection` vs `Collections`?**
**Answer:** `Collection` is an interface; `Collections` is a utility class with static helper methods.

**2. Name a few useful methods.**
**Answer:** `sort`, `reverse`, `binarySearch`, `frequency`, `unmodifiable...`, `synchronized...` and more.

**Tricky question**

**Question:** Does `Collections.unmodifiableList(list)` create an independent immutable copy?
**Answer:** No. It creates an unmodifiable view of the supplied list.

**Mini code check**

**Question:** What fails?

```java
List<String> view = Collections.unmodifiableList(list);
view.add("X");
```

**Answer:** `UnsupportedOperationException` is thrown.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


`java.util.Collections` contains utility methods for collection objects.

Examples:

```java
Collections.sort(list);      // legacy-style utility
Collections.reverse(list);
Collections.shuffle(list);
Collections.min(list);
Collections.max(list);
```

For modern code, prefer the natural list APIs when they are clearer:

```java
list.sort(Comparator.naturalOrder());
```

`Collections` also provides wrappers and factory helpers such as:

```java
Collections.unmodifiableList(list);
Collections.synchronizedList(list);
Collections.emptyList();
```

Do not confuse an **unmodifiable view** with a defensive copy.

---

## 24. Immutable and Unmodifiable Collections

### Interview Prep

**Core concepts to be ready to explain**

- Distinguish immutable/unmodifiable factories from unmodifiable views. Java 9 added `of` factories; Java 10 added `copyOf` factories.

**Interview questions**

**1. When were `List.of`, `Set.of`, `Map.of` introduced?**
**Answer:** Java 9.

**2. When were `List.copyOf`, `Set.copyOf`, `Map.copyOf` introduced?**
**Answer:** Java 10.

**Tricky question**

**Question:** Does `Collections.unmodifiableList(original)` stop changes to `original`?
**Answer:** No. The wrapper blocks mutation through the wrapper, but changes to the backing list remain visible.

**Mini code check**

**Question:** What happens?

```java
var xs = List.of("A", "B");
xs.add("C");
```

**Answer:** `UnsupportedOperationException`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Java 9 introduced convenient immutable collection factories:

```java
List<String> names =
    List.of("Alice", "Bob");

Set<String> roles =
    Set.of("ADMIN", "USER");

Map<Integer, String> users =
    Map.of(
        1, "Alice",
        2, "Bob"
    );
```

These collections reject structural modification.

For an existing collection, Java 10 added:

```java
List<String> copy = List.copyOf(names);
Set<String> setCopy = Set.copyOf(names);
Map<Integer, String> mapCopy = Map.copyOf(users);
```

These methods create unmodifiable collections and are often a clean way to expose read-only data.

### Returning empty results

Prefer an empty collection to `null` when "there are no results" is a valid outcome.

Good:

```java
public List<String> findNames() {
    return List.of();
}
```

Then callers can simply do:

```java
for (String name : findNames()) {
    System.out.println(name);
}
```

instead of writing a null check.

---

# Part 9 — Choosing, Viewing, and Processing Collections

## 25. Collection Views: Not Every Result Is a New Collection

### Interview Prep

**Core concepts to be ready to explain**

- A view is another way of looking at underlying data rather than an independent copy. Common examples include `subList`, `Map.keySet`, `values`, and `entrySet`.

**Interview questions**

**1. Why can `subList` be dangerous to use casually?**
**Answer:** Structural changes to the backing list outside the view can invalidate the view’s expected state and lead to exceptions or unspecified-looking behavior if used incorrectly.

**2. Is `map.keySet()` a copy?**
**Answer:** No. It is a view backed by the map.

**Tricky question**

**Question:** What happens when `keySet().remove(key)` succeeds?
**Answer:** It removes the corresponding mapping from the underlying map.

**Mini code check**

**Question:** What happens to the map?

```java
Map<String,Integer> m = new HashMap<>();
m.put("A", 1);
Set<String> keys = m.keySet();
keys.remove("A");
```

**Answer:** The map becomes empty because the key-set view is backed by the map.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Several collection APIs return a **view** rather than a separate copy.

A view reflects changes made through the underlying collection.

### List views with `subList`

```java
List<String> names = new ArrayList<>(
    List.of("Ana", "Ben", "Cara", "Dan")
);

List<String> middle = names.subList(1, 3);
middle.clear();

System.out.println(names);
// [Ana, Dan]
```

### Map views

A map provides views of its keys, values, and entries:

```java
Map<String, Integer> scores = new HashMap<>();
scores.put("Ana", 90);
scores.put("Ben", 80);

scores.keySet();
scores.values();
scores.entrySet();
```

For example:

```java
for (Map.Entry<String, Integer> entry : scores.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}
```

Think of these APIs as **windows into the map**, not independent snapshots.

---

## 26. `PriorityQueue`: A Queue Based on Priority

### Interview Prep

**Core concepts to be ready to explain**

- `PriorityQueue` exposes the least element first under natural ordering (or comparator). Its iterator does not promise sorted traversal; `poll` repeatedly gives priority order.

**Interview questions**

**1. Does iterating a `PriorityQueue` return sorted order?**
**Answer:** No. The iterator makes no sorted-order guarantee.

**2. What is `peek` used for?**
**Answer:** Inspect the current head/least element without removing it.

**Tricky question**

**Question:** Why can `queue.peek()` return the smallest element while the rest of the iteration looks unsorted?
**Answer:** The heap guarantees priority for the head, not total ordering of all internal positions.

**Mini code check**

**Question:** How do you get sorted output?

```java
while (!q.isEmpty()) {
    System.out.println(q.poll());
}
```

**Answer:** Repeated `poll` removes elements in priority order.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A normal queue usually processes elements in arrival order. A `PriorityQueue` instead exposes the element with the highest priority according to its ordering.

```java
PriorityQueue<Integer> queue = new PriorityQueue<>();

queue.offer(30);
queue.offer(10);
queue.offer(20);

System.out.println(queue.peek()); // 10
System.out.println(queue.poll()); // 10
```

By default, the smallest element according to natural ordering is at the head.

For a different ordering:

```java
PriorityQueue<String> longestFirst =
    new PriorityQueue<>(Comparator.comparingInt(String::length).reversed());
```

Important: iterating over a `PriorityQueue` does **not** guarantee priority order. Use `peek()` / `poll()` when consuming the queue by priority. The class is unbounded, and its capacity-growth policy is intentionally unspecified. citeturn933693search3

---

## 27. Equality and Hashing — The Foundation of Hash Collections

### Interview Prep

**Core concepts to be ready to explain**

- The key contract is: if `a.equals(b)` is true, `a.hashCode() == b.hashCode()` must also be true. Hash-based collections depend on this contract.

**Interview questions**

**1. Why override `hashCode` when overriding `equals`?**
**Answer:** Equal objects must have equal hash codes so hash-based collections can locate them consistently.

**2. Can unequal objects have the same hash code?**
**Answer:** Yes. Collisions are allowed.

**Tricky question**

**Question:** Is a different hash code proof that two objects are unequal?
**Answer:** Yes: if two equal objects had different hashes, the contract would be broken. But two different objects can share a hash.

**Mini code check**

**Question:** What is wrong?

```java
@Override
public boolean equals(Object o) { ... }
// no hashCode override
```

**Answer:** This can break behavior in `HashSet`/`HashMap`, because equal objects may inherit different identity-based hash codes.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Hash-based collections depend on the `equals` / `hashCode` contract.

The essential rule is:

> If two objects are equal according to `equals`, they must return the same `hashCode`.

A set example:

```java
record UserId(String value) {}

Set<UserId> ids = new HashSet<>();
ids.add(new UserId("alice"));
ids.add(new UserId("alice"));

System.out.println(ids.size()); // 1
```

The record supplies suitable `equals` and `hashCode` implementations for its components, making it a convenient value object.

For mutable classes, do not change fields that participate in equality or hashing while an object is stored as a key in a `HashMap` or as an element in a `HashSet`.

---

## 28. `Comparable` vs `Comparator`

### Interview Prep

**Core concepts to be ready to explain**

- `Comparable` is the type’s natural ordering. `Comparator` is external ordering logic and can provide multiple views of the same type.

**Interview questions**

**1. When prefer `Comparator`?**
**Answer:** When you need an ordering that differs from or supplements the type’s natural order.

**2. Why use `Integer.compare(a,b)` instead of `a-b` in comparisons?**
**Answer:** Subtraction can overflow; comparison helpers avoid that bug.

**Tricky question**

**Question:** Can a class have multiple natural orderings?
**Answer:** It can implement only one `Comparable` natural order, but you can provide many `Comparator`s.

**Mini code check**

**Question:** Sort by age, then name:

```java
Comparator<Student> c =
    Comparator.comparingInt(Student::age)
              .thenComparing(Student::name);
```

**Answer:** This composes two ordering criteria without arithmetic overflow or verbose anonymous classes.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


These solve related but different problems.

### `Comparable` — the object's natural order

```java
record Student(String name, int age)
        implements Comparable<Student> {

    @Override
    public int compareTo(Student other) {
        return Integer.compare(age, other.age);
    }
}
```

Now a `TreeSet<Student>` can use that natural order.

### `Comparator` — an external ordering

```java
List<Student> students = new ArrayList<>();

students.sort(Comparator.comparing(Student::name));
```

You can build more specific orders:

```java
Comparator<Student> byAgeThenName =
    Comparator.comparing(Student::age)
              .thenComparing(Student::name);
```

A useful rule:

- `Comparable` → one natural ordering belongs to the type.
- `Comparator` → choose an ordering for a particular operation.

---

## 29. Mutable, Unmodifiable, and Copied Collections

### Interview Prep

**Core concepts to be ready to explain**

- Mutable collections can be changed. Unmodifiable views block writes through the view but reflect backing changes. Copies are independent data structures.

**Interview questions**

**1. What is the difference between a view and a copy?**
**Answer:** A view reflects the backing collection; a copy has its own storage/state.

**2. What is the simplest immutable-style list factory?**
**Answer:** `List.of(...)` for a small fixed set of elements.

**Tricky question**

**Question:** If two references point to the same mutable list, is one “immutable” just because it is typed as `List`?
**Answer:** No. The interface type does not imply immutability.

**Mini code check**

**Question:** View or copy?

```java
List<String> a = new ArrayList<>(List.of("A"));
List<String> view = Collections.unmodifiableList(a);
List<String> copy = List.copyOf(a);
a.add("B");
```

**Answer:** `view` now exposes `B`; `copy` remains unchanged.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


These ideas are easy to mix up.

### Mutable

```java
List<String> names = new ArrayList<>();
names.add("Alice");
```

The list itself can change.

### Unmodifiable view

```java
List<String> view = Collections.unmodifiableList(names);
```

The caller cannot modify the view, but changes to `names` can still appear through it.

### Unmodifiable factory result

```java
List<String> fixed = List.of("Alice", "Bob");
```

The list cannot be structurally modified and does not allow `null` elements. Java 9 introduced `List.of`, `Set.of`, and `Map.of`. Java 10 added `List.copyOf`, `Set.copyOf`, and `Map.copyOf`. citeturn933693search1turn933693search5turn933693search34

### Independent mutable copy

```java
List<String> copy = new ArrayList<>(fixed);
```

Use this when you need your own modifiable list.

---

# Part 10 — Modern Collection Processing

## 30. Lambdas and Collection Operations

### Interview Prep

**Core concepts to be ready to explain**

- Java 8 introduced lambdas and enhanced functional-style collection operations. Know predicates, consumers, method references, and mutation vs transformation.

**Interview questions**

**1. What is a lambda?**
**Answer:** An expression that can implement a functional interface target type.

**2. What is `removeIf`?**
**Answer:** A default `Collection` operation that removes elements matching a predicate from a mutable collection when supported.

**Tricky question**

**Question:** Can a lambda capture any local variable and then mutate that local variable?
**Answer:** Captured local variables must be final or effectively final.

**Mini code check**

**Question:** What remains?

```java
List<Integer> xs = new ArrayList<>(List.of(1,2,3,4));
xs.removeIf(n -> n % 2 == 0);
```

**Answer:** `[1, 3]`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Java 8 made collection processing more expressive with lambdas and method references.

Instead of:

```java
for (String name : names) {
    System.out.println(name);
}
```

you can write:

```java
names.forEach(System.out::println);
```

For a simple in-place removal:

```java
names.removeIf(String::isBlank);
```

Use lambdas when they improve clarity; a normal loop is still often better for complicated control flow.

---

## 31. Streams: Processing Data as a Pipeline

### Interview Prep

**Core concepts to be ready to explain**

- A stream is a processing pipeline, not a collection. Intermediate operations are lazy; terminal operations trigger evaluation.

**Interview questions**

**1. `map` vs `filter`?**
**Answer:** `map` transforms each element; `filter` keeps elements satisfying a predicate.

**2. What is a terminal operation?**
**Answer:** An operation such as `toList`, `collect`, `forEach`, `count`, `reduce`, or `findFirst` that produces a result or side effect and consumes the stream.

**Tricky question**

**Question:** Can you reuse a stream after a terminal operation?
**Answer:** No. A stream has a single-use lifecycle; attempting to use it again throws `IllegalStateException`.

**Mini code check**

**Question:** What is the result?

```java
var r = List.of(1,2,3,4).stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .toList();
```

**Answer:** `[4, 16]`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A stream is not a collection. It is a pipeline for processing elements from a source.

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

List<Integer> squaresOfEvens = numbers.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .toList();

System.out.println(squaresOfEvens);
// [4, 16, 36]
```

A useful mental model is:

```text
source → filter → transform → result
```

Common operations:

- `filter` — keep matching elements
- `map` — transform elements
- `sorted` — order elements
- `distinct` — remove duplicates
- `limit` — keep a prefix
- `toList` / `collect` — produce a result

### Grouping data

```java
record Product(String category, double price) {}

Map<String, List<Product>> byCategory =
    products.stream()
        .collect(Collectors.groupingBy(Product::category));
```

`groupingBy` is the standard collector for this kind of grouping operation. citeturn746520search0

### `Stream.toList()` vs `Collectors.toList()`

Modern Java often uses:

```java
List<String> result = stream.toList();
```

The returned list is unmodifiable.

By contrast:

```java
List<String> result = stream.collect(Collectors.toList());
```

makes no guarantee about the result's mutability. If you specifically want a mutable `ArrayList`:

```java
List<String> result =
    stream.collect(Collectors.toCollection(ArrayList::new));
```

---

## 32. `Optional` for Missing Results

### Interview Prep

**Core concepts to be ready to explain**

- `Optional<T>` models a result that may be present or absent. It is especially useful for return values such as searches. It is not a universal replacement for nulls everywhere.

**Interview questions**

**1. What does `findFirst()` return on a stream?**
**Answer:** An `Optional<T>`.

**2. `orElse` vs `orElseGet`?**
**Answer:** `orElse` evaluates its argument eagerly; `orElseGet` invokes the supplier only when the optional is empty.

**Tricky question**

**Question:** What is printed?
**Answer:** `"A"` is returned, but `expensive()` is still evaluated because `orElse` is eager.

```java
```java
Optional<String> x = Optional.of("A");
String s = x.orElse(expensive());
```
```

**Mini code check**

**Question:** Safe default:

```java
String name = names.stream()
    .filter("Bob"::equals)
    .findFirst()
    .orElse("Unknown");
```

**Answer:** The code produces a concrete `String` while handling the absent case explicitly.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Some collection operations may find nothing.

```java
Optional<String> first =
    names.stream().filter(String::isBlank).findFirst();
```

Handle that result with methods such as:

```java
first.ifPresent(System.out::println);

String value = first.orElse("No blank name found");
```

`Optional` is particularly useful for results such as `findFirst`, `findAny`, `min`, and `max` where no matching element is a valid outcome.

Do not treat `Optional` as a universal replacement for every nullable field or parameter.

---

# Part 11 — Concurrent Collections

## 33. `ConcurrentModificationException` Is Not Thread Safety

### Interview Prep

**Core concepts to be ready to explain**

- This exception is associated with fail-fast iteration behavior when a collection detects certain structural modifications outside the iterator. It is not a general thread-safety exception.

**Interview questions**

**1. Does it mean two threads modified the collection?**
**Answer:** Not necessarily. A single thread can trigger it by modifying a collection structurally while iterating.

**2. How do you remove safely during iterator traversal?**
**Answer:** Use `Iterator.remove()` when supported, or use collection-specific operations such as `removeIf`.

**Tricky question**

**Question:** Is `ConcurrentModificationException` guaranteed whenever a collection is modified during iteration?
**Answer:** No. Fail-fast behavior is best-effort and implementation-dependent within the documented contract.

**Mini code check**

**Question:** Safer modern alternative:

```java
list.removeIf(s -> s.isBlank());
```

**Answer:** The collection manages the removal operation as part of the supported API.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A fail-fast iterator may detect a structural modification made outside the iterator while iteration is in progress.

```java
List<String> names = new ArrayList<>(List.of("A", "B", "C"));

for (String name : names) {
    if (name.equals("B")) {
        names.remove(name); // unsafe during this iteration
    }
}
```

For removal during iteration, use the iterator:

```java
Iterator<String> it = names.iterator();

while (it.hasNext()) {
    if (it.next().equals("B")) {
        it.remove();
    }
}
```

Fail-fast behavior is a bug-detection aid, not a synchronization mechanism. The API describes it as best-effort. citeturn933693search0turn933693search4

---

## 34. Concurrent Collections

### Interview Prep

**Core concepts to be ready to explain**

- Thread-safe shared data structures use concurrency-aware algorithms rather than simply wrapping ordinary collections. Choose based on workload: maps, write-heavy vs read-heavy lists, queues, blocking behavior.

**Interview questions**

**1. Why use `ConcurrentHashMap` rather than `HashMap` across threads?**
**Answer:** `ConcurrentHashMap` is designed for safe concurrent access and useful concurrent operations.

**2. When is `CopyOnWriteArrayList` a good fit?**
**Answer:** When reads/iteration are frequent and mutations are relatively rare.

**Tricky question**

**Question:** Does making a collection thread-safe make every compound operation atomic?
**Answer:** No. Thread safety of individual methods does not automatically make a multi-step “check then act” sequence atomic unless the API provides a combined atomic operation.

**Mini code check**

**Question:** Use an atomic map update:

```java
map.merge(key, 1, Integer::sum);
```

**Answer:** For `ConcurrentHashMap`, `merge` is designed as a concurrent map operation rather than a separate unsynchronized get/put sequence.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


When multiple threads genuinely share and mutate collection data, choose an implementation designed for concurrency.

Useful examples:

- `ConcurrentHashMap` — general-purpose concurrent map
- `CopyOnWriteArrayList` — useful when reads/traversals greatly outnumber writes
- `BlockingQueue` implementations — producer/consumer workflows
- `ConcurrentSkipListMap` / `ConcurrentSkipListSet` — concurrent sorted structures

Oracle's current documentation recommends `ConcurrentHashMap` for many concurrent-map use cases and `CopyOnWriteArrayList` when traversal greatly outnumbers mutation. citeturn746520search1turn746520search3

```java
ConcurrentHashMap<String, Integer> counts =
    new ConcurrentHashMap<>();

counts.merge("java", 1, Integer::sum);
```

Do not choose a concurrent collection merely because an application uses several threads. The question is whether those threads actually share the collection.

---

# Part 12 — Records as Collection-Friendly Value Objects

## 35. Records and Collections

### Interview Prep

**Core concepts to be ready to explain**

- Records, permanent since Java 16, provide concise value-oriented types with generated accessors, `equals`, `hashCode`, and `toString` based on record components.

**Interview questions**

**1. Why are records useful as map/set elements?**
**Answer:** Their generated equality and hash code are component-based, which is often appropriate for immutable-style value objects.

**2. Are records deeply immutable?**
**Answer:** No. A record’s components are final, but a component can reference a mutable object.

**Tricky question**

**Question:** Does `record Person(List<String> names) {}` guarantee `names` itself cannot change?
**Answer:** No. The reference is final, but the referenced list can still be mutable.

**Mini code check**

**Question:** What equality does this use?

```java
record User(int id, String name) {}
new User(1,"A").equals(new User(1,"A"));
```

**Answer:** `true`, because record equality compares the record components.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Records became a permanent Java language feature in **Java 16**. They are useful for concise value objects stored in sets and maps because they provide component-based `equals`, `hashCode`, and `toString` implementations.

```java
record ProductId(int value) {}

Set<ProductId> ids = new HashSet<>();
ids.add(new ProductId(10));
ids.add(new ProductId(10));

System.out.println(ids.size()); // 1
```

A record is not automatically deeply immutable: a component can still refer to a mutable object.

---

# Part 13 — Generics


## 36. Why Generics Exist

### Interview Prep

**Core concepts to be ready to explain**

- Generics provide compile-time type safety, reduce casts, and make APIs express the element types they accept or return.

**Interview questions**

**1. Main benefit of generics?**
**Answer:** Compile-time type checking and clearer APIs without pervasive casts.

**2. Why are primitives not allowed as type arguments?**
**Answer:** Generic type arguments are reference types; primitives require wrapper types such as `Integer`.

**Tricky question**

**Question:** Does generic type information always exist at runtime?
**Answer:** No. Most type arguments are erased at runtime, subject to erasure rules and reifiable-type exceptions.

**Mini code check**

**Question:** What error should the compiler prevent?

```java
List<String> xs = new ArrayList<>();
// xs.add(10);
```

**Answer:** The compiler rejects inserting an `Integer` into `List<String>`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Generics let you express the type of data a class or method is meant to work with.

Without generics:

```java
List values = new ArrayList();
values.add("Hello");
values.add(42);
```

The collection accepts unrelated types.

With generics:

```java
List<String> values = new ArrayList<>();

values.add("Hello");
// values.add(42); // compile-time error
```

This moves many mistakes from runtime to compile time.

---

## 37. Generic Classes

### Interview Prep

**Core concepts to be ready to explain**

- A generic class declares type parameters used throughout its fields and methods. The type argument is supplied by the client.

**Interview questions**

**1. What does `Box<T>` mean?**
**Answer:** `T` is a type parameter that is substituted conceptually by a concrete reference type such as `String`.

**2. Can static fields use a class type parameter?**
**Answer:** No. A static member belongs to the class, not to a particular type instantiation.

**Tricky question**

**Question:** Are `Box<String>` and `Box<Integer>` subclasses of `Box<Object>`?
**Answer:** No. Generic types are invariant.

**Mini code check**

**Question:** What type is inferred?

```java
Box<String> b = new Box<>("hello");
```

**Answer:** `T` is inferred as `String` on the right side.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A generic class uses a type parameter:

```java
class Box<T> {
    private T value;

    Box(T value) {
        this.value = value;
    }

    T get() {
        return value;
    }
}
```

Use it with a concrete type:

```java
Box<String> message = new Box<>("Hello");
String text = message.get();

Box<Integer> number = new Box<>(42);
int value = number.get();
```

Common naming conventions include:

- `T` — type
- `E` — element
- `K` — key
- `V` — value

These are conventions, not language requirements.

---

## 38. Diamond Operator

### Interview Prep

**Core concepts to be ready to explain**

- The diamond operator (`<>`) lets the compiler infer generic type arguments in many constructor expressions. It was introduced in Java 7.

**Interview questions**

**1. Why use `new ArrayList<>()`?**
**Answer:** It avoids repeating the type argument when the compiler can infer it from the target context.

**2. Does diamond mean raw type?**
**Answer:** No. The compiler still infers a parameterized type; raw types omit type arguments entirely.

**Tricky question**

**Question:** What is `var xs = new ArrayList<String>();`?
**Answer:** `xs` is inferred as `ArrayList<String>`, not `ArrayList<Object>`.

**Mini code check**

**Question:** What type is created?

```java
Map<String,Integer> m = new HashMap<>();
```

**Answer:** A `HashMap<String,Integer>` is inferred from the target type.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Java 7 introduced the diamond operator:

```java
Box<String> box = new Box<>("Hello");
```

The compiler infers the type argument on the right side.

This is cleaner than repeating:

```java
Box<String> box = new Box<String>("Hello");
```

---

## 39. Important Generic Restrictions

### Interview Prep

**Core concepts to be ready to explain**

- Know restrictions such as no primitive type arguments, no direct generic-array creation like `new T[10]`, and no `instanceof List<String>` due to erasure.

**Interview questions**

**1. Why can’t you write `new T[10]` in a generic class?**
**Answer:** Because the runtime component type of `T` is erased/unknown.

**2. Can you overload methods only by generic type arguments?**
**Answer:** Often no, because erasure can produce the same signature.

**Tricky question**

**Question:** Why is `instanceof List<?>` legal but `instanceof List<String>` not?
**Answer:** `List<?>` is reifiable; the concrete type argument `String` is erased at runtime.

**Mini code check**

**Question:** Which is legal?

```java
if (x instanceof List<?> xs) {
    System.out.println(xs.size());
}
```

**Answer:** `List<?>` can be used in a runtime type test.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


### Primitive types are not type arguments

This is invalid:

```java
List<int> numbers; // invalid
```

Use the wrapper type:

```java
List<Integer> numbers;
```

Autoboxing makes common code convenient:

```java
numbers.add(10);     // int -> Integer
int x = numbers.get(0); // Integer -> int
```

### Type parameters cannot be used directly in a static field

This is invalid:

```java
class Box<T> {
    // static T value; // invalid
}
```

Why?

A static member belongs to the class itself, while `T` belongs to a particular parameterization such as `Box<String>` or `Box<Integer>`.

A static method can still be generic by declaring **its own** type parameter:

```java
static <T> T first(List<T> list) {
    return list.get(0);
}
```

---

# Part 14 — Type Erasure and Generic Restrictions

## 40. Type Erasure

### Interview Prep

**Core concepts to be ready to explain**

- Java implements generics primarily through compile-time checking and type erasure. Type arguments are generally unavailable for ordinary runtime inspection.

**Interview questions**

**1. What is erasure?**
**Answer:** The compiler transforms parameterized types so runtime representation generally does not retain concrete generic arguments.

**2. What problem can erasure create?**
**Answer:** Certain overloads and runtime type tests become impossible because different parameterized signatures erase to the same runtime form.

**Tricky question**

**Question:** Can you overload `m(List<String>)` and `m(List<Integer>)`?
**Answer:** No. They erase to the same parameter type `List`, causing a name-clash/duplicate-signature problem.

**Mini code check**

**Question:** Why is this invalid?

```java
boolean b = obj instanceof List<String>;
```

**Answer:** The JVM cannot test that concrete type argument after erasure; use `List<?>` or inspect elements separately.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Java generics are primarily a compile-time feature.

At runtime, generic type arguments are erased. The erased representation depends on the bounds, not always simply `Object`.

For example:

```java
class Box<T> {
    T get() { ... }
}
```

At runtime, the type information for `T` is not retained in the same way as a reified generic system would.

The compiler inserts casts where necessary:

```java
String s = box.get();
```

The practical consequence is that code such as this is not allowed:

```java
if (value instanceof List<String>) { ... } // invalid
```

but this is:

```java
if (value instanceof List<?> list) {
    System.out.println(list.size());
}
```

### Why generic arrays are tricky

This is invalid:

```java
List<String>[] lists = new List<String>[10];
```

and this is invalid:

```java
T[] values = new T[10];
```

Arrays are reified and know their component type at runtime, while generic type arguments are erased.

A wildcard array is possible:

```java
List<?>[] lists = new List<?>[10];
```

because the array component type itself is reifiable.

---

# Part 15 — Raw Types

## 41. Avoid Raw Types

### Interview Prep

**Core concepts to be ready to explain**

- Raw types disable generic checking and can reintroduce runtime `ClassCastException`s. They remain mainly for legacy interoperability.

**Interview questions**

**1. What is a raw type?**
**Answer:** A generic class/interface used without type arguments, such as `List list`.

**2. Why avoid raw types?**
**Answer:** They suppress compile-time type safety and make casts/errors move to runtime.

**Tricky question**

**Question:** Can raw and parameterized types interact?
**Answer:** Yes, but unchecked warnings may result because the compiler cannot prove type safety.

**Mini code check**

**Question:** What problem appears later?

```java
List raw = new ArrayList();
raw.add(42);
List<String> xs = raw; // unchecked
String s = xs.get(0); // ClassCastException
```

**Answer:** The runtime cast to `String` fails because the raw list contained an `Integer`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A raw type omits its generic type argument:

```java
List list = new ArrayList();
```

Prefer:

```java
List<String> list = new ArrayList<>();
```

Raw types weaken compile-time type checking.

Example:

```java
List raw = new ArrayList();

raw.add("Java");
raw.add(123);

String value = (String) raw.get(1); // ClassCastException
```

With generics, the error is prevented much earlier:

```java
List<String> safe = new ArrayList<>();
// safe.add(123); // compile-time error
```

Some class literals necessarily look raw:

```java
List.class
```

Java does not allow:

```java
List<String>.class
```

---

# Part 16 — Invariance, Wildcards, and Flexible APIs

## 42. Generic Types Are Invariant

### Interview Prep

**Core concepts to be ready to explain**

- If `Dog extends Animal`, that does not imply `List<Dog> extends List<Animal>`. Use wildcards for safe covariance/contravariance at API boundaries.

**Interview questions**

**1. Why is invariance useful?**
**Answer:** It prevents unsound writes such as adding a `Cat` through a `List<Animal>` reference that actually points to a list of dogs.

**2. How express “list of some subtype of Animal”?**
**Answer:** `List<? extends Animal>`.

**Tricky question**

**Question:** Why is `List<Integer>` not a `List<Number>` even though `Integer` is a `Number`?
**Answer:** Otherwise code holding the `List<Number>` reference could insert a `Double`, violating the actual list’s element type.

**Mini code check**

**Question:** What compiles?

```java
List<? extends Number> xs = List.of(1, 2, 3);
Number n = xs.get(0);
```

**Answer:** Reading as `Number` is safe; adding arbitrary `Number` values is not.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Suppose:

```java
class Book {}
class Ebook extends Book {}
```

Even though `Ebook` is a subtype of `Book`:

```java
List<Ebook>
```

is **not** a subtype of:

```java
List<Book>
```

This is intentional.

Otherwise, code could put a different kind of `Book` into a list that was actually holding `Ebook` objects.

Arrays are different:

```java
Ebook[] ebooks = ...;
Book[] books = ebooks; // allowed
```

But arrays are reified, so an invalid write can fail at runtime:

```java
books[0] = new Book(); // ArrayStoreException
```

Generics choose the safer compile-time approach.

---

## 43. Unbounded Wildcards: `<?>`

### Interview Prep

**Core concepts to be ready to explain**

- `<?>` means “some unknown type.” It is useful when the exact generic type is irrelevant and you only need safe operations such as reading values as `Object` or checking size.

**Interview questions**

**1. Can you add a `String` to `List<?>`?**
**Answer:** No; the element type is unknown.

**2. What can you safely read from `List<?>`?**
**Answer:** `Object`.

**Tricky question**

**Question:** Can you add `null` to `List<?>`?
**Answer:** Yes, `null` is compatible with any reference type, assuming the collection permits nulls.

**Mini code check**

**Question:** What is safe here?

```java
static void print(List<?> xs) {
    for (Object x : xs) System.out.println(x);
}
```

**Answer:** The method can inspect elements without knowing their concrete type.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Use `<?>` when you want to say:

> "This is some kind of generic value, but I do not care which type."

Example:

```java
static void printSize(List<?> items) {
    System.out.println(items.size());
}
```

This accepts:

```java
printSize(List.of("A", "B"));
printSize(List.of(1, 2, 3));
```

You can safely read an element as `Object`:

```java
Object value = items.get(0);
```

But you cannot generally add an arbitrary value:

```java
// items.add("hello"); // not allowed
```

The compiler does not know what the actual element type is.

---

## 44. Upper-Bounded Wildcards: `? extends T`

### Interview Prep

**Core concepts to be ready to explain**

- `? extends T` is a producer of `T` values: safe to read as `T`, unsafe to add arbitrary `T` values because the actual subtype is unknown.

**Interview questions**

**1. Why can you read `Number` from `List<? extends Number>`?**
**Answer:** Whatever the actual subtype is, every element is a `Number`.

**2. Why can’t you add an `Integer`?**
**Answer:** The actual list might be a `List<Double>`.

**Tricky question**

**Question:** Can you add `null`?
**Answer:** Yes, because `null` is valid for any reference type and does not claim a concrete subtype.

**Mini code check**

**Question:** What is the safe API?

```java
static double sum(List<? extends Number> xs) {
    double total = 0;
    for (Number n : xs) total += n.doubleValue();
    return total;
}
```

**Answer:** The method consumes numbers without mutating their collection type.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Use:

```java
List<? extends Number>
```

when the method only needs to **read** numbers.

```java
static double total(List<? extends Number> numbers) {
    double sum = 0;

    for (Number n : numbers) {
        sum += n.doubleValue();
    }

    return sum;
}
```

Now all of these work:

```java
total(List.of(1, 2, 3));
total(List.of(1.5, 2.5));
```

You can think of it as:

> "A list of some unknown type that is Number or a subtype of Number."

Because the exact subtype is unknown, adding a specific number is not generally safe.

---

## 45. Lower-Bounded Wildcards: `? super T`

### Interview Prep

**Core concepts to be ready to explain**

- `? super T` is a consumer of `T`: you can safely add `T` values, but reads come back only as `Object` without further type checks.

**Interview questions**

**1. Why is `List<? super Integer>` useful?**
**Answer:** It can refer to `List<Integer>`, `List<Number>`, or `List<Object>`, and you can safely add `Integer` values.

**2. What type do you read from it as?**
**Answer:** `Object`.

**Tricky question**

**Question:** Can you add a `Double` to `List<? super Integer>`?
**Answer:** No. The list is only guaranteed to accept `Integer` values safely.

**Mini code check**

**Question:** What compiles?

```java
static void addDefaults(List<? super Integer> xs) {
    xs.add(0);
    xs.add(1);
}
```

**Answer:** Both additions are safe.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Use:

```java
List<? super Integer>
```

when you want to **put `Integer` values into the collection**.

```java
static void addScores(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}
```

This method can accept:

```java
List<Integer>
List<Number>
List<Object>
```

because all of them can safely hold an `Integer`.

When reading from a `List<? super Integer>`, the safe type is generally `Object`.

---

## 46. PECS: Producer Extends, Consumer Super

### Interview Prep

**Core concepts to be ready to explain**

- PECS = Producer Extends, Consumer Super. Use `extends` when reading values out; use `super` when writing values in.

**Interview questions**

**1. Which wildcard for a source list you only read?**
**Answer:** `? extends T`.

**2. Which wildcard for a destination list you add `T` values to?**
**Answer:** `? super T`.

**Tricky question**

**Question:** Does PECS mean “never use a concrete generic type”?
**Answer:** No. Use wildcards where flexibility is valuable; use concrete generic types when the API needs an exact type relationship.

**Mini code check**

**Question:** Copy values safely:

```java
static <T> void copy(
    List<? super T> dest,
    List<? extends T> src) {
    for (T x : src) dest.add(x);
}
```

**Answer:** The source produces `T`; the destination consumes `T`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A useful memory rule is:

> **PECS = Producer Extends, Consumer Super**

### Producer

If the collection gives values to you:

```java
List<? extends Number>
```

### Consumer

If the collection receives values from you:

```java
List<? super Integer>
```

Example:

```java
static double sum(List<? extends Number> values) { ... }

static void addDefaults(List<? super Integer> values) { ... }
```

Do not treat PECS as a rigid law for every generic signature; use it as a design heuristic.

---

# Part 17 — Generic Methods

## 47. Generic Methods

### Interview Prep

**Core concepts to be ready to explain**

- A generic method declares its own type parameter, independent of whether the containing class is generic. Type inference often determines the method’s type argument.

**Interview questions**

**1. What does `<T>` before the return type mean?**
**Answer:** It declares a type parameter for that method.

**2. Why use a generic method instead of `Object`?**
**Answer:** It preserves a relationship between input and output types and avoids unsafe casts.

**Tricky question**

**Question:** Can a generic method return a type different from its parameter?
**Answer:** Yes, as long as the declared type variables and bounds express the relationship.

**Mini code check**

**Question:** What is inferred?

```java
static <T> T first(List<T> xs) { return xs.get(0); }
String s = first(List.of("A"));
```

**Answer:** `T` is inferred as `String`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A generic method declares its own type parameter:

```java
static <T> T first(List<T> list) {
    return list.get(0);
}
```

The `<T>` appears **before** the return type.

Example:

```java
String name = first(List.of("Alice", "Bob"));
Integer id = first(List.of(10, 20));
```

### A useful generic utility

```java
static <T> void copyFirst(
        List<? super T> destination,
        List<? extends T> source) {

    destination.add(source.get(0));
}
```

This captures the relationship between the source and destination types rather than hard-coding one concrete type.

---

# Part 18 — Nested Classes

## 48. Why Nest Classes?

### Interview Prep

**Core concepts to be ready to explain**

- Nested classes improve cohesion and encapsulation when a helper type conceptually belongs to an enclosing type. They are not automatically better than top-level classes.

**Interview questions**

**1. Why create a nested class?**
**Answer:** To keep tightly coupled implementation types near the type they support and reduce public namespace clutter.

**2. What kinds of nested classes exist?**
**Answer:** Static nested classes and inner/member classes; Java also has local and anonymous classes.

**Tricky question**

**Question:** Is every nested class an inner class?
**Answer:** No. “Inner class” traditionally means a non-static nested class.

**Mini code check**

**Question:** What is a good candidate for nesting?

```java
class Parser {
    static class Result {
        final boolean ok;
        Result(boolean ok) { this.ok = ok; }
    }
}
```

**Answer:** `Result` is closely tied to `Parser` and does not need an outer instance.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A nested class is a class declared inside another class.

Nested classes are useful when a helper type:

- belongs conceptually to one outer type,
- should not be exposed as a top-level type,
- needs controlled access to implementation details.

There are four main forms to know:

1. non-static member class
2. static member class
3. local class
4. anonymous class

---

## 49. Non-Static Member Classes

### Interview Prep

**Core concepts to be ready to explain**

- A non-static member class is associated with an instance of its outer class and can access that outer instance’s members, including private members.

**Interview questions**

**1. Does an inner class require an outer instance?**
**Answer:** Yes, for a non-static member class instance.

**2. Why can it access private outer fields?**
**Answer:** Nested classes are granted access according to Java’s language rules; private is not a barrier between nested members of the same top-level type.

**Tricky question**

**Question:** Can you write `new Outer.Inner()` for a non-static inner class?
**Answer:** Not without an enclosing instance. The usual form is `outer.new Inner()`.

**Mini code check**

**Question:** Correct construction:

```java
Outer outer = new Outer();
Outer.Inner inner = outer.new Inner();
```

**Answer:** The inner object is associated with `outer`.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A non-static member class is associated with an instance of the outer class.

```java
class ShoppingCart {
    private int total;

    class Summary {
        void print() {
            System.out.println("Total: " + total);
        }
    }

    Summary summary() {
        return new Summary();
    }
}
```

Usage:

```java
ShoppingCart cart = new ShoppingCart();
ShoppingCart.Summary summary = cart.summary();

summary.print();
```

The inner object can access the enclosing object's instance members.

Conceptually, it has an association with the outer instance.

### When to use it

Use a non-static member class when the nested object genuinely needs the state of a particular outer object.

Do not use it merely because the class is "related" to the outer class.

---

## 50. Static Member Classes

### Interview Prep

**Core concepts to be ready to explain**

- A static nested class does not carry an implicit reference to an enclosing instance. It is often the best choice when the nested type does not need outer instance state.

**Interview questions**

**1. Why can a static nested class be instantiated without an outer object?**
**Answer:** It is associated with the outer type, not with a particular outer instance.

**2. Can it access non-static outer fields directly?**
**Answer:** No. It needs an outer instance reference.

**Tricky question**

**Question:** Is a static nested class the same as a static top-level class?
**Answer:** There is no `static` top-level class in Java; the term applies to nested classes.

**Mini code check**

**Question:** Construction:

```java
Outer.Helper h = new Outer.Helper();
```

**Answer:** No `Outer` instance is needed when `Helper` is static nested.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A static member class is better called a **static nested class**.

```java
class Parser {
    static class Result {
        final boolean success;

        Result(boolean success) {
            this.success = success;
        }
    }
}
```

Create it without a `Parser` instance:

```java
Parser.Result result = new Parser.Result(true);
```

A static nested class does not carry an implicit reference to a particular `Parser` instance.

This is often preferable when no outer-instance access is needed.

A common pattern is:

```java
class User {
    private UserId id;

    static class UserId {
        private final int value;

        UserId(int value) {
            this.value = value;
        }
    }
}
```

---

# Part 19 — Anonymous and Local Classes

## 51. Anonymous Classes

### Interview Prep

**Core concepts to be ready to explain**

- Anonymous classes are one-off class definitions. They are useful when you need state/behavior that is awkward to express as a lambda or when implementing a non-functional interface.

**Interview questions**

**1. When would you prefer a lambda?**
**Answer:** When the target type is a functional interface and the behavior is simple enough for lambda syntax.

**2. Can an anonymous class have its own fields?**
**Answer:** Yes.

**Tricky question**

**Question:** Can a lambda create a new named class type that extends a concrete class?
**Answer:** No. Lambdas target functional interfaces; anonymous classes can extend a class or implement an interface.

**Mini code check**

**Question:** Classic example:

```java
Comparator<String> byLength = new Comparator<>() {
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
};
```

**Answer:** Useful when explicit class-body behavior is needed; a lambda is shorter for this simple comparator.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


An anonymous class creates a one-off implementation without giving the class a name.

Classic example:

```java
Comparator<String> byLength = new Comparator<>() {
    @Override
    public int compare(String a, String b) {
        return Integer.compare(a.length(), b.length());
    }
};
```

This is still useful when you need a one-off implementation of a class or interface.

However, for a functional interface, a lambda is usually clearer:

```java
Comparator<String> byLength =
    Comparator.comparingInt(String::length);
```

So the practical guideline is:

- functional interface → usually prefer a lambda
- needs multiple methods/state or extends a class → anonymous class may still make sense

---

## 52. Local Classes

### Interview Prep

**Core concepts to be ready to explain**

- Local classes are declared inside a method or initializer. They can access local variables that are final or effectively final.

**Interview questions**

**1. What is the scope of a local class?**
**Answer:** Its declaration is limited to the surrounding block/method/initializer.

**2. What does effectively final mean?**
**Answer:** A local variable does not have to be declared `final` if it is never reassigned after initialization.

**Tricky question**

**Question:** Can a local class modify a captured local primitive variable?
**Answer:** It cannot mutate the captured local variable itself; Java captures a value/reference, and the local must be final/effectively final.

**Mini code check**

**Question:** Why is this legal?

```java
String prefix = "ID=";
class Printer { void print(int n) { System.out.println(prefix + n); } }
```

**Answer:** `prefix` is effectively final because it is not reassigned.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


A local class is declared inside a method or initializer.

```java
static void process() {
    class Helper {
        void run() {
            System.out.println("Processing...");
        }
    }

    Helper helper = new Helper();
    helper.run();
}
```

Use a local class when:

- the helper is needed only inside one method,
- the helper has enough behavior that a lambda or anonymous class would become awkward.

A local class can use local variables from the enclosing method only when those variables are `final` or **effectively final**.

```java
static void example() {
    String prefix = "ID: ";

    class Printer {
        void print(int id) {
            System.out.println(prefix + id);
        }
    }

    new Printer().print(42);
}
```

`prefix` is effectively final because it is never reassigned.

---

# Part 20 — A Java 16 Upgrade to Inner-Class Rules

## 53. Static Members in Inner Classes — Java 16

### Interview Prep

**Core concepts to be ready to explain**

- Java 16 relaxed the old restriction on static members in non-static inner classes. Modern Java allows static fields/methods/nested declarations there, subject to the language rules.

**Interview questions**

**1. When did the inner-class static-member rule change?**
**Answer:** Java 16.

**2. Does the change mean every old tutorial statement about inner classes is still correct?**
**Answer:** No. Legacy rules that allow only constant static fields are outdated for modern Java.

**Tricky question**

**Question:** Does a static member of an inner class suddenly make the inner class itself static?
**Answer:** No. The class is still non-static/inner; only the member is static.

**Mini code check**

**Question:** Modern Java permits:

```java
class Outer {
    class Inner {
        static int count = 0;
    }
}
```

**Answer:** This is legal in modern Java; the relaxed rule arrived in Java 16.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


The old rule taught in many older courses was:

> A non-static inner class cannot declare static members except constant variables.

That rule changed in **Java 16**.

As part of the Java 16 record-related language changes, inner classes were allowed to declare static members more generally.

For example, modern Java permits:

```java
class Outer {
    class Inner {
        static int count = 0;

        static void reset() {
            count = 0;
        }
    }
}
```

So older notes that say this is always illegal are outdated.

This change is easy to miss because most introductory material predates Java 16.

---

# Part 21 — Practical Comparison

## 54. Which Collection Should I Choose?

### Interview Prep

**Core concepts to be ready to explain**

- Start from behavior, not implementation. Ask: ordered sequence? uniqueness? key lookup? sorting? both ends? priority? concurrency?

**Interview questions**

**1. Default general-purpose list?**
**Answer:** `ArrayList`.

**2. Default queue/stack/deque?**
**Answer:** `ArrayDeque` is a strong default for ordinary single-threaded use.

**Tricky question**

**Question:** Should you choose `LinkedList` whenever insertion/deletion is frequent?
**Answer:** Not automatically. The location lookup cost and memory/cache behavior matter; many workloads still favor `ArrayList` or `ArrayDeque`.

**Mini code check**

**Question:** Map the requirement:

```text
unique + sorted -> TreeSet
key/value -> HashMap
FIFO -> ArrayDeque
ordered + index -> ArrayList
```

**Answer:** Choose the data structure that directly matches the required operations.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


Start with the operation you care about.

| Need | Good starting point |
|---|---|
| Ordered general-purpose list | `ArrayList` |
| Unique elements, no order guarantee | `HashSet` |
| Unique + insertion order | `LinkedHashSet` |
| Unique + sorted | `TreeSet` |
| Key/value lookup | `HashMap` |
| Key/value + insertion/access order | `LinkedHashMap` |
| Key/value + sorted keys | `TreeMap` |
| FIFO queue | `ArrayDeque` |
| Priority-based processing | `PriorityQueue` |
| Stack | `ArrayDeque` |
| Add/remove at both ends | `ArrayDeque` |
| Concurrent key/value access | `ConcurrentHashMap` |
| Concurrent list with far more reads than writes | `CopyOnWriteArrayList` |

Then ask:

1. Do I need ordering?
2. Do I need uniqueness?
3. Do I need sorting?
4. Do I need fast lookup by key?
5. Do I need operations at both ends?
6. Do multiple threads share and mutate this collection?
7. Do I need an unmodifiable result or a live view?

That usually gets you to the right abstraction quickly.

---

# Part 22 — Modern Java Collection Features Worth Knowing

## 55. Important Upgrade Timeline

### Interview Prep

**Core concepts to be ready to explain**

- For interviews, distinguish permanent language/API additions from preview/incubator features. Do not present previews as stable language features.

**Interview questions**

**1. Which Java release introduced sequenced collections?**
**Answer:** Java 21.

**2. Which release made compact source files and instance main methods permanent?**
**Answer:** Java 25.

**Tricky question**

**Question:** Are all Java 27 headline features permanent?
**Answer:** No. JDK 27 includes preview and incubator features such as primitive types in patterns/`instanceof`/`switch` as a fifth preview and several preview/incubator APIs.

**Mini code check**

**Question:** Stable vs preview example:

```java
// Stable: Java 21+
List<String> xs = new ArrayList<>();
System.out.println(xs.getFirst());
```

**Answer:** Sequenced-collection APIs are standard since Java 21; do not confuse them with Java 27 preview features.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


These are the upgrades from this topic area that are worth explicitly remembering.

### Java 7
**Diamond operator**

```java
List<String> names = new ArrayList<>();
```

This reduced repetitive generic type syntax.

### Java 8
**Lambdas, method references, and `Map`/`Iterable` default methods**

These made collection processing and comparator code much more expressive.

```java
names.removeIf(String::isBlank);
counts.merge("java", 1, Integer::sum);
```

### Java 9
**Unmodifiable collection factory methods**

```java
List.of(...)
Set.of(...)
Map.of(...)
```

These provide convenient unmodifiable collections. They reject `null` elements/keys/values as specified by the corresponding factory APIs. citeturn933693search1turn933693search5

### Java 10
**`copyOf` collection factories**

```java
List.copyOf(...)
Set.copyOf(...)
Map.copyOf(...)
```

These provide unmodifiable collection results. citeturn933693search5

### Java 16
**Records became a permanent language feature**

Records are useful as concise value objects, including objects stored in sets and maps.

**Inner classes may declare static members more freely**

This removed an old restriction that appears in many legacy tutorials.

### Java 21
**Sequenced collections (JEP 431)**

Introduced:

```java
SequencedCollection
SequencedSet
SequencedMap
```

with common encounter-order operations such as:

```java
getFirst()
getLast()
reversed()
```

Modern ordered collection implementations such as `ArrayList` and `ArrayDeque` expose sequenced APIs. citeturn822586search5turn933693search0turn746520search4

### Java 25
**Latest LTS release**

As of September 2026, JDK 25 is the current Long-Term Support release.

### Java 26
**Latest feature release**

As of September 2026, JDK 26 is the latest Java SE feature release.

---

# Part 23 — A Compact Mental Model

## 56. Remember the Interfaces, Not Every Class

### Interview Prep

**Core concepts to be ready to explain**

- Interviewers usually care more about why you chose `List`/`Set`/`Map`/`Deque` than whether you can recite every implementation detail.

**Interview questions**

**1. Why program to interfaces?**
**Answer:** It expresses required behavior and reduces coupling to a concrete implementation.

**2. When would you change implementations?**
**Answer:** When requirements change: ordering, sorting, concurrency, memory behavior, or operation complexity.

**Tricky question**

**Question:** Does `List` guarantee fast random access?
**Answer:** No. The interface does not promise O(1) indexed access; `ArrayList` provides it, while `LinkedList` does not.

**Mini code check**

**Question:** Good declaration:

```java
List<String> users = new ArrayList<>();
```

**Answer:** The API depends on `List`; the implementation can be changed later if appropriate.

**Interview habit:** Explain the result first, then explain *why* it happens. Avoid guessing from memory when an API contract can settle the question.


You do not need to memorize every implementation before writing Java code.

Start with the abstraction:

```text
I need...

sequence        → List
uniqueness      → Set
key → value     → Map
queue/stack     → Deque
```

Then choose an implementation:

```text
List  → ArrayList
Set   → HashSet / LinkedHashSet / TreeSet
Map   → HashMap / LinkedHashMap / TreeMap
Deque → ArrayDeque
```

Finally add constraints:

```text
Need insertion order?
Need sorting?
Need nearest-value navigation?
Need access-order behavior?
Need immutability?
```

That is a much more useful decision process than memorizing a giant class hierarchy.

---

# 57. Final Example: Putting the Ideas Together

Here is a small example using several of the concepts at once:

```java
import java.util.*;
import java.util.stream.Collectors;

record Product(int id, String name, double price) {}

public class ProductCatalog {
    public static void main(String[] args) {

        // Map for fast lookup by ID
        Map<Integer, Product> products = new LinkedHashMap<>();

        products.put(101, new Product(101, "Keyboard", 50.0));
        products.put(102, new Product(102, "Mouse", 25.0));
        products.put(103, new Product(103, "Monitor", 200.0));

        // Set for unique categories
        Set<String> categories = new LinkedHashSet<>();
        categories.add("Hardware");
        categories.add("Hardware");
        categories.add("Accessories");

        // List for an ordered result
        List<Product> result = new ArrayList<>(products.values());

        result.sort(Comparator.comparingDouble(Product::price));

        System.out.println("Products by price:");
        result.forEach(System.out::println);

        System.out.println("\nCategories:");
        categories.forEach(System.out::println);

        // Group using a stream
        Map<Boolean, List<Product>> byPrice = result.stream()
                .collect(Collectors.partitioningBy(p -> p.price() >= 100));

        System.out.println("Expensive products: " + byPrice.get(true));

        // Java 21+ sequenced collection API
        System.out.println("\nFirst product: " + result.getFirst());
        System.out.println("Last product: " + result.getLast());
    }
}
```

This one example demonstrates the main design choices:

- `Map` for lookup by key
- `LinkedHashMap` when encounter order matters
- `Set` for uniqueness
- `List` for ordered processing
- `Comparator` for custom sorting
- records as concise value objects
- Java 21+ sequenced-list operations

---


## Current Java Release Snapshot — September 2026

JDK **27** was released on **September 15, 2026** and is the latest Java SE feature release. JDK **25** is the current LTS release. The feature-release cadence is six months.

For this collection-focused guide, the most important modern upgrade remains **Java 21**, which introduced `SequencedCollection`, `SequencedSet`, and `SequencedMap`. Java 9/10 unmodifiable collection factories (`of` / `copyOf`) and Java 8 functional collection APIs are also common interview topics. JDK 27 is worth knowing as the current release, but do not present its preview/incubator features as permanent APIs.

# Final Takeaways

1. **Program to interfaces** such as `List`, `Set`, `Map`, and `Deque`.
2. **Use `ArrayList` by default** for ordinary lists unless you have a reason not to.
3. **Use `ArrayDeque`** for most queue and stack use cases.
4. **Use `HashSet`/`HashMap`** when ordering is not part of the requirement.
5. **Use `LinkedHashSet`/`LinkedHashMap`** when predictable encounter order matters.
6. **Use `TreeSet`/`TreeMap`** when sorted data and navigation are important.
7. Remember the `equals`/`hashCode` contract for hash-based collections.
8. Do not mutate keys in ways that change their equality or hash behavior while they are stored in a map.
9. Learn generics through **invariance + wildcards + PECS**, rather than memorizing isolated rules.
10. Prefer generic methods when they express a relationship between input and output types.
11. Use nested classes only when they genuinely improve encapsulation or organization.
12. Prefer static nested classes when no enclosing instance is needed.
13. Prefer lambdas over anonymous classes for simple functional-interface implementations.
14. Know the major modernization points: **Java 8 collection lambdas/default methods, Java 9 factory methods, Java 10 `copyOf`, Java 16 records and relaxed inner-class static-member rules, and Java 21 sequenced collections**.
15. Use streams for clear data-processing pipelines, not as a replacement for every ordinary loop.
16. Treat `ConcurrentModificationException` as a fail-fast bug detector, not as a threading solution.
17. Use concurrent collection implementations deliberately when data is genuinely shared across threads.
18. As of September 2026, **As of September 2026, JDK 27 is the latest Java SE feature release, published on September 15, 2026; JDK 25 remains the current LTS release.

---

## Verification Notes

This rewrite intentionally avoids implementation details that are not guaranteed by the Java API specification, such as claiming an exact `ArrayList` growth percentage.

The modern collection updates are based on the current Java SE API documentation and Oracle's Java platform documentation. In particular:

- `SequencedCollection`, `SequencedSet`, and `SequencedMap` were introduced in **Java 21**.
- Modern ordered collection implementations such as `ArrayList` and `ArrayDeque` expose sequenced APIs such as `getFirst`, `getLast`, and `reversed`. citeturn933693search0turn746520search4
- `List.of`, `Set.of`, and `Map.of` were added in **Java 9**; `copyOf` factories were added in **Java 10**. citeturn933693search1turn933693search5
- Concurrent collection guidance follows the current `java.util.concurrent` documentation, including `ConcurrentHashMap` and `CopyOnWriteArrayList`. citeturn746520search1turn746520search3
- `PriorityQueue` does not guarantee iterator traversal in priority order; priority order is exposed through queue operations such as `peek` and `poll`. citeturn933693search3
- `ArrayList`'s growth policy is intentionally unspecified; only its amortized complexity guarantees should be relied on. citeturn933693search0
- The current Java release is **JDK 26**, while **JDK 25** is the current LTS release as of September 2026.

## Interview Drill — Collections and Generics

### Rapid-fire questions

1. **`ArrayList` or `LinkedList` for most ordinary list work? Why?**  
   **Answer:** `ArrayList` is the usual starting point because indexed access and iteration are efficient, and its memory locality is often favorable. Choose differently only when the workload clearly benefits.

2. **`HashSet` or `TreeSet`?**  
   **Answer:** `HashSet` when uniqueness and fast expected lookup matter without sorted order; `TreeSet` when sorted order and navigation/range operations matter.

3. **`HashMap` or `LinkedHashMap`?**  
   **Answer:** `HashMap` when order is irrelevant; `LinkedHashMap` when predictable encounter order matters.

4. **Why doesn't `List<Dog>` extend `List<Animal>`?**  
   **Answer:** Generic types are invariant; allowing that would make unsafe writes possible.

5. **PECS?**  
   **Answer:** Producer Extends, Consumer Super.

6. **Why is `PriorityQueue` iteration not sorted?**  
   **Answer:** It is heap-based; only the head is guaranteed to be the current highest-priority/lowest-order element.

7. **Why must `equals` and `hashCode` agree?**  
   **Answer:** Hash-based collections use the hash code to find candidates and equality to confirm logical identity. Equal objects must have the same hash code.

### Classic tricky questions

**Q1**
```java
List<Integer> xs = new ArrayList<>(List.of(1, 2, 3));
xs.remove(1);
System.out.println(xs);
```
**Answer:** `[1, 3]`. The `int` overload removes by index.

To remove the value `1` instead:
```java
xs.remove(Integer.valueOf(1));
```

**Q2**
```java
List<String> a = new ArrayList<>(List.of("A"));
List<String> view = Collections.unmodifiableList(a);
a.add("B");
System.out.println(view);
```
**Answer:** `[A, B]`. The wrapper is unmodifiable through `view`, but it is still a live view of `a`.

**Q3**
```java
Map<String, Integer> m = new HashMap<>();
System.out.println(m.getOrDefault("x", 10));
System.out.println(m.containsKey("x"));
```
**Answer:** `10`, then `false`. `getOrDefault` does not insert the default.

**Q4**
```java
Optional<String> x = Optional.of("A");
String y = x.orElse(expensive());
```
**Question:** Is `expensive()` guaranteed not to run?  
**Answer:** No. `orElse` evaluates its argument eagerly. Use `orElseGet(expensiveSupplier)` for lazy fallback computation.

**Q5**
```java
List<? extends Number> nums = List.of(1, 2, 3);
// nums.add(4);
Number n = nums.get(0);
```
**Answer:** Reading as `Number` is safe; adding an `Integer` is not, because the actual list could be a `List<Double>`.

### What interviewers want to hear

Do not stop at “HashMap is O(1)” or “ArrayList is faster.” Explain the **contract**, then the **typical performance**, then the **trade-off**. Strong answers sound like: “`HashMap` provides expected constant-time lookup under good hashing; it does not guarantee iteration order, and if I need sorted keys I would consider `TreeMap`.”
