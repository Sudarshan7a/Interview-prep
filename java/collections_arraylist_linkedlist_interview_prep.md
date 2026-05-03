# 🎯 Ultimate Interview Prep Kit: Collections Framework — ArrayList & LinkedList

> **Based on:** Class transcript covering Collections Framework introduction, ArrayList (properties, methods, iteration, boxing/unboxing, generics, utility classes) and LinkedList (doubly linked list internals, methods, Stack/Queue/Deque behavior, add vs offer, peek vs poll).

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

### Collections Framework

**One-liner definition:**
The Collections Framework is a **group of built-in classes and interfaces** introduced in JDK 1.2 (by Josh Bloch) that provide ready-made, efficient data structures — so Java developers never need to build them from scratch.

**Real-world analogy — use this in interviews:**
> Think of a **professional kitchen**. A beginner chef throws rice, spices, chicken, and masala all into ONE container. Chaos. An experienced chef has **separate containers** — one for basmati rice, one for garam masala, one for biryani masala. Access is instant, cooking is fast, nothing gets mixed up. Arrays are that single chaotic container. The Collections Framework gives you the right container for every situation.

**Why it matters:**
- Arrays have three fatal limitations: fixed size, homogeneous only, requires contiguous memory. Collections solve all three.
- Real-world Java development (Spring Boot, JDBC, REST APIs) **never** uses raw arrays for business data — it always uses ArrayList, LinkedList, HashMap, etc.
- Interviewers at Accenture, TCS, Infosys ask about collections in every Java round — it's a guaranteed topic.

---

### ArrayList

**One-liner:** ArrayList is a **resizable array** (dynamic array) that can store heterogeneous objects, preserve insertion order, and allow duplicates and null values.

### LinkedList

**One-liner:** LinkedList is a **doubly linked list** implementation that stores data in dispersed (non-contiguous) memory locations — each node holding data plus addresses of its previous and next nodes. It can behave as a List, Stack, Queue, or Deque.

---

## 🧠 Deep Understanding

### Why Arrays Alone Are NOT Enough

| Problem with Arrays | Solution in Collections |
|---|---|
| Fixed size | ArrayList/LinkedList grow dynamically |
| Only homogeneous data | Collections accept heterogeneous objects |
| Requires contiguous memory | LinkedList uses dispersed memory |
| Tedious sorting/searching logic | Built-in utility methods via `Collections` class |
| Only `clone()` method available | 20+ built-in methods per class |

---

### ArrayList — How It Actually Works

#### Creating an ArrayList

```java
import java.util.ArrayList;  // From java.util, NOT java.lang — must import

ArrayList<Integer> al = new ArrayList<>();          // default capacity = 10
ArrayList<String>  al = new ArrayList<>(100);       // custom initial capacity
ArrayList<String>  al = new ArrayList<>(otherList); // from another collection
```

**Three constructors:**
1. No-arg: creates empty list, initial capacity = 10
2. `(int initialCapacity)`: custom capacity — avoids resizing if you know the size
3. `(Collection c)`: copies another collection's data — uses polymorphism (parent ref = Collection, child objects = ArrayList/LinkedList)

#### Internal Data Structure: Resizable Array (Dynamic Array)

```
Initial State (capacity = 10):
[ 100 | 200 | "Hi" | true | _ | _ | _ | _ | _ | _ ]
  [0]   [1]   [2]   [3]

After inserting 11th element — RESIZING happens:
Step 1: Create new array → capacity = (10 × 1.5) + 1 = 16
Step 2: Cut and paste ALL data to new array
Step 3: Reference points to new array, old array → garbage collected
```

**Resizing formula:** `new capacity = (current capacity × 3/2) + 1`  
Compare: StringBuilder/StringBuffer = `(current × 2) + 2`

**Why resizing is costly:** If 1,000 elements exist, resizing = 1,000 copy operations → **O(n)** time complexity. This is why you should specify initial capacity when you know the data size.

#### ArrayList — Nine Key Properties

| Property | Value |
|---|---|
| **Package** | `java.util` (must import) |
| **Initial capacity** | 10 |
| **Resizable?** | Yes — automatically, formula above |
| **Heterogeneous data?** | Yes — stores Objects, not primitives |
| **Preserves insertion order?** | Yes |
| **Duplicates allowed?** | Yes |
| **Null insertion?** | Yes (multiple nulls allowed) |
| **Internal data structure** | Resizable Array (Dynamic Array) |
| **Constructors** | 3 |

#### ArrayList Hierarchy

```
Iterable (interface)
  └── Collection (interface)
        └── SequencedCollection (interface)
              └── List (interface)
                    └── AbstractList (abstract class)    ← extends
                          └── ArrayList (class)          ← implements List, RandomAccess, Cloneable, Serializable
```

Key: `ArrayList` implements `List` interface. `List` extends `SequencedCollection` → `Collection` → `Iterable`.

#### Iteration — 4 Ways

```java
ArrayList<Integer> al = new ArrayList<>(List.of(1, 2, 3, 4, 5));

// Way 1: Traditional for loop
for (int i = 0; i < al.size(); i++) {       // size() not length
    System.out.println(al.get(i));           // get() not ar[i]
}

// Way 2: Enhanced for loop (for-each) — forward only, no conditions possible
for (Object element : al) {
    System.out.println(element);
}

// Way 3: Iterator — cursor-based, forward only
Iterator cursor = al.iterator();            // returns Iterator interface type
while (cursor.hasNext()) {
    System.out.println(cursor.next());
}

// Way 4: ListIterator — forward AND backward
ListIterator cursor = al.listIterator(al.size()); // start from end for reverse
while (cursor.hasPrevious()) {
    System.out.println(cursor.previous());
}
```

**Key distinctions:**
- `Iterator` → forward only, `hasNext()` + `next()`
- `ListIterator` → forward AND reverse, `hasNext()` + `next()` + `hasPrevious()` + `previous()`
- `ListIterator` has specialized methods not in `Iterator` — so reference type must be `ListIterator`, not `Iterator` (otherwise `hasPrevious()` not found — polymorphism rule)

#### ArrayList — 20 Key Methods

```java
ArrayList<Integer> al = new ArrayList<>(List.of(1, 2, 3, 4));

al.add(5);                    // insert at rear end → O(1) without resize
al.add(1, 99);                // insert at specific index, others shift → O(n)
al.addAll(al2);               // merge two ArrayLists — al2 appended to al
al.addAll(2, al2);            // merge from specific index

al.remove(3);                 // remove by INDEX (returns removed element)
al.remove(Integer.valueOf(3));// remove by OBJECT (returns boolean) — boxing required!

al.set(1, 100);               // replace element at index 1 — O(1)
al.get(2);                    // retrieve element at index — O(1)

al.size();                    // number of elements (NOT capacity)
al.isEmpty();                 // true if no elements
al.contains(100);             // true if element exists
al.indexOf(100);              // first index of element (-1 if not found)
al.lastIndexOf(100);          // last index of element

al.retainAll(al2);            // keep only common elements (intersection)
al.removeAll(al2);            // remove common elements from al

al.subList(1, 4);             // elements from index 1 to 3 (4 is EXCLUSIVE)
al.trimToSize();              // trim unused capacity to match size
al.clear();                   // remove ALL elements
```

