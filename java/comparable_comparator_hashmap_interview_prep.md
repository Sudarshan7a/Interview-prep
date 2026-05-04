# 🎯 Ultimate Interview Prep Kit: Comparable, Comparator & HashMap Family

> **Based on:** Class transcript covering Comparable interface (natural sorting, compareTo internals), Comparator interface (custom sorting, separate classes, lambda expressions), HashMap/LinkedHashMap/TreeMap/IdentityHashMap/WeakHashMap, Legacy Classes (Vector, Stack, HashTable, Enumeration), and Collections utility class sorting methods.

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

### Comparable

**One-liner:** Comparable is a **functional interface in `java.lang`** that enables a class to define its own **natural (default) sorting order** by implementing the `compareTo()` method.

**Analogy:**
> Think of **students registered in a university system**. By default, the system sorts them **alphabetically by name** — that's the natural order. Every student object "knows" how to compare itself to another student because the Student class itself defines the comparison rule. That self-awareness is Comparable.

---

### Comparator

**One-liner:** Comparator is a **functional interface in `java.util`** that enables **external, custom sorting** by implementing the `compare()` method in a **separate class** — giving you unlimited sorting strategies.

**Analogy:**
> Now imagine the university wants to sometimes sort students by GPA, sometimes by age, sometimes by city. The Student class can't "know" all these rules — so you hire **separate sorters** (external classes). Each sorter has one job and one sorting strategy. That's Comparator — pluggable, external, unlimited.

---

### HashMap / LinkedHashMap / TreeMap

**One-liner:** Maps store **key-value pairs** where keys are unique. HashMap = fastest (O(1), random order). LinkedHashMap = insertion order preserved. TreeMap = sorted by key (O(log n)).

**Analogy:**
> Think of a **cricket jersey number system**. Jersey #18 → Virat Kohli. Jersey #7 → MS Dhoni. The jersey number is the **key** (unique), the player name is the **value**. You look up a player by their jersey number instantly — that's a HashMap. This is why maps exist: fast lookup by a unique key.

---

## 🧠 Deep Understanding

### How `Collections.sort()` Works Internally

This is the most important foundation. **Wherever sorting happens in Java, `compareTo()` is called internally.**

```
Collections.sort(list)
        ↓
Internally calls compareTo() method
        ↓
On which class? → The class of objects stored in the list
        ↓
compareTo() returns: positive → swap | negative → don't swap | 0 → don't swap
```

**Proof from source code:**

```java
// Integer class source — implements Comparable<Integer>
public final class Integer implements Comparable<Integer> {
    public int compareTo(Integer anotherInteger) {
        return compare(this.value, anotherInteger.value);
    }
    // Internally: if x < y → -1, if x == y → 0, if x > y → 1
}

// String class source — implements Comparable<String>  
public final class String implements Comparable<String> {
    public int compareTo(String anotherString) {
        // lexicographical comparison character by character
    }
}
```

**The swap rule:**
| `compareTo()` returns | Meaning | Action |
|---|---|---|
| **Positive (+)** | Current object > other object | **SWAP** (ascending order) |
| **Negative (-)** | Current object < other object | **Don't swap** |
| **Zero (0)** | Both equal | **Don't swap** |

---

### Comparable — Natural Sorting

**Step-by-step implementation:**

```java
// STEP 1: Create a POJO class
class Employee implements Comparable<Employee> {  // Step 2: implement Comparable<Employee>
    private int employeeId;
    private String employeeName;
    private double salary;

    // Constructor
    public Employee(int employeeId, String employeeName, double salary) {
        this.employeeId = employeeId;
        this.employeeName = employeeName;
        this.salary = salary;
    }

    // Getters
    public int getEmployeeId() { return employeeId; }
    public String getEmployeeName() { return employeeName; }
    public double getSalary() { return salary; }

    // STEP 3: Override compareTo — defines the natural sorting order
    @Override
    public int compareTo(Employee other) {
        // Option A: Sort by ID (ascending)
        Integer id1 = Integer.valueOf(this.employeeId);   // boxing — int → Integer
        Integer id2 = Integer.valueOf(other.employeeId);
        return id1.compareTo(id2);   // calls Integer.compareTo()

        // Option A (optimized — one line):
        // return Integer.valueOf(this.employeeId).compareTo(Integer.valueOf(other.employeeId));

        // Option B: Sort by name (ascending)
        // return this.employeeName.compareTo(other.employeeName);

        // Option C: Sort by salary (descending) — multiply by -1
        // return Double.valueOf(this.salary).compareTo(Double.valueOf(other.salary)) * -1;
    }

    @Override
    public String toString() {
        return "ID: " + employeeId + " - " + employeeName;
    }
}

// USAGE:
ArrayList<Employee> list = new ArrayList<>();
list.add(new Employee(78, "Tim", 45000));
list.add(new Employee(17, "Sundar", 90000));
list.add(new Employee(1, "Me", 16));

Collections.sort(list);  // calls compareTo() internally
System.out.println(list);
// Output: [ID: 1 - Me, ID: 17 - Sundar, ID: 78 - Tim]  ← sorted by ID ascending
```

**How `this` keyword works in compareTo:**
```
When sort compares Employee(78, Tim) with Employee(17, Sundar):
→ currently executing object: Employee(78, Tim) → referenced by `this`
→ parameter object: Employee(17, Sundar) → referenced by `other`
→ compareTo returns: 78 > 17 → positive → SWAP
→ After swap: [Sundar, Tim] → correct ascending order
```

**Descending order — multiply by -1:**
```java
// Ascending gives: negative=no swap, positive=swap
// Descending: reverse the logic → multiply result by -1
return this.employeeName.compareTo(other.employeeName) * -1;
// Now: if name1 > name2 → positive → -1 × positive = negative → NO swap
// = larger name stays in front = descending order ✅
```

---

### Comparator — Custom Sorting

**Why Comparator?**
- Comparable allows only ONE sorting strategy per class.
- You can't make `compareTo()` sort by ID sometimes and by name sometimes.
- Comparator lets you define **unlimited separate sorting strategies** in **separate classes**.

**Step-by-step:**

```java
import java.util.Comparator;

// Separate class for each sorting strategy
class SortById implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        // NO `this` keyword! Both objects are explicit parameters
        return Integer.valueOf(e1.getEmployeeId())
                      .compareTo(Integer.valueOf(e2.getEmployeeId()));
    }
}

class SortByIdDescending implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        return Integer.valueOf(e1.getEmployeeId())
                      .compareTo(Integer.valueOf(e2.getEmployeeId())) * -1;
    }
}

class SortByName implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        return e1.getEmployeeName().compareTo(e2.getEmployeeName());
        // String already has compareTo — no boxing needed
    }
}

class SortBySalary implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        return Double.valueOf(e1.getSalary())
                     .compareTo(Double.valueOf(e2.getSalary()));
    }
}
```

**Two ways to USE Comparator:**

```java
// WAY 1: With TreeSet (constructor accepts Comparator)
SortById sortById = new SortById();
TreeSet<Employee> ts = new TreeSet<>(sortById);  // pass Comparator object to constructor
ts.add(new Employee(78, "Tim", 45000));
ts.add(new Employee(17, "Sundar", 90000));
System.out.println(ts);  // sorted by ID

// WAY 2: With ArrayList (Collections.sort has overloaded version)
ArrayList<Employee> list = new ArrayList<>();
list.add(new Employee(78, "Tim", 45000));
list.add(new Employee(17, "Sundar", 90000));
Collections.sort(list, new SortByName());  // pass list AND comparator object
System.out.println(list);
```

