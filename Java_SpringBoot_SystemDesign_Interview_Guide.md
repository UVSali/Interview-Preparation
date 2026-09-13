# Java, Spring Boot & System Design — Interview Preparation Guide
### For Backend Engineers (5+ Years Experience)

This guide covers **Java**, **Spring Boot**, **Low-Level Design (LLD)**, and **High-Level Design (HLD)** — from fundamentals through advanced, senior-level topics. Each question includes a detailed explanation, and code examples are provided wherever they aid understanding.

---

## Table of Contents

**Part 1: Java**
- 1.1 Core Java & OOP (Q1–10)
- 1.2 Collections Framework (Q11–18)
- 1.3 Exception Handling (Q19–22)
- 1.4 Multithreading & Concurrency (Q23–30)
- 1.5 Java 8+ Features (Q31–36)
- 1.6 JVM Internals & Memory Management (Q37–40)

**Part 2: Spring Boot**
- 2.1 Spring Core Concepts (Q1–9)
- 2.2 Spring MVC & REST APIs (Q10–14)
- 2.3 Spring Data JPA (Q15–19)
- 2.4 Spring Security — basics (Q20–22)
- 2.5 Transactions, AOP & Caching (Q23–26)
- 2.6 Microservices with Spring Cloud (Q27–30)
- 2.7 Testing (Q31)
- 2.8 Hibernate (Deep Dive) (Q32–41)
- 2.9 Spring Security Deep Dive: OAuth2, JWT/JWKS (Nimbus) & Session Management (Q42–53)

**Part 3: Low-Level Design (LLD)**
- 3.1 Design Principles (SOLID) (Q1–2)
- 3.2 Design Patterns (Q3–9)
- 3.3 Classic LLD Problems — Parking Lot, Elevator, LRU Cache, Rate Limiter (Q10–13)

**Part 4: High-Level Design (HLD)**
- 4.1 Core Concepts — Scalability, Load Balancing, Caching, CAP Theorem (Q1–4)
- 4.2 Databases at Scale — Sharding, Replication, Indexing (Q5–8)
- 4.3 Messaging, Gateways & Distributed Rate Limiting (Q9–12)
- 4.4 Classic HLD Case Studies — URL Shortener, Chat App (Q13–14)
- 4.5 Microservices Design Patterns — Saga, CQRS, Event Sourcing, Outbox, Strangler Fig, Sidecar, BFF, Bulkhead (Q15–21)

**Part 5: Scenario-Based Interview Questions**
- Production diagnostics, scaling trade-offs, distributed consistency, idempotency, LLD-extension scenarios, and incident-response walkthroughs (Q1–14)

**Part 6: React (Basics to Advanced)**
- 6.1 React Fundamentals (Q1–8)
- 6.2 Hooks (Q9–16)
- 6.3 State Management & Performance (Q17–20)
- 6.4 Advanced React — HOCs, Fiber, Error Boundaries, Suspense, SSR/RSC, Redux, Concurrent Rendering, Testing (Q21–30)

---

# Part 1: Java

## 1.1 Core Java & OOP

### Q1. Explain the four pillars of OOP with examples.

**Encapsulation** — Bundling data (fields) and behavior (methods) together, and restricting direct access to internal state using access modifiers.

```java
public class Account {
    private double balance; // hidden from outside

    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }
    public double getBalance() {
        return balance;
    }
}
```

**Inheritance** — A class (subclass) acquires fields/methods of another class (superclass), enabling code reuse and an "IS-A" relationship.

```java
class Vehicle { void start() { System.out.println("Starting..."); } }
class Car extends Vehicle { void openTrunk() { System.out.println("Trunk open"); } }
```

**Polymorphism** — The same interface behaves differently depending on context.
- *Compile-time (static)*: method overloading — same method name, different parameters.
- *Runtime (dynamic)*: method overriding — subclass provides its own implementation, resolved at runtime via dynamic dispatch.

```java
class Shape { double area() { return 0; } }
class Circle extends Shape { double area() { return Math.PI * 5 * 5; } }
Shape s = new Circle();
s.area(); // resolved at runtime -> Circle's area()
```

**Abstraction** — Hiding implementation details and exposing only the essential features, typically via abstract classes or interfaces.

**Why interviewers ask this:** They want to see if you can explain concepts *with your own examples*, not just recite definitions — and whether you understand runtime vs. compile-time polymorphism, which trips up a lot of candidates.

---

### Q2. Abstract class vs. Interface — when would you use each?

| Aspect | Abstract Class | Interface |
|---|---|---|
| Methods | Can have abstract + concrete methods | All abstract by default; `default`/`static` methods allowed since Java 8 |
| Variables | Can have instance variables of any type | Only `public static final` constants |
| Constructor | Can have a constructor | Cannot have a constructor |
| Inheritance | Single inheritance (`extends`) | Multiple inheritance (`implements`) |
| Access modifiers | Any (private, protected, public) | Public by default (Java 9+ allows private methods) |

**Rule of thumb:** Use an **interface** to define a *capability/contract* shared by unrelated classes (e.g., `Comparable`, `Runnable`). Use an **abstract class** when classes share a common base *state and behavior* and are naturally related (e.g., `Animal` → `Dog`, `Cat`).

---

### Q3. What's the difference between `==` and `.equals()`?

- `==` compares **references** for objects (do both variables point to the same memory location?) and **values** for primitives.
- `.equals()` compares **logical/content equality** and can be overridden. `Object`'s default `equals()` is reference equality, but classes like `String`, `Integer`, etc. override it to compare values.

```java
String a = new String("hello");
String b = new String("hello");
System.out.println(a == b);       // false (different objects)
System.out.println(a.equals(b));  // true (same content)

String c = "hello";
String d = "hello";
System.out.println(c == d);       // true (both point to the same interned string in the String pool)
```

---

### Q4. Why is `String` immutable in Java?

1. **String pool / interning** — identical literals can safely share the same object, saving memory. This is only safe because Strings can't change.
2. **Thread-safety** — immutable objects are inherently safe to share across threads without synchronization.
3. **Security** — Strings are used for things like class names, file paths, and network connections; if mutable, they could be changed after validation but before use.
4. **Hashcode caching** — `String` caches its hashcode after first computation, which is only valid because the content never changes. This makes `String` keys in `HashMap` fast.

---

### Q5. String vs. StringBuilder vs. StringBuffer

- **String** — immutable; every modification creates a new object.
- **StringBuilder** — mutable, **not** thread-safe, faster (no synchronization overhead). Use in single-threaded contexts (the vast majority of string-building code).
- **StringBuffer** — mutable, thread-safe (methods are `synchronized`), slower. Rarely needed today — prefer `StringBuilder` unless multiple threads genuinely mutate the same buffer.

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 5; i++) sb.append(i);
System.out.println(sb.toString()); // "01234"
```

---

### Q6. What is autoboxing/unboxing, and what's the Integer caching gotcha?

Autoboxing converts a primitive to its wrapper (`int` → `Integer`), and unboxing does the reverse. Java does this automatically at compile time.

**The gotcha:** `Integer` caches values from **-128 to 127** (via `Integer.valueOf()`). Comparing cached values with `==` works "by accident," but it breaks outside that range:

```java
Integer a = 100, b = 100;
System.out.println(a == b); // true (both from cache)

Integer c = 200, d = 200;
System.out.println(c == d); // false (outside cache range, two different objects)
```

**Lesson:** Always use `.equals()` to compare wrapper objects, never `==`.

---

### Q7. Explain Java access modifiers.

| Modifier | Same Class | Same Package | Subclass (different package) | Everywhere |
|---|---|---|---|---|
| `private` | ✅ | ❌ | ❌ | ❌ |
| default (no modifier) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

---

### Q8. Static vs. instance members. What's a static initializer block?

- **Static members** belong to the class itself — shared across all instances, loaded once when the class is loaded.
- **Instance members** belong to each object separately.
- A **static block** runs once, when the class is first loaded — used for one-time static setup (e.g., loading configuration, initializing static maps).

```java
class Config {
    static Map<String, String> settings = new HashMap<>();
    static {
        settings.put("env", "production");
        System.out.println("Static block executed once");
    }
}
```

---

### Q9. `final` vs. `finally` vs. `finalize()`

- **`final`** — a modifier: a `final` variable can't be reassigned, a `final` method can't be overridden, a `final` class can't be extended.
- **`finally`** — a block that always executes after a `try`/`catch`, regardless of whether an exception occurred (used for cleanup like closing resources).
- **`finalize()`** — a method called by the garbage collector before reclaiming an object's memory. **Deprecated since Java 9** and unreliable (no guarantee it runs at all) — use `try-with-resources` or `Cleaner` instead.

---

### Q10. Constructor overloading and constructor chaining.

Multiple constructors with different parameter lists let you initialize an object in different ways. `this(...)` calls another constructor in the same class (must be the first statement).

```java
class Employee {
    String name;
    double salary;

    Employee() {
        this("Unknown", 0.0); // chains to the parameterized constructor
    }
    Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
}
```


## 1.2 Collections Framework

### Q11. Give an overview of the Java Collections hierarchy.

```
Collection (interface)
├── List (ordered, allows duplicates): ArrayList, LinkedList, Vector
├── Set (no duplicates): HashSet, LinkedHashSet, TreeSet
└── Queue (FIFO/priority): PriorityQueue, ArrayDeque, LinkedList

Map (interface, separate hierarchy — not a Collection):
├── HashMap, LinkedHashMap, TreeMap, ConcurrentHashMap
```

`List` and `Set` extend `Collection`; `Map` is a separate top-level interface since it stores key-value pairs, not single elements.

---

### Q12. ArrayList vs. LinkedList

| Aspect | ArrayList | LinkedList |
|---|---|---|
| Structure | Dynamic array | Doubly linked list |
| Random access (`get(i)`) | O(1) | O(n) |
| Insert/delete at end | O(1) amortized | O(1) |
| Insert/delete at beginning/middle | O(n) (shifting) | O(1) (if you already have the node) |
| Memory | Less overhead | More overhead (node pointers) |

**Rule of thumb:** Use `ArrayList` by default — most access patterns are read-heavy. Use `LinkedList` only if you're doing frequent insertions/deletions at the head/middle and rarely need random access (in practice, `ArrayDeque` often beats `LinkedList` even for queue use cases).

---

### Q13. Explain how HashMap works internally.

1. `HashMap` stores entries in an array of **buckets**. `hashCode()` on the key is passed through an internal hash-spreading function, then reduced modulo the array size to pick a bucket.
2. Each bucket holds a **linked list** of entries that hash to the same bucket (collisions).
3. **Since Java 8**: if a bucket's linked list grows beyond a threshold (8 entries) *and* the table is large enough, it's converted to a **balanced red-black tree** for that bucket — turning worst-case lookup from O(n) into O(log n).
4. **Load factor** (default 0.75) and **resizing**: once the map's size exceeds `capacity × loadFactor`, the table doubles in size and all entries are rehashed.
5. Key lookup: compute hash → find bucket → walk the bucket (list or tree) comparing via `equals()` until a match is found.

```java
Map<String, Integer> map = new HashMap<>();
map.put("apple", 1);   // hash("apple") -> bucket index -> stored
map.get("apple");      // same hash -> same bucket -> equals() match -> 1
```

**Why `hashCode()`/`equals()` matter:** If two keys are `.equals()` but have different `hashCode()`s, they might land in different buckets and the map will treat them as distinct — a classic, hard-to-debug bug.

---

### Q14. HashMap vs. ConcurrentHashMap

- **`HashMap`** is not thread-safe — concurrent modification can corrupt internal state or cause infinite loops (in older Java versions) / lost updates.
- **`ConcurrentHashMap`** achieves thread-safety without locking the entire map:
  - Pre-Java 8: divided into **segments**, each with its own lock (lock striping).
  - **Java 8+**: uses **CAS (Compare-And-Swap)** operations on individual bins for most writes, and only synchronizes on a bucket when there's an actual collision — no global lock, so reads are essentially lock-free and highly concurrent.
- `Collections.synchronizedMap()` is an older alternative that wraps a `HashMap` with a single lock on every operation — much less scalable than `ConcurrentHashMap`.

---

### Q15. HashSet vs. LinkedHashSet vs. TreeSet

- **HashSet** — backed by a `HashMap`; no ordering guarantee; O(1) add/remove/contains.
- **LinkedHashSet** — maintains **insertion order** using a linked list alongside the hash table; slightly more overhead than `HashSet`.
- **TreeSet** — backed by a `TreeMap` (red-black tree); elements kept in **sorted order** (natural ordering or a custom `Comparator`); O(log n) operations.

---

### Q16. Comparable vs. Comparator

- **`Comparable<T>`** — implemented *by the class itself*; defines a single "natural ordering" via `compareTo()`.
- **`Comparator<T>`** — a separate object defining *custom* ordering via `compare()`; you can have many different Comparators for the same class, useful when you need multiple sort orders or can't modify the original class.

```java
class Employee implements Comparable<Employee> {
    int age;
    public int compareTo(Employee other) { return this.age - other.age; } // natural order: by age
}

// Custom order without touching the class:
Comparator<Employee> bySalaryDesc = (e1, e2) -> Double.compare(e2.salary, e1.salary);
employees.sort(bySalaryDesc);
```

---

### Q17. What are fail-fast and fail-safe iterators?

- **Fail-fast** (e.g., `ArrayList`, `HashMap` iterators) — use an internal `modCount`. If the collection is structurally modified while iterating (other than through the iterator itself), a `ConcurrentModificationException` is thrown immediately.
- **Fail-safe** (e.g., `CopyOnWriteArrayList`, `ConcurrentHashMap` iterators) — iterate over a snapshot or a structure designed for concurrent access, so they don't throw `ConcurrentModificationException`, though the iteration may not reflect the very latest state.

```java
List<Integer> list = new ArrayList<>(List.of(1, 2, 3));
for (Integer i : list) {
    if (i == 2) list.remove(i); // throws ConcurrentModificationException
}
// Correct way: use an Iterator's own remove(), or iterate over a copy
```

---

### Q18. Why must you override `hashCode()` whenever you override `equals()`?

The **equals-hashCode contract** states: if two objects are equal per `.equals()`, they **must** have the same `hashCode()`. (The reverse isn't required — unequal objects *can* share a hash code, that's just a collision.)

**Consequence of violating it:** Hash-based collections (`HashMap`, `HashSet`) use `hashCode()` to locate the bucket first, then `equals()` to confirm a match within that bucket. If two "equal" objects produce different hash codes, they can end up in different buckets — a `HashSet` might contain duplicate "equal" objects, or a `HashMap.get()` might fail to find a value you just `put()`.

```java
class Point {
    int x, y;
    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point)) return false;
        Point p = (Point) o;
        return x == p.x && y == p.y;
    }
    @Override
    public int hashCode() {
        return Objects.hash(x, y); // consistent with equals()
    }
}
```

## 1.3 Exception Handling

### Q19. Checked vs. unchecked exceptions. What's the `Error` vs `Exception` distinction?

```
Throwable
├── Error (JVM-level problems, not meant to be caught: OutOfMemoryError, StackOverflowError)
└── Exception
    ├── Checked (must be declared/caught: IOException, SQLException) — extends Exception directly
    └── Unchecked / RuntimeException (NullPointerException, IllegalArgumentException) — programmer errors
```

- **Checked exceptions** represent recoverable conditions external to the program (a missing file, a network failure) — the compiler forces you to handle or declare them (`throws`).
- **Unchecked exceptions** (`RuntimeException` and subclasses) usually indicate programming bugs — the compiler doesn't force handling.
- **Errors** represent serious problems (like running out of memory) that applications generally shouldn't try to catch/recover from.

**Design opinion often asked in interviews:** Many senior engineers prefer unchecked exceptions for business logic (e.g., Spring throws unchecked `DataAccessException` instead of checked `SQLException`) because checked exceptions force every layer in the call stack to declare or wrap them, hurting readability.

---

### Q20. What is try-with-resources, and why is it preferred over try-finally for closing resources?

Any object implementing `AutoCloseable` can be declared in a `try(...)` and will be **automatically closed**, in reverse order of declaration, even if an exception occurs — without writing explicit `finally` blocks.

```java
// Before Java 7:
BufferedReader br = null;
try {
    br = new BufferedReader(new FileReader("file.txt"));
    // use br
} finally {
    if (br != null) br.close();
}

// try-with-resources:
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    // use br
} // br.close() is called automatically, even on exception
```

It also correctly handles **suppressed exceptions** — if both the try block and the `close()` call throw, the close-time exception is attached as a "suppressed exception" rather than silently discarding the original one (a common bug with manual `finally` blocks).

---

### Q21. When should you create a custom exception?

Create a custom exception when you need to represent a **specific business error condition** that callers should handle differently from generic exceptions — e.g., `InsufficientBalanceException`, `UserNotFoundException`. This makes error handling explicit and self-documenting, and lets you attach extra context (error codes, relevant IDs).

```java
public class InsufficientBalanceException extends RuntimeException {
    private final double shortfall;
    public InsufficientBalanceException(String message, double shortfall) {
        super(message);
        this.shortfall = shortfall;
    }
    public double getShortfall() { return shortfall; }
}
```

Prefer extending `RuntimeException` unless you have a strong reason for callers to be forced to handle it (checked).

---

### Q22. What are some exception-handling anti-patterns to avoid?

1. **Swallowing exceptions** — `catch (Exception e) {}` with no logging/handling hides bugs.
2. **Catching `Exception` (or worse, `Throwable`) generically** instead of specific types — makes it easy to accidentally catch and mishandle unrelated errors.
3. **Using exceptions for normal control flow** — exceptions are relatively expensive (stack trace capture) and hurt readability when used instead of simple conditionals.
4. **Losing the original exception** — always chain the cause: `throw new ServiceException("failed", e);` instead of `throw new ServiceException("failed");`.
5. **Catching an exception just to rethrow it unchanged** — adds no value and clutters the stack trace.

## 1.4 Multithreading & Concurrency

### Q23. Thread vs. Runnable vs. Callable

- **`Thread`** — extending `Thread` directly ties you to single inheritance (Java doesn't allow extending two classes), and mixes "the task" with "the mechanism of running it."
- **`Runnable`** — a functional interface with `void run()`; preferred because you can implement it while still extending another class, and it decouples the task from the threading mechanism.
- **`Callable<V>`** — like `Runnable` but `call()` returns a value and can throw a checked exception. Used with `ExecutorService` to get a `Future<V>`.

```java
Runnable task = () -> System.out.println("Running in: " + Thread.currentThread().getName());
new Thread(task).start();

Callable<Integer> callableTask = () -> 42;
ExecutorService executor = Executors.newSingleThreadExecutor();
Future<Integer> future = executor.submit(callableTask);
System.out.println(future.get()); // 42
executor.shutdown();
```

---

### Q24. Explain the `synchronized` keyword. Method-level vs. block-level.

`synchronized` ensures **mutual exclusion** — only one thread can hold a given object's **intrinsic lock (monitor)** at a time, preventing race conditions on shared state.

- **Method-level**: `synchronized void increment()` — locks on `this` (or the `Class` object for static methods).
- **Block-level**: `synchronized(lockObject) { ... }` — locks on a specific object, letting you narrow the critical section for better performance and lock on something other than `this`.

```java
class Counter {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {   // only the critical section is locked
            count++;
        }
    }
}
```

Block-level synchronization is generally preferred — it minimizes the time the lock is held, reducing contention.

---

### Q25. What does `volatile` do, and how is it different from `synchronized`?

`volatile` guarantees:
1. **Visibility** — writes to a volatile variable by one thread are immediately visible to other threads (reads always go to main memory, not a thread-local CPU cache).
2. **No instruction reordering** around the volatile variable (via memory barriers).

It does **not** provide atomicity for compound operations (`count++` on a volatile `int` is still a race condition — read, increment, write are three separate steps).

| | `volatile` | `synchronized` |
|---|---|---|
| Visibility | ✅ | ✅ |
| Atomicity (compound ops) | ❌ | ✅ |
| Mutual exclusion | ❌ | ✅ |
| Performance | Lightweight | Heavier (lock acquisition) |

**Use `volatile`** for simple flags (`private volatile boolean running = true;`). **Use `synchronized`/`AtomicInteger`** for compound read-modify-write operations.

---

### Q26. Explain `wait()`, `notify()`, `notifyAll()` with a producer-consumer example.

These are methods on `Object`, used for **inter-thread communication**, and must be called from within a `synchronized` block on the same monitor.

- `wait()` — releases the lock and suspends the thread until notified.
- `notify()` — wakes up **one** waiting thread.
- `notifyAll()` — wakes up **all** waiting threads (safer default — avoids missed signals when multiple different conditions are being waited on).

```java
class SharedQueue {
    private final Queue<Integer> queue = new LinkedList<>();
    private final int CAPACITY = 5;

    public synchronized void produce(int value) throws InterruptedException {
        while (queue.size() == CAPACITY) {
            wait(); // releases lock, waits until consumer signals space is available
        }
        queue.add(value);
        notifyAll(); // wake up any waiting consumers
    }