**Important:** `al.remove(3)` removes index 3. `al.remove(Integer.valueOf(3))` removes the object 3. This is method overloading — same name, different parameter types.

---

### Boxing, Unboxing, Autoboxing, Auto-Unboxing

This is why Collections only store Objects, not primitives:

```java
// BOXING — manually converting primitive to object (deprecated method)
int a = 100;
Integer i = Integer.valueOf(a);  // CORRECT — static factory method
// Integer i = new Integer(a);   // DEPRECATED since Java 9, removed eventually

// UNBOXING — manually converting object to primitive
Integer i = 1000;               // autoboxing happened here
int a = i.intValue();           // manual unboxing

// AUTOBOXING — Java automatically converts primitive → object when needed
ArrayList<Integer> al = new ArrayList<>();
al.add(100);   // 100 is int → Java automatically converts to Integer object

// AUTO-UNBOXING — Java automatically converts object → primitive when needed
int x = al.get(0);  // Integer object → automatically converted to int
```

**Why is Java NOT 100% pure OOP?**  
Because of primitive data types (`int`, `double`, `char`, etc.). Pure OOP languages require everything to be an object. Java kept primitives for **performance** (no heap allocation, no GC overhead). Wrapper classes (`Integer`, `Double`, `Character`) allow "boxing" primitives into objects when needed. Think of it like 91.6% gold — not pure, but stronger.

---

### Generics — Restricting Type in Collections

```java
// Without generics — accepts anything (ClassCastException risk during sorting)
ArrayList al = new ArrayList();
al.add(100);
al.add("hello");
Collections.sort(al);  // ClassCastException — can't compare int and string!

// With generics — type-safe at compile time
ArrayList<Integer> al = new ArrayList<Integer>();
al.add(100);   // ✅ OK
al.add("hi");  // ❌ Compile error — wrong type

// Multiple generics (accept more than one type)
ArrayList<Integer> al = new ArrayList<>();  // only integers

// For sorting to work, data must be HOMOGENEOUS (same type)
// Comparing apples to oranges is impossible — that's ClassCastException
```

**What is a generic?**
> Generic is a type parameter specified in angle brackets `< >` when creating a collection object. It restricts the collection to only accept objects of that specific type, turning runtime errors (ClassCastException) into compile-time errors.

---

### Utility Classes: `Arrays` and `Collections`

```java
// ARRAYS — utility class for array objects
int[] ar = {23, 45, 12, 67};
Arrays.sort(ar);                    // sort array
System.out.println(Arrays.toString(ar)); // print array data (not hash code)
// Arrays has private constructor → can't instantiate → all methods are static

// COLLECTIONS — utility class for collection framework objects
ArrayList<Integer> al = new ArrayList<>(List.of(9, 3, 100, 1));
Collections.sort(al);              // sort any List
System.out.println(Collections.max(al)); // 100
System.out.println(Collections.min(al)); // 1
// Collections has private constructor → can't instantiate → all methods are static
```

**Two confusable names:**
- `Collection` (no S) = **interface** — parent of all collection classes (ArrayList, LinkedList, etc.)
- `Collections` (with S) = **utility class** — has static helper methods (sort, max, min, etc.)

**Utility class rule:** A class with a **private constructor** and **all-static methods** — cannot be instantiated, exists only to help other classes.

**Why can't you create object of `Collections`?**
Its constructor is marked `private`. The documentation says: "Suppresses the default constructor, ensuring non-instantiability." Every method inside is `static` — you don't need an object to call static methods.

---

### LinkedList — How It Actually Works

#### The Problem Arrays and ArrayList Can't Solve

RAM doesn't always have long stretches of free contiguous memory. At some point, ArrayList hits this limit. LinkedList solves it by using **dispersed memory** — nodes can live anywhere in RAM and are connected via addresses (pointers/references).

#### Doubly Linked List Structure

```
null ← [prev|10|next] ↔ [prev|20|next] ↔ [prev|30|next] ↔ [prev|40|next] → null
         HEAD                                                    TAIL
```

Each **node** has 3 parts:
1. **Data** — the actual value
2. **Next address** — reference to the next node
3. **Previous address** — reference to the previous node (makes it "doubly" linked)

**Head pointer** — always points to the first node  
**Tail pointer** — always points to the last node  
JVM maintains both automatically.

#### LinkedList — Nine Key Properties

| Property | Value |
|---|---|
| **Package** | `java.util` (must import) |
| **Initial capacity** | **0** (no pre-allocation — nodes created on demand) |
| **Resizable?** | N/A — grows node by node |
| **Heterogeneous data?** | Yes |
| **Preserves insertion order?** | Yes |
| **Duplicates allowed?** | Yes |
| **Null insertion?** | Yes |
| **Internal data structure** | **Doubly Linked List** |
| **Constructors** | **2** (not 3 — no capacity constructor) |

Why only 2 constructors (no capacity constructor)? Because LinkedList doesn't pre-allocate memory. It creates nodes one at a time as data is inserted. There's nothing to "reserve" upfront.

#### LinkedList Hierarchy — The Second Leg

```
Iterable → Collection → SequencedCollection → List
                                                └── AbstractSequentialList ← extends
                                                      └── LinkedList ← implements List, Deque, Cloneable, Serializable
                                               Deque ← extends Queue ← extends Collection
```

LinkedList implements **both** `List` and `Deque` — this is the critical difference from ArrayList. The Deque implementation gives LinkedList methods like `addFirst`, `addLast`, `peekFirst`, `pollFirst`, `push`, `pop`, etc.

**Why does this mean LinkedList has MORE methods than ArrayList?**  
More parent interfaces = more inherited method contracts = more methods implemented in LinkedList. Every method from List hierarchy PLUS every method from Deque/Queue hierarchy.

#### LinkedList-Specific Methods (from Deque/Queue)

```java
LinkedList<Integer> ll = new LinkedList<>();
ll.add(10); ll.add(20); ll.add(30); ll.add(40);

// ADDING
ll.addFirst(1);     // insert at head → O(1), just update head pointer + link
ll.addLast(999);    // insert at tail → O(1), just update tail pointer + link
ll.offerFirst(0);   // same as addFirst but returns false on failure (not exception)
ll.offerLast(1000); // same as addLast but returns false on failure (not exception)
ll.push(100);       // same as addFirst — for STACK behavior

// RETRIEVING (without removing)
ll.getFirst();      // → O(1), head pointer already knows first node
ll.getLast();       // → O(1), tail pointer already knows last node
ll.peekFirst();     // like getFirst but returns null (not exception) if empty
ll.peekLast();      // like getLast but returns null if empty
ll.peek();          // returns head element WITHOUT removing it

// REMOVING
ll.removeFirst();   // → O(1), head pointer just moves forward
ll.removeLast();    // → O(1), tail pointer just moves backward
ll.pollFirst();     // like removeFirst but returns null if empty (not exception)
ll.pollLast();      // like removeLast but returns null if empty
ll.poll();          // removes and returns head element
ll.pop();           // removes and returns head element — for STACK behavior
```