**Giving the user a choice:**
```java
Scanner scan = new Scanner(System.in);
System.out.println("Sort by: 1=ID↑ 2=ID↓ 3=Name↑ 4=Name↓ 5=Salary↑ 6=Salary↓");
int choice = scan.nextInt();

TreeSet<Employee> ts;
if (choice == 1) ts = new TreeSet<>(new SortById());
else if (choice == 2) ts = new TreeSet<>(new SortByIdDescending());
else if (choice == 3) ts = new TreeSet<>(new SortByName());
else if (choice == 4) ts = new TreeSet<>(new SortByNameDescending());
else if (choice == 5) ts = new TreeSet<>(new SortBySalary());
else if (choice == 6) ts = new TreeSet<>(new SortBySalaryDescending());
else { System.out.println("Invalid"); return; }

// OR with switch:
ts = switch (choice) {
    case 1 -> new TreeSet<>(new SortById());
    case 2 -> new TreeSet<>(new SortByIdDescending());
    // ...
    default -> throw new IllegalArgumentException("Invalid choice");
};
```

---

### Lambda Expression with Comparator

Since Comparator is a **functional interface** (exactly one abstract method: `compare()`), it can be implemented using a lambda expression — eliminating the need for a separate class entirely.

**Four ways to implement a Functional Interface:**
1. Regular class (implements the interface)
2. Inner class (class inside another class)
3. Anonymous inner class (no class name)
4. **Lambda expression** (shortest, most modern)

```java
// Traditional: separate class
Collections.sort(list, new SortByName());

// Lambda equivalent:
Collections.sort(list, (e1, e2) -> e1.getEmployeeName().compareTo(e2.getEmployeeName()));

// Lambda with TreeSet:
TreeSet<Employee> ts = new TreeSet<>(
    (e1, e2) -> e1.getEmployeeName().compareTo(e2.getEmployeeName())
);

// Lambda descending:
TreeSet<Employee> tsDesc = new TreeSet<>(
    (e1, e2) -> e1.getEmployeeName().compareTo(e2.getEmployeeName()) * -1
);

// Lambda for salary (need boxing):
TreeSet<Employee> bySalary = new TreeSet<>(
    (e1, e2) -> Double.valueOf(e1.getSalary()).compareTo(Double.valueOf(e2.getSalary()))
);

// Modern Java shortcut using Comparator.comparing():
list.sort(Comparator.comparing(Employee::getEmployeeName));
list.sort(Comparator.comparing(Employee::getEmployeeName).reversed()); // descending
```

**Lambda syntax breakdown:**
```
(e1, e2) -> e1.getName().compareTo(e2.getName())
  ↑            ↑
parameters    body (single expression — return is implicit)

// Same as writing:
class AnonymousComparator implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return e1.getName().compareTo(e2.getName());
    }
}
```

---

### Comparable vs Comparator — The Comparison Table

| Aspect | Comparable | Comparator |
|---|---|---|
| **Package** | `java.lang` (no import needed) | `java.util` (import required) |
| **Method** | `compareTo(Object o)` | `compare(Object o1, Object o2)` |
| **Parameters** | **1** — second object only; first = `this` | **2** — both objects explicitly |
| **Implemented by** | The **class itself** (Employee implements Comparable) | A **separate class** (SortById implements Comparator) |
| **Defines** | **Natural/default** sorting order | **Custom/external** sorting |
| **Sorting strategies per class** | **Only ONE** — can't change without modifying class | **Unlimited** — create as many Comparator classes as needed |
| **Modify original class?** | Yes — must modify Employee class | No — create separate classes |
| **Used with** | `Collections.sort(list)` — no 2nd arg | `Collections.sort(list, comparatorObj)` or TreeSet constructor |
| **Functional interface?** | Yes | Yes |

---

### HashMap Family — Deep Dive

#### HashMap

```java
HashMap<Integer, String> hm = new HashMap<>();
hm.put(18, "Virat Kohli");
hm.put(7, "MS Dhoni");
hm.put(1, "KL Rahul");
hm.put(17, "ABD");
hm.put(45, "Rohit Sharma");

// KEY METHODS:
hm.get(18);              // → "Virat Kohli" (value for key 18)
hm.containsKey(7);       // → true
hm.containsValue("ABD"); // → true
hm.size();               // → 5
hm.isEmpty();            // → false
hm.remove(17);           // removes key 17 and its value
hm.clear();              // removes all entries

// ACCESSING ENTRIES:
Set<Map.Entry<Integer, String>> entries = hm.entrySet();  // all entries
Set<Integer> keys = hm.keySet();                          // all keys (Set — unique)
Collection<String> vals = hm.values();                    // all values (Collection — duplicates allowed)

// ITERATING:
for (Map.Entry<Integer, String> entry : hm.entrySet()) {
    System.out.println(entry.getKey() + " → " + entry.getValue());
}

// Or with iterator:
Iterator<Integer> it = hm.keySet().iterator();
while (it.hasNext()) {
    int key = it.next();
    System.out.println(key + " → " + hm.get(key));
}
```

**Internal working:** Same as HashSet — hash function computes bucket address from key's `hashCode()`. Key-value pair stored at that bucket. Load factor 0.75, initial capacity 16.

**Key fact:** `entrySet()` returns `Set` (keys are unique). `values()` returns `Collection` (values can duplicate).

#### LinkedHashMap
- Exactly like HashMap but preserves **insertion order**
- Internally: hash table + doubly linked list (same as LinkedHashSet)
- Use when: you need fast O(1) key lookup AND insertion order preservation

```java
LinkedHashMap<Integer, String> lhm = new LinkedHashMap<>();
lhm.put(18, "Virat"); lhm.put(7, "Dhoni"); lhm.put(1, "KL");
// Iteration order = insertion order: 18, 7, 1
```

#### TreeMap
- Sorted by **key** (ascending by default — natural order)
- Internal structure: **Red-Black BST** (same as TreeSet)
- O(log n) for all operations
- Keys must implement Comparable OR you must provide Comparator
- Heterogeneous keys → ClassCastException (comparison required)

```java
TreeMap<Integer, String> tm = new TreeMap<>();
tm.put(18, "Virat"); tm.put(7, "Dhoni"); tm.put(45, "Rohit");
System.out.println(tm);  // {7=Dhoni, 18=Virat, 45=Rohit} — sorted by key!

// Duplicate keys: NOT allowed
// Duplicate values: ALLOWED
tm.put(7, "New Dhoni");  // replaces old value for key 7 — key not duplicated!

// Heterogeneous keys → runtime ClassCastException
tm.put("text", "value");  // compiles, fails at runtime if mixed with integers
```

**TreeMap hierarchy:** `TreeMap → AbstractMap → NavigableMap → SortedMap → SequencedMap → Map`

#### IdentityHashMap

```java
// Problem: HashMap uses equals() to compare keys
// Both "tap" references point to constant pool → same hashCode → same bucket → only ONE stored
String s1 = "tap"; String s2 = "tap";
HashMap<String, Integer> hm = new HashMap<>();
hm.put(s1, 100); hm.put(s2, 200);
System.out.println(hm.size());  // 1 — duplicate key detected via equals()

// Solution: IdentityHashMap uses == (reference comparison) instead of equals()
String s3 = new String("tap");  // NEW object in heap
String s4 = new String("tap");  // DIFFERENT new object in heap
IdentityHashMap<String, Integer> ihm = new IdentityHashMap<>();
ihm.put(s3, 100); ihm.put(s4, 200);
System.out.println(ihm.size());  // 2 — different references → different keys!

// But with constant pool (no new keyword): still 1
String s5 = "tap"; String s6 = "tap";
ihm.put(s5, 100); ihm.put(s6, 200);
System.out.println(ihm.size());  // 1 — same reference from constant pool
```

**When to use:** When object identity (memory address) matters more than object equality — e.g., tracking which specific object instances have been processed.

#### WeakHashMap

```java
WeakHashMap<String, Integer> whm = new WeakHashMap<>();
String s1 = new String("tap");
String s2 = new String("tap two");
whm.put(s1, 100);
whm.put(s2, 200);

// Make s1 a weak reference (no strong references to its object)
s1 = null;  // object now has ZERO references → garbage collectible

// Force garbage collector to run
System.gc();  // without this, GC may not collect immediately

System.out.println(whm);  // {tap two=200} — "tap" was removed by GC!
// WeakHashMap does NOT hold strong references to keys → eligible for GC
```