    public synchronized int consume() throws InterruptedException {
        while (queue.isEmpty()) {
            wait();
        }
        int value = queue.poll();
        notifyAll(); // wake up any waiting producers
        return value;
    }
}
```

In modern code, `BlockingQueue` (e.g., `LinkedBlockingQueue`) handles this internally and is almost always preferred over hand-rolled `wait`/`notify`.

---

### Q27. What is `ExecutorService`, and what thread pool types does Java provide?

`ExecutorService` decouples task submission from thread management — you submit `Runnable`/`Callable` tasks to a pool instead of manually creating `Thread` objects.

| Factory Method | Behavior |
|---|---|
| `newFixedThreadPool(n)` | Fixed number of threads; extra tasks queue up |
| `newCachedThreadPool()` | Creates threads as needed, reuses idle ones, kills after 60s idle — risky under heavy load (unbounded growth) |
| `newSingleThreadExecutor()` | One thread, tasks run sequentially |
| `newScheduledThreadPool(n)` | For delayed/periodic tasks |

```java
ExecutorService pool = Executors.newFixedThreadPool(4);
for (int i = 0; i < 10; i++) {
    int taskId = i;
    pool.submit(() -> System.out.println("Task " + taskId + " on " + Thread.currentThread().getName()));
}
pool.shutdown();
```

**Senior-level note:** In production, prefer building a `ThreadPoolExecutor` explicitly (controlling core/max pool size, queue type, and a `RejectedExecutionHandler`) over the `Executors` convenience methods, which have known pitfalls (e.g., `newFixedThreadPool` uses an unbounded queue, which can hide backpressure problems and cause OOM under sustained overload).

---

### Q28. Name some concurrent collections and when you'd use them.

- **`ConcurrentHashMap`** — thread-safe map with high read/write concurrency (see Q14).
- **`CopyOnWriteArrayList`** — every mutation copies the entire underlying array. Reads never block and never see `ConcurrentModificationException`. Best for **read-heavy, write-rare** scenarios (e.g., a list of event listeners).
- **`BlockingQueue`** (`LinkedBlockingQueue`, `ArrayBlockingQueue`) — thread-safe queue with blocking `put()`/`take()`; the backbone of producer-consumer pipelines.
- **`ConcurrentSkipListMap`/`Set`** — a concurrent, sorted alternative to `TreeMap`/`TreeSet`.

---

### Q29. What is a deadlock? How do you prevent it?

A **deadlock** occurs when two or more threads are each waiting for a lock the other holds, so none can proceed.

```java
// Thread A: synchronized(lock1) { ... synchronized(lock2) { ... } }
// Thread B: synchronized(lock2) { ... synchronized(lock1) { ... } }
// If A holds lock1 and waits for lock2, while B holds lock2 and waits for lock1 -> deadlock
```

**Four conditions required (Coffman conditions):** mutual exclusion, hold-and-wait, no preemption, circular wait. Breaking any one prevents deadlock.

**Prevention strategies:**
1. **Lock ordering** — always acquire locks in a globally consistent order.
2. **Lock timeout** — use `tryLock(timeout)` instead of blocking indefinitely.
3. **Avoid nested locks** where possible; minimize the scope of locking.
4. **Use higher-level concurrency utilities** (`java.util.concurrent`) instead of hand-rolled locking.

---

### Q30. What is the Java Memory Model (JMM), and what does "happens-before" mean?

The JMM defines how threads interact through memory — specifically, when a write by one thread is guaranteed to be visible to a read by another. Without such guarantees, compilers/CPUs are free to reorder or cache operations for performance, which can break multi-threaded correctness.

**"Happens-before"** is a partial ordering the JMM guarantees in specific cases, e.g.:
- A write to a `volatile` variable happens-before every subsequent read of that variable.
- Releasing a lock happens-before a subsequent acquisition of the same lock.
- All actions in a thread happen-before that thread's `Thread.join()` returns in the joining thread.

If two actions aren't connected by a happens-before relationship, the JVM/CPU may reorder them, and there's no guarantee one thread sees the other's effects in the expected order — this is the theoretical basis for why `volatile`, `synchronized`, and the `java.util.concurrent` utilities are necessary rather than optional "best practice."

## 1.5 Java 8+ Features

### Q31. What are lambda expressions and functional interfaces?

A **functional interface** is an interface with exactly one abstract method (it can have default/static methods too). A **lambda expression** provides a compact way to implement one, without a verbose anonymous class.

```java
// Functional interface (built-in: java.util.function.Comparator)
Comparator<String> byLength = (a, b) -> a.length() - b.length();

// Before Java 8:
Comparator<String> old = new Comparator<String>() {
    public int compare(String a, String b) { return a.length() - b.length(); }
};
```

Common built-in functional interfaces: `Function<T,R>`, `Predicate<T>`, `Consumer<T>`, `Supplier<T>`, `BiFunction<T,U,R>`.

---

### Q32. Explain the Stream API — intermediate vs. terminal operations.

A `Stream` represents a pipeline of operations over a data source (collection, array, I/O). Streams are **lazy** — nothing executes until a terminal operation is invoked.

- **Intermediate operations** (return a new Stream, lazy): `filter()`, `map()`, `sorted()`, `distinct()`, `limit()`.
- **Terminal operations** (trigger execution, produce a result): `collect()`, `forEach()`, `reduce()`, `count()`, `anyMatch()`.

```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Dave", "Eve");

List<String> result = names.stream()
    .filter(n -> n.length() > 3)      // intermediate
    .map(String::toUpperCase)          // intermediate
    .sorted()                          // intermediate
    .collect(Collectors.toList());    // terminal -> triggers execution

// result: [ALICE, CHARLIE, DAVE]
```

**Common interview follow-up:** "Are streams always faster than loops?" — No. For small collections, the abstraction overhead can make streams *slower*. Parallel streams (`.parallelStream()`) help mainly for CPU-bound work on large datasets, and can hurt performance for small datasets or I/O-bound work due to thread coordination overhead.

---

### Q33. What problem does `Optional` solve, and how should it be used?

`Optional<T>` is a container that may or may not hold a value, designed to make the possibility of "no result" **explicit in the method signature** rather than relying on `null` (which is easy to forget to check, leading to `NullPointerException`).

```java
Optional<User> findUserById(String id) {
    User u = database.get(id);
    return Optional.ofNullable(u);
}

// Usage:
findUserById("123")
    .map(User::getEmail)
    .orElse("no-email@example.com");
```

**Best practices / common mistakes:**
- Don't use `Optional` for fields or method parameters — it's designed for **return types**.
- Don't call `.get()` without checking `.isPresent()` first — defeats the purpose; use `.orElse()`, `.orElseGet()`, `.orElseThrow()`, or `.map()`/`.ifPresent()` instead.
- Don't wrap collections in `Optional` — return an empty collection instead of `Optional<List<T>>`.

---

### Q34. Why were default and static methods added to interfaces in Java 8?

Before Java 8, adding a new method to an interface would **break every existing implementation** (they'd fail to compile until they implemented the new method). `default` methods let interface designers add new methods **with a default implementation**, so existing implementations keep working. This is exactly how the Collections framework added stream support (`Collection.stream()` is a `default` method) without breaking every custom `Collection` implementation ever written.

`static` methods on interfaces provide utility methods related to the interface without needing a separate helper class (e.g., `Comparator.comparing(...)`).

```java
interface Vehicle {
    void drive();
    default void honk() { System.out.println("Beep!"); } // existing implementers unaffected
    static Vehicle createDefault() { return () -> System.out.println("Driving default vehicle"); }
}
```

---

### Q35. What are method references, and what are the four types?

A method reference is shorthand for a lambda that just calls an existing method.

| Type | Syntax | Example |
|---|---|---|
| Static method | `Class::staticMethod` | `Integer::parseInt` |
| Instance method of a particular object | `obj::instanceMethod` | `System.out::println` |
| Instance method of an arbitrary object (of a particular type) | `Class::instanceMethod` | `String::toUpperCase` |
| Constructor reference | `Class::new` | `ArrayList::new` |

```java
List<String> names = List.of("bob", "alice");
names.stream().map(String::toUpperCase).forEach(System.out::println);
```

---

### Q36. Explain `CompletableFuture` and asynchronous composition.

`CompletableFuture<T>` represents a computation that will complete in the future, and — unlike the older `Future`, which only supports blocking `.get()` — supports **non-blocking composition** of async pipelines.

```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> fetchUserFromDb())         // runs async on a default/custom executor
    .thenApply(user -> user.getName())            // transform result when ready
    .thenApply(String::toUpperCase);

// Combining two independent futures:
CompletableFuture<Integer> priceFuture = CompletableFuture.supplyAsync(() -> getPrice());
CompletableFuture<Integer> taxFuture = CompletableFuture.supplyAsync(() -> getTax());
CompletableFuture<Integer> total = priceFuture.thenCombine(taxFuture, Integer::sum);

// Handling errors:
future.exceptionally(ex -> { log.error("failed", ex); return "fallback"; });
```

Key methods: `thenApply` (transform), `thenCompose` (flatMap — chain another async call), `thenCombine` (merge two independent futures), `allOf`/`anyOf` (wait for multiple futures), `exceptionally`/`handle` (error handling).

## 1.6 JVM Internals & Memory Management

### Q37. Describe the JVM architecture at a high level.

1. **ClassLoader Subsystem** — loads `.class` files (Bootstrap → Extension/Platform → Application classloader, in a delegation hierarchy), links (verify, prepare, resolve), and initializes classes.
2. **Runtime Data Areas:**
   - **Heap** — shared across all threads; stores objects. Divided into **Young Generation** (Eden + 2 Survivor spaces) and **Old Generation**.
   - **Stack** — one per thread; stores local variables, method call frames.
   - **Method Area / Metaspace** (Java 8+) — stores class metadata, constant pool, static variables.
   - **PC Registers** — one per thread, tracks the current executing instruction.
   - **Native Method Stacks** — for native (non-Java) code.
3. **Execution Engine** — the **Interpreter** (executes bytecode line by line), the **JIT (Just-In-Time) Compiler** (compiles hot methods to native machine code for speed), and the **Garbage Collector**.

---

### Q38. Explain generational Garbage Collection and the major GC algorithms.

Java's GC exploits the observation that **most objects die young**. The heap is split:

- **Young Generation** (Eden + Survivor spaces S0/S1) — new objects are allocated in Eden. A **Minor GC** runs frequently, is fast, and moves surviving objects between Survivor spaces, promoting long-lived objects to Old Gen after surviving several cycles.
- **Old Generation (Tenured)** — long-lived objects. A **Major/Full GC** here is much more expensive since it scans a bigger heap.

**Common collectors:**
| Collector | Characteristics |
|---|---|
| **Serial GC** | Single-threaded, stop-the-world — fine for small heaps/single-core |
| **Parallel GC** | Multi-threaded version of Serial — throughput-focused, still stop-the-world |
| **CMS** (deprecated in 9, removed in 14) | Concurrent mark-sweep — reduced pause times, no compaction |
| **G1 (Garbage-First)** | Default since Java 9. Divides heap into regions, prioritizes collecting regions with the most garbage first, balances throughput and low pause times |
| **ZGC / Shenandoah** | Ultra-low-latency collectors (sub-millisecond pauses), designed for very large heaps |

---

### Q39. How would you detect and fix a memory leak in a Java application?

Java is garbage-collected, but a "leak" still happens when objects are **unintentionally kept reachable** (referenced) even though the app no longer needs them, so the GC can never reclaim them.

**Common causes:**
- Static collections that grow indefinitely (e.g., a cache with no eviction policy).
- Unclosed resources (streams, connections) holding references.
- Listener/callback registrations that are never deregistered.
- Inner classes holding an implicit reference to their outer class, kept alive longer than expected.
- `ThreadLocal` values not cleaned up in thread-pool contexts.

**Detection approach:**
1. Monitor heap usage over time (via `jstat`, Actuator `/metrics`, or an APM tool) — a steadily climbing old-gen usage that never drops after GC is the classic symptom.
2. Take heap dumps (`jmap` or on `OutOfMemoryError`) at two points in time and diff them, or open a single dump in a tool like **Eclipse MAT** and look at the "dominator tree" / retained size to find what's holding the most memory.
3. Look for suspiciously large collections or a large number of instances of one class that shouldn't be growing.

**Fix:** Remove the unwanted reference — use bounded caches (e.g., `Caffeine`, `Guava Cache` with eviction), always close resources (try-with-resources), deregister listeners, and use weak references (`WeakHashMap`, `WeakReference`) where appropriate.

---

### Q40. How do you create a truly immutable class?

1. Declare the class `final` (prevent subclassing that could add mutability).
2. Make all fields `private final`.
3. Don't provide setters.
4. If a field is a mutable object (e.g., a `Date` or `List`), **defensively copy** it in the constructor and in any getter — otherwise, external code can still mutate the internal state through the reference.

```java
public final class ImmutablePoint {
    private final int x;
    private final int y;
    private final List<String> tags;

    public ImmutablePoint(int x, int y, List<String> tags) {
        this.x = x;
        this.y = y;
        this.tags = new ArrayList<>(tags); // defensive copy on the way in
    }

    public int getX() { return x; }
    public int getY() { return y; }
    public List<String> getTags() { return new ArrayList<>(tags); } // defensive copy on the way out
}
```

This is exactly how `String` stays immutable despite internally wrapping a `char[]`/`byte[]` — no method ever exposes that array directly, and it's never modified after construction.

---

# Part 2: Spring Boot

## 2.1 Spring Core Concepts

### Q1. Spring Framework vs. Spring Boot — what problem does Spring Boot solve?

**Spring Framework** provides IoC/DI, AOP, and a rich ecosystem (Spring MVC, Spring Data, Spring Security) — but classic Spring required extensive **XML/Java configuration**: manually wiring a `DispatcherServlet`, configuring a `DataSource`, choosing and configuring an embedded server, managing dependency versions, etc.

**Spring Boot** is built on top of Spring and eliminates that boilerplate via:
1. **Auto-configuration** — sensible defaults are configured automatically based on what's on the classpath.
2. **Starter dependencies** — e.g., `spring-boot-starter-web` pulls in a curated, compatible set of libraries (Spring MVC, embedded Tomcat, Jackson) with one line.
3. **Embedded servers** — no need to deploy a WAR to an external Tomcat; run `java -jar app.jar` and it's a standalone application.
4. **Production-ready features** — Actuator (health checks, metrics) out of the box.

In short: Spring Boot doesn't replace Spring — it's **convention-over-configuration** on top of it.

---

### Q2. Explain Inversion of Control (IoC) and Dependency Injection (DI).

**IoC** is a principle: instead of your code creating and managing its own dependencies, control is inverted — a **container** (the Spring `ApplicationContext`) creates objects and wires them together.

**DI** is the mechanism through which IoC is achieved — dependencies are "injected" into a class rather than the class constructing them itself.

```java
// Without DI - tightly coupled, hard to test
class OrderService {
    private PaymentGateway gateway = new StripeGateway(); // hardcoded
}

// With DI - loosely coupled
@Service
class OrderService {
    private final PaymentGateway gateway;
    public OrderService(PaymentGateway gateway) { // Spring injects the implementation
        this.gateway = gateway;
    }
}
```

**Benefits:** loose coupling, easier unit testing (inject a mock `PaymentGateway`), and centralized configuration of how objects are wired.

---

### Q3. `@Component` vs. `@Service` vs. `@Repository` vs. `@Controller` — what's the actual difference?

All four are **specializations of `@Component`** — meaning Spring treats them identically for the purpose of component scanning and bean registration. The difference is purely **semantic/documentation**, except for one:

- **`@Component`** — generic, stereotype for any Spring-managed bean.
- **`@Service`** — marks a class holding business logic (semantic clarity).
- **`@Repository`** — marks a data-access class. Additionally, Spring wraps it with **automatic exception translation** — JDBC/ORM-specific exceptions get translated into Spring's unified `DataAccessException` hierarchy.
- **`@Controller`** — marks a web layer class handling HTTP requests (used with `@RequestMapping` methods that typically return view names).
- **`@RestController`** — `@Controller` + `@ResponseBody` combined; every method's return value is serialized directly to the response body (JSON/XML).

---

### Q4. What are the types of Dependency Injection, and why is constructor injection generally preferred?

1. **Constructor injection** — dependencies passed via the constructor.
2. **Setter injection** — dependencies set via setter methods after object creation.
3. **Field injection** — `@Autowired` directly on a field.

```java
@Service
public class OrderService {
    private final PaymentGateway gateway;
    private final NotificationService notifier;

    // Constructor injection (recommended) - can even drop @Autowired
    // if there's a single constructor, Spring infers it since 4.3
    public OrderService(PaymentGateway gateway, NotificationService notifier) {
        this.gateway = gateway;
        this.notifier = notifier;
    }
}
```

**Why constructor injection wins:**
- Fields can be `final` → **immutability**, guaranteed to be fully initialized.
- Dependencies are **explicit and visible** in the constructor signature — no hidden required fields.
- Makes **unit testing trivial** — just call `new OrderService(mockGateway, mockNotifier)`, no need for a Spring context or reflection.
- Prevents circular dependency issues from being silently created — a circular dependency via constructor injection fails fast at startup, whereas field injection can mask it.

---

### Q5. How does `@Autowired` resolve which bean to inject? What do `@Qualifier` and `@Primary` do?

Spring resolves `@Autowired` **by type** first. If multiple beans of the same type exist, it then tries to match **by bean name** (matching the field/parameter name). If that's still ambiguous, it throws `NoUniqueBeanDefinitionException` — unless you disambiguate:

- **`@Qualifier("beanName")`** — explicitly specify which bean to inject, by name.
- **`@Primary`** — mark one implementation as the default choice when multiple candidates exist (used when you don't want to specify `@Qualifier` at every injection point).

```java
public interface PaymentGateway { void pay(); }

@Service("stripeGateway")
class StripeGateway implements PaymentGateway { public void pay() {} }

@Primary
@Service("paypalGateway")
class PaypalGateway implements PaymentGateway { public void pay() {} }

@Service
class OrderService {
    public OrderService(@Qualifier("stripeGateway") PaymentGateway gateway) { ... } // explicitly picks Stripe
}
```

---

### Q6. What are Spring bean scopes?

| Scope | Behavior |
|---|---|
| `singleton` (default) | One instance per Spring container — shared everywhere |
| `prototype` | A new instance every time the bean is requested |
| `request` | One instance per HTTP request (web apps only) |
| `session` | One instance per HTTP session (web apps only) |
| `application` | One instance per `ServletContext` |

**Common gotcha:** injecting a `prototype`-scoped bean into a `singleton` bean directly only resolves it *once*, at startup — you get the same prototype instance forever after. To get a fresh prototype bean each time it's needed inside a singleton, you need a `ObjectFactory<T>`, `@Lookup` method injection, or a scoped proxy.

---

### Q7. Describe the Spring Bean lifecycle.

1. Bean **instantiation** (constructor called).
2. **Dependency injection** (properties/setters/constructor args populated).
3. `@PostConstruct` method invoked (or `InitializingBean.afterPropertiesSet()`).
4. Bean is **ready for use**.
5. On container shutdown: `@PreDestroy` invoked (or `DisposableBean.destroy()`).

```java
@Component
class DatabaseConnectionPool {
    @PostConstruct
    public void init() {
        System.out.println("Pool initialized after DI is complete");
    }
    @PreDestroy
    public void cleanup() {
        System.out.println("Closing all connections before shutdown");
    }
}
```

Also relevant: `BeanPostProcessor` implementations can hook in *before and after* initialization for every bean in the container (this is how much of Spring's own "magic" — like AOP proxy creation — is implemented).

---

### Q8. What does `@SpringBootApplication` actually do?

It's a convenience meta-annotation bundling three annotations:

```java
@SpringBootConfiguration  // = @Configuration; marks this class as a source of bean definitions
@EnableAutoConfiguration  // triggers Spring Boot's auto-configuration mechanism
@ComponentScan            // scans the current package and sub-packages for @Component/@Service/etc.
public @interface SpringBootApplication { ... }
```

This is why the class annotated `@SpringBootApplication` is conventionally placed in the **root package** — `@ComponentScan` without arguments scans that package and everything below it, so anything outside won't be picked up automatically.

---

### Q9. How does Spring Boot auto-configuration actually work?

1. When the app starts, `@EnableAutoConfiguration` triggers Spring Boot to look at `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (older versions: `spring.factories`) across all JARs on the classpath — a list of candidate `@Configuration` classes.
2. Each auto-configuration class is annotated with **conditional annotations** like `@ConditionalOnClass` (only apply if a certain class is on the classpath), `@ConditionalOnMissingBean` (only apply if the developer hasn't already defined their own bean of that type), `@ConditionalOnProperty`, etc.
3. Example: if `spring-boot-starter-data-jpa` is on the classpath and no `DataSource` bean already exists, `DataSourceAutoConfiguration` kicks in and configures one automatically from `application.properties`.

**This is the key insight for interviews:** auto-configuration is just regular `@Configuration` classes with conditional guards — and **it always backs off if you define your own bean**, which is why you can override any piece of it simply by declaring your own `@Bean`.

## 2.2 Spring MVC & REST APIs

### Q10. `@RestController` vs. `@Controller`

`@Controller` is used for traditional server-rendered views — methods typically return a **view name** (e.g., a Thymeleaf template) that gets resolved and rendered. To return raw data (JSON) from a `@Controller` method, you'd need `@ResponseBody` on each method.

`@RestController` = `@Controller` + `@ResponseBody` applied to every method automatically — the return value is serialized (usually to JSON via Jackson) and written directly to the HTTP response body. This is what virtually all REST APIs use.

---

### Q11. Describe the Spring MVC request flow (the role of `DispatcherServlet`).

Spring MVC follows the **Front Controller pattern**:

1. Every HTTP request first hits the **`DispatcherServlet`** (a single servlet registered for all requests).
2. `DispatcherServlet` consults a **`HandlerMapping`** to find which controller method should handle this URL.
3. It invokes the controller method (through a `HandlerAdapter`), possibly resolving `@RequestBody`, `@PathVariable`, `@RequestParam` arguments via `HandlerMethodArgumentResolvers`.
4. The controller returns a value; for `@RestController`, a `HttpMessageConverter` (e.g., `MappingJackson2HttpMessageConverter`) serializes it to JSON directly. For `@Controller`, a `ViewResolver` maps the returned view name to an actual template, which gets rendered.
5. The response is written back to the client.

Any unhandled exception during this flow gets routed to an applicable `@ExceptionHandler`/`@ControllerAdvice` before falling back to a default error page.

---

### Q12. Show the common request-mapping annotations.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) { ... }

    @GetMapping
    public List<User> searchUsers(@RequestParam(required = false) String name) { ... }

    @PostMapping
    public User createUser(@RequestBody @Valid UserRequest request) { ... }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody UserRequest request) { ... }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

- `@PathVariable` — extracts a value from the URL path (`/users/42` → `id=42`).
- `@RequestParam` — extracts a query parameter (`?name=John`).
- `@RequestBody` — deserializes the JSON request body into a Java object.

---

### Q13. How do you handle exceptions globally in a Spring Boot REST API?

