# Java Interview Cheat Sheet

A quick-reference for the syntax that actually comes up in coding interviews. Use this for recall drills — cover the right side and try to write the left side from memory.

---

## 1. Collections — Declaring & Basic Ops

```java
// Lists
List<Integer> list = new ArrayList<>();
list.add(5);
list.get(0);
list.size();
list.remove(0);          // remove by index
list.remove(Integer.valueOf(5)); // remove by value (int needs boxing!)
Collections.sort(list);

// Maps
Map<String, Integer> map = new HashMap<>();
map.put("a", 1);
map.get("a");                          // null if missing
map.getOrDefault("a", 0);
map.containsKey("a");
map.remove("a");
for (Map.Entry<String, Integer> e : map.entrySet()) { }

// Sets
Set<Integer> set = new HashSet<>();
set.add(5);
set.contains(5);

// Queue / Deque (use for BFS, sliding window)
Deque<Integer> stack = new ArrayDeque<>();  // as a stack: push/pop
stack.push(5);
stack.pop();
stack.peek();

Queue<Integer> queue = new LinkedList<>();  // as a queue: offer/poll
queue.offer(5);
queue.poll();

// PriorityQueue (min-heap by default)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
minHeap.offer(5);
minHeap.poll();   // removes smallest
```

---

## 2. Arrays & Strings

```java
// Arrays
int[] arr = new int[10];
int[] arr2 = {1, 2, 3};
Arrays.sort(arr);
Arrays.fill(arr, 0);
int[] copy = Arrays.copyOf(arr, arr.length);
List<Integer> boxed = Arrays.asList(1, 2, 3); // fixed-size, careful

// 2D arrays
int[][] grid = new int[rows][cols];

// Strings
String s = "hello";
s.charAt(0);
s.length();
s.substring(1, 3);        // start inclusive, end exclusive
s.toCharArray();
new String(charArray);
s.split(",");
s.trim();
s.toLowerCase();
String.valueOf(123);      // int to string
Integer.parseInt("123");  // string to int
s.equals(other);          // NEVER use == for string content

// StringBuilder (use when building strings in a loop)
StringBuilder sb = new StringBuilder();
sb.append("x");
sb.reverse();
sb.toString();
```

---

## 3. Sorting with Custom Comparators

```java
// Sorting objects
Arrays.sort(arr, (a, b) -> a - b);              // ascending
Arrays.sort(arr, (a, b) -> b - a);              // descending

List<int[]> intervals = new ArrayList<>();
intervals.sort((a, b) -> a[0] - b[0]);          // sort by first element

// Sorting a list of custom objects
list.sort(Comparator.comparing(Person::getAge));
list.sort(Comparator.comparing(Person::getAge).reversed());
list.sort(Comparator.comparing(Person::getAge).thenComparing(Person::getName));
```

---

## 4. Common Patterns (Templates to Memorize)

**Two pointers:**
```java
int left = 0, right = arr.length - 1;
while (left < right) {
    // logic
    left++; right--;
}
```

**Sliding window:**
```java
int left = 0;
for (int right = 0; right < arr.length; right++) {
    // expand window with arr[right]
    while (/* window invalid */) {
        // shrink from left
        left++;
    }
}
```

**BFS (graph/tree, using a queue):**
```java
Queue<Node> queue = new LinkedList<>();
queue.offer(start);
Set<Node> visited = new HashSet<>();
visited.add(start);
while (!queue.isEmpty()) {
    Node curr = queue.poll();
    for (Node neighbor : curr.neighbors) {
        if (!visited.contains(neighbor)) {
            visited.add(neighbor);
            queue.offer(neighbor);
        }
    }
}
```

**DFS (recursive):**
```java
void dfs(Node node, Set<Node> visited) {
    if (node == null || visited.contains(node)) return;
    visited.add(node);
    for (Node neighbor : node.neighbors) {
        dfs(neighbor, visited);
    }
}
```

**Binary search:**
```java
int lo = 0, hi = arr.length - 1;
while (lo <= hi) {
    int mid = lo + (hi - lo) / 2;
    if (arr[mid] == target) return mid;
    else if (arr[mid] < target) lo = mid + 1;
    else hi = mid - 1;
}
```

---

## 5. Java Streams — Incorporating Them Cleanly

Streams are increasingly expected in interviews, especially at companies that care about "modern, idiomatic Java." Key mindset: **use streams for transformations on collections, not for control flow with side effects or early exits.** If you need to `break` out of a loop early, a stream is usually the wrong tool.

### Traditional loop → Stream equivalent

| Task | Traditional | Stream |
|---|---|---|
| Filter | `for (x : list) if (cond) result.add(x);` | `list.stream().filter(x -> cond).collect(Collectors.toList())` |
| Map/transform | `for (x : list) result.add(f(x));` | `list.stream().map(x -> f(x)).collect(Collectors.toList())` |
| Sum | manual loop with accumulator | `list.stream().mapToInt(x -> x).sum()` |
| Sort | `Collections.sort(list)` | `list.stream().sorted().collect(Collectors.toList())` |
| Find first match | manual loop + break | `list.stream().filter(cond).findFirst()` |
| Check any/all match | manual loop + flag | `list.stream().anyMatch(cond)` / `.allMatch(cond)` |
| Count matching | manual loop + counter | `list.stream().filter(cond).count()` |
| Group by key | manual map-building loop | `list.stream().collect(Collectors.groupingBy(x -> key(x)))` |
| Join strings | StringBuilder loop | `list.stream().map(Object::toString).collect(Collectors.joining(", "))` |
| Get max/min | manual loop tracking best | `list.stream().max(Comparator.comparing(x -> x))` |

### Example: the Group Anagrams problem, streamified

```java
public List<List<String>> groupAnagrams(String[] strs) {
    return new ArrayList<>(
        Arrays.stream(strs)
            .collect(Collectors.groupingBy(this::sortChars))
            .values()
    );
}

private String sortChars(String s) {
    char[] chars = s.toCharArray();
    Arrays.sort(chars);
    return new String(chars);
}
```

Note `Collectors.groupingBy` does the hashmap-building work for you — this is a common "show the streamlined version after the manual version" interview follow-up.

### Common Stream syntax to have cold

```java
list.stream()
    .filter(x -> x > 0)
    .map(x -> x * 2)
    .sorted()
    .collect(Collectors.toList());

// toMap
list.stream().collect(Collectors.toMap(x -> x.getId(), x -> x.getName()));

// groupingBy with downstream counting
list.stream().collect(Collectors.groupingBy(x -> x.getCategory(), Collectors.counting()));

// reduce
int sum = list.stream().reduce(0, (a, b) -> a + b);

// IntStream for ranges
IntStream.range(0, 10).forEach(i -> System.out.println(i));
```

### When NOT to reach for streams in an interview
- When the interviewer wants to see you reason step-by-step (they often want the manual loop version first).
- When early termination / short-circuiting logic gets awkward to express.
- When it would obscure your logic under time pressure — a correct loop beats a fumbled stream.

**Good interview move:** write the loop version first (shows you understand the mechanics), then say "I could also express this more concisely with a stream" and show it if time allows. This demonstrates both fundamentals and modern fluency — exactly what mixed-format interviews are testing for.

---

## 6. Recall Drill Routine

1. Cover the right column of each table above. Write the syntax from memory.
2. Pick one pattern template (two pointers, BFS, etc.) per day and type it out cold, no reference.
3. After each practice problem, add any syntax you blanked on to a running "leak list" — review that list weekly instead of re-drilling everything.