**Key concept:**
- **Weak reference:** An object with zero or only weak references → eligible for GC
- **Strong reference:** An object with at least one normal variable pointing to it
- WeakHashMap allows GC to collect its key objects → entries are automatically removed
- Use when: you want a cache that automatically cleans up when keys are no longer used elsewhere (e.g., cache keyed by objects that may be deleted)

**Without `System.gc()`:** GC doesn't immediately collect — the output may still show both entries until GC runs naturally.

---

### Map Hierarchy

```
Map (interface) ← NOT related to Collection!
  └── AbstractMap (abstract class)
        ├── HashMap → LinkedHashMap (child of HashMap)
        ├── IdentityHashMap
        ├── WeakHashMap
        └── TreeMap (implements NavigableMap → SortedMap → Map)

// TreeMap hierarchy:
TreeMap → AbstractMap → NavigableMap → SortedMap → SequencedMap → Map
```

**Key insight:** Map is NOT a Collection. Map is a separate root. That's why:
- Map has no `add()` — it uses `put()`
- Map is NOT part of the Iterable hierarchy
- But it IS part of the "Collections Framework" (concept, not interface)

---

### Legacy Classes — Why They're Slow

**The four legacy classes (pre-JDK 1.2):**

| Legacy Class | Modern Replacement | Why Replaced |
|---|---|---|
| `Vector` | `ArrayList` | All methods are **synchronized** → slow |
| `Stack` | `LinkedList` or `ArrayDeque` | Extends Vector → also synchronized → slow |
| `Hashtable` | `HashMap` | All methods synchronized → slow |
| `Enumeration` | `Iterator` | Limited functionality (only hasMoreElements/nextElement) |

**Why synchronized = slow:**
```java
// Vector's add method (synchronized):
public synchronized boolean add(E e) { ... }
// synchronized = only ONE thread can access at a time
// Like a classroom with only 1 student allowed at a time — extremely slow!

// ArrayList's add method (NOT synchronized):
public boolean add(E e) { ... }
// Multiple threads can access simultaneously → much faster
```

**Stack extends Vector:**
```java
public class Stack<E> extends Vector<E> { ... }
// Since Vector is synchronized → Stack is also synchronized → slow
// Use ArrayDeque or LinkedList instead for Stack behavior
```

---

### Taking User Input for Custom Objects (Complete Pattern)

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("Enter number of employees: ");
        int n = sc.nextInt();
        sc.nextLine();  // consume leftover newline (buffer fix!)
        
        System.out.println("Format: id,name,salary");
        
        // Get user's sorting choice
        System.out.println("Sort by: 1=ID↑ 2=ID↓ 3=Name↑ 4=Name↓ 5=Salary↑ 6=Salary↓");
        int choice = sc.nextInt();
        sc.nextLine();  // buffer fix
        
        // Create TreeSet with appropriate Comparator
        TreeSet<Employee> ts;
        if (choice == 1)      ts = new TreeSet<>(new SortById());
        else if (choice == 2) ts = new TreeSet<>(new SortByIdDescending());
        else if (choice == 3) ts = new TreeSet<>(new SortByName());
        else if (choice == 4) ts = new TreeSet<>(new SortByNameDescending());
        else if (choice == 5) ts = new TreeSet<>(new SortBySalary());
        else                  ts = new TreeSet<>(new SortBySalaryDescending());
        
        // Take employee data
        for (int i = 0; i < n; i++) {
            System.out.print("Enter details of employee " + (i + 1) + ": ");
            String details = sc.nextLine();
            String[] arr = details.split(",");
            
            int id = Integer.parseInt(arr[0].trim());
            String name = arr[1].trim();
            double salary = Double.parseDouble(arr[2].trim());
            
            ts.add(new Employee(id, name, salary));
        }
        
        System.out.println("Sorted employees: " + ts);
        sc.close();
    }
}
```

---

## 💬 Interview Q&A

---

### Q1 (Basic): What is the Comparable interface? How does sorting work internally in Java?

**Perfect Answer:**

Comparable is a functional interface in `java.lang` (no import needed) that enables a class to define its own **natural sorting order** via the `compareTo()` method. Whenever `Collections.sort()` or TreeSet's natural ordering is invoked, Java internally calls `compareTo()` on the objects being sorted.

The rule is simple: `compareTo()` returns a positive integer when the current object is greater than the compared object (→ swap in ascending order), a negative integer when smaller (→ don't swap), or zero when equal (→ don't swap).

All built-in classes like `Integer`, `String`, `Double` already implement Comparable. For custom classes like Employee, you must implement it yourself and override `compareTo()` with your specific comparison logic.

```java
class Employee implements Comparable<Employee> {
    private int id;
    private String name;
    
    Employee(int id, String name) { this.id = id; this.name = name; }
    
    @Override
    public int compareTo(Employee other) {
        // Sort by ID ascending — delegating to Integer's compareTo
        return Integer.valueOf(this.id).compareTo(Integer.valueOf(other.id));
    }
    
    @Override
    public String toString() { return "ID:" + id + "-" + name; }
}

ArrayList<Employee> list = new ArrayList<>(
    List.of(new Employee(78,"Tim"), new Employee(17,"Sundar"), new Employee(1,"Me"))
);
Collections.sort(list);
System.out.println(list); // [ID:1-Me, ID:17-Sundar, ID:78-Tim]
```

**What NOT to say:** Don't say "compareTo is from the Object class." It's from the `Comparable` interface. Object has `equals()` and `hashCode()`, not `compareTo()`.

---

### Q2 (Intermediate): What is the difference between Comparable and Comparator?

**Perfect Answer:**

Both are functional interfaces used for sorting in Java, but they serve different purposes.

`Comparable` (in `java.lang`) defines the **natural/default sorting order** and is implemented **directly on the class** being sorted. Employee itself implements Comparable. This gives one fixed sorting strategy per class — you can't change it without modifying Employee. The `compareTo()` method takes **one parameter** (the second object); the first object is accessed via `this`.

`Comparator` (in `java.util`) defines **custom/external sorting** and is implemented in **separate classes** — never on the class being sorted. You can have SortById, SortByName, SortBySalary — unlimited strategies. The `compare()` method takes **two explicit parameters** — both objects are given, no `this` keyword needed.

```java
// Comparable — on the class itself
class Employee implements Comparable<Employee> {
    public int compareTo(Employee other) { return this.id - other.id; }
}

// Comparator — separate classes
class SortByName implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return e1.getName().compareTo(e2.getName()); // no 'this'
    }
}
// Usage with TreeSet:
TreeSet<Employee> ts = new TreeSet<>(new SortByName());
// Usage with ArrayList:
Collections.sort(list, new SortByName());
```

**Key interview differentiators:**
- Package: `lang` vs `util`
- Method: `compareTo(Object)` vs `compare(Object, Object)`
- Params: 1 vs 2
- Location: same class vs separate class
- Strategies: one vs unlimited

---

### Q3 (Basic): Why can't you implement Comparator on the Employee class itself?

**Perfect Answer:**

You technically can, but it completely defeats the purpose of Comparator. Comparator exists to give **external, pluggable sorting strategies** — different classes implementing different logics.

If you implement Comparator on Employee itself, you're still stuck with one sorting strategy on one class. You'd have to modify Employee every time you need a new sort order. That violates the Open/Closed Principle — you'd be modifying the class instead of extending behavior externally.

The real power of Comparator is: **one Employee class, six sorting strategies, zero modification to Employee**. SortById, SortByIdDescending, SortByName, SortByNameDescending, SortBySalary, SortBySalaryDescending — all separate, all reusable, all pluggable into TreeSet constructor or `Collections.sort()`.

```java
// Wrong approach (defeats the purpose):
class Employee implements Comparator<Employee> { ... }  // now you're stuck with one logic