#### add() vs offer() — The KEY Difference

| | `add()` (from List) | `offer()` (from Deque/Queue) |
|---|---|---|
| On success | Returns `true` | Returns `true` |
| On failure (no space/RAM) | Throws **exception** | Returns **false** |
| Use when | You want exception on failure | You want graceful false on failure |

```java
// add — from List interface
// If insert fails → IllegalStateException (exception thrown)

// offer — from Deque interface
// If insert fails → returns false (no exception)

// In practice: both insert at the rear. Choice depends on error handling preference.
```

#### peek() vs poll() — The KEY Difference

```java
ll = [1, 2, 3, 4, 5]

System.out.println(ll.peek());  // → 1 (head element SEEN, NOT removed)
System.out.println(ll);         // → [1, 2, 3, 4, 5] — UNCHANGED

System.out.println(ll.poll());  // → 1 (head element FETCHED and REMOVED)
System.out.println(ll);         // → [2, 3, 4, 5] — CHANGED
```

**Analogy from class:** Peek = someone walks past a classroom door, looks in, walks away. Element still there. Poll = that person comes in, takes the student, and leaves. Element gone.

#### Stack, Queue, Deque — Using LinkedList

```java
LinkedList<Integer> ll = new LinkedList<>();

// ===== STACK (LIFO — Last In First Out) =====
ll.push(1);  ll.push(2);  ll.push(3);  // push = addFirst
// Internal: 3 → 2 → 1 (3 is at head)
System.out.println(ll.pop());   // → 3 (last pushed, first popped)
System.out.println(ll.pop());   // → 2
System.out.println(ll.pop());   // → 1
ll.pop();  // → NoSuchElementException: stack is empty!

// ===== QUEUE (FIFO — First In First Out) =====
ll.add(1);  ll.add(2);  ll.add(3);   // add = addLast
System.out.println(ll.poll());  // → 1 (first added, first removed)
System.out.println(ll.poll());  // → 2

// ===== DEQUE (Double Ended Queue) =====
ll.addFirst(0);   // insert at front
ll.addLast(100);  // insert at rear
ll.removeFirst(); // remove from front
ll.removeLast();  // remove from rear
// Full access at both ends — no restrictions
```

---

### Converting Between ArrayList and LinkedList

```java
ArrayList<String> al = new ArrayList<>(List.of("Alice", "Bob", "Charlie"));

// ArrayList → LinkedList
// Uses LinkedList(Collection c) constructor
// al is ArrayList, LinkedList constructor accepts Collection (parent interface)
// al object passed as Collection reference — this is POLYMORPHISM (loose coupling)
LinkedList<String> ll = new LinkedList<>(al);

// LinkedList → ArrayList
// Same trick — ArrayList also has (Collection c) constructor
ArrayList<String> al2 = new ArrayList<>(ll);
```

**Why this works:** Both `ArrayList` and `LinkedList` implement `List` which extends `Collection`. So an `ArrayList` reference can be passed wherever `Collection` is expected — that's loose coupling via polymorphism.

---

### Time Complexity Comparison

| Operation | ArrayList | LinkedList |
|---|---|---|
| Insert at rear (within capacity) | **O(1)** | **O(1)** — tail pointer direct |
| Insert at rear (with resizing) | O(n) — copy all elements | N/A — no resizing |
| Insert at front | O(n) — shift all elements | **O(1)** — update head pointer |
| Insert at random index | O(n) — shift elements | O(n) — travel to node |
| Delete at front | O(n) — shift all elements | **O(1)** — update head pointer |
| Delete at rear | O(1) — last element | **O(1)** — tail pointer direct |
| Access by index | **O(1)** — direct index | O(n) — no real index, must traverse |
| Search by value | O(n) | O(n) |

**The golden rule:**
- **ArrayList** → best for **frequent reads/access by index** (O(1) get)
- **LinkedList** → best for **frequent insertions/deletions at ends** (O(1) addFirst/addLast)

---

## 💬 Interview Q&A

---

### Q1 (Basic): What is the Collections Framework? Why was it introduced?

**Perfect Answer:**

The Collections Framework is a set of built-in classes and interfaces introduced in **JDK 1.2 in 1997**, primarily designed by **Josh Bloch** of the Java team. It provides ready-made implementations of common data structures so Java developers never need to build them from scratch.

It was introduced because Java 1.0 and 1.1 had very limited and inefficient data structures — only arrays, Vector, Hashtable, and Stack. Developers were frustrated because these structures had poor time and space complexity, couldn't hold heterogeneous data easily, and required contiguous memory.

The framework provides classes like `ArrayList`, `LinkedList`, `HashSet`, `HashMap`, and interfaces like `List`, `Set`, `Map`, `Queue`, `Deque`. These are grouped together (hence "collections") and provided as a framework (ready-made, no need to write from scratch).

**Why this impresses interviewers:** Most candidates say "to store data." Mentioning Josh Bloch, JDK 1.2, the specific year, and the problem it solved immediately shows depth.

---

### Q2 (Basic): What are the three pillars of the Collections Framework?

**Perfect Answer:**

The three core pillars are **List**, **Set**, and **Map**.

**List** is an ordered collection that preserves insertion order, allows duplicate elements, and allows null values. ArrayList and LinkedList are the primary List implementations.

**Set** is a collection that enforces uniqueness — no duplicates allowed. HashSet, LinkedHashSet, and TreeSet implement Set. Set is ideal when you need to store unique values or check for membership quickly.

**Map** is a key-value pair structure where each unique key maps to a value. Think of an Aadhaar system — the Aadhaar number is the unique key, and all personal data is the value. HashMap, LinkedHashMap, and TreeMap implement Map.