Use **`@ControllerAdvice`** (or `@RestControllerAdvice` for REST APIs) combined with **`@ExceptionHandler`** to centralize error handling instead of repeating try-catch in every controller.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("USER_NOT_FOUND", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(error);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult().getFieldErrors().get(0).getDefaultMessage();
        return ResponseEntity.badRequest().body(new ErrorResponse("VALIDATION_ERROR", message));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        return ResponseEntity.internalServerError().body(new ErrorResponse("INTERNAL_ERROR", "Something went wrong"));
    }
}
```

This keeps controllers clean (no repetitive try-catch), ensures **consistent error response shape** across the whole API, and lets you map specific exceptions to specific HTTP status codes in one place.

---

### Q14. How does request validation work with `@Valid`?

Spring Boot integrates **Bean Validation (JSR-380 / Hibernate Validator)**. Annotate the request DTO's fields with constraints, then add `@Valid` in the controller method — Spring validates the incoming object before the method body executes, and throws `MethodArgumentNotValidException` on failure (typically caught by a `@ControllerAdvice`, see Q13).

```java
public class UserRequest {
    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Invalid email format")
    private String email;

    @Min(18)
    private int age;
}

@PostMapping
public User createUser(@Valid @RequestBody UserRequest request) {
    // if validation fails, this method body never executes
    return userService.create(request);
}
```

## 2.3 Spring Data JPA

### Q15. Explain the basic JPA entity annotations.

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "full_name", nullable = false, length = 100)
    private String name;

    @Column(unique = true)
    private String email;

    // getters, setters, constructors
}
```

- `@Entity` — marks a class as a JPA-managed entity, mapped to a database table.
- `@Table` — customizes the table name (optional; defaults to the class name).
- `@Id` — marks the primary key field.
- `@GeneratedValue` — how the ID is generated: `IDENTITY` (DB auto-increment), `SEQUENCE`, `AUTO`, `TABLE`.
- `@Column` — customizes column mapping (name, nullability, length, uniqueness).

---

### Q16. `JpaRepository` vs. `CrudRepository` vs. `PagingAndSortingRepository`

```
Repository (marker interface)
└── CrudRepository<T, ID>          — basic save/findById/findAll/delete
    └── PagingAndSortingRepository — adds findAll(Pageable), findAll(Sort)
        └── JpaRepository          — adds JPA-specific batch ops, flush(), getById(), Query by Example
```

In practice, you almost always extend **`JpaRepository`** directly — it includes everything the parent interfaces offer, plus JPA-specific extras like `saveAndFlush()` and support for pagination out of the box.

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);          // derived query
    List<User> findByNameContainingIgnoreCase(String name);
    Page<User> findByAgeGreaterThan(int age, Pageable pageable);
}
```

---

### Q17. Explain entity relationships and the difference between LAZY and EAGER fetching.

```java
@Entity
public class Order {
    @Id @GeneratedValue
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<OrderItem> items;
}
```

- **`@ManyToOne`** / **`@OneToOne`** default to **EAGER** fetching.
- **`@OneToMany`** / **`@ManyToMany`** default to **LAZY** fetching.
- **`LAZY`** — the related entity/collection is fetched **only when accessed**, via a proxy. Better for performance, but throws `LazyInitializationException` if accessed after the Hibernate session/transaction has closed.
- **`EAGER`** — fetched immediately along with the parent, in the same query (or a follow-up query) — simpler but can silently pull in much more data than needed.

**Best practice:** default to `LAZY` for almost everything, and fetch what you need explicitly (via `JOIN FETCH` or `@EntityGraph`) rather than relying on `EAGER`, which makes performance unpredictable as the object graph grows.

---

### Q18. What is the N+1 query problem, and how do you fix it?

**The problem:** you fetch a list of N parent entities with one query, then — because a related collection/entity is `LAZY` — accessing that relation for *each* parent triggers a **separate query per parent**, resulting in **1 + N queries** total instead of 1 or 2.

```java
List<Order> orders = orderRepository.findAll();      // Query 1
for (Order order : orders) {
    order.getUser().getName();                        // Triggers 1 query PER order (N queries!)
}
```

**Fixes:**
1. **`JOIN FETCH`** in a JPQL query — forces eager loading for that specific query only:
```java
@Query("SELECT o FROM Order o JOIN FETCH o.user")
List<Order> findAllWithUser();
```
2. **`@EntityGraph`** — declaratively specify which associations to fetch eagerly for a given repository method.
3. Enable **Hibernate batch fetching** (`hibernate.default_batch_fetch_size`) so lazy loads for multiple parents get batched into fewer queries instead of one-per-entity.

---

### Q19. How do Spring Data derived query methods and `@Query` work?

Spring Data JPA can generate queries just from a method name, by parsing keywords like `findBy`, `And`, `Or`, `GreaterThan`, `OrderBy`, `ContainingIgnoreCase`:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByNameAndAgeGreaterThan(String name, int age);
    List<User> findByEmailOrderByCreatedAtDesc(String email);
    boolean existsByEmail(String email);
}
```

For anything too complex for a derived name (joins, aggregations, or when you just want explicit control), use `@Query`:

```java
@Query("SELECT u FROM User u WHERE u.age > :minAge AND u.status = :status")
List<User> findActiveUsersOlderThan(@Param("minAge") int minAge, @Param("status") String status);

@Query(value = "SELECT * FROM users WHERE email = :email", nativeQuery = true)
Optional<User> findByEmailNative(@Param("email") String email);
```

## 2.4 Spring Security

### Q20. Authentication vs. Authorization

- **Authentication** — verifying **who you are** (e.g., checking a username/password, validating a JWT).
- **Authorization** — verifying **what you're allowed to do** once identified (e.g., "does this user have the `ADMIN` role to access this endpoint?").

Authentication always happens first; authorization decisions are made based on the authenticated identity.

---

### Q21. Describe the Spring Security filter chain at a high level.

Spring Security works primarily through a **chain of servlet filters** that sit in front of `DispatcherServlet`. Key filters (order matters):

1. **`SecurityContextPersistenceFilter`** — restores the `SecurityContext` (who's logged in) for the current request.
2. **Authentication filters** (e.g., `UsernamePasswordAuthenticationFilter`, or a custom JWT filter) — attempt to authenticate the request.
3. **`ExceptionTranslationFilter`** — catches `AccessDeniedException`/`AuthenticationException` and converts them into appropriate HTTP responses (401/403) or redirects.
4. **`FilterSecurityInterceptor`** / **`AuthorizationFilter`** (newer versions) — makes the final authorization decision, checking the authenticated user's authorities against the configured access rules for the requested URL.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/public/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .sessionManagement(sm -> sm.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }
}
```

---

### Q22. Walk through JWT-based authentication in a Spring Boot app.

1. **Login**: client sends credentials to `/login`. Server verifies them (e.g., against `UserDetailsService` + `PasswordEncoder`), then generates a signed **JWT** containing claims (user ID, roles, expiry) and returns it.
2. **Subsequent requests**: the client sends the JWT in the `Authorization: Bearer <token>` header.
3. A **custom filter** (`OncePerRequestFilter`) intercepts each request, extracts the token, **validates its signature and expiry**, and if valid, loads the user's authorities and sets the `SecurityContext` for that request — all **without a server-side session** (stateless).
4. Downstream authorization checks (`hasRole`, method security) use that `SecurityContext`.

```java
public class JwtAuthFilter extends OncePerRequestFilter {
    protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain chain)
            throws ServletException, IOException {
        String token = extractTokenFromHeader(req);
        if (token != null && jwtUtil.isValid(token)) {
            String username = jwtUtil.extractUsername(token);
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);
            var auth = new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
            SecurityContextHolder.getContext().setAuthentication(auth);
        }
        chain.doFilter(req, res);
    }
}
```

**Why stateless JWT is popular for microservices:** no server-side session store needed, so any service instance can validate any request independently — a good fit for horizontally-scaled, load-balanced deployments.

## 2.5 Transactions, AOP & Caching

### Q23. How does `@Transactional` work internally? What are propagation and isolation levels?

`@Transactional` is implemented via a **Spring AOP proxy** — when you call a `@Transactional` method, you're actually calling the proxy, which starts a transaction, invokes your real method, and commits/rolls back based on whether an exception was thrown (by default, only **unchecked** exceptions trigger a rollback).

**Propagation** — how a transaction relates to an existing one:
| Propagation | Behavior |
|---|---|
| `REQUIRED` (default) | Join existing transaction, or create a new one if none exists |
| `REQUIRES_NEW` | Always start a brand-new transaction, suspending any existing one |
| `NESTED` | Nested transaction with its own savepoint — can roll back independently of the outer one |
| `SUPPORTS` | Join if one exists, otherwise run without a transaction |

**Isolation levels** (standard SQL, weakest to strongest): `READ_UNCOMMITTED` → `READ_COMMITTED` → `REPEATABLE_READ` → `SERIALIZABLE`. Higher isolation prevents more concurrency anomalies (dirty reads, non-repeatable reads, phantom reads) at the cost of more locking/lower throughput. Spring's default typically defers to the underlying database's default (often `READ_COMMITTED` for most RDBMSs).

```java
@Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.READ_COMMITTED, rollbackFor = Exception.class)
public void transferFunds(Long fromId, Long toId, BigDecimal amount) {
    accountRepo.debit(fromId, amount);
    accountRepo.credit(toId, amount);
    // if any exception is thrown, both operations roll back together
}
```

---

### Q24. What's the classic "self-invocation" pitfall with `@Transactional` (and Spring AOP generally)?

Since `@Transactional` relies on a **proxy** wrapping the bean, calling an `@Transactional` method **from another method in the same class** bypasses the proxy entirely — you're calling `this.method()` directly, not going through the proxy, so the transactional behavior (and any other AOP advice, like `@Cacheable` or `@Async`) is silently skipped.

```java
@Service
public class OrderService {
    public void placeOrder() {
        processPayment(); // BYPASSES the proxy - @Transactional has NO effect here!
    }

    @Transactional
    public void processPayment() { ... }
}
```

**Fixes:** move `processPayment()` into a separate bean/service and inject it, or self-inject the proxy (`@Autowired private OrderService self;` and call `self.processPayment()`), or use `AopContext.currentProxy()` (with `exposeProxy=true`).

---

### Q25. Explain Spring AOP — `@Aspect`, `@Before`, `@After`, `@Around`, pointcuts.

**AOP (Aspect-Oriented Programming)** lets you modularize **cross-cutting concerns** (logging, security checks, transactions, caching) that would otherwise be scattered across many classes, by defining them once as an "aspect" and applying them declaratively.

```java
@Aspect
@Component
public class LoggingAspect {

    @Pointcut("execution(* com.example.service.*.*(..))") // matches all methods in the service package
    public void serviceMethods() {}

    @Before("serviceMethods()")
    public void logBefore(JoinPoint jp) {
        System.out.println("Entering: " + jp.getSignature());
    }

    @AfterThrowing(pointcut = "serviceMethods()", throwing = "ex")
    public void logException(JoinPoint jp, Exception ex) {
        System.out.println("Exception in " + jp.getSignature() + ": " + ex.getMessage());
    }

    @Around("serviceMethods()")
    public Object logExecutionTime(ProceedingJoinPoint pjp) throws Throwable {
        long start = System.currentTimeMillis();
        Object result = pjp.proceed(); // actually invokes the target method
        System.out.println(pjp.getSignature() + " took " + (System.currentTimeMillis() - start) + "ms");
        return result;
    }
}
```

- **`@Before`** — runs before the matched method.
- **`@After`** — runs after (regardless of outcome).
- **`@AfterReturning`** — runs after successful return.
- **`@AfterThrowing`** — runs if an exception propagates out.
- **`@Around`** — wraps the method entirely; must explicitly call `proceed()` — the most powerful, since it can modify arguments, skip the call, or alter the return value.

**Note:** Spring AOP (unlike full AspectJ) only works on **Spring-managed beans** and only intercepts calls that come **through the proxy** — which is exactly why the self-invocation issue in Q24 occurs.

---

### Q26. How do you implement caching in Spring Boot?

```java
@Configuration
@EnableCaching
public class CacheConfig { }

@Service
public class ProductService {

    @Cacheable(value = "products", key = "#id")
    public Product getProduct(Long id) {
        return productRepository.findById(id).orElseThrow(); // only runs on a cache miss
    }

    @CachePut(value = "products", key = "#product.id")
    public Product updateProduct(Product product) {
        return productRepository.save(product); // always runs, and updates the cache
    }

    @CacheEvict(value = "products", key = "#id")
    public void deleteProduct(Long id) {
        productRepository.deleteById(id); // removes the entry from the cache
    }
}
```

- **`@Cacheable`** — checks the cache first; only invokes the method (and stores the result) on a miss.
- **`@CachePut`** — always runs the method, and updates the cache with the result.
- **`@CacheEvict`** — removes an entry (or, with `allEntries=true`, clears the whole cache).

By default, Spring Boot uses a simple in-memory `ConcurrentHashMap`-based cache — fine for a single instance, but for production/multi-instance deployments, you'd back it with **Redis** or a similar distributed cache (via the `spring-boot-starter-cache` + `spring-boot-starter-data-redis` starters) so all instances share the same cache.

## 2.6 Microservices with Spring Cloud

### Q27. What are the main challenges of a microservices architecture, and how does Spring Cloud address them?

Breaking a monolith into microservices introduces distributed-systems problems that didn't exist before: how do services **find** each other, how do they handle a **downstream failure gracefully**, how is configuration managed **across many services**, and how do you trace a request across service boundaries?

Spring Cloud provides building blocks for each:
| Challenge | Spring Cloud Solution |
|---|---|
| Service discovery | Eureka / Consul |
| Client-side load balancing | Spring Cloud LoadBalancer |
| Centralized configuration | Spring Cloud Config Server |
| Resilience (fallback on failure) | Resilience4j |
| Single entry point / routing | Spring Cloud Gateway |
| Distributed tracing | Spring Cloud Sleuth + Zipkin/Micrometer Tracing |

---

### Q28. Explain service discovery with Eureka.

In a dynamically scaled system, service instances start/stop and their IPs change constantly — hardcoding URLs doesn't work. **Eureka** solves this:

1. Each microservice (a **Eureka Client**) registers itself with the **Eureka Server** on startup, sending periodic heartbeats to stay registered.
2. When Service A wants to call Service B, instead of a hardcoded URL, it asks Eureka (or a client-side cache of the registry) for available instances of "service-b."
3. A **client-side load balancer** then picks one instance (round-robin, etc.) to send the request to.
4. If an instance stops sending heartbeats, Eureka eventually evicts it from the registry, so traffic naturally stops being routed there.

```java
@EnableEurekaServer  // on the Eureka server application
public class EurekaServerApplication { }

@EnableDiscoveryClient // on each microservice
public class OrderServiceApplication { }
```

```java
@Service
public class OrderService {
    @Autowired
    private RestTemplate restTemplate; // configured with @LoadBalanced