// Right approach:
class SortById implements Comparator<Employee> { ... }
class SortByName implements Comparator<Employee> { ... }
// Employee is unchanged — follows Open/Closed Principle
```

---

### Q4 (Intermediate): How do you sort in descending order using Comparable and Comparator?

**Perfect Answer:**

The key insight is that sort swaps when `compareTo()` or `compare()` returns a **positive value**. Ascending order: positive when current > other. Descending order: we need the opposite — swap when current < other — which is achieved by **multiplying the result by -1**.

```java
// Comparable — descending by name
@Override
public int compareTo(Employee other) {
    return this.employeeName.compareTo(other.employeeName) * -1;
    // Ascending would give: -1 for "Adam < Tim"
    // × -1 gives: +1 → swap → Tim comes first → descending!
}

// Comparator — descending by ID
class SortByIdDescending implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return Integer.valueOf(e1.getEmployeeId())
                      .compareTo(Integer.valueOf(e2.getEmployeeId())) * -1;
    }
}

// Lambda — descending by salary
Collections.sort(list, (e1, e2) ->
    Double.valueOf(e1.getSalary()).compareTo(Double.valueOf(e2.getSalary())) * -1
);

// Modern — using Comparator.reversed()
list.sort(Comparator.comparing(Employee::getSalary).reversed());
```

**Why not just swap e1 and e2?** Swapping parameters (`compare(e2, e1)` instead of `compare(e1, e2)`) also achieves descending order and is a valid alternative. But multiplying by -1 makes the intent explicit and readable.

---

### Q5 (Advanced): Explain the difference between `compare()` inside `compare()`. Why do we call `compareTo()` inside `compare()`?

**Perfect Answer:**

This is a common source of confusion. When implementing a Comparator's `compare()` method, we often call `compareTo()` inside it. These are two completely different method calls.

`compare()` is the Comparator's method — it determines how two objects should be ordered relative to each other.

`compareTo()` is the wrapper class method (Integer.compareTo, String.compareTo) — it performs the actual numeric or lexicographic comparison on primitive values that have been boxed into objects.

```java
class SortById implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {    // ← this is Comparator's compare
        Integer id1 = Integer.valueOf(e1.getEmployeeId());
        Integer id2 = Integer.valueOf(e2.getEmployeeId());
        return id1.compareTo(id2);  // ← this is Integer class's compareTo
    }
}
```

**Why call `compareTo()` inside `compare()`?**
Because Integer already has the comparison logic built in. Instead of writing manual if-else logic (if id1 > id2 return 1; else if id1 < id2 return -1; else return 0), we reuse Integer's built-in `compareTo()` which does the same thing. It's about code reuse, not a design requirement.

**What NOT to say:** Don't confuse Comparator's `compare()` with Comparable's `compareTo()`. They're on different interfaces, in different packages, with different signatures.

---

### Q6 (Advanced): What is a Comparator lambda expression? How does it work?

**Perfect Answer:**

Since Comparator is a **functional interface** (exactly one abstract method: `compare()`), it qualifies for lambda expression implementation. This lets you create a Comparator inline without writing a separate class.

A lambda expression has this structure: `(parameters) -> body`. For Comparator: `(e1, e2) -> returnExpression`.

```java
// Traditional class — 5 lines:
class SortByName implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return e1.getEmployeeName().compareTo(e2.getEmployeeName());
    }
}
TreeSet<Employee> ts = new TreeSet<>(new SortByName());

// Lambda equivalent — 1 line:
TreeSet<Employee> ts = new TreeSet<>(
    (e1, e2) -> e1.getEmployeeName().compareTo(e2.getEmployeeName())
);

// With ArrayList:
Collections.sort(list, (e1, e2) -> e1.getEmployeeName().compareTo(e2.getEmployeeName()));

// Descending — multiply by -1:
TreeSet<Employee> desc = new TreeSet<>(
    (e1, e2) -> e1.getEmployeeName().compareTo(e2.getEmployeeName()) * -1
);
```

**Why is return statement not needed in lambda?** If the lambda body is a single expression, the return is implicit. The expression's value is automatically returned.

**Why are type declarations for e1, e2 not needed?** The compiler infers the type from the generic `<Employee>` context. You can optionally write `(Employee e1, Employee e2)` but it's redundant.

**Interviewers love this follow-up:** "What are the four ways to implement a functional interface?" — Regular class, inner class, anonymous inner class, lambda expression.

---

### Q7 (Basic): What is a HashMap? How is it different from a HashSet?

**Perfect Answer:**

HashMap is a key-value pair data structure where each unique key maps to a value. It's part of the Map family, which is separate from the Collection hierarchy — Map does NOT implement Collection.

HashSet, on the other hand, stores only unique values (no pairs) and implements the Set interface which extends Collection.

The deep connection: **HashSet internally uses HashMap!** When you `add("hello")` to a HashSet, it internally does `map.put("hello", PRESENT)` where PRESENT is a dummy static Object. That's why HashSet inherits all of HashMap's behavior — O(1) operations, load factor, bucket structure, collision handling. The only difference is that HashSet exposes only the key portion.

```java
HashMap<Integer, String> hm = new HashMap<>();
hm.put(18, "Virat");     // key=18, value="Virat"
hm.put(7, "Dhoni");

System.out.println(hm.get(18));           // "Virat"
System.out.println(hm.containsKey(7));    // true
System.out.println(hm.containsValue("Dhoni")); // true

// Keys are unique, values can duplicate
hm.put(9, "Rohit");
hm.put(45, "Rohit");  // different key, same value — ALLOWED
hm.put(18, "KL");     // same key 18 — REPLACES "Virat" with "KL"
```

---

### Q8 (Intermediate): What are entrySet(), keySet(), and values() in HashMap?

**Perfect Answer:**

These three methods give you different views of a HashMap's contents for iteration.

`entrySet()` returns a `Set<Map.Entry<K,V>>` — all key-value pairs as Entry objects. Each Entry has `getKey()` and `getValue()`. This is the most complete view, used when you need both key and value simultaneously.

`keySet()` returns a `Set<K>` — just the keys. Keys are always a Set because they're unique. Use this when you only need keys, or when you'll use `get(key)` to fetch values.

`values()` returns a `Collection<V>` — just the values. Values are a Collection (not Set) because **duplicate values are allowed**. A HashMap can have key=7 → "Dhoni" and key=45 → "Dhoni" simultaneously.

```java
HashMap<Integer, String> hm = new HashMap<>(
    Map.of(18, "Virat", 7, "Dhoni", 1, "KL", 45, "Rohit")
);

// entrySet — iterate key-value pairs
for (Map.Entry<Integer, String> entry : hm.entrySet()) {
    System.out.println(entry.getKey() + " → " + entry.getValue());
}

// keySet — iterate keys
for (Integer key : hm.keySet()) {
    System.out.println(key + " → " + hm.get(key));
}

// values — iterate values only
for (String value : hm.values()) {
    System.out.println(value);
}
```

**Why `values()` returns Collection not Set?** Because values can repeat (two players could theoretically have same name), so forcing Set uniqueness on values would be wrong.

---

### Q9 (Advanced): How does HashMap handle duplicate keys? What happens when you put() with an existing key?

**Perfect Answer:**

HashMap does NOT allow duplicate keys. If you `put()` with an existing key, the old value is **silently replaced** (overwritten) by the new value. The key count does NOT increase.

```java
HashMap<Integer, String> hm = new HashMap<>();
hm.put(18, "Virat Kohli");
System.out.println(hm.get(18));  // "Virat Kohli"

hm.put(18, "Virat");  // same key 18 — replaces value
System.out.println(hm.get(18));  // "Virat"  ← old value gone
System.out.println(hm.size());   // 1  ← not 2!
```

**The full duplicate detection chain:**
1. `put(18, "Virat")` — compute hashCode of key 18 → find bucket
2. Bucket already has key 18? → call `equals(18, 18)` → true → **replace value**
3. Bucket has different key? → **collision** → add to chain

**Duplicate values ARE allowed:**
```java
hm.put(18, "Kohli");
hm.put(45, "Kohli");  // different keys, same value → BOTH stored
System.out.println(hm.size());  // 2 — both entries exist
```

---

### Q10 (Advanced): What is IdentityHashMap? How is it different from HashMap?

**Perfect Answer:**

IdentityHashMap compares keys using **reference equality** (`==`) instead of **value equality** (`equals()`). In a regular HashMap, duplicate detection uses `hashCode()` + `equals()` — so two String objects with the same value are considered the same key. In IdentityHashMap, only two references pointing to the exact same object in memory are considered the same key.

```java
// HashMap — value comparison (equals)
String s1 = new String("tap");
String s2 = new String("tap");   // different object, same value
HashMap<String, Integer> hm = new HashMap<>();
hm.put(s1, 1); hm.put(s2, 2);
System.out.println(hm.size());   // 1 — s1.equals(s2) = true → duplicate!

