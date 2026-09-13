# Java Collections, Generics, and Nested Classes — Rewritten Notes

> **Scope:** A practical rewrite of the original notes covering the Java Collections Framework, generics, and nested classes.
>
> **Approach:** Learn the common ideas first, then the implementations that solve specific problems. Examples are intentionally small so you can run them and experiment.

---

# Part 1 — The Collections Framework

## 1. The Big Picture

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
18. As of September 2026, **JDK 26 is the latest Java SE release and JDK 25 is the latest LTS release**.

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
