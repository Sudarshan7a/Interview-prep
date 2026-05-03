# 🎯 Ultimate Interview Prep Kit: Collections — ArrayDeque, PriorityQueue, HashSet, TreeSet & LinkedHashSet

> **Based on:** Class transcript covering ArrayDeque (properties, iteration, circular array, descending iterator), PriorityQueue (binary min-heap internals, heapify up/down, log n complexity), TreeSet (red-black BST, LVR traversal, range operations), HashSet (hashing, hash function, load factor, bucket collision, equals/hashCode override), LinkedHashSet, and the decision framework for choosing the right Set.

---

## Table of Contents

1. [📌 Core Concept](#-core-concept)
2. [🧠 Deep Understanding](#-deep-understanding)
3. [💬 Interview Q&A (14 Questions)](#-interview-qa)
4. [🎯 How to Explain It](#-how-to-explain-it)
5. [⚠️ Edge Cases & Gotchas](#️-edge-cases--gotchas)
6. [✅ Quick Revision Card](#-quick-revision-card)
7. [🔗 Connections](#-connections)
8. [🎓 Real-World Scenarios](#-real-world-scenarios)
9. [📝 Practice Questions](#-practice-questions)
10. [🚀 The Winning Formula](#-the-winning-formula)

---

## 📌 Core Concept

### ArrayDeque

**One-liner:** ArrayDeque is a **resizable circular array** implementation of the Deque interface — no index, no null, initial capacity 16. Use it as a Stack or Queue without the overhead of LinkedList.

**Analogy:** Think of a **revolving door** — you can enter from either side (front or rear), and the door keeps rotating. That's a Deque. ArrayDeque is that same revolving door but built on an efficient array.

---

### PriorityQueue

**One-liner:** PriorityQueue is a **binary min-heap tree** where the **smallest element always gets the highest priority** and sits at the top — used in GPS, OS scheduling, and data compression.

**Analogy:** Think of a **hospital emergency room queue**. It's not first-come-first-served. The most critical patient (smallest = most urgent) always gets treated first, regardless of arrival order. That's a PriorityQueue.

---

### Set (HashSet / TreeSet / LinkedHashSet)

**One-liner:** Set is a collection that **never allows duplicate elements**. Three implementations — HashSet (O(1) random, unordered), TreeSet (O(log n) sorted), LinkedHashSet (O(1) insertion-order preserved).

**Analogy:** Think of **Aadhaar card numbers**. Every citizen gets a unique number — no duplicates allowed. The government's database is effectively a giant Set. If you try to assign an existing number again, the system silently rejects it.

---

## 🧠 Deep Understanding

### ArrayDeque — How It Actually Works

#### Creating and Using ArrayDeque

```java
import java.util.ArrayDeque;

ArrayDeque<Integer> ad = new ArrayDeque<>();  // default capacity = 16
// ArrayDeque does NOT allow null — null insertion throws NullPointerException
// ArrayDeque does NOT have index — get() method doesn't exist
// Methods: same as LinkedList's Deque methods
```

#### Key Properties

| Property | Value |
|---|---|
| **Package** | `java.util` |
| **Initial capacity** | **16** |
| **Resizing formula** | **current × 2** (doubles) |
| **Null insertion** | ❌ **NOT allowed** — NullPointerException |
| **Index** | ❌ **No index** — for loop with get() won't work |
| **Heterogeneous data** | ✅ Yes (without generic) |
| **Preserves insertion order** | ✅ Yes |
| **Duplicates allowed** | ✅ Yes |
| **Internal data structure** | **Resizable circular array** |
| **Constructors** | 3 (no-arg, int capacity, Collection) |

#### Hierarchy

```
Iterable → Collection → Deque → Queue → Collection → Iterable
                ↑
          ArrayDeque (implements Deque, extends AbstractCollection)
```

#### Iteration — What Works and What Doesn't

```java
ArrayDeque<Integer> ad = new ArrayDeque<>(List.of(100, 200, 300, 400));

// ❌ Traditional for loop — NOT possible (no index, no get() method)
// for (int i = 0; i < ad.size(); i++) { ad.get(i); }  → compile error

// ✅ Enhanced for loop — works fine (no index needed)
for (Object element : ad) {
    System.out.println(element);
}

// ✅ Iterator — works fine (forward only)
Iterator<Integer> cursor = ad.iterator();
while (cursor.hasNext()) {
    System.out.println(cursor.next());
}

// ❌ ListIterator — NOT possible (ListIterator only works on List-based classes)
// ad.listIterator(); → compile error

// ✅ Descending Iterator — works (because ArrayDeque implements Deque)
Iterator<Integer> desc = ad.descendingIterator();
while (desc.hasNext()) {
    System.out.println(desc.next());  // prints in reverse with hasNext() not hasPrevious()!
}
```

**Why no ListIterator?** Two reasons:
1. ListIterator requires index, and ArrayDeque has no index.
2. `listIterator()` is a method of the `List` interface. ArrayDeque doesn't implement `List` — it implements `Deque`.

**Why does Descending Iterator use `hasNext()` not `hasPrevious()`?**
Because the descending iterator's job is to iterate backwards — that IS its forward direction. It was designed for one direction only (reverse), so there's no concept of "previous" for it.

#### Stack, Queue, Deque with ArrayDeque

```java
// All same methods as LinkedList (from Deque/Queue hierarchy):
ad.addFirst(10);   ad.addLast(100);     // Deque insertion
ad.offerFirst(5);  ad.offerLast(500);   // Deque insertion (false on fail)
ad.peekFirst();    ad.peekLast();       // View without removing
ad.pollFirst();    ad.pollLast();       // Remove with null on empty
ad.removeFirst();  ad.removeLast();     // Remove with exception on empty
ad.push(1);        ad.pop();            // Stack: LIFO behavior
ad.offer(10);      ad.poll();           // Queue: FIFO behavior
```

**ArrayDeque vs LinkedList for Stack/Queue:**

| | ArrayDeque | LinkedList |
|---|---|---|
| Null allowed | ❌ No | ✅ Yes |
| Memory per element | Less (just data) | More (data + 2 addresses) |
| Index support | ❌ No | ❌ No (technically) |
| Preferred for Stack/Queue | ✅ Yes (faster, less memory) | Works but heavier |

---

### PriorityQueue — How It Actually Works

#### Creating and Inserting

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();  // initial capacity = 11
pq.offer(45);  // or pq.add(45)
pq.offer(10);
pq.offer(25);
// Internal state: not simply [10, 25, 45] — it's a heap tree!
System.out.println(pq);  // output: [10, 45, 25] — level-by-level, NOT fully sorted
```

#### The Binary Min-Heap — Visualized

Every node can have at most 2 children (binary). The parent is ALWAYS smaller than or equal to its children (min-heap property). The smallest element always sits at the **root** (top).

```
Insert order: 10, 100, 200, 1

After inserting 10:           After inserting 100:       After inserting 200:
        10                          10                         10
                                   /                         /   \
                                 100                        100   200

After inserting 1:  (1 < 100, swap; 1 < 10, swap)
        1            → swap with parent →       1
       / \                                     / \
     10   200                               10   200
     /                                     /
   100                                   100

Final heap = [1, 10, 200, 100]  ← this is what println shows (level-by-level)
```

#### How Insertion Works (Heapify Up)

1. New element is placed at the **next available position** (last leaf).
2. It compares itself with its parent: **if smaller → swap** (min-heap: parent must always be ≤ children).
3. Repeat step 2 until no more swaps needed or it reaches the root.
4. This process is called **heapify up**.

#### How Deletion (poll) Works (Heapify Down)

1. The root (smallest element) is removed and returned.
2. The **last inserted element** is moved to the root temporarily.
3. It compares itself with its children: **if larger than any child → swap with the smaller child**.
4. Repeat until no more swaps needed or it reaches a leaf.
5. This process is called **heapify down**.

```java
pq = [1, 10, 200, 100]

pq.poll() → returns 1
After poll: move last element (100) to root → [100, 10, 200]
heapify down: 100 > 10? Yes → swap → [10, 100, 200]
Result after poll: pq = [10, 100, 200]

pq.poll() → returns 10
After poll: move last element (200) to root → [200, 100]
heapify down: 200 > 100? Yes → swap → [100, 200]
Result: pq = [100, 200]

pq.poll() → returns 100
Result: pq = [200]

pq.poll() → returns 200
Result: pq = []

pq.poll() → returns null  (not exception — because poll() is safe)
```

#### Printing PriorityQueue in Sorted Order

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(10); pq.offer(32); pq.offer(200); pq.offer(1); pq.offer(100); pq.offer(22); pq.offer(78);

// Option 1: While loop with poll()
while (!pq.isEmpty()) {
    System.out.print(pq.poll() + " ");  // 1 10 22 32 78 100 200
}

// Option 2: Enhanced for loop (prints level-by-level, NOT sorted!)
for (Object e : pq) {
    System.out.print(e + " ");  // NOT sorted — avoid for sorted output
}
```

#### Key Properties

| Property | Value |
|---|---|
| **Initial capacity** | **11** |
| **Heterogeneous data** | ❌ NOT allowed (comparison required → ClassCastException) |
| **Preserves insertion order** | ❌ No — internal min-heap ordering |
| **Duplicates allowed** | ✅ Yes |
| **Null insertion** | ❌ NOT allowed — with whom to compare null? |
| **Internal data structure** | **Binary Min-Heap Tree** |
| **Constructors** | **7** |
| **Time complexity** | **O(log n)** insert and delete |

**Why O(log n)?** Because the heap is a complete binary tree. At each level, elements double (1, 2, 4, 8...). Comparing an element at each level means traversing at most `log₂(n)` levels — neither O(1) nor O(n), but in between.

#### 7 Constructors

1. No-arg → default capacity 11
2. `(int initialCapacity)` → custom capacity
3. `(Comparator c)` → custom sorting (max-heap, custom priority)
4. `(int initialCapacity, Comparator c)` → both custom
5. `(Collection c)` → convert any collection to PriorityQueue
6. `(PriorityQueue pq)` → copy from another PriorityQueue
7. `(SortedSet ss)` → from a sorted set

**Making a Max-Heap (largest element first):**
```java
// Uses Comparator (covered in Comparator/Comparable session)
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
maxHeap.offer(10);  maxHeap.offer(50);  maxHeap.offer(30);
System.out.println(maxHeap.poll());  // 50 — largest first
```

---

### TreeSet — How It Actually Works

#### The Concept

TreeSet gives you a **sorted, duplicate-free** collection using a **Binary Search Tree (BST)** — specifically a **Red-Black Tree** (self-balancing BST).

```java
TreeSet<Integer> ts = new TreeSet<>();
ts.add(50); ts.add(30); ts.add(70); ts.add(20); ts.add(40); ts.add(60); ts.add(80);
System.out.println(ts);  // [20, 30, 40, 50, 60, 70, 80] — always sorted!
```

#### How Data Is Inserted (BST Rules)

```
Insert 50: root = 50

Insert 30: 30 < 50 → goes LEFT of 50
Insert 70: 70 > 50 → goes RIGHT of 50

Insert 20: 20 < 50 → go left; 20 < 30 → goes LEFT of 30
Insert 40: 40 < 50 → go left; 40 > 30 → goes RIGHT of 30
Insert 60: 60 > 50 → go right; 60 < 70 → goes LEFT of 70
Insert 80: 80 > 50 → go right; 80 > 70 → goes RIGHT of 70

Final tree:
              50 (BLACK)
            /           \
        30 (RED)        70 (RED)
       /    \          /    \
    20(B)  40(B)    60(B)  80(B)

Rule: Left subtree values < parent. Right subtree values > parent.
```

#### How Sorted Output Happens (LVR — In-Order Traversal)

LVR = **L**eft subtree → **V**isit node → **R**ight subtree

The traversal path always goes in a reverse-U shape starting from the bottom-left:

```
LVR traversal: 20 → 30 → 40 → 50 → 60 → 70 → 80
(Start at leftmost leaf, reverse-U through the tree)
```

This produces sorted ascending order automatically!

#### Red-Black Tree Rules (for interviews)

- Every node is either RED or BLACK.
- The **root** is always BLACK.
- **Parent nodes** (nodes with children) are BLACK.
- **Leaf nodes** (no children) are RED.
- Self-balancing: if insertions cause imbalance, rotations ensure tree height stays ~log n.
- Also called: **BST** (Binary Search Tree) or **RBT** (Red-Black Tree).

#### Key Properties

| Property | Value |
|---|---|
| **Initial capacity** | **0** (no pre-allocation — BST grows on demand) |
| **Heterogeneous data** | ❌ NOT allowed (comparison required → ClassCastException) |
| **Preserves insertion order** | ❌ No — always sorted order |
| **Duplicates allowed** | ❌ No |
| **Null insertion** | ❌ No — cannot compare null |
| **Internal data structure** | **Red-Black Tree (Binary Search Tree)** |
| **Default sorting** | **Ascending (natural order)** |
| **Constructors** | 5 (no-arg, Comparator, Collection, SortedSet, NavigableMap) |
| **Time complexity** | **O(log n)** for all operations |

#### TreeSet-Specific Methods

```java
TreeSet<Integer> ts = new TreeSet<>(List.of(20, 30, 40, 50, 60, 70, 80, 90));

ts.first();          // 20 — smallest element
ts.last();           // 90 — largest element

// CEILING — greater than or equal to (≥)
ts.ceiling(60);      // 60 (exists)
ts.ceiling(51);      // 60 (51 doesn't exist, next higher = 60)
ts.ceiling(100);     // null (nothing ≥ 100)

// FLOOR — less than or equal to (≤)
ts.floor(60);        // 60 (exists)
ts.floor(55);        // 50 (55 doesn't exist, next lower = 50)
ts.floor(15);        // null (nothing ≤ 15)

// HIGHER — strictly greater than (>)
ts.higher(60);       // 70 (strictly > 60)
ts.higher(90);       // null

// LOWER — strictly less than (<)
ts.lower(60);        // 50 (strictly < 60)
ts.lower(20);        // null

// HEAD SET — elements from beginning up to (exclusive by default)
ts.headSet(50);          // [20, 30, 40] — excludes 50
ts.headSet(50, true);    // [20, 30, 40, 50] — includes 50 (boolean = inclusive)

// TAIL SET — elements from (inclusive by default) to end
ts.tailSet(60);          // [60, 70, 80, 90] — includes 60
ts.tailSet(60, false);   // [70, 80, 90] — excludes 60

// SUBSET — elements between two values
ts.subSet(30, 70);              // [30, 40, 50, 60] — 30 inclusive, 70 exclusive
ts.subSet(30, true, 70, true);  // [30, 40, 50, 60, 70] — both inclusive

// POLL operations — remove and return
ts.pollFirst();      // removes and returns 20 (smallest)
ts.pollLast();       // removes and returns 90 (largest)
```

**Memory trick for ceiling/floor vs higher/lower:**
- Ceiling/Floor = **≥ and ≤** (includes the element itself if present)
- Higher/Lower = **strictly > and <** (never includes the element itself)

---

### HashSet — How It Actually Works (The Deep Dive)

This is the **most frequently asked** collection internals question at product-based companies.

#### Overview

```java
HashSet<Integer> hs = new HashSet<>();
hs.add(100); hs.add(200); hs.add(300); hs.add(400);
System.out.println(hs);  // [100, 400, 200, 300] — RANDOM order, not insertion order!
```

#### The Hashing Process — Step by Step

When you call `hs.add(100)`, here's exactly what happens internally:

```
Step 1: hashCode() is called on 100
         → 100.hashCode() (Integer class has its own hashCode implementation)
         → returns some integer hash value

Step 2: Hash function calculates bucket location
         → bucket = hash % capacity  (simplified example: 100 % 16 = 4)
         → bucket 4 is where 100 will be stored

Step 3: 100 is placed in bucket 4
```

```
Visual: HashSet internal structure (16 buckets = initial capacity)
Bucket 0: []
Bucket 1: []
Bucket 2: []
Bucket 3: []
Bucket 4: [100]  ← 100 % 16 = 4 (simplified)
Bucket 5: []
...
Bucket 12: [200]  ← 200 % 16 = 8 (simplified) — actual bucket varies
...
```

#### Hash Function, Hashing, Hash Table

- **Hash Function:** The process that takes an element, calls its `hashCode()`, performs mathematical operations, and generates a bucket location (address).
- **Hashing:** The complete process of determining a bucket location using the hash function.
- **Hash Table:** The internal data structure (array of buckets) where elements are stored.

#### Internally, HashSet Uses HashMap

**HashSet internally stores:**
- **Key** = the element you insert (e.g., 100)
- **Value** = a static dummy constant: `PRESENT` (just a boolean placeholder)

This is why in HashSet, duplicates (duplicate keys) are not allowed — same as HashMap's key uniqueness rule!

```java
// Internally, HashSet works like:
// hs.add(100) → map.put(100, PRESENT)  where PRESENT = new Object()
// hs.contains(100) → map.containsKey(100)
```

#### Bucket Collision and What Happens

What if two elements hash to the same bucket?

```
hs.add(100) → bucket 7
hs.add(2500) → also bucket 7  ← COLLISION!

Result in bucket 7: [100] → [2500]  (LinkedList formed)
```

**The collision resolution chain:**
1. Up to **8 elements** in same bucket → stored as **LinkedList** (chain)
2. After **8 elements** in same bucket → **LinkedList converts to Red-Black Tree** (Java 8+)

This is why in worst case, time complexity degrades from O(1) to O(n) or O(log n).

#### Load Factor and Rehashing

- **Default capacity:** 16 buckets
- **Load factor:** 0.75 (75%)
- **Threshold:** 16 × 0.75 = **12** — when 12 buckets are filled, rehashing triggers
- **Rehashing:** Capacity doubles to 32, ALL elements are re-hashed to new bucket locations

```java
HashSet<Integer> hs = new HashSet<>();         // 16 buckets
HashSet<Integer> hs = new HashSet<>(32);       // custom initial capacity
HashSet<Integer> hs = new HashSet<>(16, 0.5f); // custom capacity + load factor
```

**Why 75%?** Balance between time and space. Too high (e.g., 100%) → many collisions → O(n) operations. Too low (e.g., 25%) → frequent resizing → wasted memory.

#### Key Properties

| Property | Value |
|---|---|
| **Initial capacity** | **16 buckets** |
| **Load factor** | **0.75 (75%)** |
| **Resizing trigger** | When 75% of buckets are filled |
| **Resizing amount** | **Doubles** (16 → 32 → 64…) |
| **Heterogeneous data** | ✅ Yes (but comparison issues at runtime) |
| **Preserves insertion order** | ❌ No — random bucket locations |
| **Duplicates allowed** | ❌ No |
| **Null insertion** | ✅ Yes — ONE null allowed |
| **Internal data structure** | **Hash Table (backed by HashMap)** |
| **Constructors** | **5** |
| **Time complexity (avg)** | **O(1)** insert, remove, contains |
| **Time complexity (worst)** | **O(n)** on full collision |

---

### equals() and hashCode() — The Contract

This is a guaranteed product-company interview question.

#### The Problem

```java
class Student {
    int id;
    String name;
    Student(int id, String name) { this.id = id; this.name = name; }
}

HashSet<Student> hs = new HashSet<>();
Student s1 = new Student(1, "Arjun");
Student s2 = new Student(1, "Arjun");  // same data, different object

hs.add(s1);
hs.add(s2);
System.out.println(hs.size());  // 2 ← PROBLEM! Should be 1 (same student)
```

**Why does this happen?**
- `hashCode()` from `Object` returns a value based on **memory address** — s1 and s2 have different addresses → different hash codes → different buckets → both get inserted!
- `equals()` from `Object` compares **references (addresses)**, not field values.

#### The Fix — Override hashCode() and equals()

```java
import java.util.Objects;

class Student {
    int id;
    String name;

    Student(int id, String name) { this.id = id; this.name = name; }

    @Override
    public int hashCode() {
        return Objects.hash(id, name);  // Objects utility class (with 's')
        // Objects.hash(id) — if comparing only by id
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;           // Step 1: same reference?
        if (!(obj instanceof Student)) return false;  // Step 2: same type?
        Student other = (Student) obj;          // Step 3: downcast
        return this.id == other.id &&           // Step 4: compare fields
               Objects.equals(this.name, other.name);
    }

    @Override
    public String toString() {
        return "Student(" + id + ")";
    }
}

// Now:
HashSet<Student> hs = new HashSet<>();
hs.add(new Student(1, "Arjun"));
hs.add(new Student(1, "Arjun"));   // same data
System.out.println(hs.size());      // 1 ← CORRECT!
```

**The Contract:** If two objects are `equal()`, they MUST have the same `hashCode()`. (The reverse is not required — different hash codes don't have to mean not equal.)

**Eclipse shortcut:** Right-click → Source → Generate hashCode() and equals() → select fields → Generate.

---

### LinkedHashSet — The Simple One

LinkedHashSet = **HashSet + LinkedList order tracking**

```java
LinkedHashSet<Integer> lhs = new LinkedHashSet<>();
lhs.add(100); lhs.add(200); lhs.add(300); lhs.add(400);
System.out.println(lhs);  // [100, 200, 300, 400] — INSERTION ORDER preserved!
```

- Same as HashSet in all properties.
- The one difference: it **preserves insertion order** by maintaining a doubly linked list that connects elements in insertion order.
- Uses more memory than HashSet (linked list overhead for ordering).
- No duplicates. No nulls... wait, actually one null is allowed.
- Hierarchy: `LinkedHashSet extends HashSet extends AbstractSet → Set → Collection → Iterable`

---

### Choosing the Right Set — Decision Framework

```
Need UNIQUE elements (no duplicates)?
  └── YES → Use a SET
        ├── Need SORTED order?
        │     └── YES → TreeSet (O(log n), range operations available)
        │
        └── NO (order doesn't matter / insertion order matters)
              ├── Need INSERTION ORDER preserved?
              │     └── YES → LinkedHashSet (O(1), insertion order)
              │
              └── NO (random order fine)
                    └── HashSet (O(1), fastest, least memory)
```

---

## 💬 Interview Q&A

---

### Q1 (Basic): What is the difference between ArrayDeque and LinkedList for Stack/Queue behavior?

**Perfect Answer:**

Both ArrayDeque and LinkedList implement the `Deque` interface, so they support the same Stack, Queue, and Deque methods. The key differences are in performance and memory.

ArrayDeque is backed by a **resizable circular array** with initial capacity 16. It's faster in practice because array access is cache-friendly — data is stored contiguously in memory. It does not allow null insertion.

LinkedList is backed by a **doubly linked list**. Each node stores data plus two addresses (prev/next), so it uses more memory per element. It does allow null insertion, and it has index support (via get(int index)), which ArrayDeque does not.

```java
// ArrayDeque — preferred for Stack/Queue (faster, less memory)
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1); stack.push(2); stack.push(3);
System.out.println(stack.pop());  // 3

// LinkedList — preferred when null elements are needed or when used as a List
Deque<Integer> queue = new LinkedList<>();
queue.offer(1); queue.offer(2);
System.out.println(queue.poll());  // 1
```

**What NOT to say:** Don't say "LinkedList is better for Stack." For pure Stack/Queue behavior, ArrayDeque is the preferred choice since Java's own documentation recommends it over Stack and LinkedList for Deque use cases.

---

### Q2 (Basic): Why can't you use a traditional for loop with ArrayDeque or PriorityQueue?

**Perfect Answer:**

Traditional for loops rely on index-based access — typically `for (int i = 0; i < size; i++)` combined with `get(i)`. The `get(int index)` method comes from the `List` interface. Neither ArrayDeque nor PriorityQueue implement `List` — ArrayDeque implements `Deque`, PriorityQueue implements `Queue`. So `get()` simply doesn't exist in either class.

Additionally, neither data structure has a physical index internally. ArrayDeque is a circular array but exposes no positional index to the caller. PriorityQueue is a binary min-heap tree — heap nodes don't have sequential indices.

The correct alternatives are: enhanced for-each loop (for both), iterator (for both), and descending iterator (for ArrayDeque, since it implements Deque; NOT for PriorityQueue since PriorityQueue doesn't implement Deque).

```java
// Enhanced for loop — works for both
for (Object e : pq) { System.out.println(e); }   // prints level-by-level (not sorted!)

// To get sorted output from PriorityQueue — use poll()
while (!pq.isEmpty()) { System.out.print(pq.poll() + " "); }  // sorted
```

---

### Q3 (Intermediate): Explain how PriorityQueue works internally. What is the internal data structure?

**Perfect Answer:**

PriorityQueue's internal data structure is a **binary min-heap tree**. "Binary" means each node has at most two children. "Min-heap" means the parent is always smaller than or equal to its children — guaranteeing the smallest element is always at the root (head).

When you insert an element, it's placed at the next available leaf position, then compared with its parent. If it's smaller than the parent, they swap. This bubbling-up process is called **heapify up**. It repeats until the element finds its correct position.

When you remove (poll) an element, the root (smallest) is deleted. The last element temporarily moves to the root, then compares with its children and swaps with the smaller one — this is **heapify down**.

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(10); pq.offer(100); pq.offer(200); pq.offer(1);
// After insertion: min-heap with 1 at root

System.out.println(pq);  // [1, 10, 200, 100] — level-by-level, not fully sorted!
System.out.println(pq.poll());  // 1 — always the minimum

// To print sorted order:
while (!pq.isEmpty()) System.out.print(pq.poll() + " ");
// Output: 1 10 100 200
```

**Time complexity:** O(log n) for both insert and delete. Why? Because the heap is a complete binary tree — at each level, the number of nodes doubles (1, 2, 4, 8...), so max levels = log₂(n). Traversal touches at most log n levels.

**Physically in RAM:** Despite being conceptually a tree, in memory PriorityQueue uses an **array** to store elements level by level. Left child of element at index `i` is at `2i + 1`; right child is at `2i + 2`.

---

### Q4 (Intermediate): What is the difference between `peek()` and `poll()` in PriorityQueue?

**Perfect Answer:**

Both `peek()` and `poll()` access the **head element** — the smallest element in the min-heap.

`peek()` **reads** the head element and returns it **without removing** it. If the queue is empty, it returns `null` (no exception). The PriorityQueue is unchanged.

`poll()` **removes** the head element, triggers heapify-down internally to re-establish heap order, and returns the removed element. If empty, returns `null`. The PriorityQueue is modified.

```java
PriorityQueue<Integer> pq = new PriorityQueue<>(List.of(5, 3, 1, 4, 2));
System.out.println(pq.peek());  // 1 — head element, NOT removed
System.out.println(pq);         // [1, 2, 3, 4, 5] or similar — unchanged

System.out.println(pq.poll());  // 1 — head element, REMOVED
System.out.println(pq.peek());  // 2 — new head after 1 was removed
```

**Why can't you use a for-each loop and call poll() simultaneously?**
You'll get `ConcurrentModificationException`. The for-each loop uses an iterator that tracks the collection's structure. Calling poll() inside it modifies the heap while iterating — violating the iterator's contract. Use a while loop with `!pq.isEmpty()` instead.

---

### Q5 (Intermediate): What is TreeSet? Why is it called a Red-Black Tree?

**Perfect Answer:**

TreeSet is a sorted, duplicate-free Set implementation backed by a **Red-Black Tree**, which is a self-balancing Binary Search Tree. When you insert any element, it's automatically placed in sorted order based on BST rules: values smaller than a node go left, values larger go right.

It's called a Red-Black Tree because each node is assigned a color — either red or black — following specific rules that keep the tree balanced. Parent nodes (nodes with children) are black; leaf nodes (no children) are red; the root is always black. These coloring rules ensure the tree height stays approximately log n, preventing the worst-case O(n) degradation of an unbalanced BST.

The sorted output is produced by **in-order traversal** (also called LVR — Left, Visit, Right): start from the leftmost leaf, traverse in a reverse-U pattern to produce ascending order.

```java
TreeSet<String> ts = new TreeSet<>();
ts.add("Banana"); ts.add("Apple"); ts.add("Cherry"); ts.add("Apple"); // duplicate ignored
System.out.println(ts);  // [Apple, Banana, Cherry] — sorted alphabetically, no duplicates
```

**Time complexity:** O(log n) for all operations — because the tree height is bounded by log n.

**When to use:** When you need sorted unique elements, or when you need range operations (headSet, tailSet, subSet, ceiling, floor, higher, lower).

---

### Q6 (Advanced): Explain how HashSet determines where to store an element. What is hashing?

**Perfect Answer:**

When you insert an element into a HashSet, three things happen internally:

1. **hashCode() is called** on the element. Every object in Java has a `hashCode()` method inherited from `Object`. For standard types like `Integer` and `String`, it's already overridden to return consistent hash values. For custom objects, you must override it yourself.

2. **The hash function computes a bucket address** using the hash code and the current capacity (simplified: `bucket = hashCode % capacity`). This generates a specific index into the internal array of buckets (the hash table).

3. **The element is stored in that bucket.** Since bucket addresses appear random from the outside, HashSet output looks unordered.

This entire process — calling hashCode, computing the bucket address, storing the element there — is called **hashing**.

```java
// Internally: when you call hs.add(100)
// Step 1: 100.hashCode() → some integer, say 100
// Step 2: bucket = 100 % 16 = 4   (simplified; actual math is more complex)
// Step 3: 100 is stored in bucket 4

// Result: O(1) average time for add/contains/remove
// because we can always directly jump to bucket 4 to find 100
```

**Why is this O(1)?** Because finding an element doesn't require traversing all elements — just computing the same bucket address and checking that specific bucket directly.

---

### Q7 (Advanced): What happens when two elements hash to the same bucket? (Bucket Collision)

**Perfect Answer:**

Bucket collision occurs when the hash function assigns two different elements to the same bucket. This is unavoidable in practice — with 16 buckets and potentially thousands of elements, collisions will happen.

When a collision occurs, the elements in that bucket are organized as a **LinkedList** — the new element is appended to the chain in that bucket. When checking if an element exists (`contains()`), HashSet goes to the bucket, then traverses the chain comparing with `equals()` until a match is found.

From Java 8 onward, if a single bucket chain grows beyond **8 elements**, the LinkedList converts to a **Red-Black Tree** for that bucket. This prevents O(n) degradation in pathological collision cases.

```
Without collision:         With collision:
Bucket 4: [100]           Bucket 7: [100] → [2500] → [500]  ← LinkedList chain
                          (all three have bucket address = 7)
```

**When does collision make HashSet slow?** If almost all elements hash to the same bucket, `contains()` becomes O(n) — traversing the entire chain. This is the **worst case** for HashSet. In practice, with good hash codes and proper load factor, collisions are rare.

**When does rehashing happen?** When 75% of buckets (the load factor) are filled, HashSet doubles its capacity and re-hashes ALL elements into new bucket positions. This is called **rehashing** — analogous to ArrayList's resizing.

---

### Q8 (Advanced): What happens when you insert custom objects into a HashSet without overriding equals() and hashCode()?

**Perfect Answer:**

By default, `hashCode()` from `Object` returns a value derived from the **object's memory address**. And `equals()` from `Object` compares **references** (addresses). So two objects with identical field values are treated as completely different — they'll get different hash codes, land in different buckets, and both get inserted.

This violates the expected behavior for domain objects:

```java
class Student {
    int id; String name;
    Student(int id, String name) { this.id = id; this.name = name; }
}

HashSet<Student> hs = new HashSet<>();
hs.add(new Student(1, "Arjun"));
hs.add(new Student(1, "Arjun"));  // same data!
System.out.println(hs.size());    // 2 — WRONG! Should be 1 (duplicate!)
```

**The fix:** Override both `hashCode()` and `equals()`. The contract: if two objects are `equal()`, they MUST return the same `hashCode()`. Eclipse can generate this automatically (Source → Generate hashCode() and equals()).

```java
@Override
public int hashCode() {
    return Objects.hash(id, name);  // Objects utility class (with 's')
}

@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (!(obj instanceof Student)) return false;
    Student other = (Student) obj;
    return this.id == other.id && Objects.equals(this.name, other.name);
}
```

After overriding, `hs.size()` returns 1 correctly — the duplicate is silently ignored.

**Why is this a product-company question?** Because in real applications, you store domain objects (User, Product, Order) in HashSets and HashMaps. Without proper equals/hashCode, your "no duplicates" guarantee silently breaks.

---

### Q9 (Intermediate): What is load factor? Why is the default 0.75?

**Perfect Answer:**

Load factor is a threshold that determines when the HashSet should resize (rehash) itself. The default load factor of 0.75 means: when 75% of the available bucket locations are occupied, HashSet doubles its capacity and re-distributes all existing elements into the new, larger hash table.

For the default capacity of 16: `16 × 0.75 = 12`. Once 12 buckets are filled, the 13th insertion triggers rehashing to 32 buckets.

0.75 was chosen as the optimal balance:
- **Too high (e.g., 0.95):** Buckets fill up heavily → many collisions → long chains → O(n) operations
- **Too low (e.g., 0.25):** Rehashing happens too frequently → wasted memory and time
- **0.75:** Empirically proven to minimize collision probability while avoiding excessive memory use

You can customize it:
```java
HashSet<Integer> hs = new HashSet<>(16, 0.5f);  // resize at 50% capacity
```

---

### Q10 (Intermediate): What is LinkedHashSet? How does it preserve insertion order if it uses hashing?

**Perfect Answer:**

LinkedHashSet is a subclass of HashSet. It adds a doubly linked list that runs through all entries in **insertion order**. Each bucket entry has an additional pair of pointers — `before` and `after` — that link all elements in the order they were added.

So while the hash table still determines *which bucket* an element lands in (random-seeming), the linked list ensures that when you iterate, you traverse elements in insertion order by following the `before/after` chain.

```java
LinkedHashSet<String> lhs = new LinkedHashSet<>();
lhs.add("Banana"); lhs.add("Apple"); lhs.add("Cherry");
System.out.println(lhs);  // [Banana, Apple, Cherry] — insertion order preserved!

// vs HashSet:
HashSet<String> hs = new HashSet<>();
hs.add("Banana"); hs.add("Apple"); hs.add("Cherry");
System.out.println(hs);   // [Apple, Cherry, Banana] — random order!
```

**Trade-off:** LinkedHashSet uses more memory than HashSet — the extra linked list pointers per element. Time complexity remains O(1) for all operations.

**When to use:** When you need both uniqueness AND insertion order — for example, tracking the order in which unique URLs were visited.

---

### Q11 (Tricky): Can you explain the differences between ceiling/floor and higher/lower in TreeSet?

**Perfect Answer:**

Both pairs retrieve elements relative to a given value, but differ in whether they include the given value itself.

`ceiling(x)` returns the **smallest element ≥ x** (greater than OR equal to). If x exists in the set, x itself is returned. If not, the next element above x is returned.

`floor(x)` returns the **largest element ≤ x** (less than OR equal to). If x exists, x itself is returned. If not, the next element below x is returned.

`higher(x)` returns the **smallest element strictly > x** (greater than, never equal). Even if x exists in the set, higher(x) skips it and returns the next one.

`lower(x)` returns the **largest element strictly < x** (less than, never equal).

```java
TreeSet<Integer> ts = new TreeSet<>(List.of(20, 30, 40, 50, 60, 70, 80));

ts.ceiling(50);   // 50  (50 exists, returns 50)
ts.ceiling(55);   // 60  (55 doesn't exist, next ≥ 55 is 60)
ts.higher(50);    // 60  (strictly > 50, skips 50 itself)
ts.higher(80);    // null (nothing strictly > 80)

ts.floor(50);     // 50  (50 exists, returns 50)
ts.floor(45);     // 40  (45 doesn't exist, next ≤ 45 is 40)
ts.lower(50);     // 40  (strictly < 50, skips 50 itself)
ts.lower(20);     // null (nothing strictly < 20)
```

**Memory trick:** ceiling/floor = **non-strict** (≥ and ≤). higher/lower = **strict** (> and <).

---

### Q12 (Advanced): When would you use HashSet vs TreeSet vs LinkedHashSet?

**Perfect Answer:**

The choice depends on three questions: Do you need sorting? Do you need insertion order? What's your performance requirement?

**Use HashSet when:**
- You just need uniqueness — no ordering required
- Maximum performance matters: O(1) for add, remove, contains
- Memory efficiency matters (no linked list overhead, no tree pointers)
- Example: finding duplicate email IDs in 30,000 records

**Use TreeSet when:**
- You need elements in **sorted order** (ascending by default)
- You need **range operations** (headSet, tailSet, subSet, ceiling, floor)
- Slightly worse performance is acceptable: O(log n) for all operations
- Example: maintaining a sorted leaderboard, finding nearest values to a threshold

**Use LinkedHashSet when:**
- You need **uniqueness + insertion order** preserved
- O(1) performance is needed (faster than TreeSet)
- Slight memory overhead is acceptable (linked list pointers)
- Example: tracking unique pages visited in browser history, in visit order

```java
// Real decision:
// "Find duplicate emails from 30,000 records" → HashSet (O(1) lookup, fast)
// "Display unique cities in alphabetical order" → TreeSet (auto-sorted)
// "Show unique recently viewed products in viewing order" → LinkedHashSet
```

---

### Q13 (Advanced): What is the time complexity of HashSet vs TreeSet vs ArrayList for the contains() operation? Why?

**Perfect Answer:**

`ArrayList.contains()` is **O(n)** — it must iterate through every element one by one, comparing with `equals()`, until it finds a match (or reaches the end). With 1 million elements, it might check all 1 million.

`HashSet.contains()` is **O(1) average** — it computes the element's bucket address directly via the hash function, goes straight to that bucket, and checks (at most) a small chain if there's collision. No traversal of other elements.

`TreeSet.contains()` is **O(log n)** — it traverses the BST from root to leaf, comparing at each level. With 1 million elements, it takes at most log₂(1,000,000) ≈ 20 comparisons.

```java
// Practical impact — checking if "alice@example.com" is in 1,000,000 emails:
ArrayList → check up to 1,000,000 elements     (slow)
HashSet   → check 1 bucket directly            (instantaneous)
TreeSet   → check ~20 tree nodes               (fast)
```

**This is why** for membership checks (duplicate detection, "is element present?" checks), HashSet is the go-to choice in production code.

---

### Q14 (Advanced): What is the difference between `Collection` (interface), `Collections` (class), `Objects` (class), and `Object` (class)?

**Perfect Answer:**

These four names look deceptively similar but are completely different things:

`Object` is the **root class** of all Java classes. Every class implicitly extends Object. It provides `hashCode()`, `equals()`, `toString()`, `getClass()`, and other core methods.

`Collection` (with capital C, no 's') is the **root interface** of the collections hierarchy — parent of List, Set, Queue. Provides the basic contract: `add()`, `remove()`, `size()`, `contains()`, `iterator()`.

`Collections` (with capital C, with 's') is a **utility class** — all static methods, private constructor, cannot instantiate. Provides `sort()`, `max()`, `min()`, `reverse()`, `shuffle()` for collection objects.

`Objects` (lowercase 'o' + 's') is a **utility class** from `java.util` — introduced in Java 7. Provides null-safe operations: `Objects.equals(a, b)`, `Objects.hash(field1, field2)`, `Objects.toString()`, `Objects.requireNonNull()`.

```java
Object o = new Object();             // Root class — instantiable
Collection<Integer> c = new ArrayList<>();  // Interface — reference type only
Collections.sort(list);             // Utility class — static methods only
Objects.hash(id, name);             // Utility class — null-safe helpers
```

**Why `Objects.hash()` in hashCode override?** Because `Objects.hash(id, name)` is null-safe and handles multiple fields elegantly. If name is null, `"name".hashCode()` throws NPE; `Objects.hash(id, name)` handles it safely.

---

## 🎯 How to Explain It

### 90-Second Version (Rapid-fire rounds)

> "These are all collection framework classes. ArrayDeque is a resizable circular array implementing Deque — initial capacity 16, no null, no index, best for Stack or Queue operations without LinkedList overhead.
>
> PriorityQueue is a binary min-heap tree — smallest element always at the root, time complexity O(log n). Internally it's array-stored but conceptually a tree. Use it wherever priority-based processing is needed — OS scheduling, GPS shortest path, data compression.
>
> For Set — three choices. HashSet uses hashing: element's hashCode() is called, a hash function computes a bucket address, element goes there — O(1) average, random order, duplicates blocked, one null allowed. TreeSet is a Red-Black Tree — O(log n), auto-sorted ascending, no duplicates, range operations available. LinkedHashSet is HashSet plus a linked list for insertion-order tracking — O(1), insertion order preserved, no duplicates.
>
> Critical: for custom objects in HashSet, always override both hashCode() AND equals(). Without this, duplicates slip through silently."

---

### 5-Minute Version (Deep-dive rounds)

> "Let me walk through each of these, starting with what problem they solve.
>
> ArrayDeque is the preferred alternative to LinkedList for Stack and Queue operations. It uses a resizable circular array — initial capacity 16, doubles on resize. No null allowed (unlike LinkedList), no index (unlike ArrayList). You can use enhanced for loop and iterator. Descending iterator works (since it implements Deque) but ListIterator doesn't (that's for List-only classes). For Stack: push/pop. For Queue: offer/poll. For Deque: addFirst/addLast/removeFirst/removeLast.
>
> PriorityQueue is a min-heap — the smallest element is always at the root and gets dequeued first. When you insert, the element bubbles up (heapify up) by comparing with its parent. When you poll, the last element moves to root and sinks down (heapify down) by comparing with children. O(log n) for both because the tree height is bounded by log n. Printing with enhanced for loop gives level-by-level output (not sorted). Use a while-poll loop for sorted output. Heterogeneous data causes ClassCastException. 7 constructors — notably one that accepts Comparator for max-heap or custom ordering.
>
> For Set — the core rule is no duplicates. Three implementations with different trade-offs.
>
> HashSet uses hashing internally: hashCode() → hash function → bucket address. Initial capacity 16, load factor 0.75. Rehashing happens at 75% capacity. Bucket collision creates a LinkedList chain; after 8 elements, it becomes a Red-Black Tree. O(1) average for all operations. For custom objects, you MUST override hashCode() and equals() — without it, same-data objects get different bucket addresses and both get inserted, silently breaking the uniqueness guarantee.
>
> TreeSet uses a Red-Black BST — self-balancing binary search tree. Insertion follows BST rules: smaller → left, larger → right. Output is always sorted (ascending by default). LVR traversal (in-order) produces the sorted sequence. O(log n) for all operations. Special methods: ceiling/floor (≥/≤), higher/lower (strict >/< ), headSet/tailSet/subSet for range operations.
>
> LinkedHashSet extends HashSet — same hash table internals but adds a doubly linked list connecting all elements in insertion order. O(1) operations, insertion order preserved, slightly more memory than HashSet."

---

## ⚠️ Edge Cases & Gotchas

| Edge Case | What Happens | Solution |
|---|---|---|
| `ad.add(null)` in ArrayDeque | **NullPointerException** — ArrayDeque never accepts null | Use LinkedList if null elements are needed |
| Traditional for loop on ArrayDeque/PriorityQueue | Compile error — `get(int)` method doesn't exist | Use enhanced for loop or iterator |
| `listIterator()` on ArrayDeque | Compile error — `listIterator()` is a `List` method only | Use `iterator()` or `descendingIterator()` |
| `descendingIterator()` — using `hasPrevious()` | Compile error — descending iterator uses `hasNext()` | Call `hasNext()` and `next()` (descending is its forward direction) |
| `for (Object e : pq) + pq.poll()` inside loop | `ConcurrentModificationException` | Use `while (!pq.isEmpty()) { pq.poll(); }` |
| PriorityQueue with heterogeneous data | `ClassCastException` at runtime | Use generics `PriorityQueue<Integer>` |
| PriorityQueue printed with for-each | Level-by-level output (NOT sorted) | Use while-poll loop for sorted output |
| `pq.poll()` on empty PriorityQueue | Returns **null** (not exception) | Use `pq.poll() != null` check |
| Custom object in HashSet without equals/hashCode override | Two "identical" objects both get inserted (different addresses = different buckets) | Override both `hashCode()` and `equals()` |
| `hs.add(null)` in HashSet | ✅ Allowed — one null per HashSet | Fine, but be careful: null has no hashCode, handled specially |
| `ts.add(null)` in TreeSet | **NullPointerException** — cannot compare null to anything | Filter nulls before adding |
| Heterogeneous data in TreeSet + sort | **ClassCastException** — can't compare String to Integer | Use generics to enforce type safety |
| `ts.ceiling(x)` where x > all elements | Returns **null** (not exception) | Check `!= null` before using result |
| `ts.headSet(50)` — is 50 included? | **Excluded by default** (exclusive) | Use `ts.headSet(50, true)` for inclusive |
| `ts.tailSet(50)` — is 50 included? | **Included by default** (inclusive) | Use `ts.tailSet(50, false)` for exclusive |
| HashSet `contains()` on custom object (no hashCode override) | Returns false even if equal-valued object exists (wrong bucket!) | Always override hashCode AND equals together |

---

## ✅ Quick Revision Card

### The 9 Properties Comparison Table

| Property | ArrayDeque | PriorityQueue | HashSet | TreeSet | LinkedHashSet |
|---|---|---|---|---|---|
| **Initial capacity** | 16 | 11 | 16 buckets | 0 | 16 buckets |
| **Null allowed** | ❌ | ❌ | ✅ (one) | ❌ | ✅ (one) |
| **Duplicates** | ✅ | ✅ | ❌ | ❌ | ❌ |
| **Order** | Insertion | Min-heap | Random | Sorted ↑ | Insertion |
| **Heterogeneous** | ✅ | ❌ (runtime) | ✅ | ❌ (runtime) | ✅ |
| **Index** | ❌ | ❌ | ❌ | ❌ | ❌ |
| **Internal DS** | Circular Array | Binary Min-Heap | Hash Table | Red-Black BST | Hash Table + LinkedList |
| **Time complexity** | O(1) ends | O(log n) | O(1) avg | O(log n) | O(1) avg |
| **Constructors** | 3 | 7 | 5 | 5 | - |

### Code Templates to Memorize

```java
// PriorityQueue — sorted print
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(5); pq.offer(1); pq.offer(3);
while (!pq.isEmpty()) { System.out.print(pq.poll() + " "); }  // 1 3 5

// TreeSet — range methods
TreeSet<Integer> ts = new TreeSet<>(List.of(10, 20, 30, 40, 50));
ts.ceiling(25);          // 30  (≥ 25)
ts.floor(25);            // 20  (≤ 25)
ts.higher(30);           // 40  (strictly > 30)
ts.lower(30);            // 20  (strictly < 30)
ts.headSet(30);          // [10, 20] — 30 excluded
ts.tailSet(30);          // [30, 40, 50] — 30 included
ts.subSet(20, 40);       // [20, 30] — 20 included, 40 excluded
ts.first(); ts.last();   // 10, 50

// HashSet — custom object with equals/hashCode
class Product {
    int id; String name;
    @Override public int hashCode() { return Objects.hash(id); }
    @Override public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Product)) return false;
        return this.id == ((Product) o).id;
    }
}

// HashSet equals/hashCode shortcut in Eclipse:
// Right-click → Source → Generate hashCode() and equals()

// Set selection framework:
// Uniqueness only, fast → HashSet
// Uniqueness + sorted  → TreeSet
// Uniqueness + insertion order → LinkedHashSet
```

### Must-Remember Facts

- `Object` = root class → has `hashCode()` and `equals()`
- `Objects` (with S) = utility class → `Objects.hash()`, `Objects.equals()` (null-safe)
- `Collection` (no S) = root interface → parent of List, Set, Queue
- `Collections` (with S) = utility class → sort, max, min (all static)
- HashSet internally uses **HashMap** (element = key, dummy `PRESENT` = value)
- TreeSet internally uses **TreeMap** (same principle)
- Load factor 0.75 = when 75% filled → rehash (double capacity)
- Bucket collision: ≤8 elements → **LinkedList**; >8 elements → **Red-Black Tree**
- Override `hashCode()` AND `equals()` together — always both, never just one
- `ceiling/floor` = ≥ / ≤ (non-strict). `higher/lower` = > / < (strict)
- `headSet(x)` = elements < x (exclusive). `tailSet(x)` = elements ≥ x (inclusive)
- `heapify up` = after insert. `heapify down` = after poll

---

## 🔗 Connections

### How This Relates to Other Concepts

| Concept | Connection |
|---|---|
| **Polymorphism** | `Collection c = new HashSet<>()` — parent interface reference. `Collections.sort()` works on any `List`. HashSet backing HashMap uses polymorphism internally. |
| **Inheritance** | LinkedHashSet extends HashSet. TreeSet implements NavigableSet → SortedSet → Set → Collection → Iterable. |
| **Method Overloading** | `headSet(x)` vs `headSet(x, boolean)`. `subSet(x, y)` vs `subSet(x, b, y, b)` — same name, different parameters. |
| **Overriding** | `hashCode()` and `equals()` from Object must be overridden for custom objects in Hash-based collections. `toString()` for readable output. |
| **Object class** | `hashCode()` and `equals()` originate from Object — every class inherits them. `Objects` utility class (with 's') provides null-safe wrappers. |
| **Autoboxing** | `pq.offer(100)` — int 100 is autoboxed to Integer. Collections only work with objects, not primitives. |
| **Generics** | `HashSet<Integer>`, `TreeSet<String>` — restricts type at compile time, prevents ClassCastException. |
| **Comparable/Comparator** | TreeSet and PriorityQueue use natural ordering (Comparable) by default. Pass Comparator constructor for custom sorting (e.g., max-heap, reverse alphabetical). |

### Design Patterns Using These Collections

- **Priority Queue Pattern:** OS process scheduling, network packet management, event-driven simulation
- **Template Method:** TreeSet uses a fixed BST insertion algorithm; custom Comparator fills in the "compare" step
- **Facade Pattern:** HashSet provides a simple add/contains/remove facade over complex hashing internals

### System Design Connections

- **LRU Cache:** LinkedHashMap (or LinkedHashSet for keys) — maintains access order
- **Duplicate Detection:** HashSet.contains() — O(1) check for 100M records
- **Leaderboard:** TreeSet with NavigableSet — range queries for "top 10 players above score X"
- **Priority Processing:** PriorityQueue — task scheduling, CPU scheduling, Dijkstra's shortest path
- **Unique ID Generation:** HashSet — ensures no duplicate IDs (like Aadhaar, mobile numbers)
- **Event Deduplication:** HashSet — in stream processing, filter already-seen events

---

## 🎓 Real-World Scenarios

### Scenario 1: Food Delivery App — Order Priority System (PriorityQueue)

**Problem:** A delivery app must process orders by urgency — express orders (30 min) before standard (60 min) and scheduled (90 min). Within the same urgency, shorter estimated time gets priority.

```java
import java.util.*;

class Order implements Comparable<Order> {
    int orderId;
    String type;     // EXPRESS, STANDARD, SCHEDULED
    int estimatedMin;
    int priority;    // lower = higher priority (EXPRESS=1, STANDARD=2, SCHEDULED=3)

    Order(int id, String type, int estimatedMin, int priority) {
        this.orderId = id; this.type = type;
        this.estimatedMin = estimatedMin; this.priority = priority;
    }

    @Override
    public int compareTo(Order other) {
        if (this.priority != other.priority)
            return Integer.compare(this.priority, other.priority);
        return Integer.compare(this.estimatedMin, other.estimatedMin);
    }

    @Override
    public String toString() {
        return "Order#" + orderId + " [" + type + ", " + estimatedMin + "min]";
    }
}

class OrderDispatcher {
    private PriorityQueue<Order> queue = new PriorityQueue<>();

    void addOrder(Order order) {
        queue.offer(order);
        System.out.println("Queued: " + order);
    }

    Order dispatchNext() {
        Order next = queue.poll();
        if (next != null) System.out.println("Dispatching: " + next);
        return next;
    }

    int pendingOrders() { return queue.size(); }
}

// Usage
OrderDispatcher dispatcher = new OrderDispatcher();
dispatcher.addOrder(new Order(1, "STANDARD", 60, 2));
dispatcher.addOrder(new Order(2, "EXPRESS", 20, 1));
dispatcher.addOrder(new Order(3, "SCHEDULED", 90, 3));
dispatcher.addOrder(new Order(4, "EXPRESS", 15, 1));

// Dispatch all in priority order
while (dispatcher.pendingOrders() > 0) {
    dispatcher.dispatchNext();
}
// Output order: Order#4 (EXPRESS 15min), Order#2 (EXPRESS 20min),
//               Order#1 (STANDARD 60min), Order#3 (SCHEDULED 90min)
```

---

### Scenario 2: Social Media — Unique Hashtag Tracker (HashSet + TreeSet)

**Problem:** Track trending hashtags. Need to: (1) quickly detect duplicate hashtags O(1), (2) display trending hashtags alphabetically.

```java
import java.util.*;

class HashtagTracker {
    private HashSet<String> seen = new HashSet<>();      // O(1) duplicate detection
    private TreeSet<String> trending = new TreeSet<>();   // sorted display

    // Returns true if new hashtag, false if duplicate
    boolean trackHashtag(String hashtag) {
        String normalized = hashtag.toLowerCase().trim();
        if (seen.contains(normalized)) {   // O(1) lookup
            System.out.println("Duplicate: #" + normalized);
            return false;
        }
        seen.add(normalized);
        trending.add(normalized);
        return true;
    }

    // Get hashtags alphabetically between two letters (range operation)
    SortedSet<String> getHashtagsInRange(String from, String to) {
        return trending.subSet(from, true, to, true);
    }

    // Get top hashtags (alphabetically first N)
    List<String> getFirstN(int n) {
        List<String> result = new ArrayList<>();
        Iterator<String> it = trending.iterator();
        int count = 0;
        while (it.hasNext() && count < n) {
            result.add(it.next());
            count++;
        }
        return result;
    }

    void printTrending() {
        System.out.println("Trending (sorted): " + trending);
    }
}

// Usage
HashtagTracker tracker = new HashtagTracker();
tracker.trackHashtag("#Java");
tracker.trackHashtag("#Python");
tracker.trackHashtag("#Java");    // duplicate detected
tracker.trackHashtag("#Android");
tracker.trackHashtag("#Kotlin");
tracker.trackHashtag("#Python");  // duplicate detected

tracker.printTrending();
// Trending (sorted): [android, java, kotlin, python]

System.out.println(tracker.getHashtagsInRange("j", "l"));
// [java, kotlin]

System.out.println(tracker.getFirstN(2));
// [android, java]
```

---

### Scenario 3: E-Commerce — Product Deduplication (HashSet with Custom Object + equals/hashCode)

**Problem:** Product catalog receives data from multiple suppliers. Same product may arrive multiple times. Need to store only unique products (by SKU) and display them in insertion order (first-seen wins).

```java
import java.util.*;

class Product {
    String sku;
    String name;
    double price;

    Product(String sku, String name, double price) {
        this.sku = sku; this.name = name; this.price = price;
    }

    @Override
    public int hashCode() {
        return Objects.hash(sku);  // uniqueness based on SKU only
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Product)) return false;
        return Objects.equals(this.sku, ((Product) obj).sku);
    }

    @Override
    public String toString() {
        return "[SKU:" + sku + " | " + name + " | ₹" + price + "]";
    }
}

class ProductCatalog {
    // LinkedHashSet: no duplicates (by SKU) + insertion order (first-seen product kept)
    private LinkedHashSet<Product> catalog = new LinkedHashSet<>();

    void addProduct(Product p) {
        boolean added = catalog.add(p);
        if (!added) {
            System.out.println("Duplicate SKU ignored: " + p.sku);
        }
    }

    void displayCatalog() {
        System.out.println("=== Product Catalog ===");
        for (Product p : catalog) {
            System.out.println(p);
        }
    }

    int totalUniqueProducts() { return catalog.size(); }
}

// Usage
ProductCatalog catalog = new ProductCatalog();
catalog.addProduct(new Product("SKU001", "iPhone 16", 89999));
catalog.addProduct(new Product("SKU002", "Samsung S25", 79999));
catalog.addProduct(new Product("SKU001", "Apple iPhone 16", 91999));  // duplicate SKU!
catalog.addProduct(new Product("SKU003", "OnePlus 13", 69999));
catalog.addProduct(new Product("SKU002", "Samsung Galaxy S25", 82000));  // duplicate SKU!

catalog.displayCatalog();
// [SKU:SKU001 | iPhone 16 | ₹89999.0]    ← first-seen wins
// [SKU:SKU002 | Samsung S25 | ₹79999.0]  ← first-seen wins
// [SKU:SKU003 | OnePlus 13 | ₹69999.0]
System.out.println("Unique products: " + catalog.totalUniqueProducts());  // 3
```

---

## 📝 Practice Questions

1. **Coding:** Create a PriorityQueue of 10 random integers. Print all elements in ascending order using a while-poll loop. Then explain why enhanced for loop doesn't give sorted output.

2. **Design:** Implement a "Top K Frequent Elements" function. Given an array `[1, 1, 1, 2, 2, 3]` and k=2, return `[1, 2]`. Hint: use HashMap for frequency counting + PriorityQueue for top-k selection.

3. **Tricky — TreeSet range operations:** Given a TreeSet of student marks `{35, 42, 55, 60, 72, 85, 91, 98}`, write code to:
   - Find all students who passed (≥ 40)
   - Find all distinction students (≥ 75)
   - Find the highest mark below 90
   - Find the lowest mark above 60

4. **Custom HashSet question:** Why does this print 2 instead of 1? Fix it:
```java
HashSet<String[]> hs = new HashSet<>();
hs.add(new String[]{"Alice", "Engineer"});
hs.add(new String[]{"Alice", "Engineer"});
System.out.println(hs.size());  // prints 2, not 1 — why?
```

5. **System Design:** Design a "Recently Played Songs" feature for a music app that:
   - Keeps only the last 10 unique songs played
   - If a song is played again, move it to the front
   - Supports O(1) lookup to check if a song is already in the recent list

6. **Conceptual:** Explain why `HashSet.contains()` is O(1) while `TreeSet.contains()` is O(log n) and `ArrayList.contains()` is O(n). Draw what happens internally when each is called.

7. **Interview simulation:** "I have a list of 1 million user IDs. I need to find duplicates efficiently. Which collection would you use and why? What is the time and space complexity?"

8. **Edge case:** What happens if you add `null` to: (a) HashSet, (b) TreeSet, (c) ArrayDeque, (d) PriorityQueue? Predict output before running.

---

## 🚀 The Winning Formula

### What Interviewers Are ACTUALLY Testing

1. **Can you explain internals, not just usage?** — Anyone can say "HashSet doesn't allow duplicates." The separator is explaining *how* — hashCode, bucket address, load factor, collision resolution.

2. **Do you know when to use what?** — "Use HashSet when order doesn't matter and O(1) is needed. TreeSet when sorted order or range operations are required. LinkedHashSet when insertion order matters." Scenario-based judgment.

3. **Do you know the equals/hashCode contract?** — This is a trap question in every Java interview above fresher level. Custom objects in HashSet without overriding = silent duplicate insertion. Know it cold.

4. **Can you connect concepts?** — "Why does TreeSet use O(log n)? Because it's a BST — at each comparison, you eliminate half the tree. That's the same reason binary search is O(log n)."

5. **Do you know the PriorityQueue internals?** — Not just "smallest element is at top." Explain heapify up, heapify down, why it's O(log n), why it's stored as array in RAM despite being conceptually a tree.

### The Perfect Closing Statement

> "The beauty of Java's collections framework is that each class is purpose-built. HashSet gives you O(1) operations through hashing — the computer finds the right bucket directly, no searching needed. TreeSet gives you automatic sorting through a self-balancing BST — always O(log n), never O(n). PriorityQueue gives you always-minimum access through a min-heap — crucial for algorithms like Dijkstra's and Huffman coding. When I choose between them in production code, I ask: what's more important — speed, order, or range queries? And the framework has a perfect answer for each."

### Red Flags to Avoid

- ❌ Saying TreeSet's internal data structure is "array" — it's **Red-Black BST**
- ❌ Saying HashSet is always O(1) without mentioning worst case O(n) on full collision
- ❌ Saying PriorityQueue is "sorted" — only the **head is guaranteed to be smallest**; the rest follow heap order, not full sort
- ❌ Not knowing that PriorityQueue prints level-by-level (not sorted) with `println`
- ❌ Forgetting that `descendingIterator()` uses `hasNext()` not `hasPrevious()`
- ❌ Saying you can use a traditional for loop on ArrayDeque or PriorityQueue
- ❌ Not knowing the hashCode/equals contract — "they go together, always"
- ❌ Confusing `ceiling/floor` (≥ and ≤) with `higher/lower` (strictly > and <)
- ❌ Saying `headSet(x)` includes x — it's **exclusive** by default
- ❌ Confusing `Collection` (interface), `Collections` (class), `Object` (class), `Objects` (class)

### The One-Sentence Summary You Can Always Fall Back On

> **"ArrayDeque for stack/queue without null overhead; PriorityQueue for smallest-first priority (min-heap, O(log n)); HashSet for fastest unique storage (O(1), hashing); TreeSet for sorted unique storage (O(log n), BST); LinkedHashSet for unique storage with insertion order (O(1), hash + linked list) — always override hashCode AND equals for custom objects in any hash-based collection."**

---

*This prep kit covers 100% of what was taught in your transcript on ArrayDeque, PriorityQueue, TreeSet, HashSet, and LinkedHashSet — including the heapify visualization, LVR traversal, hashing internals, bucket collision, load factor, and the critical equals/hashCode override. These topics appear in 90%+ of Java technical interviews at all levels.*