// IdentityHashMap — reference comparison (==)
IdentityHashMap<String, Integer> ihm = new IdentityHashMap<>();
ihm.put(s1, 1); ihm.put(s2, 2);
System.out.println(ihm.size());  // 2 — s1 != s2 (different refs) → not duplicate!

// BUT — with String constant pool (no new keyword):
String s3 = "tap"; String s4 = "tap";   // both reference same constant pool object
ihm.put(s3, 1); ihm.put(s4, 2);
System.out.println(ihm.size());  // 1 — s3 == s4 (same reference!) → duplicate!
```

**When to use IdentityHashMap:** When you specifically need reference identity (the actual memory address) to be the key criterion — for example, tracking which object instances have been processed, regardless of their values.

---

### Q11 (Advanced): What is WeakHashMap? When would you use it?

**Perfect Answer:**

WeakHashMap is like a regular HashMap except it holds **weak references** to its keys. This means: if no strong references to a key object exist elsewhere in the program, the garbage collector can collect that key object, and WeakHashMap **automatically removes the corresponding entry**.

A strong reference is a normal variable pointing to an object. A weak reference is what WeakHashMap maintains — it doesn't prevent GC from collecting the object.

```java
WeakHashMap<String, Integer> whm = new WeakHashMap<>();
String s1 = new String("tap");
whm.put(s1, 100);

// s1 is a strong reference — object won't be collected
System.out.println(whm);  // {tap=100}

// Remove the strong reference
s1 = null;                 // now "tap" object has ZERO strong refs → eligible for GC
System.gc();               // explicitly request GC (optional in production)

System.out.println(whm);  // {} — entry automatically removed!
```

**Use case:** **Memory-sensitive caches.** If you're caching objects keyed by their references, and those objects are deleted elsewhere, WeakHashMap automatically cleans up the cache entries. You don't have to manually remove them — preventing memory leaks.

**Difference from HashMap:** HashMap holds strong references to keys — keys are never GC'd while the HashMap exists. WeakHashMap allows GC to collect keys — entries disappear when keys have no other strong references.

---

### Q12 (Intermediate): Why should you avoid Vector and Stack in modern Java?

**Perfect Answer:**

Vector and Stack are **legacy classes** from pre-JDK 1.2. They're not recommended because all their methods are **synchronized** — meaning only one thread can access them at a time. This was designed for thread safety, but it makes them significantly slower than their modern alternatives.

The synchronized keyword means: if 100 threads want to call `vector.add()`, they must queue up and each wait their turn. Imagine a classroom where only 1 student is allowed in at a time — that's how synchronized works. With ArrayList, all 100 can call `add()` concurrently (though with caveats about thread safety).

```java
// Vector's add — synchronized (slow)
public synchronized boolean add(E e) { ... }

// ArrayList's add — not synchronized (fast)
public boolean add(E e) { ... }

// Stack extends Vector — also synchronized
public class Stack<E> extends Vector<E> { }
```

**Modern replacements:**
- `Vector` → `ArrayList` (single thread) or `CopyOnWriteArrayList` (multi-thread)
- `Stack` → `ArrayDeque` or `LinkedList`
- `Hashtable` → `HashMap` (single thread) or `ConcurrentHashMap` (multi-thread)
- `Enumeration` → `Iterator`

---

### Q13 (Intermediate): What is the difference between HashMap, LinkedHashMap, and TreeMap?

**Perfect Answer:**

| Aspect | HashMap | LinkedHashMap | TreeMap |
|---|---|---|---|
| **Order** | Random (hash-based) | Insertion order | Sorted by key (ascending) |
| **Internal structure** | Hash table | Hash table + LinkedList | Red-Black BST |
| **Time complexity** | O(1) average | O(1) average | O(log n) |
| **Null keys** | One null key allowed | One null key allowed | No null keys (comparison fails) |
| **Duplicate keys** | Not allowed | Not allowed | Not allowed |
| **Duplicate values** | Allowed | Allowed | Allowed |
| **Use when** | Fastest lookup, order doesn't matter | Fast lookup + insertion order needed | Sorted key traversal or range operations |

```java
// HashMap — random order
HashMap<Integer, String> hm = new HashMap<>();
hm.put(3, "C"); hm.put(1, "A"); hm.put(2, "B");
// Possible output: {1=A, 3=C, 2=B}  ← random bucket order

// LinkedHashMap — insertion order
LinkedHashMap<Integer, String> lhm = new LinkedHashMap<>();
lhm.put(3, "C"); lhm.put(1, "A"); lhm.put(2, "B");
// Output: {3=C, 1=A, 2=B}  ← insertion order preserved