    public Product getProduct(Long id) {
        return restTemplate.getForObject("http://product-service/api/products/" + id, Product.class);
        // "product-service" is resolved via Eureka + load balancer, not a hardcoded host
    }
}
```

---

### Q29. What is Spring Cloud Gateway, and why use an API Gateway in microservices?

An **API Gateway** is a single entry point that sits in front of all microservices, so clients don't need to know about (or talk directly to) each individual service. **Spring Cloud Gateway** provides:

- **Routing** — forward `/orders/**` to the order service, `/products/**` to the product service, etc.
- **Cross-cutting concerns in one place** — authentication, rate limiting, request logging, CORS — instead of duplicating them in every microservice.
- **Load balancing** integrated with service discovery.

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://order-service          # lb:// = load-balanced via service discovery
          predicates:
            - Path=/api/orders/**
          filters:
            - name: RequestRateLimiter
```

---

### Q30. Explain the Circuit Breaker pattern and how Resilience4j implements it.

If Service A calls Service B and B is slow/failing, without protection, A's threads pile up waiting on B — potentially exhausting A's own resources and causing a **cascading failure** across the system.

A **Circuit Breaker** wraps calls to a potentially-failing dependency, monitors the failure rate, and has three states:
- **CLOSED** — calls go through normally; failures are counted.
- **OPEN** — once the failure rate crosses a threshold, the circuit "trips" — calls **fail fast immediately** (or go to a fallback) without even attempting the real call, for a configured wait duration.
- **HALF_OPEN** — after the wait duration, a limited number of test calls are allowed through; if they succeed, the circuit closes again, otherwise it reopens.

```java
@Service
public class ProductClient {

    @CircuitBreaker(name = "productService", fallbackMethod = "getProductFallback")
    public Product getProduct(Long id) {
        return restTemplate.getForObject("http://product-service/api/products/" + id, Product.class);
    }

    public Product getProductFallback(Long id, Exception ex) {
        return new Product(id, "Unavailable", BigDecimal.ZERO); // graceful degradation
    }
}
```

```yaml
resilience4j:
  circuitbreaker:
    instances:
      productService:
        failure-rate-threshold: 50
        wait-duration-in-open-state: 10s
        sliding-window-size: 10
```

## 2.7 Testing

### Q31. What are the main Spring Boot testing annotations, and when do you use each?

| Annotation | Scope | Use case |
|---|---|---|
| `@SpringBootTest` | Loads the **full application context** | Integration tests that need the real (or near-real) wiring |
| `@WebMvcTest(Controller.class)` | Loads **only** the web layer (controllers, filters) | Testing controllers in isolation, with mocked service layer |
| `@DataJpaTest` | Loads **only** JPA-related beans, with an in-memory DB by default | Testing repositories |
| `@Mock` / `@InjectMocks` (Mockito) | No Spring context at all | Pure unit tests |

```java
// Pure unit test - fast, no Spring context
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {
    @Mock private PaymentGateway paymentGateway;
    @InjectMocks private OrderService orderService;

    @Test
    void shouldProcessPaymentSuccessfully() {
        when(paymentGateway.charge(any())).thenReturn(true);
        assertTrue(orderService.placeOrder(new Order()));
        verify(paymentGateway, times(1)).charge(any());
    }
}

// Controller test - web layer only, mocked service
@WebMvcTest(OrderController.class)
class OrderControllerTest {
    @Autowired private MockMvc mockMvc;
    @MockBean private OrderService orderService;

    @Test
    void shouldReturn200ForValidOrder() throws Exception {
        when(orderService.getOrder(1L)).thenReturn(new Order(1L, "PENDING"));
        mockMvc.perform(get("/api/orders/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.status").value("PENDING"));
    }
}
```

**Testing pyramid guidance (commonly asked):** favor many fast unit tests (mocked dependencies) → fewer `@WebMvcTest`/`@DataJpaTest` slice tests → a small number of full `@SpringBootTest` integration tests, since full-context tests are slow and should cover critical end-to-end paths, not every edge case.

---

## 2.8 Hibernate (Deep Dive)

### Q32. What is Hibernate, and how does it relate to JPA?

**JPA (Java Persistence API)** is just a **specification** — a set of interfaces (`EntityManager`, `@Entity`, `@Id`, etc.) defining how Java objects should be mapped to relational tables, with no implementation of its own.

**Hibernate** is the most widely used **implementation** of that specification (other implementations include EclipseLink). When you use `spring-boot-starter-data-jpa`, you're coding against the JPA interfaces, but **Hibernate is doing the actual work underneath** — generating SQL, managing the session, caching, etc. Hibernate also offers some extra features beyond the JPA spec (like its own native `Session` API, `@NaturalId`, and extra caching controls) for when JPA alone isn't enough.

---

### Q33. Session vs. SessionFactory — and is either thread-safe?

- **`SessionFactory`** — heavyweight, created **once per application** (typically once per database), **immutable and thread-safe**. It's responsible for creating `Session` objects and holds the second-level cache.
- **`Session`** — lightweight, represents a **single unit of work** with the database (roughly, one per request/transaction in a web app), wraps a JDBC connection, and holds the **first-level cache**. A `Session` is **NOT thread-safe** — it must never be shared across threads.

```java
SessionFactory factory = new Configuration().configure().buildSessionFactory(); // once, at startup
Session session = factory.openSession(); // once per unit of work
Transaction tx = session.beginTransaction();
// ... do work ...
tx.commit();
session.close();
```

In a Spring Boot app, you rarely touch this directly — Spring manages a `Session` (via `EntityManager`) per transaction automatically, but understanding this underlying model is exactly what interviewers probe for.

---

### Q34. Explain Hibernate's entity lifecycle states.

| State | Meaning |
|---|---|
| **Transient** | A plain new object (`new User()`), not associated with any Session, no corresponding database row |
| **Persistent** | Associated with an active Session; any changes are tracked and auto-synced to the DB (dirty checking) |
| **Detached** | Was persistent, but its Session has since closed; changes are no longer tracked automatically |
| **Removed** | Marked for deletion within the current transaction (after calling `remove()`/`delete()`) |

```java
User user = new User("Alice");     // Transient
session.persist(user);             // now Persistent - tracked by this session
session.close();                   // now Detached - no longer tracked
session2.merge(user);              // re-attached / merged back into a new Persistent instance
```

---

### Q35. `get()` vs. `load()` — what's the practical difference?

Both fetch an entity by primary key, but:
- **`get()`** — hits the database **immediately**; returns `null` if not found.
- **`load()`** — returns a **lazy proxy immediately without hitting the DB**; the actual query only fires when you access a field on the proxy. If the row doesn't exist, it throws `ObjectNotFoundException` — but only **when the proxy is accessed**, not when `load()` is called.

```java
User u1 = session.get(User.class, 1L);   // SELECT fires now; u1 could be null
User u2 = session.load(User.class, 1L);  // no SELECT yet - just a proxy
u2.getName();                            // SELECT fires NOW; throws if row #1 doesn't exist
```

**When to use `load()`:** when you just need a reference to set up a relationship (e.g., `order.setUser(session.load(User.class, userId))`) and don't actually need the user's fields — avoids an unnecessary query. Modern JPA equivalent: `getReference()` vs. `find()`.

---

### Q36. `save()`/`persist()` and `update()`/`merge()` — how are they different?

| | `save()` (Hibernate-only) | `persist()` (JPA) | `update()` (Hibernate-only) | `merge()` (JPA) |
|---|---|---|---|---|
| Purpose | Insert a new transient entity | Insert a new transient entity | Reattach a **detached** entity, assuming it exists | Merge a detached entity's state into a persistent copy, returns that copy |
| Return value | Returns the generated ID | `void` | `void` | Returns the merged (now-persistent) entity |
| Safe on already-persistent entity? | Can cause issues/duplicate identifiers | No-op if already persistent | N/A (for detached only) | Yes — safe to call on transient, detached, or persistent |

**Practical guidance:** prefer the JPA-standard `persist()`/`merge()` over Hibernate's own `save()`/`update()` for portability — and **always use `merge()`, never `update()`,** when you're not sure whether the entity might already be persistent (e.g., after coming back from a web form), since `update()` can throw `NonUniqueObjectException` if a persistent instance with the same ID already exists in the session.

---

### Q37. Explain Hibernate's caching layers: first-level, second-level, and query cache.

1. **First-level cache (Session cache)** — scoped to a single `Session`, **enabled by default, cannot be disabled**. If you `get()` the same entity twice in one session, the second call returns the cached instance with **zero SQL** issued.
2. **Second-level cache (SessionFactory cache)** — scoped across **all sessions**, application-wide, **disabled by default** — must be explicitly configured with a provider like **Ehcache**, **Caffeine**, or **Hazelcast**. Useful for reference/lookup data that rarely changes and is read often (e.g., a "countries" table).
3. **Query cache** — caches the **result set of a specific query** (the list of matching entity IDs), separate from the second-level cache which stores the entity data itself; needs both to be enabled together to be useful, and is easy to misuse (any write to the underlying table invalidates cached queries against it, which can hurt more than it helps for frequently-written tables).

```java
@Entity
@Cacheable
@org.hibernate.annotations.Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class Country {
    @Id private Long id;
    private String name;
}
```

**Interview tip:** be ready to explain *why* the second-level cache is risky in a multi-instance (horizontally scaled) deployment — each instance would have its own local cache unless you use a **distributed** cache provider, which can lead to one instance serving stale data after another instance updates a row directly in the DB.

---

### Q38. What is "dirty checking," and how does Hibernate implement it?

When an entity is loaded into a `Session`, Hibernate keeps a snapshot of its original field values. At **flush time** (explicit `flush()`, or automatically before a query/commit), Hibernate compares the entity's **current** in-memory state against that snapshot — any field that changed triggers an `UPDATE` statement, **without you ever calling `save()` or `update()` explicitly**.

```java
@Transactional
public void raiseSalary(Long employeeId, double amount) {
    Employee emp = employeeRepository.findById(employeeId).orElseThrow(); // now Persistent
    emp.setSalary(emp.getSalary() + amount); // just a plain setter call!
    // No explicit save() call needed - Hibernate's dirty checking detects the
    // change and issues an UPDATE automatically when the transaction commits.
}
```

This is convenient but also a common source of **surprise production bugs** — accidentally mutating a persistent entity (e.g., inside a loop building a report) can silently trigger unwanted `UPDATE` statements.

---

### Q39. HQL vs. Criteria API vs. Native SQL — when do you use each?

- **HQL (Hibernate Query Language)** — SQL-like but **object-oriented**: queries reference entity/field names, not table/column names, and are database-agnostic. Good default choice for most queries.
```java
List<User> users = session.createQuery("FROM User u WHERE u.age > :age", User.class)
    .setParameter("age", 18).list();
```
- **Criteria API** — builds queries **programmatically** (as Java objects, not strings), useful for queries whose structure changes dynamically at runtime based on optional filters (e.g., a search form with 5 optional fields) — avoids string concatenation and gets compile-time type checking.
```java
CriteriaBuilder cb = session.getCriteriaBuilder();
CriteriaQuery<User> cq = cb.createQuery(User.class);
Root<User> root = cq.from(User.class);
cq.where(cb.gt(root.get("age"), 18));
```
- **Native SQL** — an escape hatch for database-specific features (vendor-specific functions, complex query hints, or performance-critical hand-tuned SQL) that HQL/Criteria can't express.

---

### Q40. What causes `LazyInitializationException`, and how do you prevent it?

It's thrown when code tries to access a **lazily-loaded** association (e.g., `order.getItems()`) **after the Session that loaded the parent entity has already closed** — there's no active session left to run the query that would fetch the proxy's real data.

**Classic scenario:** a `@Service` method loads an entity inside a `@Transactional` boundary and returns it to a `@Controller`; the transaction (and session) closes when the service method returns, but the controller (or, worse, the JSON serializer) then tries to access a lazy field.

**Fixes:**
1. Fetch what you need **eagerly for that specific query**, using `JOIN FETCH` or `@EntityGraph` (see Spring Data JPA Q17–18) — the best fix, since it avoids extending the session unnecessarily.
2. Access the lazy field **while still inside the transactional boundary** (e.g., in the service layer, before returning).
3. **(Generally discouraged)** the "Open Session in View" pattern keeps the session open through view rendering — convenient, but it hides N+1 problems and couples the persistence layer to the web layer's lifetime, so most senior engineers actively avoid it in modern REST APIs.

---

### Q41. Explain Optimistic vs. Pessimistic locking in Hibernate/JPA.

Both solve the same problem — **preventing lost updates when two transactions modify the same row concurrently** — with very different trade-offs.

**Optimistic locking** — assume conflicts are rare; add a `@Version` column that's checked (and incremented) on every update. If another transaction already bumped the version, the current transaction fails with `OptimisticLockException` and must retry.
```java
@Entity
public class Account {
    @Id private Long id;
    private BigDecimal balance;
    @Version
    private Long version; // Hibernate auto-manages this
}
// UPDATE account SET balance=?, version=? WHERE id=? AND version=<old_version>
// If 0 rows are affected (version already changed by someone else) -> OptimisticLockException
```
**Pessimistic locking** — assume conflicts are likely; acquire an actual database row lock (`SELECT ... FOR UPDATE`) up front, blocking other transactions from touching that row until the current one finishes.
```java
Account acc = entityManager.find(Account.class, id, LockModeType.PESSIMISTIC_WRITE);
```

**Rule of thumb:** use **optimistic locking** for most web applications (high read concurrency, low actual conflict rate — e.g., editing a user profile). Use **pessimistic locking** for scenarios with **high contention and where a failed retry is unacceptable** — e.g., decrementing limited inventory stock at checkout.

## 2.9 Spring Security Deep Dive: OAuth2, JWT/JWKS & Session Management

### Q42. Explain the four OAuth2 roles and why OAuth2 exists in the first place.

OAuth2 solves a specific problem: letting a third-party application access a user's resources **without the user ever handing over their password** to that third party.

- **Resource Owner** — the end user who owns the data (e.g., a user who has photos on a cloud service).
- **Client** — the application requesting access on the user's behalf (e.g., a photo-printing app).
- **Authorization Server** — authenticates the resource owner and issues **access tokens** to the client after the user approves (e.g., Okta, Keycloak, Auth0, or your own Spring Authorization Server).
- **Resource Server** — hosts the protected resources (e.g., the photo-storage API) and validates the access token on every incoming request before serving data.

**In a typical microservices setup:** one dedicated Authorization Server issues tokens; every other microservice acts purely as a Resource Server, validating tokens independently.

---

### Q43. Walk through the OAuth2 Authorization Code flow (with PKCE).

This is the flow used by essentially all modern web and mobile apps (the old Implicit and Resource Owner Password grants are now discouraged/deprecated in the OAuth2.1 draft for security reasons).

1. Client redirects the user's browser to the Authorization Server's `/authorize` endpoint, including a `code_challenge` (a hashed random value — this is the **PKCE**, "Proof Key for Code Exchange," extension).
2. User logs in and approves the requested scopes at the Authorization Server.
3. Authorization Server redirects back to the client with a short-lived **authorization code**.
4. Client exchanges that code (plus the original `code_verifier`, matching the earlier `code_challenge`) for an **access token** (and usually a **refresh token**) by calling the `/token` endpoint directly, server-to-server.
5. Client uses the access token in the `Authorization: Bearer <token>` header for subsequent API calls to the Resource Server.

**Why PKCE matters even for confidential clients now (not just public/mobile clients as originally designed):** it protects against the authorization code being intercepted and replayed by an attacker, since the attacker wouldn't have the matching `code_verifier`.

---

### Q44. What's the difference between the Authorization Code, Client Credentials, and Refresh Token grant types?

| Grant Type | Use Case |
|---|---|
| **Authorization Code (+ PKCE)** | A human user logs in through a browser/app (standard login flow) |
| **Client Credentials** | **Machine-to-machine** — no human user involved; a service authenticates as itself (e.g., a backend job calling another internal API) |
| **Refresh Token** | Exchange a long-lived refresh token for a new access token once the current one expires, without forcing the user to log in again |

```java
// Client Credentials example - service-to-service call, no user context
POST /oauth2/token
grant_type=client_credentials&client_id=order-service&client_secret=***&scope=inventory.read
```

---

### Q45. OAuth2 vs. OpenID Connect (OIDC) — aren't they the same thing?

**OAuth2 is an *authorization* framework** — it answers "what is this client allowed to do?" via an access token, which is opaque by design (OAuth2 doesn't actually mandate a token format).

**OIDC is built on top of OAuth2** and adds **authentication/identity** — it standardizes a third token, the **ID Token** (always a JWT, containing user identity claims like `sub`, `email`, `name`), so the client can actually know *who* logged in, not just that *some* authorization happened.

**Common interview trap:** using a plain OAuth2 access token to "identify" the user is technically misusing the spec — if you need to know the user's identity, you want OIDC's ID Token, not the access token.

---

### Q46. Break down a JWT's structure and how signature verification works.

A JWT is three Base64URL-encoded segments joined by dots: `header.payload.signature`.

```
eyJhbGciOiJSUzI1NiIsImtpZCI6ImFiYzEyMyJ9.eyJzdWIiOiJ1c2VyMSIsImV4cCI6MTcxMDAwMDAwMH0.<signature-bytes>
     └── Header ──────────────────────┘      └── Payload/Claims ───────────────┘   └ Signature ┘
```

- **Header** — algorithm used (`alg`, e.g., `RS256`) and, importantly, a **`kid`** (Key ID) identifying *which* signing key was used — critical for key rotation (see Q47).
- **Payload** — the claims: `sub` (subject/user ID), `iss` (issuer), `aud` (audience — who the token is intended for), `exp`/`nbf` (expiry/not-before timestamps), plus custom claims like `scope`/`roles`.
- **Signature** — computed over the header+payload using the Authorization Server's **private key**. Anyone with the corresponding **public key** can verify the signature (proving the token wasn't tampered with) **without ever needing the private key** — this is exactly what lets Resource Servers validate tokens independently, without calling back to the Authorization Server on every request.

**Critical security point interviewers probe:** a Resource Server must **explicitly pin the expected algorithm** (e.g., only accept `RS256`) — a well-known attack tricks a naive verifier into accepting a token signed with the `none` algorithm or re-signed with a symmetric secret guessed/derived from the public key.

---

### Q47. What is JWKS, and how does key rotation work?

**JWKS (JSON Web Key Set)** is a JSON document — published at a well-known endpoint (e.g., `https://auth.example.com/.well-known/jwks.json`) — listing the Authorization Server's **current public keys**, each tagged with a `kid`.

**Why it's needed:** without it, every Resource Server would need the signing public key **hardcoded/manually distributed** — making key rotation (a security best practice, done periodically or after a suspected compromise) a painful, error-prone, multi-service deployment exercise.

**How rotation actually works:**
1. The Authorization Server generates a new key pair and adds the new public key to the JWKS endpoint **alongside** the old one (doesn't remove the old key immediately).
2. New tokens are signed with the new private key, tagged with the new `kid` in their header.
3. Resource Servers fetch/cache the JWKS document (usually with a TTL) and, for each incoming token, use its `kid` to pick the **matching key** from the set to verify the signature.
4. Because both keys are published simultaneously for a while, **tokens signed with the old key remain valid** until they naturally expire — only then is the old key safely removed from the JWKS document.

This is precisely why JWT validation should **always** point at the issuer's JWKS URI rather than a hardcoded public key/certificate.

---

### Q48. How does Spring Security's OAuth2 Resource Server validate a JWT, and where does Nimbus JOSE+JWT fit in?

When you add `spring-boot-starter-oauth2-resource-server` and configure:
```properties
spring.security.oauth2.resourceserver.jwt.issuer-uri=https://auth.example.com
```
Spring Security auto-configures a **`NimbusJwtDecoder`** — Spring's default JWT decoder is built directly on top of the **Nimbus JOSE+JWT** library (a well-established, low-level Java library for JWT/JOSE parsing and cryptographic verification; Spring Security doesn't reimplement token parsing/crypto itself, it delegates to Nimbus). At startup, it:

1. Fetches the JWKS document from the issuer's `/.well-known/jwks.json` (derived from the `issuer-uri`) and **caches the public keys**.
2. For each incoming request's JWT, reads the `kid` from the header, picks the matching cached public key, and **cryptographically verifies the signature**.
3. Validates standard claims: `exp` (not expired), `nbf` (not used before its time), and (if configured) `iss`/`aud`.
4. Converts the payload into a `Jwt` object, and a **`JwtAuthenticationConverter`** maps `scope`/`scp` claims into Spring Security `GrantedAuthority` objects — conventionally prefixed `SCOPE_` — so you can write `@PreAuthorize("hasAuthority('SCOPE_read:orders')")`.

**Using Nimbus directly** (without Spring's wrapper — sometimes needed for custom validation logic):
```java
JWKSource<SecurityContext> keySource = new RemoteJWKSet<>(new URL("https://auth.example.com/.well-known/jwks.json"));
JWSKeySelector<SecurityContext> keySelector = new JWSVerificationKeySelector<>(JWSAlgorithm.RS256, keySource);
ConfigurableJWTProcessor<SecurityContext> jwtProcessor = new DefaultJWTProcessor<>();
jwtProcessor.setJWSKeySelector(keySelector);

JWTClaimsSet claims = jwtProcessor.process(rawJwtString, null); // throws if signature/claims invalid
```

**Common production misconfigurations to mention (a strong senior-level signal):** not restricting the accepted signing algorithm (leaving it open to algorithm-confusion attacks), skipping audience (`aud`) validation (a token meant for Service A gets wrongly accepted by Service B), and forgetting that `@WithMockUser` in tests **doesn't work** for resource-server setups since it produces the wrong authentication type — use `SecurityMockMvcRequestPostProcessors.jwt()` instead.

---

### Q49. Access Token vs. Refresh Token — purpose, lifetime, and storage.

| | Access Token | Refresh Token |
|---|---|---|
| Purpose | Sent with every API request to prove authorization | Exchanged for a new access token when it expires |
| Lifetime | Short (minutes) — limits damage if leaked | Long (days/weeks) |
| Sent to Resource Server? | Yes, on every call | No — only ever sent to the Authorization Server's token endpoint |
| Storage (browser apps) | Memory (safest) or a `HttpOnly`, `Secure` cookie | `HttpOnly`, `Secure`, `SameSite` cookie — **never** `localStorage` |

**Why not just issue long-lived access tokens and skip refresh tokens entirely?** A stolen long-lived access token stays dangerous for its entire lifetime, with a stateless JWT typically impossible to revoke early. Short-lived access tokens + a refresh token limits the exposure window if a token is leaked, while refresh tokens can be tracked/revoked server-side (they're usually opaque and checked against a store, unlike stateless JWT access tokens).

**Interview trap re: storage:** `localStorage` is readable by any JavaScript on the page, making tokens stored there vulnerable to **XSS**-based theft — an `HttpOnly` cookie (inaccessible to JS) is the safer default for browser-based apps, at the cost of needing CSRF protection (see Q52) since cookies are sent automatically by the browser.

---

### Q50. Stateful vs. Stateless session management — what's the actual trade-off?

**Stateful (traditional server-side sessions)** — after login, the server creates a session, stores session data server-side (in memory, or a shared store like Redis for multi-instance setups), and gives the client a `JSESSIONID` cookie that's just a reference/lookup key.
- ✅ Easy to invalidate immediately (just delete the session server-side) — important for "log out everywhere" or forced revocation.
- ❌ Requires **sticky sessions** or a **shared session store**, adding infrastructure complexity in a horizontally-scaled deployment.

**Stateless (JWT-based)** — the server verifies the token's signature and claims on every request, storing **nothing** server-side about the session.
- ✅ Any instance can validate any request independently — a natural fit for microservices/horizontal scaling.
- ❌ **Cannot be instantly revoked** — a stolen or logically-invalidated token remains valid until it expires naturally, since there's no server-side record to delete. (Mitigations: short expiry + refresh tokens, or a server-side token blocklist — which reintroduces some statefulness specifically for revocation.)

```java
http.sessionManagement(sm -> sm.sessionCreationPolicy(SessionCreationPolicy.STATELESS)); // typical for a JWT-based REST API
```

---

### Q51. How do you enforce concurrent session control in Spring Security (e.g., "only one active login per user")?

```java
http.sessionManagement(sm -> sm
    .maximumSessions(1)                  // only one active session per user
    .maxSessionsPreventsLogin(false)     // false = new login kicks out the old session; true = new login is blocked instead
);
```

This requires a **`SessionRegistry`** to track active sessions per user, which only works with **stateful** (session-based) authentication — it has no direct equivalent for stateless JWTs, since there's no server-side session to count. Replicating "single active login" with JWTs typically means tracking active token IDs (`jti` claims) in a shared store like Redis and checking/invalidating them there — effectively reintroducing a bit of server-side state just for this feature.

---

### Q52. CSRF and CORS in Spring Security — why does one often get disabled for REST APIs while the other doesn't?

**CSRF (Cross-Site Request Forgery)** protection exists because **browsers automatically attach cookies** to requests to a domain, including requests triggered by a malicious page the user has open in another tab. Spring Security's CSRF protection requires a per-session token to be sent back with state-changing requests, proving the request actually originated from your own frontend.
- **Needed** when using cookie-based session authentication.
- **Commonly disabled** for stateless, **token-based** (JWT in an `Authorization` header) APIs — since the browser doesn't automatically attach an `Authorization` header the way it does cookies, the core CSRF attack vector doesn't apply the same way (this reasoning does **not** hold if you're storing the JWT in a cookie instead of sending it via a header).

**CORS (Cross-Origin Resource Sharing)** is a completely different, browser-enforced mechanism controlling **which origins (domains) are allowed to call your API from client-side JavaScript** at all — this is needed regardless of your auth mechanism whenever your frontend and backend are on different origins (different domain/port), and disabling/misconfiguring it (e.g., a wildcard `*` with credentials) is itself a security risk.

```java
http
    .csrf(csrf -> csrf.disable()) // reasonable for a stateless, header-based JWT API
    .cors(cors -> cors.configurationSource(corsConfigurationSource())); // still needed & should stay tightly scoped
```

---

### Q53. Explain method-level security: `@PreAuthorize`, `@PostAuthorize`, `@Secured`.

Enabled via `@EnableMethodSecurity`, these let you enforce authorization **at the method level**, closer to the business logic, instead of (or in addition to) URL-pattern rules in the security filter chain.

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long userId) { ... } // checked BEFORE the method runs

@PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")
public User getUserProfile(Long userId) { ... } // access SpEL expressions referencing method args & the authenticated principal

@PostAuthorize("returnObject.owner == authentication.name")
public Document getDocument(Long docId) { ... } // checked AFTER the method runs, against the RETURN VALUE
```

- **`@PreAuthorize`** — evaluated **before** method execution; can reference method parameters. The most commonly used.
- **`@PostAuthorize`** — evaluated **after** execution, can inspect the **return value** — useful for "can this user see *this specific* returned object" checks that can't be expressed from the input parameters alone.
- **`@Secured`** — older, simpler, role-only checks (`@Secured("ROLE_ADMIN")`), no SpEL support — mostly superseded by `@PreAuthorize` today.

# Part 3: Low-Level Design (LLD)

## 3.1 Design Principles

### Q1. What is LLD, and how is it different from HLD?

**High-Level Design (HLD)** is about the overall system architecture — how services communicate, how data flows, how the system scales (load balancers, databases, caching, message queues). It answers "what are the building blocks and how do they fit together?"

**Low-Level Design (LLD)** zooms into a *specific component* and answers "how exactly is this built?" — the actual classes, their responsibilities, relationships, interfaces, and the design patterns applied. It's closer to the actual code you'd write.

**Interview framing:** HLD interviews ask you to design "a URL shortener" (system-level). LLD interviews ask you to design "the class structure for a parking lot" — you're expected to draw out actual classes, interfaces, and relationships (and often write working code).

---

### Q2. Explain the SOLID principles with examples.

**S — Single Responsibility Principle:** A class should have only one reason to change — one job.
```java
// Violates SRP: handles both business logic AND persistence AND notification
class OrderProcessor {
    void processOrder(Order o) { /* business logic */ }
    void saveToDatabase(Order o) { /* persistence */ }
    void sendEmail(Order o) { /* notification */ }
}
// Fixed: split into OrderProcessor, OrderRepository, NotificationService
```

**O — Open/Closed Principle:** Classes should be open for extension but closed for modification — add new behavior by adding new code, not editing existing, tested code.
```java
interface DiscountStrategy { double apply(double price); }
class FestivalDiscount implements DiscountStrategy { public double apply(double p) { return p * 0.9; } }
class NewYearDiscount implements DiscountStrategy { public double apply(double p) { return p * 0.8; } }
// Adding a new discount type = new class, zero changes to existing code
```

**L — Liskov Substitution Principle:** Subtypes must be substitutable for their base type without breaking behavior.
```java
// Classic violation: Square extends Rectangle but breaks setWidth/setHeight independence assumptions
// If code expects Rectangle and gets Square, behavior surprises callers -> LSP violation
```

**I — Interface Segregation Principle:** Don't force a class to implement methods it doesn't need — prefer several small, specific interfaces over one large one.
```java
// Violates ISP: forces Robot to implement eat() which makes no sense
interface Worker { void work(); void eat(); }
// Fixed: split into Workable { work(); } and Eatable { eat(); }
```

**D — Dependency Inversion Principle:** High-level modules shouldn't depend on low-level modules directly — both should depend on abstractions.
```java
// Violates DIP: OrderService is tightly coupled to a concrete MySQLRepository
class OrderService { MySQLRepository repo = new MySQLRepository(); }