For interviews at service-based companies (TCS, Infosys, Accenture), List questions dominate. For product companies, Map internals (especially HashMap's internal working) are asked heavily.

---

### Q3 (Basic): What are the differences between an array and an ArrayList?

**Perfect Answer:**

| Aspect | Array | ArrayList |
|---|---|---|
| Size | Fixed — must declare size at creation | Dynamic — grows automatically |
| Data type | Primitive OR objects | Objects only (primitives auto-boxed) |
| Heterogeneous? | No — all elements must be same type | Yes — stores any Object type |
| Memory | Contiguous locations | Contiguous (still array internally, but resizable) |
| Import needed? | No — java.lang implicitly | Yes — java.util |
| Methods | Only `clone()` | 20+ methods (add, remove, sort, etc.) |
| Utility class | `Arrays` | `Collections` |
| Syntax for access | `ar[i]` | `al.get(i)` |
| Length/Size | `ar.length` (variable) | `al.size()` (method) |

Key point to emphasize: ArrayList can store only **objects** — but when you write `al.add(100)`, **autoboxing** silently converts the `int` 100 to an `Integer` object. This is why Java is "not 100% pure OOP" — it keeps primitives for performance, but wraps them when needed.

---

### Q4 (Intermediate): Explain ArrayList's internal resizing mechanism.

**Perfect Answer:**

ArrayList's internal data structure is a **resizable array** (also called dynamic array). When you create `new ArrayList<>()`, a backing array of capacity 10 is created internally.

When you insert an 11th element, the current array is full. At this point:
1. A **brand new backing array** is created with capacity = `(current capacity × 3/2) + 1` = 16
2. All 10 existing elements are **cut and pasted** (not just copied — the old array becomes eligible for garbage collection)
3. The new element is inserted into the new array
4. The `ArrayList` reference now points to the new backing array

This makes resizing an **O(n)** operation. That's why providing an initial capacity is important when you know the data size — `new ArrayList<>(1000)` avoids all resizing for up to 1,000 elements.

**Gotcha interviewers love:** "If resizing creates a new array, why not just expand the old one?" — Because Java arrays are fixed-size objects. Once created, an array's length cannot change. ArrayList simulates dynamic growth by creating a new array and migrating data.

---

### Q5 (Intermediate): What is the difference between `Collection` (interface) and `Collections` (class)?

**Perfect Answer:**

These are two completely different things that look similar and trip up most candidates.

`Collection` (no 'S') is the **root interface** of the collections hierarchy. It's the parent interface for `List`, `Set`, and `Queue`. Every collection class like `ArrayList` and `LinkedList` ultimately implements `Collection`. It defines the basic contract — `add()`, `remove()`, `size()`, `contains()`, `iterator()` — that all collections must honor.

`Collections` (with 'S') is a **utility class** in `java.util` — a helper class for collection objects. It has a `private` constructor (cannot be instantiated) and every method is `static`. It provides operations like `Collections.sort()`, `Collections.max()`, `Collections.min()`, `Collections.reverse()`. Since you can't create an object of it, you call its methods directly via the class name.

```java
// Collection — interface (parent of everything)
Collection<String> c = new ArrayList<>();  // ArrayList implements Collection

// Collections — utility class (helper)
Collections.sort(c);   // static method, no object needed
Collections.max(c);    // find max element
```

Quick memory trick: class has an 's' → `Collections` with 's' is the class.

---

### Q6 (Intermediate): Explain boxing, unboxing, autoboxing, and auto-unboxing.

**Perfect Answer:**

Java maintains primitive types (`int`, `double`, `char`, etc.) for performance, but Collections can only store **objects**. Wrapper classes bridge this gap.

**Boxing** is the manual process of converting a primitive to its wrapper object:
```java
int a = 100;
Integer i = Integer.valueOf(a);  // boxing — manual, preferred static factory method
```

**Unboxing** is the reverse — converting a wrapper object to a primitive:
```java
Integer i = Integer.valueOf(1000);
int a = i.intValue();  // unboxing — extracting primitive from object
```

**Autoboxing** is Java doing boxing automatically:
```java
ArrayList<Integer> al = new ArrayList<>();
al.add(100);  // Java converts 100 (int) → Integer object automatically
```

**Auto-unboxing** is Java doing unboxing automatically:
```java
int x = al.get(0);  // Integer object → int automatically
```

Why it matters for interviews: When you write `al.add(100)`, it looks like you're adding a primitive. Behind the scenes, Java performs autoboxing. When `Collections.sort()` fails with `ClassCastException` on heterogeneous data, it's because the sort internally tries to cast objects — Integer to Boolean to String is impossible.

**Is Java 100% pure OOP?** No — because of primitives. But you can make it fully OOP by using wrapper classes everywhere. Trade-off: more memory and GC overhead.

---

### Q7 (Intermediate): What is an Iterator? How does it differ from ListIterator?

**Perfect Answer:**

Iterator is a cursor-based mechanism to traverse a collection without needing to know its index. It comes from the `Iterable` interface (grandparent of all collections) via the `iterator()` method.

**Iterator:**
- Can traverse **forward only**
- Methods: `hasNext()` (returns boolean — is next element present?) and `next()` (fetches current element, moves cursor forward)
- Can be used with any Collection

**ListIterator:**
- Can traverse **forward AND backward**
- Methods: `hasNext()`, `next()`, `hasPrevious()`, `previous()`
- Can only be used with **List** implementations (ArrayList, LinkedList)
- For reverse traversal, pass `list.size()` as argument to `listIterator()` to start from the end

```java
// Iterator — forward only
Iterator cursor = al.iterator();
while (cursor.hasNext()) {
    System.out.println(cursor.next());
}

// ListIterator — reverse traversal
ListIterator cursor = al.listIterator(al.size());  // MUST be ListIterator type
while (cursor.hasPrevious()) {
    System.out.println(cursor.previous());
}
// If declared as Iterator cursor = al.listIterator() → hasPrevious() not found!
// Because hasPrevious() is a SPECIALIZED method of ListIterator — parent can't see it
```

**Common gotcha:** If you declare `Iterator cursor = al.listIterator(al.size())`, calling `cursor.hasPrevious()` gives an error. `hasPrevious()` is a specialized method of `ListIterator`. The reference type must be `ListIterator` to access it — tight coupling required for specialized methods.

---

### Q8 (Intermediate): What is an ArrayList's internal data structure? What about LinkedList?

**Perfect Answer:**

ArrayList's internal data structure is a **resizable array** (also called dynamic array). Internally, it maintains a regular Java array that grows when capacity is exceeded. The documentation explicitly describes ArrayList as "a resizable-array implementation of the List interface." This means ArrayList still requires **contiguous memory** — the same limitation as arrays, just with automatic resizing.

LinkedList's internal data structure is a **doubly linked list**. Each element is stored in a **node** containing the data, a reference to the next node, and a reference to the previous node. Nodes can exist at **any location in RAM** — no contiguous memory required. The `LinkedList` class in Java always implements a doubly linked list (not single linked list), giving O(1) access at both ends via head and tail pointers.

This fundamental difference drives all the performance trade-offs: ArrayList wins on random access (O(1) via index), LinkedList wins on insertions/deletions at ends (O(1) via pointer update, no shifting needed).

---

### Q9 (Advanced): Explain the LinkedList hierarchy and why it has more methods than ArrayList.

**Perfect Answer:**

LinkedList's hierarchy has two distinct legs, unlike ArrayList which has only one.

The first leg is identical to ArrayList: `LinkedList → AbstractSequentialList → List → SequencedCollection → Collection → Iterable`. This gives LinkedList all the List methods — `add()`, `get()`, `remove()`, `size()`, `set()`, etc.

The second leg is new: `LinkedList also implements → Deque → Queue → Collection`. This second family gives LinkedList additional methods for double-ended queue operations: `addFirst()`, `addLast()`, `peekFirst()`, `peekLast()`, `pollFirst()`, `pollLast()`, `offer()`, `push()`, `pop()`.

Because LinkedList inherits from more parent interfaces, it accumulates more method contracts to implement — giving it significantly more methods than ArrayList.

This dual inheritance is made possible because **multiple interface implementation** is allowed in Java (multiple inheritance via interfaces). `class LinkedList extends AbstractSequentialList implements List, Deque, Cloneable, Serializable` — one class, multiple interface contracts.

---

### Q10 (Advanced): What's the difference between `add()` and `offer()` in LinkedList?

**Perfect Answer:**

Both `add()` and `offer()` insert data at the rear of the LinkedList. The critical difference is their behavior when insertion fails.

`add()` comes from the **List interface**. If insertion fails (extremely rare — e.g., no memory), it throws an **exception** (`IllegalStateException` or `OutOfMemoryError`). It returns `true` on success.

`offer()` comes from the **Queue/Deque interface**. If insertion fails, it returns **false** — no exception thrown. It also returns `true` on success.

```java
LinkedList<Integer> ll = new LinkedList<>();
boolean result1 = ll.add(100);    // returns true; throws exception on failure
boolean result2 = ll.offer(100);  // returns true; returns false on failure (no exception)
```

**When to use which:** In queue-based processing systems where failure is expected and should be handled gracefully (not crashed), use `offer()`. If you want the program to loudly fail on insertion error, use `add()`.

Same logic applies to `offerFirst()`/`offerLast()` vs `addFirst()`/`addLast()`.

---

### Q11 (Advanced): What is the difference between `peek()` and `poll()`?

**Perfect Answer:**

Both `peek()` and `poll()` access the **head element** (first element) of a LinkedList. The difference is what happens to the element after access.

`peek()` **reads** the head element and **returns it without removing it**. The LinkedList is unchanged. If the list is empty, `peek()` returns `null` (no exception).

`poll()` **removes** the head element, returns it, and **modifies the LinkedList**. After `poll()`, the next element becomes the head. If the list is empty, `poll()` returns `null` (no exception).

```java
LinkedList<Integer> ll = new LinkedList<>(List.of(1, 2, 3, 4, 5));

System.out.println(ll.peek());  // → 1
System.out.println(ll);         // → [1, 2, 3, 4, 5] — unchanged

System.out.println(ll.poll());  // → 1
System.out.println(ll);         // → [2, 3, 4, 5] — head removed
```

Same pattern: `peekFirst()`/`peekLast()` vs `pollFirst()`/`pollLast()`.

Compare also: `getFirst()` throws `NoSuchElementException` if empty. `peekFirst()` returns `null` if empty. Choose based on whether empty list is an error or a valid state.

---

### Q12 (Advanced): When would you use ArrayList vs LinkedList? Give a scenario-based answer.

**Perfect Answer:**

The choice comes down to the type of operations your application performs most frequently.

**Use ArrayList when:**
- You need **frequent random access by index** (O(1) with `get(index)`)
- Your data size is predictable and you can set initial capacity
- Most operations are **reads** with rare insertions/deletions
- Example: storing a catalog of 1,000 products, frequently fetching product details by position

**Use LinkedList when:**
- You need **frequent insertions and deletions at either end** (O(1) with `addFirst`, `addLast`, `removeFirst`, `removeLast`)
- Data size is highly unpredictable and fluctuates significantly
- You need to implement Stack, Queue, or Deque behavior
- Example: implementing a task scheduling system where tasks are added to the front (priority) and consumed from the rear, or vice versa

**The nuance interviewers test:** "LinkedList is better for random insertions" — technically, to insert at a random position, LinkedList must traverse to that node (O(n)), same as ArrayList's shifting (O(n)). The difference is **at the ends**: LinkedList is O(1), ArrayList is O(n) (shifting). So be precise: LinkedList is better for insertions **at the ends**, not universally at all positions.

**Memory consideration:** Same 10 elements in LinkedList use **more memory** than ArrayList. Each LinkedList node stores data + previous address + next address. ArrayList stores only the data. If memory is constrained and access patterns favor reading, ArrayList wins.

---

### Q13 (Advanced): Why does `Collections.sort()` throw ClassCastException on heterogeneous data?

**Perfect Answer:**

`Collections.sort()` works by comparing elements to determine their relative order. To compare two objects, they must implement the `Comparable` interface (or you must provide a `Comparator`).

When you have heterogeneous data — say `[100, "hello", true, 3.14]` — internally, `sort()` tries to cast each element to `Comparable`. An `Integer` object cannot be cast to `String`. Java's type system tries `(String) integerObject` internally, which is impossible because `Integer` and `String` are unrelated classes. This throws a **ClassCastException**.

The fix is **generics** — specifying the type parameter ensures only homogeneous data enters the collection:

```java
// Without generics — compiles, fails at runtime
ArrayList al = new ArrayList();
al.add(100);  al.add("hello");
Collections.sort(al);  // ClassCastException at runtime

// With generics — type mismatch caught at compile time
ArrayList<Integer> al = new ArrayList<>();
al.add(100);   // ✅
al.add("hi");  // ❌ Compile error — incompatible types
Collections.sort(al);  // ✅ Works perfectly
```

**Analogy from class:** How would you rank a group of people where one metric is height and another is weight? You can't mix metrics. You need one consistent metric — that's homogeneous data.

---

### Q14 (Tricky): Explain how to demonstrate Stack, Queue, and Deque behavior using LinkedList.

**Perfect Answer:**

LinkedList implements `Deque` (which extends `Queue`), so it naturally supports all three behavioral patterns using different method sets.

**Stack (LIFO — Last In First Out):**
```java
LinkedList<Integer> stack = new LinkedList<>();
stack.push(1);  stack.push(2);  stack.push(3);  // push = addFirst
// Internal: 3 → 2 → 1 (head = 3)
System.out.println(stack.pop());   // → 3 (last in, first out)
System.out.println(stack.pop());   // → 2
// pop() on empty list → NoSuchElementException
```

**Queue (FIFO — First In First Out):**
```java
LinkedList<Integer> queue = new LinkedList<>();
queue.offer(1);  queue.offer(2);  queue.offer(3);  // offer = addLast
System.out.println(queue.poll());  // → 1 (first in, first out)
System.out.println(queue.poll());  // → 2
```

**Deque (Double Ended — access both ends):**
```java
LinkedList<Integer> deque = new LinkedList<>();
deque.addFirst(0);   deque.addLast(100);  // insert at either end
deque.removeFirst(); deque.removeLast();  // remove from either end
```

All three use the **same LinkedList class** — only the methods you call determine the behavior. This demonstrates the power of the Deque interface: one class, multiple data structure personalities.

---

## 🎯 How to Explain It

### 90-Second Version (Rapid-fire rounds)

> "The Collections Framework is a set of built-in classes and interfaces introduced in JDK 1.2 to solve the limitations of arrays — fixed size, homogeneous only, contiguous memory. The three pillars are List, Set, and Map.
>
> ArrayList is a resizable array that stores heterogeneous objects, preserves insertion order, allows duplicates and null. Initial capacity is 10. If exceeded, a new array of 1.5× size is created and data is migrated. Best for random access by index — O(1). Bad for frequent resizing — O(n).
>
> LinkedList is a doubly linked list — each node has data, next address, and previous address. Initial capacity is zero. Uses dispersed memory. Best for insertions/deletions at ends — O(1). Implements both List and Deque — so it can behave as a List, Stack, Queue, or Deque. Bad for random access — O(n) traversal.
>
> The key utility classes: `Collections` (with S) is the static helper for all collections. `Arrays` is the static helper for arrays. Both have private constructors — can't be instantiated."

---

### 5-Minute Version (Deep-dive rounds)

> "Let me explain the Collections Framework from its origin and then deep dive into ArrayList and LinkedList.
>
> Java developers in the 1990s were frustrated with the limited data structures — just arrays, Vector, Hashtable. Josh Bloch solved this in JDK 1.2 with the Collections Framework — a group of built-in classes and interfaces. The three pillars: List (ordered, allows duplicates), Set (unique elements only), Map (key-value pairs).
>
> ArrayList is a resizable array. Initial capacity 10. When you add element 11, Java creates a new backing array at capacity 16 (1.5× + 1), copies all data, and the old array gets garbage collected. This resizing is O(n). If you know your data size, always pass it to the constructor to avoid resizing. ArrayList stores only objects — when you write `al.add(100)`, autoboxing silently converts int to Integer object. That's why `Collections.sort()` fails on heterogeneous data — it can't cast Integer to String. Solution: generics. `ArrayList<Integer>` restricts the type at compile time. Hierarchy: ArrayList → AbstractList → List → SequencedCollection → Collection → Iterable.
>
> LinkedList is different fundamentally. It's a doubly linked list — each node has data, next-node address, and prev-node address. Initial capacity zero — no pre-allocation. Can live in dispersed memory. LinkedList implements both List AND Deque — that second leg from Deque→Queue gives it methods like addFirst, addLast, push, pop, peek, poll, offer.
>
> The critical differences: add vs offer — both insert, but add throws exception on failure, offer returns false. peek vs poll — both read the head element, but peek doesn't remove it, poll removes it. push and pop are for Stack behavior (LIFO). offer and poll are for Queue behavior (FIFO). addFirst/addLast/removeFirst/removeLast are for Deque (both ends).
>
> Time complexity summary: ArrayList — O(1) for random access, O(n) for insert at front. LinkedList — O(1) for insert at ends, O(n) for random access. Choose ArrayList when you read frequently. Choose LinkedList when you insert/delete at ends frequently."

---

### Follow-Up Responses

**"Can ArrayList accept primitives?"**
> "No, directly. But autoboxing converts primitives to wrapper objects transparently. When you write `al.add(5)`, the `int` 5 becomes `Integer` object. That's why Java is not 100% pure OOP — primitives exist for performance, wrapper classes bridge them to the OOP world."

**"What happens if ArrayList is empty and you call get(0)?"**
> "IndexOutOfBoundsException — there's no element at index 0. Unlike LinkedList's peek() which returns null on empty, ArrayList's get() always throws on invalid index."

**"Can I use LinkedList as a Stack instead of Java's Stack class?"**
> "Yes — and it's actually preferred. Java's `Stack` class extends `Vector`, which is synchronized and outdated. `LinkedList` with `push()` and `pop()` provides the same Stack behavior without synchronization overhead."

---

## ⚠️ Edge Cases & Gotchas

| Edge Case | What Happens | Solution |
|---|---|---|
| `al.remove(3)` — intent is remove data 3, not index 3 | Removes element at index 3 (method overloading, int = index) | Use `al.remove(Integer.valueOf(3))` to pass object |
| Calling `Collections.sort()` on heterogeneous ArrayList | ClassCastException — can't compare different types | Use generics `ArrayList<Integer>` to enforce type |
| Declaring `Iterator cursor = al.listIterator(size)` then calling `cursor.hasPrevious()` | Compile error — hasPrevious() not in Iterator | Declare as `ListIterator cursor` — tight coupling needed |
| `ArrayList<Integer> al = new ArrayList<>()` then `al.size()` | Returns 0 (no elements), NOT 10 (initial capacity is not size) | `size()` = number of elements, capacity ≠ size |
| `ll.pop()` on empty LinkedList | NoSuchElementException | Use `ll.poll()` which returns null on empty |
| Passing generic type with interface reference: `List<Integer> ll = new LinkedList<>()` then `ll.offer(5)` | Compile error — offer() is from Deque, not List | Use `LinkedList<Integer>` or `Deque<Integer>` reference type |
| `Collections.sort()` on LinkedList with string data | Works — strings are `Comparable`, sorts alphabetically | But sorts by ASCII (capital letters before lowercase) |
| ArrayList resizing with initial capacity 5, inserting 6 elements | No exception — auto-resizes to 8 (5×1.5+1) | Just more memory/time — no error |
| `al.subList(1, 5)` where al has 4 elements | IndexOutOfBoundsException — 5 exceeds size | Last index in subList is EXCLUSIVE but must be ≤ size |
| `Collections.sort()` on null elements in ArrayList | NullPointerException — null has no compareTo | Filter nulls before sorting |
| `LinkedList.remove()` with no args | Removes head element (same as removeFirst) | Use `remove(index)` or `remove(object)` for specific removal |

---

## ✅ Quick Revision Card

### The 9 Properties to State for ANY Collection

(Memorize this structure — use it for every collection class in interviews)

1. How to create (constructor)
2. Initial capacity
3. Can it grow dynamically?
4. Heterogeneous data?
5. Preserves insertion order?
6. Duplicates allowed?
7. Null insertion?
8. Internal data structure
9. Hierarchy

### ArrayList Quick Facts
- Package: `java.util` — **must import**
- Initial capacity: **10**
- Resizing formula: `new capacity = (old × 3/2) + 1`
- Internal structure: **Resizable Array**
- Constructors: **3** (no-arg, int capacity, Collection)
- Hierarchy: List → SequencedCollection → Collection → Iterable
- Access: O(1) by index | Insert at rear: O(1) without resize | Insert at front: O(n)

### LinkedList Quick Facts
- Package: `java.util` — **must import**
- Initial capacity: **0** (no pre-allocation)
- Internal structure: **Doubly Linked List**
- Constructors: **2** (no-arg, Collection) — no capacity constructor!
- Hierarchy: List + **Deque** → both parent legs
- Head pointer + Tail pointer maintained automatically by JVM
- Insert at ends: O(1) | Access by index: O(n)

### Key Method Distinctions

```
add()    vs offer()  → both insert, but: add = exception on fail; offer = false on fail
peek()   vs poll()   → both read head, but: peek = no remove; poll = removes head
getFirst() vs peekFirst() → both read head, but: getFirst = exception if empty; peekFirst = null
push()   = addFirst  (stack behavior)
pop()    = removeFirst (stack behavior, exception if empty)
```

### Code Templates to Memorize

```java
// ArrayList creation
ArrayList<Type> al = new ArrayList<>();
al.add(element);
al.get(index);
al.size();
Collections.sort(al);

// LinkedList creation + Stack
LinkedList<Type> ll = new LinkedList<>();
ll.push(element);     // addFirst
ll.pop();             // removeFirst — NoSuchElementException if empty
ll.peek();            // read head, don't remove — null if empty

// LinkedList + Queue
ll.offer(element);    // addLast — false if fails (not exception)
ll.poll();            // removeFirst — null if empty

// Iterator
Iterator cursor = al.iterator();
while (cursor.hasNext()) { System.out.println(cursor.next()); }

// ListIterator — reverse
ListIterator cursor = al.listIterator(al.size());
while (cursor.hasPrevious()) { System.out.println(cursor.previous()); }
```

### Must-Remember Facts
- `Collection` (no S) = **interface** (parent)
- `Collections` (with S) = **utility class** (helper, all static methods, private constructor)
- `Arrays` = utility class for arrays (private constructor, all static methods)
- Utility class = private constructor + all static methods = cannot instantiate
- Autoboxing = primitive → object automatically (when adding to collection)
- Auto-unboxing = object → primitive automatically (when assigning from collection)
- Generic = `<Type>` parameter restricts collection to one type at compile time
- `toString()` overridden in `AbstractCollection` class (not ArrayList itself) — that's why printing reference gives data, not hash code

---

## 🔗 Connections

### How This Relates to Other Concepts

| Concept | Connection |
|---|---|
| **Polymorphism** | `Collection c = new ArrayList<>()` is loose coupling. `Collections.sort(list)` works because `List` is the parent — any List can be sorted. Converting ArrayList to LinkedList uses parent-reference (Collection) in constructor. |
| **Interfaces** | `List`, `Collection`, `Iterable`, `Deque`, `Queue` — all interfaces. Collections framework IS interfaces + classes. Without understanding interfaces, you'll never understand why `Iterator cursor = al.iterator()` works. |
| **Method Overloading** | `al.remove(3)` vs `al.remove(Integer.valueOf(3))` — same name, different parameter types. `ll.add(element)` vs `ll.add(index, element)`. Collections are full of overloaded methods. |
| **Exception Handling** | `add()` vs `offer()`: add throws exception, offer returns false. `getFirst()` throws NoSuchElementException, `peekFirst()` returns null. Exception handling knowledge explains these API design choices. |
| **Boxing/Autoboxing** | Collections only store objects — primitives are autoboxed. ClassCastException in sort = boxing gone wrong with heterogeneous types. |
| **Abstract Classes** | `AbstractList`, `AbstractSequentialList` — intermediate abstract classes in the hierarchy. They provide partial implementations so concrete classes (ArrayList, LinkedList) don't have to implement everything from scratch. |
| **Generics** | `<T>` parameters in collection classes. The E in ArrayList's methods (`E get(int index)`) means "element type" — a generic type. |
| **Static keyword** | `Collections` and `Arrays` utility classes: all static methods. You know why — can't create object, must call via class name. |

### Design Patterns Using Collections

- **Strategy Pattern:** `Collections.sort(list, comparator)` — swap sorting strategy at runtime
- **Iterator Pattern:** The `Iterator` interface is literally named after and implements this pattern
- **Factory Pattern:** `Collections.unmodifiableList()`, `Collections.synchronizedList()` — create specialized views
- **Composite Pattern:** A `Collection` of `Collection` objects (List of Lists)

### System Design Connections

- **Caching:** LinkedList + HashMap = LRU Cache (doubly linked list for O(1) add/remove, HashMap for O(1) lookup)
- **Task Scheduling:** LinkedList as priority queue / deque for task management
- **Undo/Redo:** LinkedList as a history stack (push for do, pop for undo)
- **API Rate Limiting:** LinkedList as a sliding window queue for request counting

---

## 🎓 Real-World Scenarios

### Scenario 1: E-Commerce — Product Catalog (ArrayList)

**Problem:** An e-commerce platform stores product catalog. Users frequently browse products by page (index-based access). Product count is predictable (~10,000 items).

```java
import java.util.*;

class Product {
    int id;
    String name;
    double price;

    Product(int id, String name, double price) {
        this.id = id; this.name = name; this.price = price;
    }

    @Override
    public String toString() {
        return "Product{id=" + id + ", name='" + name + "', price=" + price + "}";
    }
}

class ProductCatalog {
    private ArrayList<Product> catalog;

    ProductCatalog(int expectedSize) {
        // Pass expected size — avoids costly resizing
        this.catalog = new ArrayList<>(expectedSize);
    }

    void addProduct(Product p) {
        catalog.add(p);  // O(1) if within capacity
    }

    // Pagination — O(1) access by index
    List<Product> getPage(int pageNumber, int pageSize) {
        int start = (pageNumber - 1) * pageSize;
        int end = Math.min(start + pageSize, catalog.size());

        if (start >= catalog.size()) return new ArrayList<>();
        return catalog.subList(start, end);  // O(1) — direct index access
    }

    // Sort by price — single utility method call
    void sortByPrice() {
        catalog.sort(Comparator.comparingDouble(p -> p.price));
    }

    Product getMostExpensive() {
        return Collections.max(catalog, Comparator.comparingDouble(p -> p.price));
    }

    int totalProducts() { return catalog.size(); }
}

// Usage
ProductCatalog store = new ProductCatalog(10000);
store.addProduct(new Product(1, "Laptop", 75000));
store.addProduct(new Product(2, "Phone", 45000));
store.addProduct(new Product(3, "Headphones", 3500));

store.sortByPrice();
System.out.println("Page 1: " + store.getPage(1, 2));
System.out.println("Most expensive: " + store.getMostExpensive());
```

**Why ArrayList here:** Predictable size (avoid resizing) + frequent page-based index access (O(1)) = perfect ArrayList use case.

---

### Scenario 2: Banking — Transaction History Queue (LinkedList)

**Problem:** A banking system processes transactions in FIFO order. Transactions arrive unpredictably. The system also needs to peek at the next transaction without processing it, and occasionally prioritize urgent transactions at the front.

```java
import java.util.*;

class Transaction {
    String id;
    String type;  // DEBIT, CREDIT
    double amount;
    boolean urgent;

    Transaction(String id, String type, double amount, boolean urgent) {
        this.id = id; this.type = type;
        this.amount = amount; this.urgent = urgent;
    }

    @Override
    public String toString() {
        return "[" + id + "] " + type + " ₹" + amount + (urgent ? " ⚡URGENT" : "");
    }
}

class TransactionQueue {
    private LinkedList<Transaction> queue = new LinkedList<>();

    // Regular transaction — added to rear (FIFO)
    void enqueue(Transaction t) {
        if (t.urgent) {
            queue.offerFirst(t);   // urgent → jump to front
        } else {
            queue.offerLast(t);    // normal → go to rear
        }
        // offer (not add) — returns false on failure, no exception
    }

    // Preview next transaction WITHOUT processing
    Transaction peek() {
        return queue.peekFirst();  // null if empty — no exception
    }

    // Process next transaction
    Transaction process() {
        Transaction t = queue.pollFirst();  // null if empty — no exception
        if (t != null) {
            System.out.println("Processing: " + t);
        }
        return t;
    }

    int pending() { return queue.size(); }
    boolean hasTransactions() { return !queue.isEmpty(); }
}

// Usage
TransactionQueue tq = new TransactionQueue();
tq.enqueue(new Transaction("T001", "DEBIT",  5000, false));
tq.enqueue(new Transaction("T002", "CREDIT", 10000, false));
tq.enqueue(new Transaction("T003", "DEBIT",  500, true));  // urgent — goes to front

System.out.println("Next: " + tq.peek());   // T003 — peeked, not removed
tq.process();  // T003 — urgent processed first
tq.process();  // T001
tq.process();  // T002
```

**Why LinkedList here:** Unpredictable data size (no capacity concerns) + frequent insertions at front for urgent items + queue processing at rear = perfect LinkedList use case. `offer` instead of `add` because failure should be logged gracefully in banking, not crash.

---

### Scenario 3: Browser History — Undo/Redo with LinkedList Stack

**Problem:** Implement browser back/forward navigation using Stack behavior.

```java
import java.util.*;

class BrowserHistory {
    private LinkedList<String> backStack  = new LinkedList<>();
    private LinkedList<String> forwardStack = new LinkedList<>();
    private String current;

    BrowserHistory(String homepage) {
        this.current = homepage;
    }

    void visit(String url) {
        backStack.push(current);    // push current to back history
        forwardStack.clear();       // visiting new page clears forward history
        current = url;
        System.out.println("Visited: " + current);
    }

    String goBack() {
        if (backStack.isEmpty()) {
            System.out.println("No history.");
            return current;
        }
        forwardStack.push(current);  // save current to forward history
        current = backStack.pop();   // retrieve last visited
        System.out.println("Back to: " + current);
        return current;
    }

    String goForward() {
        if (forwardStack.isEmpty()) {
            System.out.println("No forward history.");
            return current;
        }
        backStack.push(current);
        current = forwardStack.pop();
        System.out.println("Forward to: " + current);
        return current;
    }

    void showCurrent() { System.out.println("Current: " + current); }
}

// Usage
BrowserHistory browser = new BrowserHistory("google.com");
browser.visit("youtube.com");
browser.visit("github.com");
browser.visit("stackoverflow.com");

browser.goBack();     // github.com
browser.goBack();     // youtube.com
browser.goForward();  // github.com
browser.visit("reddit.com");  // clears forward history
browser.goForward();  // No forward history (reddit.com replaced it)
```

**Why LinkedList as Stack here:** Browser history is classic Stack (LIFO) behavior. LinkedList's `push()` and `pop()` implement this with O(1) complexity at head. No size limit needed — history grows dynamically.

---

## 📝 Practice Questions

1. **Coding:** Create an `ArrayList<Integer>` with 10 random numbers. Without using `Collections.sort()`, implement bubble sort on the ArrayList using `get()` and `set()` methods. Then sort the same data using `Collections.sort()` and verify both results match.

2. **Design:** Build a simple "recently viewed products" feature using LinkedList that:
   - Keeps only the last 5 viewed products
   - Adding a new product removes the oldest if size exceeds 5
   - Allows peeking at the most recently viewed without removing

3. **Tricky:** What is the output? Explain each line:
```java
ArrayList al = new ArrayList();
al.add(100);
al.add("hello");
al.add(3.14);
System.out.println(al.remove(1));   // Line A
System.out.println(al.remove(Integer.valueOf(100)));  // Line B
System.out.println(al);             // Line C
```

4. **Conceptual:** A company stores employee salaries in an `ArrayList<Integer>`. They want to find the second highest salary. Write two approaches: (a) using `Collections` utility, (b) using a loop. Compare their complexity.

5. **Fill the blank:** Why does this fail? Fix it:
```java
List<Integer> ll = new LinkedList<>(List.of(1, 2, 3));
ll.push(0);        // Error here — why?
ll.peekFirst();    // Error here — why?
```

6. **System Design:** Design an undo-redo system for a text editor using LinkedList. What data structure represents the undo history? What about redo? Write the `type(char)`, `undo()`, and `redo()` methods.

7. **Memory:** Explain why storing 100 integers in a LinkedList uses more memory than the same 100 integers in an ArrayList. Be precise about what each node stores.

8. **Interview Simulation:** An interviewer asks: "I have a list of employee IDs. I need to add new IDs at the front constantly (like a priority queue) and occasionally read from the end. Which collection? Why?" Answer with time complexity justification.

---

## 🚀 The Winning Formula

### What Interviewers Are ACTUALLY Testing

1. **Do you know WHEN to use which collection?** — Not just definitions. Scenario-based thinking: "If the app inserts frequently at the front, ArrayList is O(n) shifts, LinkedList is O(1) pointer update — so LinkedList."

2. **Do you understand the hierarchy?** — "Can you sort any Collection? No — `Collections.sort()` takes a `List`. Why? Because List guarantees ordered access by index. A `Set` has no index — sorting is meaningless."

3. **Do you know utility classes?** — Companies asked placed students "how do you sort an ArrayList without a loop?" — `Collections.sort()`. This IS the interview question.

4. **Do you understand autoboxing?** — "Why do Collections only store objects? What happens when I add an int?" If you say "it stores int directly," you've failed the underlying concept question.

5. **Can you differentiate similar methods?** — `add` vs `offer`, `peek` vs `poll`, `remove` vs `poll`, `get` vs `peek`. These are asked specifically in 1+ year experience interviews.

6. **Do you know internal data structures?** — "What is ArrayList's internal data structure?" Saying "array" is incomplete. Say "resizable array" or "dynamic array." For LinkedList: "doubly linked list."

### The Perfect Closing Statement

> "Collections is one of the areas where Java truly shines over lower-level languages. In C or C++, implementing a doubly linked list requires writing 50+ lines from scratch, managing pointers manually, and handling edge cases yourself. In Java, `new LinkedList<>()` gives you a production-ready doubly linked list with 30+ methods in one line. The framework was designed by Josh Bloch so that developers focus on **what** to build, not **how** to build data structures. That's the philosophy I apply in development — use the right tool from the framework for the job, understand its trade-offs, and write readable, maintainable code."

### Red Flags to Avoid

- ❌ Saying `Collection` and `Collections` are the same — they're not
- ❌ Saying ArrayList can store primitives directly — it can't (autoboxing happens)
- ❌ Saying LinkedList initial capacity is 10 — it's **0**
- ❌ Confusing `size()` with capacity — `size()` = number of elements, not max capacity
- ❌ Saying LinkedList has an actual index in memory — it doesn't; index operations traverse nodes
- ❌ Calling `Arrays.sort()` a method of ArrayList, or `Collections.sort()` a method of arrays
- ❌ Not knowing the difference between `remove(int)` (by index) and `remove(Object)` (by value)
- ❌ Saying `ListIterator` is the same as `Iterator` — different capabilities (forward only vs both directions)
- ❌ Placing general `catch(Exception e)` first in multiple catch blocks — same rule applies everywhere

### The One-Sentence Summary You Can Always Fall Back On

> **"ArrayList = dynamic array, O(1) reads, great for known-size data; LinkedList = doubly linked list, O(1) end insertions, great for dynamic data and Stack/Queue/Deque needs — both store only Objects via autoboxing, and `Collections` utility class is the toolbox that works with both."**

---

*This prep kit covers 100% of ArrayList + LinkedList interview topics — from 3.5 LPA to 10+ LPA rounds. Practice the ATM, browser history, and transaction queue examples until you can write them without looking.*