// TreeMap — sorted by key
TreeMap<Integer, String> tm = new TreeMap<>();
tm.put(3, "C"); tm.put(1, "A"); tm.put(2, "B");
// Output: {1=A, 2=B, 3=C}  ← sorted ascending by key
```

---

### Q14 (Tricky): TreeSet sorted String objects correctly but threw ClassCastException for Employee objects. Why? How do you fix it?

**Perfect Answer:**

TreeSet sorts automatically, but it relies entirely on `compareTo()` to determine ordering. For Strings and Integers, the JDK has already implemented `compareTo()` because `String` and `Integer` both implement `Comparable`.

When you try to store Employee objects in a TreeSet, TreeSet internally calls `compareTo()` on the Employee. But Employee doesn't implement `Comparable` by default — it has no `compareTo()` method. At runtime, TreeSet tries to cast Employee to `Comparable` to call `compareTo()`, fails, and throws `ClassCastException: Employee cannot be cast to Comparable`.

Two fixes:

**Fix 1 — Comparable (natural order):**
```java
class Employee implements Comparable<Employee> {
    @Override
    public int compareTo(Employee other) {
        return Integer.valueOf(this.id).compareTo(Integer.valueOf(other.id));
    }
}
TreeSet<Employee> ts = new TreeSet<>();  // uses compareTo automatically
```

**Fix 2 — Comparator (custom order):**
```java
class SortById implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return Integer.valueOf(e1.getId()).compareTo(Integer.valueOf(e2.getId()));
    }
}
TreeSet<Employee> ts = new TreeSet<>(new SortById());  // pass comparator
```

**The rule:** Wherever comparison/sorting happens in Java, either `compareTo()` (from Comparable) must exist on the object, OR a `Comparator` object must be provided. Without one of these, ClassCastException occurs at runtime.

---

## 🎯 How to Explain It

### 90-Second Version (Rapid-fire rounds)

> "Comparable and Comparator are two approaches to sorting custom objects. Comparable is in java.lang, implemented directly on the class itself — like Employee implements Comparable — and defines natural/default ordering via compareTo() which takes one parameter. Internally, Collections.sort() calls this compareTo(). If the result is positive, elements swap; if negative, no swap.
>
> Comparator is in java.util, implemented in separate external classes — SortById, SortByName, SortBySalary — giving unlimited sorting strategies without touching the original class. compare() takes two explicit parameters, no `this` keyword. You pass the Comparator object to TreeSet's constructor or to Collections.sort() as second argument. Lambda expressions can replace separate classes entirely since Comparator is a functional interface.
>
> For maps: HashMap is key-value storage using hash table — O(1), random order, one null key. LinkedHashMap adds insertion order. TreeMap sorts by key using BST. IdentityHashMap uses reference equality instead of value equality. WeakHashMap allows GC to collect keys when no strong references exist."

---

### 5-Minute Version (Deep-dive rounds)

> "Let me explain these in terms of how they actually work under the hood.
>
> Every time sorting happens in Java — Collections.sort(), TreeSet's automatic ordering — one method is always called internally: compareTo(). Not equals(). Not hashCode(). compareTo(). This is the core truth of sorting in Java.
>
> String and Integer already have compareTo() because they implement Comparable. When you sort an ArrayList of integers, sort() calls integer.compareTo(otherInteger) on every pair. Positive result → swap (ascending). Negative → no swap. Zero → no swap.
>
> For custom objects like Employee, the class has no compareTo() by default. If you try Collections.sort() on an ArrayList of Employees, or try storing Employees in a TreeSet, you get ClassCastException — because Java tries to cast Employee to Comparable and fails.
>
> Fix one: Implement Comparable directly on Employee. Override compareTo(). Define one sorting logic — ID, name, or salary. This becomes the natural order. Limitation: only one strategy.
>
> Fix two: Comparator. Create separate classes — SortById, SortByName, SortBySalary, each implementing Comparator<Employee> and overriding compare(e1, e2). Pass these objects to TreeSet's constructor or Collections.sort() as the second argument. You get unlimited sorting strategies, and Employee class is never touched. This is Amazon's filter section — sort by price, by rating, by relevance — each is a Comparator object being swapped in.
>
> For maps: HashMap gives O(1) key-value lookup using the same hashing as HashSet. LinkedHashMap adds insertion order. TreeMap sorts by key. IdentityHashMap uses == not equals() — useful when object identity matters. WeakHashMap allows GC to collect unused keys automatically — great for memory-sensitive caches. Legacy classes Vector, Stack, Hashtable are synchronized → slow → replaced by ArrayList, ArrayDeque, HashMap in modern Java."

---

### Follow-Up Responses

**"Can you do multi-level sorting with Comparator?"**
> "Yes — if names are equal, compare by ID. In the compare() method: first compare by name. If result is 0 (equal), then compare by ID. This way, primary sort is name, secondary sort is ID."

```java
public int compare(Employee e1, Employee e2) {
    int nameResult = e1.getName().compareTo(e2.getName());
    if (nameResult != 0) return nameResult;   // different names — use name result
    return Integer.valueOf(e1.getId()).compareTo(Integer.valueOf(e2.getId())); // same name — use ID
}
```

**"Why do you box int to Integer before calling compareTo()?"**
> "Because compareTo() is a method, and primitive `int` doesn't have methods — only objects do. By boxing (Integer.valueOf(this.id)), we convert the primitive to an Integer wrapper object, which has the compareTo() method already implemented. Auto-boxing doesn't always work in this context, so explicit boxing is safer."

---

## ⚠️ Edge Cases & Gotchas

| Edge Case | What Happens | Solution |
|---|---|---|
| `Collections.sort(list)` on Employee without Comparable | `ClassCastException` at runtime — Employee can't be cast to Comparable | Implement `Comparable<Employee>` and override `compareTo()` |
| Implementing Comparator on the Employee class itself | Compiles, but defeats the purpose — still one strategy | Create separate classes for each sorting strategy |
| Using `int` instead of `Integer` in compareTo | `int.compareTo()` doesn't compile — primitives have no methods | Box to `Integer.valueOf(this.employeeId)` first |
| Forgetting to box salary (`double`) for compareTo | Same issue — `double.compareTo()` invalid | Use `Double.valueOf(this.salary).compareTo(...)` |
| `Collections.sort(list)` vs `Collections.sort(list, comparator)` | Different overloads — first uses Comparable, second uses Comparator | Check which one you need based on your setup |
| TreeMap with heterogeneous keys | `ClassCastException` at runtime — can't compare Integer with String | Always use generics `TreeMap<Integer, String>` to enforce key type |
| TreeMap null keys | `NullPointerException` — can't compare null | Filter nulls before inserting; use HashMap if null keys needed |
| HashMap null keys | One null key allowed — stored in bucket 0 | Valid, but avoid null keys for clarity |
| `values()` returns Collection, not Set | You can't use Set methods on it | Correctly type as `Collection<V>` |
| WeakHashMap without `System.gc()` | Entry may not be removed immediately | GC timing is non-deterministic; use for caching, not guaranteed removal |
| Constant pool strings in IdentityHashMap | `"tap" == "tap"` is true (same pool ref) → treated as same key | Create `new String("tap")` explicitly to test reference inequality |
| Lambda expression bracket mismatch | Compile error — hard to debug | Write lambda step by step; ensure constructor brackets match |
| `compareTo()` returning integer difference (e.g., `return id1 - id2`) | Integer overflow for extreme values (e.g., MAX_VALUE - MIN_VALUE) | Always use `Integer.valueOf(id1).compareTo(Integer.valueOf(id2))` |
| TreeSet duplicate detection with Comparator returning 0 | Element considered duplicate and NOT inserted — even if actual objects differ! | Your compare() must return 0 only for truly identical records |

---

## ✅ Quick Revision Card

### Comparable — Key Facts
- Package: **`java.lang`** — no import needed
- Method: **`compareTo(Object o)`** — 1 parameter (second object)
- First object: via **`this`** keyword
- Implemented **on the class itself**: `class Employee implements Comparable<Employee>`
- Defines **natural/default sorting** — one strategy only
- Works with: `Collections.sort(list)` (no 2nd arg) | TreeSet automatic sort

### Comparator — Key Facts
- Package: **`java.util`** — import required
- Method: **`compare(Object o1, Object o2)`** — 2 parameters (both explicit)
- No `this` keyword needed — both objects are parameters
- Implemented in **separate classes**: `class SortById implements Comparator<Employee>`
- Defines **custom/external sorting** — unlimited strategies
- Works with: `Collections.sort(list, comparatorObj)` | `new TreeSet<>(comparatorObj)`
- Functional interface → can use **lambda expression**

### Lambda Syntax for Comparator
```java
// Full form (class):
class SortByName implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return e1.getName().compareTo(e2.getName());
    }
}

// Lambda (one line):
(e1, e2) -> e1.getName().compareTo(e2.getName())

// Usage:
TreeSet<Employee> ts = new TreeSet<>((e1, e2) -> e1.getName().compareTo(e2.getName()));
Collections.sort(list, (e1, e2) -> e1.getName().compareTo(e2.getName()));

// Descending:
(e1, e2) -> e1.getName().compareTo(e2.getName()) * -1
```

### Map Methods to Memorize
```java
hm.put(key, value);       // insert/update
hm.get(key);              // retrieve value by key
hm.remove(key);           // remove entry by key
hm.containsKey(key);      // boolean — does key exist?
hm.containsValue(val);    // boolean — does value exist?
hm.size();                // number of entries
hm.isEmpty();             // boolean — is map empty?
hm.entrySet();            // Set<Map.Entry<K,V>> — all key-value pairs
hm.keySet();              // Set<K> — all keys (unique → Set)
hm.values();              // Collection<V> — all values (duplicates allowed → Collection)
hm.putAll(otherMap);      // merge another map
hm.clear();               // remove all entries
```

### Map Decision Framework
```
Need key-value storage?
  → Yes → USE MAP (not Set or List)
    ├── Need sorted keys? → TreeMap (O(log n), sorted ascending)
    ├── Need insertion order? → LinkedHashMap (O(1), insertion order)
    ├── Need fastest? → HashMap (O(1), random order)
    ├── Need reference equality for keys? → IdentityHashMap
    └── Need GC-able keys (cache)? → WeakHashMap
