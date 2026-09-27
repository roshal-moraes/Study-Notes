# Java Collections Cheat Sheet

For daily review — focus on construction syntax and method names, since that's the recurring gap.

---

## PriorityQueue (Heap)

```java
// Min-heap (default)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

// Max-heap
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
// or: new PriorityQueue<>((a, b) -> b - a);

// Custom objects, compare by a field
PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] - b[0]);
```

| Method | Does | Complexity | Notes |
|---|---|---|---|
| `add(x)` / `offer(x)` | insert | O(log n) | same behavior, `add` more common |
| `peek()` | look at top | O(1) | returns `null` if empty (no throw) |
| `poll()` | remove & return top | O(log n) | returns `null` if empty (no throw) |
| `remove(x)` | remove specific element | O(n) | linear scan, not just root |
| `size()` | count | O(1) | |

---

## TreeMap / TreeSet (sorted, balanced BST-backed)

```java
TreeMap<Integer, String> map = new TreeMap<>();
TreeSet<Integer> set = new TreeSet<>();
```

| Method (TreeMap) | Does | Complexity |
|---|---|---|
| `floorKey(k)` / `floorEntry(k)` | largest key ≤ k | O(log n) |
| `ceilingKey(k)` / `ceilingEntry(k)` | smallest key ≥ k | O(log n) |
| `lowerKey(k)` | largest key < k | O(log n) |
| `higherKey(k)` | smallest key > k | O(log n) |
| `firstKey()` / `lastKey()` | min / max key | O(log n) |
| `pollFirstEntry()` / `pollLastEntry()` | remove & return min/max entry | O(log n) |

Same names apply to `TreeSet` for values instead of keys (`floor`, `ceiling`, `lower`, `higher`, `first`, `last`).

---

## HashMap / HashSet

```java
HashMap<String, Integer> map = new HashMap<>();
```

| Method | Does | Notes |
|---|---|---|
| `getOrDefault(k, def)` | read with fallback | avoids null checks |
| `computeIfAbsent(k, fn)` | insert-if-missing then return | `map.computeIfAbsent(k, x -> new ArrayList<>()).add(v)` — classic "graph adjacency list" pattern |
| `merge(k, v, fn)` | combine on conflict | `map.merge(k, 1, Integer::sum)` — classic "increment count" pattern |
| `putIfAbsent(k, v)` | insert only if key missing | |
| `containsKey(k)` / `containsValue(v)` | existence check | O(1) / O(n) |
| `entrySet()` | iterate key-value pairs | `for (Map.Entry<K,V> e : map.entrySet())` |

---

## Deque / ArrayDeque (stack, queue, sliding window)

```java
Deque<Integer> deque = new ArrayDeque<>();
```

| Method | Does | Use case |
|---|---|---|
| `addFirst(x)` / `addLast(x)` | insert at either end | O(1) |
| `removeFirst()` / `removeLast()` | remove from either end | O(1), throws if empty |
| `pollFirst()` / `pollLast()` | same, returns `null` if empty | preferred over `remove*` for safety |
| `peekFirst()` / `peekLast()` | look without removing | |
| `push(x)` / `pop()` | stack behavior (LIFO, front) | `push` = `addFirst`, `pop` = `removeFirst` |
| `offer(x)` / `poll()` | queue behavior (FIFO) | `offer` = `addLast`, `poll` = `pollFirst` |

Use `ArrayDeque` instead of `Stack` (legacy, synchronized, slower) and instead of `LinkedList` when you just need stack/queue semantics.

---

## ArrayList / LinkedList

```java
List<Integer> list = new ArrayList<>();
```

| Method | Does | Complexity |
|---|---|---|
| `add(x)` | append | O(1) amortized |
| `add(i, x)` | insert at index | O(n) — shifts elements |
| `get(i)` / `set(i, x)` | read/write by index | O(1) (ArrayList), O(n) (LinkedList) |
| `remove(i)` | remove by index | O(n) — shifts elements |
| `remove(Object o)` | remove by value | O(n), ambiguous with `remove(int)` — cast if removing an `Integer` by value: `list.remove((Integer) 5)` |
| `indexOf(x)` | find index | O(n) |
| `Collections.sort(list)` | sort in place | O(n log n) |
| `list.sort(comparator)` | sort with custom order | O(n log n) |

---

## Arrays utility class

```java
int[] arr = new int[10];
Arrays.sort(arr);                          // ascending, primitives
Arrays.fill(arr, -1);                      // fill all with value
int idx = Arrays.binarySearch(arr, target); // requires sorted array
int[] copy = Arrays.copyOf(arr, newLength);
int[] slice = Arrays.copyOfRange(arr, from, to); // [from, to)
List<Integer> asList = Arrays.asList(1, 2, 3);   // fixed-size, backed by array
```

**Sorting 2D arrays / objects by custom key** (descending sort trick, no `Collections.reverseOrder()` for arrays):
```java
Arrays.sort(intervals, (a, b) -> a[0] - b[0]);        // ascending by first element
Arrays.sort(intervals, (a, b) -> b[0] - a[0]);        // descending
```

---

## Collections utility class

```java
Collections.sort(list);
Collections.reverse(list);
Collections.max(list); / Collections.min(list);
Collections.emptyList(); / Collections.unmodifiableList(list);
Collections.frequency(list, x);  // count occurrences
```

---

## Generic array creation (the recurring slip)

Java disallows `new T[]` directly for generics — you cast a raw array:

```java
List<Character>[] buckets = (List<Character>[]) new List[10];
```

One `new`, one cast wrapping it — **not** `new (List<Character>[]) new List[10]`.

---

## StringBuilder

```java
StringBuilder sb = new StringBuilder();
sb.append(x);              // works for any type
sb.deleteCharAt(sb.length() - 1);  // remove last char (backtracking undo)
sb.reverse();
sb.toString();
sb.charAt(i);
sb.setLength(0);            // clear/reset
```

---

## Quick decision guide

| Need | Use |
|---|---|
| Fast min/max repeatedly | `PriorityQueue` |
| Sorted order + range queries (floor/ceiling) | `TreeMap` / `TreeSet` |
| O(1) key lookup, no order needed | `HashMap` / `HashSet` |
| Stack or queue behavior | `ArrayDeque` |
| Sliding window max/min | `ArrayDeque` (monotonic deque) |
| Index-based random access | `ArrayList` |
| Graph adjacency list | `HashMap<Integer, List<Integer>>` + `computeIfAbsent` |