// Follows DIP: depends on an abstraction, concrete implementation is injected
class OrderService {
    private final OrderRepository repo; // interface
    OrderService(OrderRepository repo) { this.repo = repo; }
}
```

**Why interviewers love SOLID questions:** they reveal whether you write code that's actually maintainable and testable at scale, not just code that "works." Expect follow-ups like "show me a violation of X and how you'd fix it" — practice recognizing violations in real code, not just reciting definitions.

## 3.2 Design Patterns

### Q3. Singleton Pattern — implementation approaches and thread-safety

Ensures a class has **exactly one instance**, globally accessible (e.g., a configuration manager, a connection pool).

```java
// 1. Eager initialization - simple, but instance created even if never used
public class EagerSingleton {
    private static final EagerSingleton INSTANCE = new EagerSingleton();
    private EagerSingleton() {}
    public static EagerSingleton getInstance() { return INSTANCE; }
}

// 2. Thread-safe lazy initialization with double-checked locking
public class LazySingleton {
    private static volatile LazySingleton instance; // volatile prevents subtle reordering bugs
    private LazySingleton() {}
    public static LazySingleton getInstance() {
        if (instance == null) {                       // 1st check - avoid locking on every call
            synchronized (LazySingleton.class) {
                if (instance == null) {                // 2nd check - only one thread creates it
                    instance = new LazySingleton();
                }
            }
        }
        return instance;
    }
}

// 3. Enum singleton (Joshua Bloch's recommended approach) - thread-safe, serialization-safe, concise
public enum EnumSingleton {
    INSTANCE;
    public void doSomething() { }
}
```

**Interview tip:** be ready to explain *why* `volatile` is required in double-checked locking — without it, another thread could see a partially-constructed object due to instruction reordering during construction.

---

### Q4. Factory Method Pattern

Delegates object creation to a factory method instead of calling `new` directly, so the calling code doesn't need to know the concrete class being instantiated.

```java
interface Notification { void notifyUser(); }
class EmailNotification implements Notification { public void notifyUser() { System.out.println("Email sent"); } }
class SmsNotification implements Notification { public void notifyUser() { System.out.println("SMS sent"); } }

class NotificationFactory {
    public static Notification createNotification(String type) {
        switch (type) {
            case "EMAIL": return new EmailNotification();
            case "SMS": return new SmsNotification();
            default: throw new IllegalArgumentException("Unknown type: " + type);
        }
    }
}
// Usage: Notification n = NotificationFactory.createNotification("EMAIL");
```

**Benefit:** adding a new notification type only requires a new class + a factory branch — client code that calls `createNotification()` never changes.

---

### Q5. Builder Pattern

Solves the problem of constructors with many (especially optional) parameters — the classic "telescoping constructor" problem.

```java
public class Pizza {
    private final String size;      // required
    private final boolean cheese, pepperoni, mushroom; // optional

    private Pizza(Builder builder) {
        this.size = builder.size;
        this.cheese = builder.cheese;
        this.pepperoni = builder.pepperoni;
        this.mushroom = builder.mushroom;
    }

    public static class Builder {
        private final String size;
        private boolean cheese, pepperoni, mushroom;

        public Builder(String size) { this.size = size; } // required param via constructor
        public Builder cheese(boolean val) { this.cheese = val; return this; }
        public Builder pepperoni(boolean val) { this.pepperoni = val; return this; }
        public Builder mushroom(boolean val) { this.mushroom = val; return this; }
        public Pizza build() { return new Pizza(this); }
    }
}

// Usage - readable, no confusion about parameter order:
Pizza pizza = new Pizza.Builder("Large").cheese(true).pepperoni(true).build();
```

Lombok's `@Builder` generates this exact pattern automatically — worth mentioning if asked about reducing boilerplate.

---

### Q6. Observer Pattern

Defines a one-to-many dependency: when one object (the **subject**) changes state, all its dependents (**observers**) are notified automatically. This is the foundation of event-driven systems, GUI listeners, and pub-sub.

```java
interface OrderObserver { void onOrderPlaced(Order order); }

class EmailNotifier implements OrderObserver {
    public void onOrderPlaced(Order order) { System.out.println("Emailing confirmation for " + order.getId()); }
}
class InventoryUpdater implements OrderObserver {
    public void onOrderPlaced(Order order) { System.out.println("Reducing stock for " + order.getId()); }
}

class OrderSubject {
    private final List<OrderObserver> observers = new ArrayList<>();
    public void subscribe(OrderObserver o) { observers.add(o); }
    public void placeOrder(Order order) {
        // ... order logic ...
        observers.forEach(o -> o.onOrderPlaced(order)); // notify all observers
    }
}
```

Java's built-in `java.util.Observer`/`Observable` are deprecated — in real code, this pattern shows up as Spring's `ApplicationEventPublisher`/`@EventListener`, or message broker pub-sub.

---

### Q7. Strategy Pattern

Defines a family of interchangeable algorithms, encapsulates each one, and lets the algorithm vary independently of the client using it — a direct application of the Open/Closed Principle.

```java
interface PaymentStrategy { void pay(double amount); }
class CreditCardPayment implements PaymentStrategy {
    public void pay(double amount) { System.out.println("Paid " + amount + " via credit card"); }
}
class UpiPayment implements PaymentStrategy {
    public void pay(double amount) { System.out.println("Paid " + amount + " via UPI"); }
}

class ShoppingCart {
    private PaymentStrategy strategy;
    public void setPaymentStrategy(PaymentStrategy strategy) { this.strategy = strategy; }
    public void checkout(double amount) { strategy.pay(amount); }
}
// Usage: cart.setPaymentStrategy(new UpiPayment()); cart.checkout(500);
```

**Strategy vs. Factory:** Factory is about *creating* objects; Strategy is about *choosing behavior* at runtime, often injected rather than created internally.

---

### Q8. Decorator Pattern

Attaches new behavior to an object **dynamically**, by wrapping it in decorator objects, without modifying the original class or using inheritance explosion (a new subclass for every combination of features).

```java
interface Coffee { double cost(); String description(); }

class SimpleCoffee implements Coffee {
    public double cost() { return 2.0; }
    public String description() { return "Coffee"; }
}

abstract class CoffeeDecorator implements Coffee {
    protected final Coffee decoratedCoffee;
    CoffeeDecorator(Coffee coffee) { this.decoratedCoffee = coffee; }
}

class MilkDecorator extends CoffeeDecorator {
    MilkDecorator(Coffee c) { super(c); }
    public double cost() { return decoratedCoffee.cost() + 0.5; }
    public String description() { return decoratedCoffee.description() + " + Milk"; }
}

// Usage: Coffee order = new MilkDecorator(new SimpleCoffee());
// order.description() -> "Coffee + Milk", order.cost() -> 2.5
```

This is exactly how Java's I/O classes work: `new BufferedReader(new InputStreamReader(new FileInputStream(...)))` — each layer wraps and adds behavior to the one inside it.

---

### Q9. Adapter Pattern

Converts the interface of a class into another interface that a client expects, letting incompatible interfaces work together — commonly needed when integrating a third-party library whose interface doesn't match what your code expects.

```java
// Third-party class with an incompatible interface
class LegacyPaymentGateway { void makePayment(String amountInPaise) { ... } }

// Your application's expected interface
interface PaymentProcessor { void pay(double amountInRupees); }

// Adapter bridges the gap
class LegacyPaymentAdapter implements PaymentProcessor {
    private final LegacyPaymentGateway legacyGateway;
    LegacyPaymentAdapter(LegacyPaymentGateway gateway) { this.legacyGateway = gateway; }
    public void pay(double amountInRupees) {
        legacyGateway.makePayment(String.valueOf((int)(amountInRupees * 100))); // convert & delegate
    }
}
```

## 3.3 Classic LLD Problems

### Q10. Design a Parking Lot system.

**Requirements to clarify first (always do this in an interview):** multiple floors? multiple vehicle types (car, bike, truck)? payment on exit? real-time slot availability display?

**Core classes:**

```java
enum VehicleType { CAR, BIKE, TRUCK }

class Vehicle {
    private String licensePlate;
    private VehicleType type;
}

class ParkingSpot {
    private String spotId;
    private VehicleType allowedType;
    private boolean isOccupied;
    private Vehicle currentVehicle;

    boolean canFitVehicle(Vehicle v) { return !isOccupied && allowedType == v.getType(); }
    void assignVehicle(Vehicle v) { this.currentVehicle = v; this.isOccupied = true; }
    void removeVehicle() { this.currentVehicle = null; this.isOccupied = false; }
}

class ParkingFloor {
    private int floorNumber;
    private List<ParkingSpot> spots;

    Optional<ParkingSpot> findAvailableSpot(VehicleType type) {
        return spots.stream().filter(s -> s.canFitVehicle(new Vehicle(null, type))).findFirst();
    }
}

class Ticket {
    private String ticketId;
    private Vehicle vehicle;
    private ParkingSpot spot;
    private LocalDateTime entryTime;
    private LocalDateTime exitTime;
}

class ParkingLot { // Singleton - only one parking lot instance for the whole system
    private static ParkingLot instance;
    private List<ParkingFloor> floors;
    private Map<String, Ticket> activeTickets;

    public static synchronized ParkingLot getInstance() {
        if (instance == null) instance = new ParkingLot();
        return instance;
    }

    public Ticket parkVehicle(Vehicle vehicle) {
        for (ParkingFloor floor : floors) {
            Optional<ParkingSpot> spot = floor.findAvailableSpot(vehicle.getType());
            if (spot.isPresent()) {
                spot.get().assignVehicle(vehicle);
                Ticket ticket = new Ticket(UUID.randomUUID().toString(), vehicle, spot.get(), LocalDateTime.now(), null);
                activeTickets.put(ticket.getTicketId(), ticket);
                return ticket;
            }
        }
        throw new NoSpotAvailableException("Parking full for type: " + vehicle.getType());
    }

    public double unparkVehicle(String ticketId) {
        Ticket ticket = activeTickets.remove(ticketId);
        ticket.getSpot().removeVehicle();
        return FeeCalculator.calculate(ticket); // Strategy pattern for different fee structures
    }
}
```

**Patterns used:** Singleton (one `ParkingLot`), Strategy (pluggable `FeeCalculator` for hourly/flat pricing), Factory (could generate spots by type). **Follow-up interviewers ask:** "how would you handle concurrent parking requests for the last spot?" — answer: synchronize spot assignment (or use an atomic "claim" operation / DB-level row locking if persisted) to avoid two vehicles being assigned the same spot in a race condition.

---

### Q11. Design an Elevator System.

**Key classes:**

```java
enum Direction { UP, DOWN, IDLE }
enum ElevatorState { MOVING, STOPPED, DOOR_OPEN }

class ElevatorRequest {
    int floor;
    Direction direction; // direction the passenger wants to go (for external hall buttons)
}

class Elevator {
    int id;
    int currentFloor;
    Direction direction;
    ElevatorState state;
    TreeSet<Integer> upStops = new TreeSet<>();     // sorted floors to visit while going up
    TreeSet<Integer> downStops = new TreeSet<>((a, b) -> b - a); // sorted descending

    void addStop(int floor) {
        if (floor > currentFloor) upStops.add(floor);
        else downStops.add(floor);
    }

    void step() { // called on each simulation tick
        if (direction == Direction.UP && !upStops.isEmpty()) {
            currentFloor++;
            if (upStops.first() == currentFloor) { openDoor(); upStops.pollFirst(); }
        }
        // similar for DOWN; switch direction / go IDLE when both sets are empty
    }
}

class ElevatorController { // decides which elevator answers which request
    List<Elevator> elevators;

    Elevator findBestElevator(ElevatorRequest request) {
        // Common strategy: pick the closest elevator already moving in the same direction,
        // that hasn't passed the requested floor yet. Falls back to the nearest idle elevator.
        return elevators.stream()
            .filter(e -> e.direction == request.direction || e.direction == Direction.IDLE)
            .min(Comparator.comparingInt(e -> Math.abs(e.currentFloor - request.floor)))
            .orElseThrow();
    }

    void handleRequest(ElevatorRequest request) {
        findBestElevator(request).addStop(request.floor);
    }
}
```

**Key design discussion points interviewers probe:** how do you avoid starving requests (fairness), how do you handle a request when all elevators are busy (queue it), and how do you separate the "dispatch algorithm" (which elevator answers a call) from the "elevator's own movement logic" — a good answer keeps these as separate, swappable components (the dispatch strategy is itself a great place to apply the Strategy pattern).

---

### Q12. Design an LRU (Least Recently Used) Cache.

**Requirement:** `get(key)` and `put(key, value)` in **O(1)** time, evicting the least-recently-used entry when capacity is exceeded.

**Approach:** combine a **HashMap** (O(1) key lookup) with a **doubly linked list** (O(1) removal/insertion to track recency order — most-recently-used at the head, least-recently-used at the tail).

```java
class LRUCache<K, V> {
    private final int capacity;
    private final Map<K, Node<K, V>> map = new HashMap<>();
    private final Node<K, V> head = new Node<>(null, null); // most recently used side
    private final Node<K, V> tail = new Node<>(null, null); // least recently used side

    static class Node<K, V> {
        K key; V value;
        Node<K, V> prev, next;
        Node(K key, V value) { this.key = key; this.value = value; }
    }

    LRUCache(int capacity) {
        this.capacity = capacity;
        head.next = tail;
        tail.prev = head;
    }

    public V get(K key) {
        if (!map.containsKey(key)) return null;
        Node<K, V> node = map.get(key);
        remove(node);
        insertAtHead(node); // accessing a node makes it "most recently used"
        return node.value;
    }

    public void put(K key, V value) {
        if (map.containsKey(key)) {
            remove(map.get(key));
        }
        Node<K, V> node = new Node<>(key, value);
        insertAtHead(node);
        map.put(key, node);

        if (map.size() > capacity) {
            Node<K, V> lru = tail.prev;
            remove(lru);
            map.remove(lru.key); // evict the least recently used entry
        }
    }

    private void remove(Node<K, V> node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void insertAtHead(Node<K, V> node) {
        node.next = head.next;
        node.prev = head;
        head.next.prev = node;
        head.next = node;
    }
}
```

**Why this is a favorite interview question:** it tests whether you can combine two data structures to hit a time-complexity target that neither alone achieves, and whether you can handle the linked-list pointer manipulation correctly (a very common source of bugs live in an interview — practice this one by hand). Java's built-in `LinkedHashMap` actually supports this exact use case out of the box via its `removeEldestEntry()` hook — worth mentioning as the "in practice, I'd just use this" answer, while still being able to build it from scratch.

---

### Q13. Design a Rate Limiter.

**Common algorithms:**

**1. Token Bucket** — a bucket holds tokens, refilled at a fixed rate up to a max capacity; each request consumes one token; if empty, the request is rejected/delayed. Allows short bursts up to the bucket size.

```java
class TokenBucketRateLimiter {
    private final int capacity;
    private final int refillRatePerSecond;
    private double tokens;
    private long lastRefillTimestamp;

    TokenBucketRateLimiter(int capacity, int refillRatePerSecond) {
        this.capacity = capacity;
        this.refillRatePerSecond = refillRatePerSecond;
        this.tokens = capacity;
        this.lastRefillTimestamp = System.nanoTime();
    }

    synchronized boolean allowRequest() {
        refill();
        if (tokens >= 1) {
            tokens -= 1;
            return true;
        }
        return false;
    }