```

### Must-Remember Facts
- `compareTo()` is from **Comparable** interface (java.lang) — **not** Object class
- `compare()` is from **Comparator** interface (java.util)
- `keySet()` → **Set** (keys unique). `values()` → **Collection** (values can duplicate)
- **entrySet()** gives `Map.Entry` objects with `getKey()` and `getValue()`
- `HashMap` is NOT part of Collection — it implements `Map` directly
- **Legacy classes are synchronized = SLOW** → use ArrayList, ArrayDeque, HashMap instead
- **Four ways to implement functional interface:** regular class, inner class, anonymous inner class, lambda
- `System.gc()` requests GC — needed to observe WeakHashMap behavior
- **IdentityHashMap:** `==` comparison. **HashMap:** `equals()` comparison
- TreeMap sorts by **key**, not value

---

## 🔗 Connections

### How This Relates to Other Concepts

| Concept | Connection |
|---|---|
| **Polymorphism** | Comparator object (SortById) passed as parent-type (Comparator) reference to TreeSet constructor — classic loose coupling. Lambda replaces concrete class object entirely. |
| **Functional Interfaces** | Both Comparable and Comparator are functional interfaces. Comparator is a perfect candidate for lambda expression, method reference. |
| **Inheritance** | String, Integer, Double all implement Comparable — that's why they sort automatically. Comparable enables sorting via inheritance chain. |
| **Autoboxing** | `int` must be boxed to `Integer` before calling `compareTo()`. Autoboxing doesn't always work in compareTo context — explicit boxing is safer. |
| **Open/Closed Principle** | Comparator embodies OCP — add new sorting strategies without modifying existing classes. |
| **equals() & hashCode()** | HashMap uses hashCode() + equals() for key comparison. IdentityHashMap replaces equals() with ==. Understanding both connects to HashSet's internal working. |
| **Legacy vs Modern** | Vector (synchronized) → ArrayList. Stack → ArrayDeque. Hashtable → HashMap. Enumeration → Iterator. Same concepts, modern implementations. |
| **Multi-threading** | Vector, Stack, Hashtable are thread-safe (synchronized) but slow. ConcurrentHashMap is the modern thread-safe map. Understanding synchronized connects to multi-threading. |

### Design Patterns Using These Concepts

- **Strategy Pattern:** Comparator IS the Strategy pattern — different Comparator implementations = different sorting strategies, swappable at runtime
- **Template Method:** Collections.sort() is the template — it calls compareTo() which subclasses define
- **Factory Pattern:** `switch(choice) { case 1 -> new SortById(); ... }` — factory selecting appropriate Comparator

### System Design Connections

- **Caching:** WeakHashMap as memory-sensitive cache — entries auto-expire when keys are GC'd
- **LRU Cache:** LinkedHashMap with `removeEldestEntry()` override — interview classic
- **Database-like queries:** TreeMap for range-based key queries (similar to TreeSet's headSet/tailSet)
- **Sorting in real apps:** Every e-commerce filter section uses Comparator-like patterns
- **Leaderboard systems:** TreeMap sorted by score for real-time ranking

---

## 🎓 Real-World Scenarios

### Scenario 1: E-Commerce — Product Sorting System (Comparator)

**Problem:** Flipkart's product listing needs to sort products by: price (low-high, high-low), rating (high-low), and name (A-Z). User picks the sort option dynamically.

```java
import java.util.*;

class Product {
    private String name;
    private double price;
    private double rating;
    private int reviewCount;

    Product(String name, double price, double rating, int reviewCount) {
        this.name = name; this.price = price;
        this.rating = rating; this.reviewCount = reviewCount;
    }

    public String getName() { return name; }
    public double getPrice() { return price; }
    public double getRating() { return rating; }
    public int getReviewCount() { return reviewCount; }

    @Override
    public String toString() {
        return name + " (₹" + price + " | ★" + rating + " | " + reviewCount + " reviews)";
    }
}

// Separate Comparator classes for each sort option
class SortByPriceLowToHigh implements Comparator<Product> {
    public int compare(Product p1, Product p2) {
        return Double.valueOf(p1.getPrice()).compareTo(Double.valueOf(p2.getPrice()));
    }
}

class SortByPriceHighToLow implements Comparator<Product> {
    public int compare(Product p1, Product p2) {
        return Double.valueOf(p1.getPrice()).compareTo(Double.valueOf(p2.getPrice())) * -1;
    }
}

class SortByRating implements Comparator<Product> {
    public int compare(Product p1, Product p2) {
        // Higher rating first = descending
        return Double.valueOf(p1.getRating()).compareTo(Double.valueOf(p2.getRating())) * -1;
    }
}

class SortByName implements Comparator<Product> {
    public int compare(Product p1, Product p2) {
        return p1.getName().compareTo(p2.getName());
    }
}

class SortByRelevance implements Comparator<Product> {
    // Multi-level: by rating first, then by review count
    public int compare(Product p1, Product p2) {
        int ratingComp = Double.valueOf(p1.getRating())
                               .compareTo(Double.valueOf(p2.getRating())) * -1;
        if (ratingComp != 0) return ratingComp;
        // If rating equal → more reviews first
        return Integer.valueOf(p1.getReviewCount())
                      .compareTo(Integer.valueOf(p2.getReviewCount())) * -1;
    }
}

class ProductListingService {
    public List<Product> getSortedProducts(List<Product> products, String sortOption) {
        Comparator<Product> comparator = switch (sortOption) {
            case "PRICE_LOW"  -> new SortByPriceLowToHigh();
            case "PRICE_HIGH" -> new SortByPriceHighToLow();
            case "RATING"     -> new SortByRating();
            case "NAME"       -> new SortByName();
            default           -> new SortByRelevance();
        };
        Collections.sort(products, comparator);
        return products;
    }
}

// Usage
List<Product> products = new ArrayList<>(List.of(
    new Product("iPhone 16",    89999, 4.5, 12000),
    new Product("Samsung S25",  79999, 4.3,  8000),
    new Product("OnePlus 13",   69999, 4.4,  6000),
    new Product("Pixel 9",      74999, 4.6,  3000),
    new Product("Nothing Phone", 39999, 4.2, 15000)
));

ProductListingService service = new ProductListingService();
System.out.println("=== Price: Low to High ===");
service.getSortedProducts(new ArrayList<>(products), "PRICE_LOW")
       .forEach(System.out::println);

System.out.println("\n=== Rating: High to Low ===");
service.getSortedProducts(new ArrayList<>(products), "RATING")
       .forEach(System.out::println);
```

---

### Scenario 2: Banking — Account Manager (HashMap)

**Problem:** A bank needs to look up account holder details by account number (key), display all account holders, and find all holders with a specific branch code (value search).

```java
import java.util.*;

class BankAccount {
    String holderName;
    double balance;
    String branch;

    BankAccount(String holderName, double balance, String branch) {
        this.holderName = holderName;
        this.balance = balance;
        this.branch = branch;
    }

    @Override
    public String toString() {
        return holderName + " | ₹" + balance + " | " + branch;
    }
}

class BankSystem {
    // Account number (key) → Account details (value)
    private HashMap<String, BankAccount> accounts = new HashMap<>();

    void addAccount(String accNo, BankAccount account) {
        accounts.put(accNo, account);
    }

    BankAccount getAccount(String accNo) {
        return accounts.get(accNo);  // O(1) lookup by account number
    }

    void showAllAccounts() {
        System.out.println("=== All Accounts ===");
        for (Map.Entry<String, BankAccount> entry : accounts.entrySet()) {
            System.out.println(entry.getKey() + " → " + entry.getValue());
        }
    }

    List<String> getAccountsByBranch(String branch) {
        List<String> result = new ArrayList<>();
        for (Map.Entry<String, BankAccount> entry : accounts.entrySet()) {
            if (entry.getValue().branch.equals(branch)) {
                result.add(entry.getKey());
            }
        }
        return result;
    }

    // Transfer money between accounts
    boolean transfer(String fromAcc, String toAcc, double amount) {
        BankAccount from = accounts.get(fromAcc);
        BankAccount to = accounts.get(toAcc);
        if (from == null || to == null) return false;
        if (from.balance < amount) {
            System.out.println("Insufficient funds!");
            return false;
        }
        from.balance -= amount;
        to.balance += amount;
        return true;
    }
}

// Usage
BankSystem bank = new BankSystem();
bank.addAccount("ACC001", new BankAccount("Alice", 150000, "Koramangala"));
bank.addAccount("ACC002", new BankAccount("Bob", 85000, "Indiranagar"));
bank.addAccount("ACC003", new BankAccount("Carol", 220000, "Koramangala"));
bank.addAccount("ACC004", new BankAccount("Dave", 40000, "Whitefield"));

bank.showAllAccounts();

System.out.println("\nLookup ACC002: " + bank.getAccount("ACC002"));
System.out.println("Koramangala accounts: " + bank.getAccountsByBranch("Koramangala"));

bank.transfer("ACC001", "ACC002", 25000);
System.out.println("\nAfter transfer:");
System.out.println("ACC001: " + bank.getAccount("ACC001").balance);
System.out.println("ACC002: " + bank.getAccount("ACC002").balance);
```

---

### Scenario 3: Student Gradebook — TreeMap with Comparable

**Problem:** A school system maintains student records sorted by roll number (TreeMap), displays sorted grade reports, and allows sorting by different criteria using Comparator.

```java
import java.util.*;

class Student implements Comparable<Student> {
    int rollNo;
    String name;
    double percentage;
    String grade;  // A, B, C, D, F

    Student(int rollNo, String name, double percentage) {
        this.rollNo = rollNo;
        this.name = name;
        this.percentage = percentage;
        this.grade = calculateGrade(percentage);
    }

    private String calculateGrade(double pct) {
        if (pct >= 90) return "A+";
        if (pct >= 80) return "A";
        if (pct >= 70) return "B";
        if (pct >= 60) return "C";
        return "F";
    }

    @Override
    public int compareTo(Student other) {
        // Natural order: by roll number ascending
        return Integer.valueOf(this.rollNo).compareTo(Integer.valueOf(other.rollNo));
    }

    @Override
    public String toString() {
        return "Roll#" + rollNo + " | " + name + " | " + percentage + "% | " + grade;
    }
}

class SortByPercentageDescending implements Comparator<Student> {
    public int compare(Student s1, Student s2) {
        return Double.valueOf(s1.percentage).compareTo(Double.valueOf(s2.percentage)) * -1;
    }
}

// TreeMap: roll number → Student
TreeMap<Integer, Student> gradebook = new TreeMap<>();
gradebook.put(105, new Student(105, "Alice", 92.5));
gradebook.put(101, new Student(101, "Bob", 75.0));
gradebook.put(103, new Student(103, "Carol", 88.5));
gradebook.put(102, new Student(102, "Dave", 65.0));
gradebook.put(104, new Student(104, "Eve", 95.0));

System.out.println("=== Sorted by Roll Number ===");
for (Map.Entry<Integer, Student> e : gradebook.entrySet()) {
    System.out.println(e.getValue());
}

// Get students with roll number between 102 and 104
System.out.println("\nRoll 102-104: " + gradebook.subMap(102, true, 104, true));

// Sort by percentage using Comparator
List<Student> byPercentage = new ArrayList<>(gradebook.values());
Collections.sort(byPercentage, new SortByPercentageDescending());
System.out.println("\n=== Sorted by Percentage (High to Low) ===");
byPercentage.forEach(System.out::println);
```

---

## 📝 Practice Questions

1. **Coding:** Create a `Book` class with `title`, `author`, and `price`. Implement Comparable to sort by price ascending. Test with `Collections.sort()` on an `ArrayList<Book>`.

2. **Design:** Create a Student class with `name`, `age`, `cgpa`. Create 4 Comparator classes: by name A-Z, by name Z-A, by CGPA high-low, by age. Give user a choice and display sorted results.

3. **Tricky:** What is the output? Why?
```java
TreeSet<Employee> ts = new TreeSet<>(new SortByName());
ts.add(new Employee(1, "Alice", 50000));
ts.add(new Employee(2, "Alice", 60000));  // same name, different ID
ts.add(new Employee(3, "Bob", 40000));
System.out.println(ts.size());  // 2 or 3? Why?
```

4. **Lambda conversion:** Convert this to a lambda expression:
```java
class SortBySalaryDesc implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return Double.valueOf(e1.getSalary()).compareTo(Double.valueOf(e2.getSalary())) * -1;
    }
}
```

5. **HashMap operations:** Given a HashMap of student names and marks, write code to: (a) find the student with highest marks, (b) find all students who scored above 80, (c) display results sorted by student name.

6. **System Design:** Design a simple LRU (Least Recently Used) cache using LinkedHashMap. The cache should hold maximum 5 entries. When full, the least recently accessed entry should be removed.

7. **Comparable vs Comparator:** When would you use Comparable over Comparator and vice versa? Give a scenario for each where the other approach would be wrong.

8. **Advanced:** Explain why this causes issues:
```java
class Employee implements Comparator<Employee> {  // implemented on Employee itself
    public int compare(Employee e1, Employee e2) {
        return e1.id - e2.id;
    }
}
TreeSet<Employee> ts = new TreeSet<>(new Employee(1, "test", 1000));
// What happens? Why is this a bad design?
```

---

## 🚀 The Winning Formula

### What Interviewers Are ACTUALLY Testing

1. **Do you know the rule?** "Wherever sorting happens, compareTo() is called internally." This single sentence separates people who understand Java from those who've just memorized syntax.

2. **Can you implement it from scratch?** Not just say "implement Comparable" — can you write Employee with compareTo(), handle boxing from int to Integer, handle ascending vs descending?

3. **Do you know when to use which?** Comparable = one natural order, embedded in the class. Comparator = multiple external orders, pluggable. The Flipkart/Amazon/Myntra filter section analogy hits the interviewer every time.

4. **Can you use lambda?** In 2026, writing a separate class for a single Comparator when lambda exists is a red flag. Know both — explain you can do either.

5. **HashMap internals?** Do you know entrySet vs keySet vs values? Do you know values() returns Collection not Set (and why)? These are the questions that filter serious candidates.

6. **Legacy awareness?** Knowing that Vector is slow BECAUSE of synchronization, and connecting it to multi-threading, shows you understand Java's evolution — not just its current state.

### The Perfect Closing Statement

> "Comparable and Comparator answer a fundamental question in software development: how do you make your data orderable? Every product ranking, every leaderboard, every database ORDER BY clause — they all implement the same concept. Java's collections framework made this pluggable: you can define natural order once in your class with Comparable, and then plug in unlimited external strategies with Comparator. That flexibility — the ability to sort by price, rating, name, or reviews with a single object swap — is what makes Java's collections so powerful in production systems."

### Red Flags to Avoid

- ❌ Saying "compareTo() is from Object class" — it's from **Comparable interface**
- ❌ Forgetting to box `int` to `Integer` before calling `compareTo()`
- ❌ Implementing Comparator on the Employee class itself (defeats the purpose)
- ❌ Not knowing the package difference: Comparable = `java.lang`, Comparator = `java.util`
- ❌ Saying sort happens alphabetically by default for custom objects — NO, it throws ClassCastException
- ❌ Confusing `compare()` (Comparator, 2 params) with `compareTo()` (Comparable, 1 param)
- ❌ Saying `values()` returns a `Set` — it returns `Collection` (duplicates allowed)
- ❌ Not knowing that `entrySet()` gives `Map.Entry` objects with `getKey()` and `getValue()`
- ❌ Saying "use Vector for thread safety" — the modern answer is `CopyOnWriteArrayList` or `ConcurrentHashMap`
- ❌ Not knowing that HashMap and HashSet both use the same internal hash table mechanism

### The One-Sentence Summary You Can Always Fall Back On

> **"Comparable defines how an object compares to others (one natural order, in java.lang, on the class itself, one param); Comparator defines external comparison strategies (unlimited custom orders, in java.util, separate classes, two params, supports lambda); HashMap gives O(1) key-value lookup via hashing; TreeMap sorts by key; LinkedHashMap preserves insertion order — and all of them ultimately use the same principle: hash function or compareTo() to organize data."**

---

*This prep kit covers 100% of what was discussed across 4 days of transcript content on Comparable, Comparator, HashMap family, IdentityHashMap, WeakHashMap, Legacy classes, and lambda expressions with Comparator. Practice the Employee sorting program until you can write it from memory — it appears in 80%+ of Java product-company interviews.*