    private void refill() {
        long now = System.nanoTime();
        double secondsElapsed = (now - lastRefillTimestamp) / 1_000_000_000.0;
        tokens = Math.min(capacity, tokens + secondsElapsed * refillRatePerSecond);
        lastRefillTimestamp = now;
    }
}
```

**2. Leaky Bucket** — similar, but processes requests from a queue at a fixed rate, smoothing out bursts rather than allowing them.

**3. Fixed Window Counter** — count requests in a fixed time window (e.g., per minute); simple but allows 2x the limit right at window boundaries (burst at the edge of two windows).

**4. Sliding Window Log/Counter** — tracks request timestamps (or sub-window counts) to avoid the boundary burst problem of fixed windows, at the cost of more memory/computation.

**Interview follow-up to expect:** "how would this work across multiple servers?" — answer: the counter/bucket state needs to be centralized (e.g., in **Redis**, using `INCR` + `EXPIRE` or a Lua script for atomicity), since each server can't maintain an independent in-memory counter without allowing the limit to be exceeded N-times-over (once per server).

---

# Part 4: High-Level Design (HLD)

## 4.1 Core Concepts

### Q1. Vertical vs. Horizontal Scaling

- **Vertical scaling (scale up)** — add more resources (CPU, RAM) to a single machine. Simple, no architecture changes needed, but has a hard ceiling (max machine size) and creates a single point of failure.
- **Horizontal scaling (scale out)** — add more machines and distribute load across them. Virtually unlimited scaling potential and better fault tolerance, but requires the application to be designed for it — statelessness, a load balancer, distributed data handling.

**Interview expectation at 5 YOE:** you should be able to articulate *why* most large-scale systems favor horizontal scaling despite the added complexity — hardware limits on a single machine, and the fact that a single machine is always a single point of failure no matter how powerful.

---

### Q2. Explain Load Balancing — algorithms and L4 vs. L7.

A **load balancer** distributes incoming traffic across multiple backend instances, improving availability (routes around failed instances) and scalability.

**Common algorithms:**
- **Round Robin** — cycles through instances in order.
- **Least Connections** — routes to the instance with the fewest active connections (better for uneven request durations).
- **Weighted Round Robin** — some instances get proportionally more traffic (useful when instances have different capacities).
- **IP Hash** — routes based on a hash of the client IP, giving basic session stickiness.

**Layer 4 (Transport layer)** — balances based on IP/port only, without inspecting the actual request content. Faster, but less intelligent (can't route based on URL path, headers, etc.).

**Layer 7 (Application layer)** — inspects the actual HTTP request (path, headers, cookies) to make smarter routing decisions (e.g., route `/api/v2/**` to a different backend fleet). Slightly more overhead, but much more flexible — this is what tools like NGINX, HAProxy (in L7 mode), and cloud ALBs typically do.

---

### Q3. Explain caching strategies and eviction policies.

**Cache-population strategies:**
- **Cache-Aside (Lazy Loading)** — application checks the cache first; on a miss, reads from the DB and populates the cache. Simplest, most common pattern; cache only holds what's actually requested.
- **Write-Through** — every write goes to the cache *and* the DB synchronously. Keeps cache consistent, but adds latency to writes.
- **Write-Back (Write-Behind)** — writes go to the cache immediately, and are flushed to the DB asynchronously later. Fast writes, but risks data loss if the cache fails before flushing.
- **Read-Through** — the cache itself is responsible for loading from the DB on a miss (transparent to the application), often paired with write-through.

**Eviction policies (when the cache is full):**
- **LRU (Least Recently Used)** — evict the entry not accessed for the longest time (see LLD Q12 for the implementation).
- **LFU (Least Frequently Used)** — evict the entry accessed the fewest times.
- **FIFO** — evict the oldest entry regardless of access pattern.
- **TTL-based** — entries expire automatically after a fixed time, regardless of usage.

**Where caching typically sits in a real system:** browser cache → CDN → API Gateway/reverse-proxy cache → application-level cache (Redis/Memcached) → database query cache — each layer trades off freshness for reduced load on the layer behind it.

---

### Q4. Explain the CAP theorem.

In a **distributed system**, when a network partition occurs (some nodes can't communicate with others), you can only guarantee **two of these three properties simultaneously**:

- **Consistency (C)** — every read receives the most recent write (all nodes see the same data at the same time).
- **Availability (A)** — every request receives a response (success or failure), even during a partition.
- **Partition Tolerance (P)** — the system continues to function despite network partitions between nodes.

Since network partitions are a **fact of life** in any real distributed system (P is non-negotiable), the actual trade-off in practice is **C vs. A** during a partition:

- **CP systems** (e.g., traditional RDBMS clusters with synchronous replication, HBase, ZooKeeper) — sacrifice availability to guarantee every read is up to date; some requests fail/block during a partition rather than risk returning stale data.
- **AP systems** (e.g., Cassandra, DynamoDB by default) — sacrifice strict consistency to always respond, accepting that different nodes might briefly disagree (**eventual consistency**) until the partition heals.

**Common interview trap:** CAP is about behavior *during a partition specifically* — when there's no partition, a well-designed system can be both consistent and available. Also, be ready to explain **eventual consistency** with a concrete example (e.g., a "like count" on a post being briefly inconsistent across replicas is usually an acceptable trade-off; a bank balance usually is not).

## 4.2 Databases at Scale

### Q5. SQL vs. NoSQL — when do you use each?

| | SQL (RDBMS) | NoSQL |
|---|---|---|
| Schema | Fixed, defined upfront | Flexible/schema-less |
| Relationships | Strong (joins, foreign keys) | Typically denormalized |
| Consistency | Strong (ACID) by default | Often eventual/tunable (BASE) |
| Scaling | Primarily vertical (horizontal is harder) | Built for horizontal scaling |
| Examples | PostgreSQL, MySQL | MongoDB (document), Cassandra (wide-column), Redis (key-value), Neo4j (graph) |

**Use SQL when:** data is highly relational, you need strong consistency and complex queries/joins/transactions (e.g., financial systems, order management).

**Use NoSQL when:** you need massive horizontal scale, flexible/evolving schemas, or a specific access pattern that maps naturally to a non-relational model (e.g., a session store as key-value, a social graph as a graph DB, time-series/event data as wide-column).

**Senior-level nuance to mention:** this isn't binary in practice — most large systems are **polyglot persistence**: PostgreSQL for the core transactional data, Redis for caching/sessions, Elasticsearch for search, Cassandra for high-write time-series data — chosen per access pattern, not "SQL vs. NoSQL" as a single company-wide decision.

---

### Q6. Explain Database Sharding.

**Sharding** is horizontal partitioning of data across multiple database instances — each shard holds a subset of the total data, so no single machine needs to hold (or serve queries for) the entire dataset.

**Common sharding strategies:**
- **Range-based** — partition by a value range (e.g., user IDs 1–1M on shard 1, 1M–2M on shard 2). Simple, but can create hot spots if access isn't uniform across the range.
- **Hash-based** — apply a hash function to the shard key and use the result to pick a shard. Distributes load evenly, but makes range queries across shards harder, and *resharding* (changing shard count) requires rehashing most data (mitigated by consistent hashing — see Q11).
- **Directory-based** — a lookup service maps each key to its shard, giving full flexibility at the cost of an extra hop and a potential single point of failure for the lookup service itself.

**Key challenges to mention:** cross-shard joins/transactions become expensive or impossible without a distributed transaction protocol; picking a good **shard key** is critical (it should distribute load evenly and match your most common query pattern, since queries not filtered by the shard key may need to fan out to every shard).

---

### Q7. Explain Database Replication.

**Replication** keeps copies of the same data on multiple database nodes, primarily for **availability** (survive a node failure) and **read scalability** (spread read traffic across replicas).

- **Master-Slave (Primary-Replica)** — all writes go to the primary; replicas asynchronously (or synchronously) copy the primary's changes and serve read traffic. Simple, but the primary is a write bottleneck and a failover (promoting a replica to primary) is needed if it goes down.
- **Master-Master (Multi-Primary)** — multiple nodes accept writes, and changes are replicated between them. Removes the single write bottleneck, but introduces the risk of **write conflicts** (two primaries updating the same row differently) that need a conflict-resolution strategy.

**Synchronous vs. asynchronous replication:** synchronous guarantees zero data loss on failover (the primary waits for replica acknowledgment before confirming a write) but adds write latency; asynchronous is faster but risks losing the last few writes if the primary fails before replicating them.

---

### Q8. Explain database Indexing — how it works and its trade-offs.

An **index** is an auxiliary data structure (most commonly a **B-Tree**, or a **hash index** for pure equality lookups) that lets the database find rows matching a query condition **without scanning the entire table**.

```sql
CREATE INDEX idx_users_email ON users(email);
-- Now: SELECT * FROM users WHERE email = 'x@y.com' is an O(log n) B-Tree lookup
-- instead of an O(n) full table scan.
```

**Trade-offs (frequently asked as a follow-up):**
- **Faster reads**, but **slower writes** — every `INSERT`/`UPDATE`/`DELETE` must also update every index on that table.
- **Extra storage** — each index is essentially a copy of the indexed column(s) plus pointers to the actual rows.
- **Composite indexes** (multiple columns) only help queries that filter on a **left-prefix** of the index's column order — an index on `(last_name, first_name)` speeds up queries filtering by `last_name` alone or by both, but not by `first_name` alone.
- Over-indexing a write-heavy table is a real anti-pattern — index only the columns actually used in `WHERE`, `JOIN`, and `ORDER BY` clauses that matter for your query load.

## 4.3 Messaging, Gateways & Distributed Rate Limiting

### Q9. When and why do you use a Message Queue? Kafka vs. RabbitMQ.

A **message queue** decouples producers from consumers — the producer doesn't need the consumer to be available/fast right now, it just publishes a message and moves on. This is essential for:
- **Asynchronous processing** — e.g., "send a welcome email" shouldn't block the signup API response.
- **Smoothing traffic spikes** — absorb a burst of requests into a queue and let consumers process at a sustainable rate, instead of the spike directly overwhelming a downstream service.
- **Decoupling services** in a microservices architecture — a service can publish an event without knowing (or caring) who consumes it.

| | Kafka | RabbitMQ |
|---|---|---|
| Model | Distributed log — messages persisted, consumers track their own offset | Traditional broker/queue — messages typically removed once acknowledged |
| Throughput | Very high (built for high-volume event streaming) | High, but generally lower than Kafka at extreme scale |
| Message replay | Yes — consumers can re-read from any offset | Not natively (once consumed/acked, it's gone) |
| Routing | Simpler (topic + partition) | Rich routing (exchanges: direct, topic, fanout, headers) |
| Best for | Event streaming, log aggregation, high-throughput pipelines, event sourcing | Complex routing needs, task queues, RPC-style patterns |

---

### Q10. Explain the API Gateway pattern at the system level.

In a microservices architecture, an **API Gateway** is the single entry point clients talk to, instead of calling dozens of internal services directly. Responsibilities typically centralized here:
- **Routing** requests to the correct backend service.
- **Authentication/authorization** — validate the caller once, at the edge, instead of in every service.
- **Rate limiting & throttling** per client/API key.
- **Request/response transformation**, and sometimes **aggregation** — combining calls to multiple backend services into one client-facing response.
- **Cross-cutting observability** — centralized logging, metrics, and tracing for every request entering the system.

**Trade-off to mention:** it becomes a critical piece of infrastructure — if it goes down, everything behind it is unreachable — so it must itself be deployed redundantly (multiple instances behind a load balancer) and kept lightweight (avoid putting heavy business logic in the gateway itself, which just recreates a monolith at the edge).

---

### Q11. Explain Consistent Hashing and why it's needed.

**The problem it solves:** with plain hash-based sharding (`hash(key) % N`), adding or removing a single node (N changes) causes **almost every key to remap to a different node** — meaning a massive, unnecessary data migration just to add one server.

**Consistent hashing** solves this: both the servers and the keys are mapped onto a conceptual **hash ring** (0 to 2^32-1). A key is assigned to the **first server clockwise from the key's position on the ring**. When a server is added or removed, only the keys **between that server and its predecessor on the ring** need to move — everything else stays put.

```
Ring: [Server A]---[Server B]---[Server C]---(back to A)
Key "user123" hashes to a point between B and C -> assigned to Server C
Adding a new Server D between B and C only remaps keys in that one arc, not the whole ring.
```

**Refinement — virtual nodes:** to avoid uneven load distribution (a real risk with only a few points on the ring), each physical server is mapped to **many virtual points** on the ring, spreading its "territory" out and balancing load more evenly. This is used in DynamoDB, Cassandra, and many distributed caches/CDNs.

---

### Q12. How would you design a Rate Limiter that works across multiple servers?

The algorithm-level mechanics (token bucket, sliding window, etc.) are covered in the LLD section (Q13) — the extra challenge at the system level is that a rate limit like "100 requests/min per user" must hold **globally**, even though requests for the same user may land on **different server instances** behind a load balancer. An in-memory counter on each server would let the limit be exceeded by up to (limit × number of servers).

**Common solution:** centralize the counter state in a shared, fast data store — typically **Redis**:
```
INCR user:123:requests   -- atomically increments the counter
EXPIRE user:123:requests 60  -- resets it after the time window
```
Using `INCR` (atomic) avoids race conditions that a naive "read-then-write" would have across concurrent requests. For sliding-window accuracy, a Lua script (executed atomically inside Redis) or a Redis sorted set (storing individual request timestamps) is commonly used instead of a single fixed-window counter.

**Where to enforce it:** often at the **API Gateway** (Q10) so the limit is enforced once, at the edge, before a request even reaches the backend services — protecting everything downstream in one place rather than duplicating limiter logic in every microservice.

## 4.4 Classic HLD Case Studies

### Q13. Design a URL Shortener (like bit.ly).

**Step 1 — Clarify requirements:** Scale (reads vs writes ratio — typically read-heavy, ~100:1), custom aliases needed?, expiration?, analytics needed?

**Step 2 — Capacity estimate (senior interviewers expect this):** e.g., 100M new URLs/month ≈ ~40 writes/sec average; if reads are 100x writes, ~4,000 reads/sec — this justifies heavy caching on the read path.

**Step 3 — API:**
```
POST /api/shorten  { "longUrl": "..." }  -> { "shortUrl": "https://sho.rt/aZ9k3" }
GET  /aZ9k3  -> HTTP 302 redirect to the original long URL
```

**Step 4 — Core design decision: generating the short code.**
- **Option A — Base62 encode an auto-incrementing ID.** A counter (e.g., from a dedicated ID-generation service, or a distributed counter like a Snowflake-style ID) is encoded into a-z, A-Z, 0-9 (62 characters) to produce a short, unique string. Simple and guarantees uniqueness, but a naive single DB auto-increment counter can become a write bottleneck at scale.
- **Option B — Hash the long URL** (e.g., MD5/SHA-256, take first 7 chars) — but must handle **collisions** (two different URLs hashing to overlapping prefixes) by checking existence and appending/re-hashing on conflict.

**Step 5 — High-level architecture:**
```
Client -> Load Balancer -> API Gateway -> [Shortening Service] -> ID Generator (Snowflake/DB counter)
                                        -> [Redirect Service] -> Cache (Redis) -> DB (fallback on cache miss)
```
- The **write path** (shortening) is low-volume: generate ID → encode → store `{shortCode -> longUrl}` mapping in the DB.
- The **read path** (redirect) is the hot path: check **cache first** (a huge % of clicks hit recently-created or popular links) → on a cache miss, fall back to the DB, then populate the cache.
- **Database choice:** a simple key-value structure (`shortCode -> longUrl`) doesn't need relational features — a NoSQL store (DynamoDB, Cassandra) or even a well-indexed SQL table both work; NoSQL is often chosen here purely for horizontal write/read scalability at very large scale.

**Follow-ups to expect:** "How do you handle analytics (click counts) without slowing down the redirect?" → answer: fire an **async event** (to a message queue) on each redirect and aggregate click counts in a separate downstream consumer, rather than synchronously writing to the DB on the hot redirect path.

---

### Q14. Design a Chat Application (like WhatsApp/Slack) — 1:1 messaging at a high level.

**Step 1 — Clarify requirements:** 1:1 only, or group chat too? Delivery guarantees (at-least-once? exactly-once?) Online presence/read receipts needed? Message history/persistence?

**Step 2 — Key design decision: how does the server push messages to a recipient in real time?**
Plain HTTP request-response doesn't work for the server to *initiate* sending a message to a client. Options:
- **WebSockets** — a persistent, full-duplex connection between client and server; the standard choice for chat apps today, since it allows the server to push messages instantly.
- **Long polling** — client repeatedly asks "any new messages?"; simpler infrastructure, but higher latency and more overhead. Sometimes kept as a fallback for clients/networks that can't sustain WebSockets.

**Step 3 — High-level architecture:**
```
User A --(WebSocket)--> Chat Server 1
User B --(WebSocket)--> Chat Server 2
                              |
                    [Message Queue / Pub-Sub, e.g. Kafka or Redis Pub/Sub]
                              |
                     [Message Persistence Service] --> Database (per-conversation message history)
                              |
                   [Presence Service] (tracks who's online, on which server)
```
- Since A and B might be connected to **different server instances** (behind a load balancer), a chat server can't just hold the message in memory and hand it to the recipient directly — it needs to know *which* server the recipient is connected to (via a **presence/connection-registry service**, often backed by Redis) and route the message there, or broadcast via a pub-sub layer that every chat server subscribes to.
- **Message persistence:** every message is also written to a database (often a wide-column store like Cassandra/HBase, since chat history is write-heavy, append-only, and queried by conversation+time range) so it survives even if the recipient is offline at send time — delivered on their next connect.
- **Offline delivery:** if the recipient isn't currently connected, the message is queued (or just relies on the persisted history) and a push notification is triggered to their device.

**Follow-ups to expect:** "How do you scale to millions of concurrent WebSocket connections?" → answer: each chat server can hold a large but finite number of open connections; horizontally scale the number of chat servers, use a **connection-aware load balancer** (sticky routing so a client reconnects to a server that still recognizes its session, or a shared presence registry so any server can look up "which server is User X connected to"), and keep chat servers stateless beyond the live connection itself so any of them can be scaled/restarted independently.

---

## 4.5 Microservices Design Patterns

### Q15. What is the Saga pattern, and when do you need it?

**The problem:** in a monolith, a multi-step business operation (e.g., "place an order" = create order + reserve inventory + charge payment) is wrapped in a single **ACID database transaction** — if any step fails, everything rolls back automatically. In microservices, those three steps live in **three different databases** owned by three different services — a single ACID transaction across all of them (2-Phase Commit) is generally avoided in practice because it's slow, doesn't scale, and creates tight coupling between services' availability.

**The Saga pattern** breaks the operation into a sequence of local transactions, each in its own service, with a **compensating transaction** defined for each step to undo it if a later step fails.

**Two implementation styles:**
- **Orchestration** — a central **Saga Orchestrator** explicitly tells each service what to do next and calls the right compensating actions on failure. Easier to understand, test, and monitor as a single flow; the orchestrator itself is a new component to build and could become a bottleneck/coupling point.
- **Choreography** — no central coordinator; each service publishes an event when it finishes its step, and other services react to those events independently. More decoupled, but the overall flow becomes harder to trace/debug since the logic is spread across every participating service.

```
Orchestration example (Order placement):
Orchestrator -> Order Service: create order (PENDING)
Orchestrator -> Payment Service: charge card
   -> if fails: Orchestrator -> Order Service: cancel order (compensating action)
Orchestrator -> Inventory Service: reserve stock
   -> if fails: Orchestrator -> Payment Service: refund (compensating action)
                Orchestrator -> Order Service: cancel order (compensating action)
Orchestrator -> Order Service: confirm order (COMPLETED)
```

**Trade-off to always mention:** sagas give you **eventual consistency**, not the immediate strong consistency of a single ACID transaction — the order might briefly exist in a "PENDING" state visible to other parts of the system before the saga completes or compensates.

---

### Q16. Explain CQRS (Command Query Responsibility Segregation).

**CQRS** splits the responsibility for **writes (Commands)** and **reads (Queries)** into separate models — potentially even separate services and separate databases — instead of using one unified model/table for both.

**Why:** read and write workloads often have very different requirements. Writes need to enforce business rules and consistency; reads often need to be fast, heavily denormalized, and shaped very differently per use case (e.g., a dashboard needs aggregated data, a detail page needs a single record). Forcing both through the same normalized relational model is often a compromise that serves neither well at scale.

```
Write side: Order Service -> writes normalized data to PostgreSQL, publishes "OrderPlaced" events
Read side:  Event Consumer -> builds a denormalized "OrderSummary" view in Elasticsearch/Redis,
            optimized exactly for what the UI's order-history page needs to query
```

**Trade-off:** the read model is only as fresh as the last processed event — you're explicitly trading strong consistency for read performance and flexibility, which pairs naturally with Event Sourcing (Q17).

---

### Q17. What is Event Sourcing, and how is it different from normal CRUD persistence?

Instead of storing just the **current state** of an entity (a row that gets overwritten on every `UPDATE`), Event Sourcing stores the **full sequence of events** that led to that state as the source of truth. The current state is then *derived* by replaying those events (or reading a periodically-saved snapshot plus replaying more recent events).

```
Traditional CRUD: accounts table row -> { id: 1, balance: 700 }  (history of how it got there is lost)

Event Sourcing:    event log -> [ AccountOpened(balance=0), Deposited(500), Deposited(300), Withdrawn(100) ]
                    current balance = replay all events = 0 + 500 + 300 - 100 = 700
```

**Benefits:** a complete, immutable **audit trail** for free; the ability to reconstruct state **as of any point in time**; and natural support for CQRS read models (each event consumer builds its own projection).

**Costs:** significantly more complex than CRUD — querying "current state" requires either replaying events (slow for long histories) or maintaining snapshots; handling **event schema evolution** as your domain model changes over time is a genuinely hard, often-underestimated problem.

---

### Q18. What is the "dual-write problem," and how does the Outbox pattern solve it?

**The problem:** a service often needs to **both** update its own database **and** publish an event about that change (e.g., save the order, *and* notify other services via Kafka). Doing these as two separate operations is dangerous — if the DB write succeeds but the message broker publish fails (network blip, broker down), other services never learn about a change that definitely happened. There's no single transaction spanning a database and a message broker.

**The Outbox pattern's solution:** write the event into an **"outbox" table in the same database**, as part of the **same local ACID transaction** as the actual business data change. A separate process then reads unpublished rows from the outbox table and publishes them to the message broker (either via polling, or — more efficiently — via **Change Data Capture (CDC)** tools like Debezium reading the database's transaction log directly).

```sql
BEGIN TRANSACTION;
  INSERT INTO orders (id, status) VALUES (123, 'PLACED');
  INSERT INTO outbox (id, event_type, payload, published) VALUES (1, 'OrderPlaced', '{...}', false);
COMMIT; -- both rows are guaranteed to be written together, or neither is

-- Separate poller/CDC process later reads outbox WHERE published = false, publishes to Kafka, marks published = true
```

Because both inserts share one local transaction, you get a guarantee that the event is **never lost** relative to the data change — at the cost of some added infrastructure (the outbox table + a relay process) and typically **at-least-once delivery** (so consumers must be idempotent — see Q52 in the scenario section).

---

### Q19. Explain the Strangler Fig pattern for migrating a monolith to microservices.

Named after a fig vine that gradually grows around a host tree, eventually replacing it — the pattern migrates a monolith **incrementally**, rather than a risky "big bang" rewrite.

1. Put a **routing layer** (often an API Gateway or reverse proxy) in front of the existing monolith.
2. Pick one bounded piece of functionality; build it as a **new microservice**.
3. **Redirect just that slice of traffic** at the routing layer to the new service, while everything else still flows to the monolith.
4. Repeat, extracting one capability at a time, until the monolith either shrinks to nothing or only handles a small, stable remainder.

**Why it's the senior-preferred answer over "let's rewrite it":** it keeps the system shippable and rollback-able at every step (revert the routing rule for one slice if the new service has issues), spreads risk over time instead of one massive cutover, and lets the team learn/adjust the target architecture as they go rather than committing to a complete design upfront.

---

### Q20. What are the Sidecar and Backend-for-Frontend (BFF) patterns?

**Sidecar** — deploy a helper component **alongside** each service instance (same pod/host, separate process) to handle a cross-cutting concern — most commonly, a **service mesh proxy** (like Envoy in Istio) intercepting all network traffic to handle retries, mTLS, load balancing, and observability **without the application code knowing about it at all**. This lets platform teams add/upgrade this behavior without touching or redeploying every service's code.

**Backend for Frontend (BFF)** — instead of one generic API serving every client type, create a **dedicated backend per client category** (e.g., a mobile-BFF and a web-BFF), each aggregating/shaping data from downstream microservices exactly the way that specific frontend needs it. Avoids a single general-purpose API accumulating awkward compromises trying to serve very different clients (a mobile app wanting a lightweight payload vs. a web dashboard wanting a rich, deeply-nested response) at once.

---

### Q21. What is the Bulkhead pattern, and how does it relate to the Circuit Breaker (Q30 in Spring Boot section)?

Named after a ship's **bulkheads** — dividing the hull into watertight compartments so a leak in one doesn't sink the whole vessel. Applied to software: **partition resources (thread pools, connection pools) per downstream dependency**, so that one slow/failing dependency can't exhaust resources needed to keep talking to everything else.

```
Without bulkheads: ONE shared thread pool for all outbound calls.
   Service B is slow -> all its calls occupy threads -> pool exhausted
   -> even calls to healthy Service C can't get a thread -> total outage, caused by one dependency.

With bulkheads: separate, dedicated thread pools per dependency.
   Service B being slow only exhausts B's own pool.
   Calls to Service C still have their own threads available -> C keeps working fine.
```

**How it complements Circuit Breakers:** a circuit breaker stops you from *calling* a failing dependency once it's clearly unhealthy; a bulkhead limits the *blast radius* while it's still in the process of failing (before the breaker has tripped) or during partial degradation. Resilience4j (already covered in Q30) provides both as separate, composable building blocks — production systems typically combine bulkhead + circuit breaker + timeout + retry together for real resilience, not just one in isolation.

---

# Part 5: Scenario-Based Interview Questions

Scenario questions test *judgment under ambiguity*, not textbook recall. For each one below: **clarify assumptions out loud, state your approach, and always name the trade-off** — that structure matters more than landing on one "correct" answer.

### Q1. "API response times have degraded significantly in production over the past week. Walk me through how you'd diagnose this."

**A strong answer follows a funnel, from broad to specific — don't jump straight to a guess:**
1. **Quantify it first:** Is it *all* endpoints or specific ones? All the time, or at specific hours (traffic-correlated)? Check APM dashboards (New Relic/Datadog/Grafana) for p50/p95/p99 latency trends — a rising p99 while p50 stays flat points to a subset of requests hitting something specific (e.g., a slow query path, GC pauses), not a uniform slowdown.
2. **Correlate with recent changes:** Was there a deploy, a config change, or a traffic-pattern shift (new client, marketing campaign) right before the degradation started? This is often the fastest path to root cause.
3. **Check the obvious resource metrics:** CPU, memory (and GC pause frequency/duration — a growing old-gen heap causing longer GC pauses is a classic slow-creeping cause), thread pool saturation, DB connection pool exhaustion.
4. **Look at the database:** slow query logs, a missing index that only starts to hurt once a table crosses some row-count threshold, or lock contention from a new access pattern.
5. **Check downstream dependencies:** is a third-party API or another microservice you call now slower, dragging your own response time down with it?

**What makes this a senior-level answer:** explicitly separating *symptoms* (latency) from *root cause hypotheses*, and explaining what data you'd pull **before** proposing a fix — jumping straight to "we should add more servers" without this diagnostic process is a common way candidates lose points here.

---

### Q2. "Your primary database is becoming a bottleneck as traffic grows 10x. Walk through your options."

**Structure the answer as a progression, cheapest/simplest first:**
1. **Query/index optimization first** — often the highest ROI, lowest-risk fix (add a missing index, fix an N+1 pattern, rewrite an inefficient query) before touching architecture.
2. **Caching** — a read-heavy bottleneck often disappears with a well-placed cache (Redis) in front of hot queries (see HLD Q3).
3. **Read replicas** — if reads dominate writes, offload read traffic to replicas (HLD Q7), keeping writes on the primary.
4. **Vertical scaling** — a quick, no-code-change stopgap, but with a hard ceiling and doesn't address a fundamentally write-bound bottleneck.
5. **Sharding** — the heaviest option, needed when writes themselves exceed a single primary's capacity (HLD Q6) — mention this is a significant undertaking (picking a shard key, handling cross-shard queries) and shouldn't be the first thing reached for.

**Trade-off to state explicitly:** each step trades simplicity for scale — the senior-level answer is knowing *which* option actually addresses the specific bottleneck (read-heavy vs. write-heavy vs. poorly-optimized queries) rather than reflexively reaching for the most complex solution.

---

### Q3. "How would you design a system to handle a flash-sale event expecting 100x normal traffic for one hour?"

**Key considerations to raise:**
- **Pre-scale, don't rely purely on auto-scaling** — auto-scaling has a reaction lag (spinning up new instances, warming caches/connection pools) that a sudden 100x spike can outrun; pre-provisioning ahead of a *known* event is safer than reactive scaling alone.
- **Protect the database** — it's usually the hardest component to scale on short notice. Push as much load as possible to caches (pre-warm the cache with product data before the sale starts) and a **queue-based admission system** for the actual "buy" action, so the database only processes purchases at a rate it can sustain rather than the full incoming burst.
- **Static content on a CDN** — product images/pages served from edge caches, not your origin servers.
- **Idempotency for the purchase action** (see Q4) — retries from impatient users double-clicking "Buy Now" under load are a near-certainty; must not result in double-charging.
- **Graceful degradation plan** — e.g., a virtual "waiting room" queue that admits users at a controlled rate, rather than the whole system falling over trying to serve everyone at once. Decide *up front* what's allowed to degrade (e.g., "recommended for you" widgets can be skipped under load) vs. what must never fail (checkout).

---

### Q4. "How do you prevent duplicate processing of the same payment request (e.g., from a client retry)?"

**Core mechanism: idempotency keys.**
1. The client generates a unique **idempotency key** (e.g., a UUID) for each logical operation and sends it with the request (commonly as a header: `Idempotency-Key: <uuid>`).
2. On the server, before processing, check if that key has been seen before (a fast lookup, e.g., in Redis or a dedicated table with a **unique constraint** on the key).
3. If it's new: process the request, then **atomically** store the key alongside the result.
4. If it's a duplicate (same key seen again — a retry): **don't reprocess** — return the previously-stored result directly.

```sql
-- The unique constraint is what actually prevents a race between two near-simultaneous retries
CREATE TABLE idempotency_keys (
    key VARCHAR(64) PRIMARY KEY,
    response_payload JSONB,
    created_at TIMESTAMP
);
```

**Why this matters even with "exactly-once" message queues:** most real message queues only guarantee **at-least-once** delivery (see Q52's note on the Outbox pattern) — true exactly-once delivery across a distributed system is famously difficult/costly, so idempotency at the consumer/handler level is what actually makes at-least-once delivery *safe* in practice, and is the answer interviewers are generally looking for over "just use an exactly-once queue."

---

### Q5. "Service A calls a third-party API that's occasionally slow or down. How do you protect your system?"

This is really asking you to combine several resilience patterns already covered — a good answer **names them together as a layered defense**, not any single one in isolation:
1. **Timeouts** — never wait indefinitely on any external call; a sensible timeout bounds the worst case.
2. **Retries with exponential backoff (+ jitter)** — for transient failures, retry a few times with increasing delay; jitter avoids many clients retrying in lockstep and creating a "thundering herd" against the already-struggling dependency.
3. **Circuit Breaker** (Spring Boot Q30) — stop calling entirely once failures cross a threshold, failing fast instead of piling up waiting threads.
4. **Bulkhead** (Q21) — isolate the resources used for this specific dependency so its failure doesn't starve calls to unrelated, healthy dependencies.
5. **Fallback/graceful degradation** — return cached or default data, or a clearly degraded (but non-crashing) experience, instead of a hard failure.

**What separates a strong answer:** explicitly stating that these compose (timeout triggers a retry; repeated retry failures trip the breaker; the breaker's open state triggers the fallback) rather than listing them as independent alternatives.

---

### Q6. "Two microservices (Order and Inventory) need to stay consistent, but you're told a distributed transaction (2PC) is off the table. What do you do?"

This is testing whether you can apply the **Saga pattern** (already covered in Q15) to a concrete case, plus reason about a specific new wrinkle:

**"What if the compensating action itself fails?"** (a very common, sharper follow-up) — e.g., the Saga tries to release the previously-reserved inventory (compensating for a failed payment) but the Inventory service is down at that exact moment.
- Compensating actions must themselves be **retried** (with backoff) until they succeed — they can't be "fire and forget."
- If retries are exhausted, the failed compensation needs to be **persisted for manual/automated follow-up** (a dead-letter queue + alerting, or a reconciliation job that periodically finds and fixes inconsistent state) — the system should never just silently give up and leave permanently inconsistent data with no trace.
- This is exactly why sagas are described as giving **eventual**, not immediate, consistency — "eventual" sometimes means "eventually, with some operational help," and a senior answer says so rather than implying it's always fully automatic.

---

### Q7. "You need to migrate a monolith to microservices with zero downtime. What's your approach?"

Apply the **Strangler Fig pattern** (Q19) as the backbone of the answer, but a strong response also covers the practical migration concerns interviewers listen for:
- **Start with the least risky, most decoupled module** (e.g., something like notifications or reporting, not the core checkout flow) to prove the pattern works before touching critical paths.
- **Data migration is usually the hardest part**, not the code — if the new service needs its own database, you often need a period of **dual-writing or CDC-based syncing** between the old and new data stores while both are momentarily in play, then a careful cutover.
- **Feature-flag or percentage-based routing** at the gateway to shift traffic gradually (1% → 10% → 100%) rather than an all-or-nothing switch, with an easy rollback path at every stage.
- **Define what "done" looks like for each slice** before starting — an open-ended migration with no clear extraction boundaries tends to stall indefinitely.

---

### Q8. "Extend your Parking Lot design (Part 3, Q10) to support advance reservations."

A good LLD scenario-extension answer shows you can **adapt an existing design without a rewrite** — the interviewer is checking whether your original design was flexible or accidentally rigid.

- Add a `Reservation` class (`vehicleType`, `startTime`, `endTime`, `spotId`) separate from the live `Ticket`.
- `ParkingSpot` needs a notion of being "reserved for a future window" distinct from "currently occupied" — e.g., a list of reserved time windows on the spot, checked in addition to the simple `isOccupied` boolean from the original design.
- `findAvailableSpot()` now needs a time-aware overload: given a requested window, exclude spots with an overlapping reservation, not just currently-occupied ones.
- **Discuss the concurrency angle unprompted:** two users trying to reserve the last available spot for an overlapping time window is the exact same race condition as the original "last spot" problem, just now bound to a time range instead of "right now" — the same locking/atomic-claim reasoning applies.

---

### Q9. "Your LRU Cache implementation (Part 3, Q12) needs to be thread-safe. How do you change it?"

- **Simplest fix:** wrap every public method (`get`, `put`) in a single `synchronized` block/method — correct, but serializes *all* access, so under high concurrency it becomes a bottleneck (every thread queues up for the same lock even for unrelated keys).
- **Better:** use a `ReentrantReadWriteLock` — BUT note that `get()` in an LRU cache isn't a pure read, since it also **mutates recency order** (moves the accessed node to the head) — so `get()` still needs the *write* lock, not the read lock, undermining much of the benefit of a read-write lock here. This is a great "gotcha" to raise proactively — many candidates assume `get()` can safely use a read lock and miss this.
- **More scalable approach:** shard the cache into **N independent segments** (each with its own lock, or backed by `ConcurrentHashMap` segments), hashing keys to a segment — similar in spirit to how `ConcurrentHashMap` itself scales (Java Q14). Reduces contention since unrelated keys in different segments no longer block each other, at the cost of the "total capacity" and "global LRU order" now being approximate across segments rather than perfectly exact.
- **Pragmatic answer:** mention that `Collections.synchronizedMap(new LinkedHashMap<>(...))` or a well-tuned `Caffeine` cache (which handles exactly this problem internally, with high concurrency) is what you'd actually reach for in production rather than hand-rolling this.

---

### Q10. "Modify your Rate Limiter (Part 3, Q13) to support different limits for different API endpoints and different user tiers."

- Move from a single global limiter instance to a **map keyed by `(endpoint, userId or apiKey)`** (or `(endpoint, tier)` if limits are tier-based, not per-user), each with its **own bucket/window state** and its own configured limit.
- Store the limit **configuration** (e.g., "free tier: 100/min, pro tier: 10,000/min") separately from the limiter's runtime state, so limits can be changed (e.g., via a config service or admin panel) without redeploying.
- At the system level (tying back to HLD Q12): this composite key's state still needs to live in a shared store like Redis if enforced across multiple gateway instances — the key in Redis becomes something like `ratelimit:{endpoint}:{userId}`.
- **Good follow-up to raise yourself:** what happens when a user is on multiple tiers' boundary (e.g., they just upgraded)? Cheapest correct answer: the limiter reads the *current* tier at request time rather than caching it, accepting a tiny risk of using a stale tier for the duration of any local cache TTL.

---

### Q11. "A junior engineer's service has a slow memory leak in production. Walk through your debugging process."

This mirrors the JVM memory leak question (Java Q39) but as a live scenario — the interviewer wants your **process**, not just the final answer:
1. **Confirm it's actually a leak, not just normal usage** — check if old-gen heap usage trends upward over days/weeks and never drops back down after a full GC, versus just being a large-but-stable working set.
2. **Correlate with load/deploys** — did this start after a specific release? That narrows the search dramatically before touching any profiling tool.
3. **Capture heap dumps at two points in time** (e.g., a day apart) and diff them, or open one dump in Eclipse MAT and check the dominator tree for what's retaining the most memory.
4. **Look for the "usual suspects" first** — unbounded caches/collections, unclosed resources, listener registrations without matching deregistration, `ThreadLocal` values not cleared in a thread-pool context (Java Q39 covers all of these).
5. **Propose a mitigation vs. a fix, and say which is which** — e.g., a scheduled restart is a *mitigation* to buy time in production; finding and removing the actual unwanted reference is the *fix*. A senior answer doesn't present a Band-Aid as if it were the resolution.

---

### Q12. "How would you design a feature-flag system to safely roll out a risky change?"

- **Core data model:** a flag has a `key`, a `default state` (on/off), and **targeting rules** — e.g., "on for 10% of users," "on for users in the `beta` cohort," "on for internal employees only."
- **Evaluation needs to be fast and local** — checking a flag shouldn't add meaningful latency to every request, so the service typically pulls the **full flag configuration into memory** (via a small SDK) and refreshes it periodically or via a push mechanism (e.g., streaming updates, or polling every N seconds), rather than making a network call to evaluate every single flag check.
- **Consistent bucketing:** for percentage rollouts, hash the user ID (deterministically) to decide in/out — the same user should consistently land on the same side of a given flag across requests, rather than getting a different experience on every page load.
- **Kill switch requirement:** flipping a flag off should take effect within seconds across all instances — this is the actual point of the whole system (the ability to instantly disable a bad change without a redeploy), so it's worth calling out explicitly as the primary design driver, not an afterthought.
- **Follow-up to expect:** "what happens to a request mid-flight when a flag flips?" — reasonable answer: the flag value is read **once** at the start of handling a given request/operation and used consistently through it, rather than being re-checked mid-operation (which could cause inconsistent partial behavior within a single request).

---

### Q13. "Design a distributed lock (e.g., so only one instance of a scheduled job runs at a time across several servers)."

- **Simplest common approach: Redis-based lock**, using `SET lock_key unique_value NX PX 30000` — `NX` ("set if Not eXists") makes the lock acquisition atomic, and `PX 30000` sets a 30-second auto-expiry so a **crashed holder doesn't hold the lock forever**.
```
SET job:daily-report:lock <random-token> NX PX 30000
-- Only succeeds if the key doesn't already exist -> this instance "won" the lock
```
- **The value must be unique per acquirer (not a fixed string)** — so that only the instance that actually holds the lock can release it (`DEL` only if the stored value matches its own token), preventing one instance from accidentally releasing a lock that a *different* instance has since acquired after the first one's lock expired.
- **The hard follow-up to be ready for:** "what if the job runs longer than the lock's TTL?" — the lock could expire mid-job, letting a second instance start the same job concurrently. Answer: either extend ("heartbeat") the lock's TTL periodically while the job is still running, or accept the small risk and make the job itself idempotent as a backstop (tying back to Q4's idempotency-key idea).
- **Worth mentioning as the more rigorous answer:** for genuinely high-stakes locking, plain single-node Redis locking has known edge cases around failover; the **Redlock algorithm** (acquiring the lock across a majority of independent Redis nodes) or a consensus-based store like **ZooKeeper/etcd** (which provide stronger guarantees via Raft/ZAB consensus) are the more bulletproof — but heavier — alternatives.

---

### Q14. "Your fraud-detection system suddenly starts flagging 12% of legitimate transactions as fraudulent, instead of the normal ~1%. Walk through your response."

**A strong answer separates "stop the bleeding" from "root cause" — and says so explicitly:**
1. **Immediate mitigation:** if the fraud model/rules are behind a feature flag or a config value (e.g., a threshold), the fastest safe action is often to **roll back the most recent change to the fraud logic** or **loosen the threshold temporarily**, accepting a short window of higher fraud risk in exchange for not blocking a large fraction of legitimate customers — a clear business trade-off worth stating out loud rather than assuming.
2. **Check for a recent deploy/config/data change** correlated with when the spike started — a fraud-rules update, a new upstream data feed, or a change in a dependency (e.g., a geolocation provider suddenly returning different/wrong data) are common real causes.
3. **Look at what the flagged transactions have in common** — a shared attribute (all from one region, one payment method, one client SDK version) usually points straight at the root cause.
4. **Add a human-review/manual-override path** for flagged transactions above a certain value while the automated system is untrusted, rather than either fully trusting it or blocking everything.
5. **Post-incident:** propose a safeguard for next time — e.g., **canary/shadow-mode deployment** for fraud-rule changes (running the new rules alongside the old ones, comparing outputs, before fully switching over) so a bad change is caught before it affects real customers.


# Part 6: React (Basics to Advanced)

## 6.1 React Fundamentals

### Q1. What is React, and what problem does the Virtual DOM solve?

React is a JavaScript library for building UIs out of reusable **components**, using a **declarative** style — you describe *what* the UI should look like for a given state, and React figures out *how* to update the actual DOM to match, rather than you manually issuing DOM mutation commands (`document.getElementById(...).innerHTML = ...`) yourself.

**The Virtual DOM** is a lightweight, in-memory JavaScript representation of the real DOM tree. Direct DOM manipulation is **slow** — every change can trigger layout recalculation and repainting. React instead:
1. Builds a virtual DOM tree representing the desired UI.
2. On a state change, builds a **new** virtual DOM tree.
3. **Diffs** the new tree against the previous one (the "reconciliation" algorithm, Q24).
4. Applies only the **minimal set of actual DOM mutations** needed to reconcile the difference, batched together.

**Common interview nuance to raise:** the Virtual DOM isn't inherently "faster than the DOM" in some magical sense — it's a strategy for **minimizing and batching** expensive real-DOM writes, which is what actually helps performance for non-trivial UIs.

---

### Q2. What is JSX, and how does it actually work under the hood?

JSX is a syntax extension letting you write HTML-like markup directly in JavaScript. It's **not** understood by browsers natively — a build tool (Babel, or the TypeScript compiler) **transpiles** it into plain `React.createElement()` calls (or, with the newer JSX transform, calls to functions imported automatically from `react/jsx-runtime`).

```jsx
// What you write:
const element = <h1 className="title">Hello, {name}</h1>;

// What it compiles to (classic transform):
const element = React.createElement('h1', { className: 'title' }, 'Hello, ', name);
```

`React.createElement()` returns a plain JavaScript object (a lightweight description of a DOM node) — **this object *is* the Virtual DOM node** referenced in Q1.

---

### Q3. Functional components vs. Class components — is one "better"?

```jsx
// Class component (the original way)
class Greeting extends React.Component {
    render() { return <h1>Hello, {this.props.name}</h1>; }
}

// Functional component (modern standard, using Hooks for state/lifecycle)
function Greeting({ name }) {
    return <h1>Hello, {name}</h1>;
}
```

Class components use `this.state`/`this.setState()` and lifecycle methods (`componentDidMount`, etc.). Since **Hooks were introduced (React 16.8)**, functional components can do everything class components can (state via `useState`, lifecycle-equivalent behavior via `useEffect`) with **less boilerplate, no `this` binding confusion, and better logic reuse via custom hooks** — functional components with Hooks are now the standard for all new code; class components are mostly only seen in legacy codebases today.

---

### Q4. Props vs. State

- **Props** — data passed **into** a component from its parent; **read-only** from the receiving component's perspective (a component must never mutate its own props).
- **State** — data owned and managed **internally** by a component, which can change over time (via `useState`/`setState`) and triggers a **re-render** when it does.

```jsx
function Counter({ initialValue }) {  // initialValue is a PROP (from parent, read-only)
    const [count, setCount] = useState(initialValue); // count is STATE (owned here)
    return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

---

### Q5. Describe the component lifecycle (class components) and its Hooks equivalent.

| Phase | Class method | Hooks equivalent |
|---|---|---|
| Mount (first render) | `componentDidMount()` | `useEffect(() => {...}, [])` (empty dependency array) |
| Update (re-render) | `componentDidUpdate(prevProps, prevState)` | `useEffect(() => {...}, [dep1, dep2])` (runs when a dependency changes) |
| Unmount | `componentWillUnmount()` | the **cleanup function** returned from `useEffect` |

```jsx
useEffect(() => {
    const subscription = subscribeToSomething();
    return () => subscription.unsubscribe(); // cleanup - equivalent to componentWillUnmount
}, []); // empty array - equivalent to componentDidMount, runs once
```

---

### Q6. Controlled vs. Uncontrolled components (forms)

- **Controlled** — the form input's value is driven entirely by React state; every keystroke updates state via `onChange`, and the input's `value` is set from that state. React is the **single source of truth**.
```jsx
function ControlledInput() {
    const [value, setValue] = useState('');
    return <input value={value} onChange={e => setValue(e.target.value)} />;
}
```
- **Uncontrolled** — the DOM itself holds the current value; React reads it only when needed (e.g., on submit) via a `ref`, rather than tracking every keystroke in state.
```jsx
function UncontrolledInput() {
    const inputRef = useRef();
    const handleSubmit = () => console.log(inputRef.current.value);
    return <input ref={inputRef} defaultValue="" />;
}
```

**When to use uncontrolled:** simple forms, file inputs (which can't be controlled in the traditional sense), or performance-sensitive cases where re-rendering on every keystroke is undesirable and you only need the value at submission time.

---

### Q7. Why does React want a stable, unique `key` prop for list items?

When rendering a list, React uses `key` to match elements between the previous and new virtual DOM tree during reconciliation (Q24) — it's how React decides "is this the *same* logical item that just moved/changed, or a completely new item?"

```jsx
{items.map(item => <ListItem key={item.id} {...item} />)}  // ✅ stable, unique ID

{items.map((item, index) => <ListItem key={index} {...item} />)}  // ⚠️ works, but risky if list order changes
```

**Why using the array `index` as a key is a common but risky shortcut:** if items are reordered, inserted, or removed from the middle of the list, the index-to-item mapping shifts — React can end up reusing the wrong DOM node/component instance for what is logically a different item, causing subtle bugs (stale internal state showing up attached to the wrong row, broken animations, or unnecessary full re-renders instead of an efficient move). It's fine **only** when the list is static and never reordered/filtered.

---

### Q8. What is "one-way data binding" in React, and how does data flow down/up a component tree?

Data flows **down** the tree via props — a parent passes data to children, never the reverse directly. For a child to affect a parent's state, the parent must explicitly pass **a callback function down as a prop**, which the child calls — the actual state change still happens in the parent.

```jsx
function Parent() {
    const [name, setName] = useState('');
    return <Child name={name} onNameChange={setName} />; // pass both data AND a way to change it, down
}
function Child({ name, onNameChange }) {
    return <input value={name} onChange={e => onNameChange(e.target.value)} />; // calls parent's function
}
```

This unidirectional flow is what makes React apps easier to reason about compared to older two-way-binding frameworks — at any point, you can trace exactly where a piece of state lives and what can change it.

## 6.2 Hooks

### Q9. `useState` — basics and the batching gotcha.

```jsx
const [count, setCount] = useState(0);
setCount(count + 1); // schedules a re-render with the new value; doesn't mutate `count` immediately
```

**Common gotcha:** calling `setCount` multiple times in the same event handler using the **current** `count` variable doesn't add up the way you'd expect, because `count` inside that handler is a snapshot from the render that created it (a "stale closure" — see Q19):
```jsx
function handleClick() {
    setCount(count + 1); // uses the SAME stale `count` value for all three calls
    setCount(count + 1);
    setCount(count + 1);
} // Result: count only increases by 1, not 3!

// Fix: use the "updater function" form, which always receives the LATEST state
function handleClick() {
    setCount(c => c + 1);
    setCount(c => c + 1);
    setCount(c => c + 1);
} // Result: increases by 3, as expected
```

---

### Q10. `useEffect` — the dependency array and cleanup function, explained properly.

```jsx
useEffect(() => {
    console.log('Effect ran');
    return () => console.log('Cleanup ran'); // optional cleanup
}, [someValue]); // dependency array
```

- **No dependency array** — runs after **every** render (rarely what you want).
- **Empty array `[]`** — runs **once**, after the first render only.
- **`[someValue]`** — runs after the first render, and again **any time `someValue` changes** between renders.
- **Cleanup function** — runs **before** the effect runs again (if dependencies changed) *and* when the component unmounts — used to cancel subscriptions, clear timers, or abort in-flight requests to avoid acting on stale data or leaking resources.

**Interview-favorite gotcha (an actual, common production bug):** an `async` function can't be passed directly as the effect callback (`useEffect` expects its callback to return either nothing or a cleanup function — a Promise doesn't fit that contract) — you must define the async logic inside and call it:
```jsx
useEffect(() => {
    let cancelled = false;
    async function fetchData() {
        const res = await fetch(url);
        if (!cancelled) setData(await res.json()); // guard against setting state after unmount
    }
    fetchData();
    return () => { cancelled = true; };
}, [url]);
```

---

### Q11. `useContext` — what problem does it solve, and what's its main limitation?

**Prop drilling** is the problem: passing a value through 5 layers of components that don't themselves need it, just to reach a deeply nested child that does, clutters every intermediate component's props. `useContext` lets a deeply nested component read a value directly from a `Provider` higher up the tree, skipping the intermediate layers entirely.

```jsx
const ThemeContext = createContext('light');

function App() {
    return (
        <ThemeContext.Provider value="dark">
            <Toolbar /> {/* doesn't need theme itself, doesn't need to pass it down manually */}
        </ThemeContext.Provider>
    );
}
function ThemedButton() {
    const theme = useContext(ThemeContext); // reads directly, skipping Toolbar entirely
    return <button className={theme}>Click</button>;
}
```

**Main limitation (a common follow-up):** **any** component consuming a context re-renders whenever that context's value changes — even if the specific piece of data it cares about didn't actually change (if the value is an object with multiple fields, changing *any* field triggers *all* consumers to re-render). This makes plain Context a poor fit for high-frequency updates (e.g., mouse position); dedicated state-management libraries (Q20) or splitting into multiple, narrower contexts helps.

---

### Q12. `useRef` — what is it for, beyond just accessing DOM nodes?

`useRef` returns a mutable object (`{ current: ... }`) that **persists across re-renders** but — critically — **updating it does NOT trigger a re-render**, unlike state.

```jsx
function Timer() {
    const intervalRef = useRef(null); // holds a value across renders, invisible to the render output

    useEffect(() => {
        intervalRef.current = setInterval(() => console.log('tick'), 1000);
        return () => clearInterval(intervalRef.current);
    }, []);
}
```

**Two distinct use cases to mention:** (1) holding a reference to a DOM element (`<input ref={myRef} />`) to imperatively call methods like `.focus()`; (2) holding any mutable value you need to persist across renders **without** causing the component to re-render when it changes (a timer ID, a previous-value cache, a flag) — this second use case is less commonly known but frequently tested.

---

### Q13. `useMemo` vs. `useCallback` — what's the actual difference, and when should you NOT use them?

Both **memoize** something to avoid expensive recomputation/re-creation on every render — the difference is *what* they memoize:
- **`useMemo`** — memoizes a **computed value**.
- **`useCallback`** — memoizes a **function reference** itself (technically just `useMemo` that returns a function — `useCallback(fn, deps)` ≡ `useMemo(() => fn, deps)`).

```jsx
const expensiveResult = useMemo(() => computeExpensiveValue(a, b), [a, b]); // recompute only if a or b changed

const handleClick = useCallback(() => doSomething(id), [id]); // same function reference across renders unless id changes
// Useful because passing a NEW function reference on every render to a memoized child (React.memo, Q18)
// would defeat that child's memoization, since props would "look different" every time.
```

**When NOT to use them (a frequent, important follow-up):** memoization itself has a cost — storing the previous inputs and comparing them on every render. For cheap computations (basic arithmetic, string formatting) or components that rarely re-render, the overhead of memoizing can exceed the cost of just redoing the work — **profile before reaching for `useMemo`/`useCallback` everywhere**, don't apply them reflexively.

---

### Q14. What are custom Hooks, and why are they useful?

A custom Hook is just a regular JavaScript function whose name starts with `use`, which itself calls other Hooks internally — a way to **extract and reuse stateful logic** across multiple components without duplicating code or resorting to older patterns like HOCs/render props for this purpose.

```jsx
function useWindowWidth() {
    const [width, setWidth] = useState(window.innerWidth);
    useEffect(() => {
        const handleResize = () => setWidth(window.innerWidth);
        window.addEventListener('resize', handleResize);
        return () => window.removeEventListener('resize', handleResize);
    }, []);
    return width;
}

// Usage - any component can now reuse this logic in one line:
function MyComponent() {
    const width = useWindowWidth();
    return <p>Window width: {width}</p>;
}
```

Each component calling `useWindowWidth()` gets its **own independent** state — a custom Hook shares the *logic*, not the state itself, across components.

---

### Q15. What are the "Rules of Hooks," and *why* do they exist (not just what they are)?

**The rules:** (1) only call Hooks at the **top level** of a component/custom Hook — never inside conditionals, loops, or nested functions; (2) only call Hooks from React function components or other custom Hooks — never from plain JS functions.

**Why they exist (the actual implementation reason, which is what separates a senior answer):** React doesn't track hooks by name — it tracks them **by call order**, using an internal linked list (or array) matched against the **position** each `useState`/`useEffect` call occupies during a render. If a hook call is skipped conditionally on one render but not another, every subsequent hook's position shifts, and React ends up handing component B's state to what is now hook-call-slot 3, even though that slot previously belonged to a different hook — silently corrupting state.

```jsx
// ❌ Breaks the rule - conditional hook call
if (condition) {
    const [value, setValue] = useState(0); // sometimes called, sometimes skipped -> shifts every later hook's slot
}

// ✅ Correct - hook always called, condition moves inside
const [value, setValue] = useState(0);
if (condition) { /* use value here */ }
```

---

### Q16. `useReducer` — when is it a better choice than multiple `useState` calls?

`useReducer` manages state via a **reducer function** `(state, action) => newState`, similar to Redux's core pattern, but local to a single component (or shared via Context).

```jsx
function reducer(state, action) {
    switch (action.type) {
        case 'increment': return { count: state.count + 1 };
        case 'decrement': return { count: state.count - 1 };
        default: throw new Error('Unknown action');
    }
}

function Counter() {
    const [state, dispatch] = useReducer(reducer, { count: 0 });
    return <button onClick={() => dispatch({ type: 'increment' })}>{state.count}</button>;
}
```

**When it's the better choice:** when you have **several related pieces of state that update together** (managing them as separate `useState` calls risks them getting out of sync, or requires awkwardly calling multiple setters together every time), or when the update logic itself is non-trivial and benefits from being centralized, named, and testable as a pure function separate from the component's rendering logic.

## 6.3 State Management & Performance

### Q17. What is "lifting state up," and when do you need it?

When two sibling components need to share/coordinate state, that state can't live in either sibling alone — it must be moved ("lifted") to their **closest common parent**, which then passes the state (and setters, as callbacks) down to both children as props.

```jsx
function Parent() {
    const [selectedId, setSelectedId] = useState(null); // lifted here, since both siblings need it
    return (
        <>
            <ItemList onSelect={setSelectedId} />
            <ItemDetail selectedId={selectedId} /> {/* needs to know what ItemList selected */}
        </>
    );
}
```

This is the most basic form of state management in React — reaching for Context or an external library (Q20) only becomes necessary once lifting state up would require passing it through too many unrelated intermediate layers, or too many disconnected parts of the tree need it.

---

### Q18. What is `React.memo`, and when does it actually help (or not)?

`React.memo` wraps a component so React **skips re-rendering it** if its props are shallowly equal to the previous render's props — a performance optimization for components that render often with unchanged props.

```jsx
const ExpensiveRow = React.memo(function ExpensiveRow({ data }) {
    return <div>{/* expensive rendering logic */}</div>;
});
```

**When it does NOT help (a frequent trap to call out proactively):** if a parent passes an **inline function or a new object/array literal** as a prop on every render (`<ExpensiveRow onClick={() => ...} data={{...}} />`), that prop is a **new reference every time** even if its contents look the same — shallow equality fails, and `React.memo` provides zero benefit. This is exactly why `useCallback`/`useMemo` (Q13) are so often paired with `React.memo` — memoizing the *props themselves* is what actually lets the memoized child's shortcut kick in.

---

### Q19. Why do unnecessary re-renders happen, and what is a "stale closure"?

**Re-renders cascade** by default: when a component re-renders, **all of its children re-render too**, regardless of whether their own specific props actually changed — `React.memo` (Q18) is what opts a subtree out of that default.

**A "stale closure"** happens when a function (e.g., inside `useEffect`, a `setTimeout`, or an event handler) captures a variable's value **from the render it was created in**, and that captured value doesn't update even though the component re-rendered since then with a new value:

```jsx
function Counter() {
    const [count, setCount] = useState(0);
    useEffect(() => {
        const interval = setInterval(() => {
            console.log(count); // always logs the count from WHEN THE EFFECT WAS SET UP (0), never updates!
        }, 1000);
        return () => clearInterval(interval);
    }, []); // empty deps -> effect (and its closure over `count`) never re-runs to "see" a newer count
}
```

**Fix:** either add `count` to the dependency array (so the effect re-runs and captures a fresh closure each time count changes), or use the functional/ref-based pattern to always read the latest value without needing the effect itself to re-run.

---

### Q20. Context API vs. Redux (or Zustand) — when do you actually need an external state library?

- **Context API** (built into React) — fine for **low-frequency-updating, broadly-needed** state (theme, logged-in user, locale) shared across many components. Not designed as a full state-management solution — no built-in devtools, middleware, or fine-grained update optimization out of the box (see Q11's re-render caveat).
- **Redux** (or lighter alternatives like **Zustand**) — brings a centralized store, explicit action-based updates, middleware (e.g., for async logic or logging), time-travel debugging via devtools, and — critically — **fine-grained subscriptions**, so a component only re-renders when the *specific slice* of state it reads actually changes, not on every store update.

**Practical guidance (a strong senior answer, and increasingly the industry-consensus one in 2026):** don't reach for Redux by default — plenty of apps do fine with just local state + lifting state up + Context for a few global concerns. Redux/Zustand earns its complexity when state is **large, deeply shared across unrelated parts of the tree, and updated frequently enough that Context's re-render behavior becomes a real, measured problem** — not before.

## 6.4 Advanced React

### Q21. What are Higher-Order Components (HOCs) and the Render Props pattern? Are they still relevant?

Both are **pre-Hooks** patterns for **sharing logic** between components (the same goal as custom Hooks, Q14, solve today).

**HOC** — a function that takes a component and returns a **new** component with added behavior/props:
```jsx
function withLoading(Component) {
    return function WrappedComponent({ isLoading, ...props }) {
        if (isLoading) return <Spinner />;
        return <Component {...props} />;
    };
}
const UserListWithLoading = withLoading(UserList);
```

**Render Props** — a component takes a **function as a prop** (often named `render` or `children`) and calls it with some internal state/logic, letting the caller decide what to render with that data:
```jsx
<MouseTracker render={({ x, y }) => <p>Mouse at {x}, {y}</p>} />
```

**Are they still relevant?** Mostly **superseded by custom Hooks** for new code — Hooks achieve the same logic-reuse goal with far less boilerplate and without the "wrapper hell" (deeply nested HOCs) or awkward prop-name collisions HOCs can cause. That said, you'll still encounter both in older/legacy codebases and some library APIs, so recognizing them is more important than writing new ones today.

---

### Q22. Explain React's reconciliation algorithm and the Fiber architecture at a high level.

**Reconciliation** is the diffing process (referenced in Q1/Q7) that decides the minimal set of real DOM changes needed after a state update. React makes this tractable (rather than a naively expensive general tree-diff, which is worse than linear) with a couple of key heuristics:
1. **Different element types at the same position → tear down the old subtree, build a new one from scratch** (no attempt to diff a `<div>` against a `<span>`'s children).
2. **Same element type → keep the DOM node, just update its changed attributes**, and recurse into children.
3. **Lists use `key`s** (Q7) to match items across renders instead of assuming pure positional correspondence.

**Fiber** (React's reconciler since v16) reimplemented this process to make it **interruptible** — the old ("stack") reconciler processed the entire tree synchronously in one uninterruptible pass, which could block the main thread long enough to make an app feel janky for large updates. Fiber breaks the work into small units that can be **paused, resumed, prioritized, or abandoned** — this is the underlying mechanism that makes features like `useTransition`/`useDeferredValue` (Q28) and Concurrent Rendering possible at all; without Fiber's interruptible architecture, there would be no way to let an urgent update (like a keystroke) "cut in line" ahead of a big, non-urgent render already in progress.

---

### Q23. What are Error Boundaries, and what can't they catch?

An **Error Boundary** is a component that catches JavaScript errors thrown by its child component tree during rendering, preventing the **entire app** from unmounting/crashing due to one broken component — showing a fallback UI instead.

```jsx
class ErrorBoundary extends React.Component {
    state = { hasError: false };
    static getDerivedStateFromError(error) { return { hasError: true }; }
    componentDidCatch(error, info) { logErrorToService(error, info); }
    render() {
        if (this.state.hasError) return <h1>Something went wrong.</h1>;
        return this.props.children;
    }
}
```

**Must be a class component** — there's currently no Hooks-based equivalent for catching render errors this way. **What it does NOT catch (a commonly-tested list):** errors inside **event handlers** (use a plain try/catch there instead), errors in **asynchronous code** (`setTimeout`, promises), errors during **server-side rendering**, and errors thrown **inside the error boundary itself**.

---

### Q24. Explain code-splitting with `React.lazy` and `Suspense`.

By default, a bundler packages your entire app into one (or a few) JavaScript file(s) — as an app grows, that bundle grows, slowing down the **initial** page load even for code the user may never actually need on this particular page (e.g., an admin panel most users never open).

**Code-splitting** breaks the bundle into smaller chunks, loaded **on demand**:
```jsx
const AdminPanel = React.lazy(() => import('./AdminPanel')); // separate chunk, only fetched when rendered

function App() {
    return (
        <Suspense fallback={<Spinner />}> {/* shown while the chunk is being fetched */}
            <AdminPanel />
        </Suspense>
    );
}
```

`React.lazy` tells React "don't load this component's code until it's actually about to render," and `Suspense` provides the fallback UI to show during that loading gap — commonly paired with route-based splitting (each page/route is its own chunk) as the highest-value, lowest-effort place to apply this.

---

### Q25. SSR vs. CSR vs. SSG — what are the trade-offs?

- **CSR (Client-Side Rendering)** — the server sends a mostly-empty HTML shell; the browser downloads the JS bundle, then React renders everything client-side. Simple, but slower initial content display (blank screen until JS loads/runs) and historically weaker for SEO (though modern crawlers execute JS reasonably well now).
- **SSR (Server-Side Rendering)** — the server runs React on each request to produce **fully-formed HTML**, sent to the browser immediately (fast perceived load, good SEO), then React "hydrates" it client-side (attaching event listeners, becoming interactive). Costs server compute on every request.
- **SSG (Static Site Generation)** — HTML is pre-rendered **at build time**, not per-request — the fastest possible serving (just static files, cacheable on a CDN), but content is only as fresh as the last build; not suited for highly dynamic, per-user content.

**Practical framing:** these aren't just React features — they're typically provided by a **meta-framework** on top of React (like Next.js), which is why "Next.js vs. plain React + a bundler" is itself a common follow-up: plain React (via Create React App/Vite) is CSR-only out of the box.

---

### Q26. What are React Server Components (RSC), and how are they different from traditional SSR?

**Traditional SSR** still ships the **full component's JavaScript** to the browser (for hydration) even though it already rendered HTML server-side — you pay the bundle-size cost of every component regardless.

**React Server Components** (introduced via React 18's architecture, used by frameworks like Next.js's App Router) go further: certain components are marked to run **only on the server**, and their JavaScript is **never sent to the browser at all** — only the rendered output/instructions are. This can dramatically shrink the client bundle for apps with a lot of server-only logic (e.g., a component that just fetches and displays data, with no client-side interactivity).

- **Server Components** — can directly access backend resources (databases, filesystem) and never re-render on the client; cannot use state (`useState`) or effects (`useEffect`), since they don't run there.
- **Client Components** (marked with `"use client"`) — the traditional model: ship JS, hydrate, support state/effects/interactivity.

**Interview framing:** this is a genuinely new mental model (as of the last couple of years), not just an incremental Hooks addition — a candidate who can explain *why* it matters (bundle size, direct backend access without an API layer, security of not exposing server-only code) signals more current knowledge than one who's only worked with the classic CSR/SSR split.

---

### Q27. How does Redux work (actions, reducers, store), and what does middleware like Thunk/Saga add?

Redux enforces a strict, one-directional update cycle: **Component dispatches an Action → Reducer computes new State → Store updates → subscribed Components re-render.**

```jsx
// Action - a plain object describing "what happened"
const incrementAction = { type: 'INCREMENT', payload: 1 };

// Reducer - a PURE function: (previousState, action) => newState — never mutates, never has side effects
function counterReducer(state = { count: 0 }, action) {
    switch (action.type) {
        case 'INCREMENT': return { count: state.count + action.payload };
        default: return state;
    }
}

const store = createStore(counterReducer);
store.dispatch(incrementAction);
```

**Why reducers being pure matters:** it's what makes Redux's devtools "time travel debugging" possible (replay any sequence of actions deterministically) and makes state changes easy to test in isolation.

**The gap middleware fills:** a pure reducer **cannot** perform async work (an API call) or other side effects itself. **Redux Thunk** lets an action be a function (instead of a plain object) that receives `dispatch` and can perform async logic, dispatching further actions as it progresses. **Redux Saga** takes a more structured approach using generator functions to describe complex async flows (sequencing, cancellation, retries) more explicitly and testably than nested thunks.

**Modern note:** **Redux Toolkit (RTK)** is now the officially recommended way to write Redux — it drastically reduces boilerplate (`createSlice` generates actions + reducer together, uses Immer internally so you can write "mutating-looking" code that's actually still immutable under the hood) and includes RTK Query for data-fetching, addressing most historical complaints about Redux being verbose.

---

### Q28. What do `useTransition` and `useDeferredValue` solve (React 18's Concurrent Rendering)?

Both let you tell React that **some** state updates are less urgent than others, so urgent ones (typing, clicking) aren't blocked behind expensive, non-urgent rendering work — made possible by the interruptible Fiber architecture (Q22).

```jsx
function SearchPage() {
    const [query, setQuery] = useState('');
    const [isPending, startTransition] = useTransition();

    function handleChange(e) {
        setQuery(e.target.value);           // URGENT - the input must feel instantly responsive
        startTransition(() => {
            setSearchResults(filterHugeList(e.target.value)); // NON-URGENT - can be deferred/interrupted
        });
    }
    return (
        <>
            <input value={query} onChange={handleChange} />
            {isPending && <Spinner />}
            <ResultsList results={searchResults} />
        </>
    );
}
```

`useDeferredValue` solves a very similar problem from the other direction — instead of wrapping the *update* in a transition, you wrap a **value** itself, telling React it's fine to keep showing a slightly-stale version of that value while a more urgent re-render happens, catching up shortly after.

**Why this matters at a senior level:** before React 18, the only lever for this kind of problem was manual debouncing/throttling of the expensive work in your own code; these hooks give React itself the information needed to make that trade-off automatically and more granularly (it can still interrupt the "non-urgent" work entirely if a newer urgent update comes in, rather than just delaying it by a fixed timer).

---

### Q29. How do you test React components? What's the philosophy behind React Testing Library?

**React Testing Library (RTL)** is built around one guiding principle: **test components the way a user actually interacts with them**, not their internal implementation details (state variables, which specific internal method got called).

```jsx
import { render, screen, fireEvent } from '@testing-library/react';

test('increments count when button is clicked', () => {
    render(<Counter />);
    const button = screen.getByRole('button', { name: /increment/i }); // query by what a USER would see/click
    fireEvent.click(button);
    expect(screen.getByText('Count: 1')).toBeInTheDocument(); // assert on rendered OUTPUT, not internal state
});
```

**Why this philosophy matters (a strong thing to articulate in an interview):** tests written against implementation details (e.g., asserting a specific internal state variable's value) break every time you refactor *how* a component works internally, even when its actual user-facing behavior hasn't changed at all — brittle tests that don't earn their keep. RTL's query methods (`getByRole`, `getByText`, `getByLabelText`) deliberately make it awkward to reach into implementation details, nudging you toward tests that only break when user-facing behavior genuinely changes.

---

### Q30. What performance profiling tools/techniques would you use to find why a React app feels slow?

1. **React DevTools Profiler** — records a render session and shows which components rendered, how long each took, and *why* each one re-rendered (props changed, state changed, parent re-rendered, or forced) — usually the fastest way to find an unnecessary re-render culprit.
2. **Chrome DevTools Performance tab** — for diagnosing issues below the React level entirely (long JS tasks blocking the main thread, layout thrashing, excessive garbage collection).
3. **`why-did-you-render`** (a debugging library) — can automatically log *why* a specific component re-rendered, useful for pinpointing an unstable prop reference (Q18) without manually adding console logs everywhere.
4. **Bundle analysis** (e.g., `source-map-explorer`, Vite's bundle visualizer) — for diagnosing slow **initial load** specifically, distinct from slow re-renders — telling you if code-splitting (Q24) would actually help.

**What separates a senior answer:** correctly distinguishing "slow initial load" (a bundle-size/network problem, fixed by code-splitting/SSR) from "slow interactions after load" (a re-render/computation problem, fixed by memoization or Fiber's Concurrent features) — proposing a memoization fix for a bundle-size problem (or vice versa) is a common way to sound like you're pattern-matching rather than actually diagnosing.


---

## Final Tips for the Interview

1. **For LLD/HLD and scenario questions, always clarify requirements first.** Jumping straight to a solution without asking about scale, constraints, and edge cases is one of the most common reasons candidates lose points, even with 5+ years of experience.
2. **Think out loud, and name your trade-offs.** Interviewers are evaluating your reasoning process and judgment at least as much as the final answer — "there's no single right answer here, but I'd pick X because..." is a strong signal at the senior level.
3. **Connect concepts to real production experience wherever you can.** "I ran into this N+1 problem in production and fixed it with `@EntityGraph`" or "we hit this exact dual-write problem and moved to the Outbox pattern" lands far better than reciting a textbook definition.
4. **Practice writing the LLD code (Parking Lot, LRU Cache, etc.) by hand.** Many interviews expect you to actually type working code on a whiteboard/shared editor, not just describe it verbally — the pointer manipulation in things like the LRU cache is exactly where candidates stumble live.
5. **For scenario/production questions, separate "mitigation" from "root cause fix"** — and say out loud which one you're proposing. Rolling back a bad deploy is a mitigation; finding *why* it was bad is the fix. Conflating the two is a common tell that someone hasn't actually run an incident before.
6. **For security questions (OAuth2/JWT), know the failure modes, not just the happy path** — algorithm-confusion attacks, missing audience validation, and token storage risks (localStorage vs. HttpOnly cookies) come up constantly as follow-ups once you've explained the basic flow correctly.
7. **For React, be ready to justify *why* you'd reach for a hook/pattern, not just how to use it.** "When would you NOT use `useMemo`?" and "why do the Rules of Hooks exist?" are exactly the kind of second-order questions that separate a mid-level from a senior-level answer.

Good luck with your interviews!
