# Senior Java Full-Stack Interview Bank
## 360 questions with answer hints • 5 years of experience

Java 11 / 17 / 21 • Spring • Spring Boot • Spring MVC / REST • Hibernate / JPA • SQL • Transactions • Security • Concurrency • Microservices • Modular monoliths • JavaScript • React • Production • Ownership

Prepared 29 October 2026. This is a revision and self-assessment bank, not a prediction of one company's interview or a guarantee of selection.

## How to use this bank

- **★ Priority** marks 97 questions to prepare first. All other questions are useful follow-ups.
- Cover the hint and give a 60–90 second spoken answer. Then compare against the hint.
- For senior-level answers, add: **mechanism → concrete example → trade-off/failure case → how you would test or measure it**.
- For a scenario, start with **impact and evidence**, then mitigation, diagnosis, fix and verification. Do not jump straight to scaling or rewriting.
- For coding/SQL questions, write the solution yourself. Hints deliberately do not contain full implementations. State assumptions, complexity, edge cases and test cases.
- Score each answer: **0** = cannot explain; **1** = definition only; **2** = mechanism + example; **3** = trade-offs + failure handling + validation. Revisit 0s and 1s first.
- Use genuine project examples. Bulk ingestion, external API calls, query optimization, security migrations and releases are strong examples only to the extent that you actually worked on them. Never invent performance numbers.

## Version and terminology guardrails

- The Spring examples assume the common **Spring Framework 6 / Spring Boot 3 / Spring Security 6 style**, unless a question explicitly says otherwise. This is a study baseline, not a claim that those are the newest versions. Check the target project's actual versions.
- Java 17 includes features finalized in earlier releases: records and instanceof patterns in 16, text blocks in 15, switch expressions in 14. Sealed classes became final in 17. Avoid saying all these first appeared in 17.
- In **Java 21**, virtual threads, record patterns, pattern switch and sequenced collections are final. Structured concurrency, scoped values and string templates are preview features **in that release**; later releases may differ. Java 21 virtual-thread pinning behavior is also version-specific. [S1, S2]
- PostgreSQL is the reference when a SQL hint is engine-specific. SQL isolation and index behavior vary by database and version. In particular, PostgreSQL Read Uncommitted behaves as Read Committed, and its Repeatable Read prevents phantoms but can still permit serialization anomalies. [S8]
- A hint is a recall prompt, not an exhaustive answer. “Use retries,” “add a cache,” or “increase pool size” is incomplete without conditions, limits and failure behavior.

## Topic index and first-pass questions

| Section | Topic | Question range | Priority questions |
|---|---|---|---|
| 01 | [Java fundamentals and object-oriented design](#section-01) | Q001–Q015 | Q001, Q002, Q003, Q007, Q012 |
| 02 | [Collections and data structures](#section-02) | Q016–Q030 | Q016, Q017, Q018, Q024, Q029 |
| 03 | [Lambdas, streams, and Optional](#section-03) | Q031–Q045 | Q031, Q032, Q033, Q041 |
| 04 | [Java 11, 17, and 21](#section-04) | Q046–Q060 | Q046, Q047, Q048, Q056 |
| 05 | [JVM, memory, garbage collection, and performance](#section-05) | Q061–Q075 | Q061, Q062, Q063, Q070 |
| 06 | [Multithreading and synchronization](#section-06) | Q076–Q090 | Q076, Q077, Q078, Q080 |
| 07 | [Executors, CompletableFuture, and concurrency design](#section-07) | Q091–Q105 | Q091, Q092, Q093, Q099 |
| 08 | [Spring core, dependency injection, and AOP](#section-08) | Q106–Q120 | Q106, Q107, Q109, Q113 |
| 09 | [Spring Boot configuration and operations](#section-09) | Q121–Q135 | Q121, Q122, Q125, Q128 |
| 10 | [Spring MVC, REST APIs, and HTTP](#section-10) | Q136–Q150 | Q136, Q137, Q139, Q141 |
| 11 | [Hibernate and JPA persistence](#section-11) | Q151–Q165 | Q151, Q152, Q154, Q156 |
| 12 | [Transactions, isolation, and locking](#section-12) | Q166–Q180 | Q166, Q167, Q168, Q174 |
| 13 | [SQL, indexing, and database design](#section-13) | Q181–Q195 | Q181, Q182, Q184, Q188 |
| 14 | [SQL exercises: explain the query approach](#section-14) | Q196–Q210 | Q196, Q197, Q202 |
| 15 | [Spring Security, authentication, and authorization](#section-15) | Q211–Q225 | Q211, Q212, Q214, Q219 |
| 16 | [Microservices and distributed systems](#section-16) | Q226–Q240 | Q226, Q227, Q228, Q230 |
| 17 | [Modular monoliths and architecture boundaries](#section-17) | Q241–Q255 | Q241, Q242, Q244, Q248 |
| 18 | [JavaScript fundamentals and asynchronous behavior](#section-18) | Q256–Q270 | Q256, Q257, Q258, Q263 |
| 19 | [React components, rendering, and hooks](#section-19) | Q271–Q285 | Q271, Q272, Q274, Q277 |
| 20 | [React state management, frontend architecture, and performance](#section-20) | Q286–Q300 | Q286, Q287, Q290 |
| 21 | [Testing, build pipelines, and release confidence](#section-21) | Q301–Q315 | Q301, Q302, Q304, Q310 |
| 22 | [Production bugs and incident scenarios](#section-22) | Q316–Q330 | Q316, Q317, Q319, Q323, Q330 |
| 23 | [Ownership, delivery, and senior-engineer behavior](#section-23) | Q331–Q345 | Q331, Q332, Q333, Q337 |
| 24 | [Coding and system-design follow-ups](#section-24) | Q346–Q360 | Q346, Q353, Q355, Q356 |

## Suggested revision order

1. **Java-round foundation:** sections 1–8, 11–15. Focus on collections, concurrency, proxies, persistence and transaction correctness.
2. **Application engineering:** sections 9–10 and 16–17. Explain API contracts, operational behavior and architectural trade-offs.
3. **Full-stack round:** sections 18–20. Write working React/JavaScript examples and explain rendering, state and async races.
4. **Senior-level differentiation:** sections 21–24. Practise debugging with evidence, safe delivery, real ownership stories and system design.

If time is short, finish the starred questions first and practise at least one SQL problem, one Java coding problem and one production scenario per session. Prioritize understanding over completing the list.

<a id="section-01"></a>

## 01. Java fundamentals and object-oriented design

**Q001 ★ Priority. How do equals() and hashCode() work together, and what breaks if only equals() is overridden?**

*Hint:* Equal objects must share a hash; unequal objects may collide. Explain failed hash-based lookups and test equality contracts.

**Q002 ★ Priority. How would you create an immutable Java class containing a List?**

*Hint:* Restrict construction/mutation; final fields; defensive copies on input/output; nested elements must also be immutable or copied.

**Q003 ★ Priority. Is Java pass-by-value or pass-by-reference?**

*Hint:* Always pass-by-value, including copied object references. Mutation can affect the original object; reassigning the parameter cannot replace the caller's reference.

**Q004. When would you choose composition over inheritance?**

*Hint:* Prefer replaceable collaborators and narrow interfaces; inheritance requires a genuine substitutable is-a relationship. Discuss fragile base classes.

**Q005. How do overloading and overriding differ?**

*Hint:* Compile-time overload selection versus runtime instance-method dispatch; covariant returns; static methods are hidden, not overridden.

**Q006. What do final, finally, and finalize mean?**

*Hint:* Restriction, cleanup block, and obsolete finalization mechanism respectively. Prefer try-with-resources; final references do not make objects immutable.

**Q007 ★ Priority. Checked versus unchecked exceptions: how do you design an API's exceptions?**

*Hint:* Recovery expectations, domain errors, preserving causes, consistent translation; avoid catching Exception and silently continuing.

**Q008. How does try-with-resources handle multiple failures?**

*Hint:* AutoCloseable; reverse close order; primary exception retained; close failures become suppressed exceptions.

**Q009. Why is String immutable, and when should you use StringBuilder?**

*Hint:* Safe sharing/hash caching/string pool; StringBuilder for repeated mutable concatenation; avoid claiming every + expression is inefficient.

**Q010. What is the difference between == and equals() for strings and boxed values?**

*Hint:* Identity versus logical equality; boxing caches can disguise bugs; unboxing null throws NullPointerException.

**Q011. How do interfaces and abstract classes differ?**

*Hint:* Multiple interface contracts versus one class inheritance chain; shared state/constructors in abstract classes; default-method conflict resolution.

**Q012 ★ Priority. What is a good example of SOLID in a Spring service?**

*Hint:* Depend on PaymentGateway interface; inject implementations; separate orchestration from persistence; explain a real change made easier, not just acronyms.

**Q013. What is PECS in generics, and why is List<Integer> not a List<Number>?**

*Hint:* Producer extends, consumer super; invariance protects type safety; distinguish reading from adding.

**Q014. What does type erasure prevent or complicate?**

*Hint:* Runtime generic type checks, new T(), generic arrays, overloaded methods with identical erasure; bridge methods preserve polymorphism.

**Q015. How would you represent money and timestamps correctly?**

*Hint:* BigDecimal with explicit rounding and currency; avoid binary floating-point for exact money; Instant for timeline, ZoneId for local presentation.

<a id="section-02"></a>

## 02. Collections and data structures

**Q016 ★ Priority. Explain HashMap lookup, insertion, collisions, and resizing.**

*Hint:* Hash to bucket; equality resolves keys; collision bins may treeify under implementation conditions; resize cost; average O(1), not unconditional.

**Q017 ★ Priority. Why should HashMap keys normally be immutable?**

*Hint:* Changing fields used by equality/hash after insertion can make entries unreachable by lookup; immutable identifiers prevent this.

**Q018 ★ Priority. How does ConcurrentHashMap differ from Collections.synchronizedMap()?**

*Hint:* Concurrent access and atomic per-key operations versus a synchronized wrapper; compound operations still need appropriate atomicity.

**Q019. ArrayList versus LinkedList: which would you choose and why?**

*Hint:* Array locality/random access versus node overhead; list-position discovery can dominate insertion; choose from workload, not slogan.

**Q020. HashSet versus LinkedHashSet versus TreeSet?**

*Hint:* Uniqueness with no guaranteed order, insertion order, or sorted order; TreeSet ordering must align with intended equality.

**Q021. How do Comparable and Comparator differ?**

*Hint:* Natural order versus external order strategies; comparator chaining; null handling; avoid subtraction overflow.

**Q022. What causes ConcurrentModificationException?**

*Hint:* Best-effort fail-fast checks, even in one thread; modifying outside iterator rules; use iterator.remove or suitable collection operations.

**Q023. How do fail-fast and weakly consistent iterators differ?**

*Hint:* Fail-fast is not a concurrency guarantee; concurrent collection iterators can tolerate changes without providing a fixed snapshot.

**Q024 ★ Priority. When should you use computeIfAbsent or putIfAbsent?**

*Hint:* Atomic map operations for per-key initialization; avoid slow/recursive mapping functions; thread safety of stored mutable values is separate.

**Q025. When is CopyOnWriteArrayList appropriate?**

*Hint:* Many reads, rare writes, snapshot iteration; full-array copy and memory cost make frequent updates expensive.

**Q026. How do PriorityQueue and ArrayDeque differ?**

*Hint:* Heap priority ordering versus double-ended queue/stack; PriorityQueue iteration is not sorted; ArrayDeque generally suits stack usage.

**Q027. What is the difference between an unmodifiable view and an immutable copy?**

*Hint:* Views reflect backing changes; copies isolate structure; neither automatically deep-copies mutable elements.

**Q028. How would you implement an LRU cache?**

*Hint:* Access-order LinkedHashMap plus eviction for simple cases; concurrency, TTL, size accounting and stampede control need additional design.

**Q029 ★ Priority. Which structure would you use for a bounded producer-consumer buffer?**

*Hint:* BlockingQueue; capacity establishes backpressure; choose blocking, timed offer, or rejection behavior explicitly.

**Q030. Why can a collection retain too much memory after a workload spike?**

*Hint:* Retained capacity, unbounded caches/queues, references held by entries; measure retained heap rather than only element count.

<a id="section-03"></a>

## 03. Lambdas, streams, and Optional

**Q031 ★ Priority. map versus flatMap: explain with orders and line items.**

*Hint:* map transforms each element; flatMap flattens nested streams. Orders to all line items is the natural example.

**Q032 ★ Priority. How do intermediate and terminal stream operations execute?**

*Hint:* Intermediate operations are lazy; terminal operation triggers traversal; short-circuiting can limit work; streams are single-use.

**Q033 ★ Priority. When would you avoid parallelStream()?**

*Hint:* Blocking I/O, small inputs, shared mutation, order constraints, common-pool contention; benchmark representative workloads.

**Q034. How do reduce and collect differ?**

*Hint:* Associative reduction versus mutable accumulation; identity/combiner correctness matters, especially in parallel execution.

**Q035. How do you group employees by department and count them?**

*Hint:* groupingBy with counting; choose key/value types; discuss null keys and ordering requirements.

**Q036. How do you find the top three frequent words with deterministic ties?**

*Hint:* Normalize/tokenize; groupingBy/counting; sort count descending then word ascending; limit; consider heap for large cardinality.

**Q037. What happens with duplicate keys in Collectors.toMap()?**

*Hint:* Default collector throws; define merge policy intentionally; choose map supplier if ordering matters.

**Q038. How do findFirst and findAny differ?**

*Hint:* Encounter-order guarantee versus any matching element; ordering can constrain parallel optimization.

**Q039. Why should side effects inside stream pipelines be avoided?**

*Hint:* Interference, race conditions, unpredictable execution under optimization; use pure transformations and terminal collection.

**Q040. How do primitive streams improve numeric processing?**

*Hint:* Avoid boxing; IntStream/LongStream/DoubleStream; sum/statistics; account for overflow and precision.

**Q041 ★ Priority. Optional.orElse versus orElseGet: what is the practical difference?**

*Hint:* orElse evaluates its argument eagerly; orElseGet invokes supplier only when empty; useful for expensive fallback calls.

**Q042. Where would you use Optional, and where would you avoid it?**

*Hint:* Return type for possibly absent value; avoid reflexive use in entity fields/parameters; never return null Optional.

**Q043. Can a lambda capture a changing local variable?**

*Hint:* Captured locals must be final or effectively final; referenced objects can mutate, creating separate concurrency risks.

**Q044. What are Predicate, Function, Consumer, Supplier, and UnaryOperator?**

*Hint:* Boolean test, transform, side effect, value producer, same-type transform; show one composition use case.

**Q045. How would you debug a complex stream pipeline?**

*Hint:* Simplify stages; inspect intermediate results; tests for empty/duplicate/null inputs; peek is not a reliable business-side-effect mechanism.

<a id="section-04"></a>

## 04. Java 11, 17, and 21

**Q046 ★ Priority. Which useful APIs and language features arrived in Java 11?**

*Hint:* Standard HTTP Client; String.isBlank/strip/lines/repeat; Files.readString/writeString; var in lambda parameters. Local-variable var itself arrived in Java 10.

**Q047 ★ Priority. What can a Java 17 application use that a Java 11 application cannot?**

*Hint:* Records, text blocks, switch expressions, instanceof patterns, sealed classes; distinguish available by 17 from first introduced in 17.

**Q048 ★ Priority. Which major Java 21 features are final, and which were preview?**

*Hint:* Final: virtual threads, sequenced collections, record patterns, pattern switch. Structured concurrency, scoped values and string templates were preview in 21.

**Q049. When would you use a record instead of a normal class?**

*Hint:* Transparent data carrier with generated accessors/equality; shallow immutability; unsuitable for mutable entity requirements without careful mapping support.

**Q050. Are records deeply immutable?**

*Hint:* No; a record component can reference a mutable collection/object. Defensive copying and immutable elements are separate concerns.

**Q051. What problem do sealed classes solve?**

*Hint:* Restrict allowed subtype hierarchy; model closed domains; combine with exhaustive pattern matching.

**Q052. How do switch expressions differ from traditional switch statements?**

*Hint:* Produce a value; arrow syntax avoids fall-through; yield inside blocks; exhaustiveness requirements.

**Q053. How does pattern matching for instanceof simplify code?**

*Hint:* Type test and binding together; scope follows control flow; avoids repeated casts.

**Q054. What are record patterns and guarded pattern switch cases?**

*Hint:* Destructure record components; type-based branches and when guards; ordering/dominance and null handling matter.

**Q055. What are sequenced collections in Java 21?**

*Hint:* Uniform first/last/reversed operations for collections with encounter order; reversed views are not necessarily independent copies.

**Q056 ★ Priority. Do virtual threads make CPU-heavy code faster?**

*Hint:* No inherent CPU speedup; improve scalability of blocking workloads; CPU cores and downstream capacity still bound throughput.

**Q057. What can pin virtual threads in Java 21?**

*Hint:* Blocking while holding synchronized monitors or in certain native calls can retain carrier threads; use version-specific diagnostics and avoid long blocking critical sections.

**Q058. Should you pool virtual threads?**

*Hint:* Normally use one per task; bound scarce resources with semaphores/pools instead; do not create unlimited downstream concurrency.

**Q059. How would you migrate a Java 11 service to Java 17 or 21?**

*Hint:* Check framework/dependency support, removed APIs/strong encapsulation, build toolchain, tests, load/GC baselines; canary and rollback plan.

**Q060. How do --release, preview flags, and JDK runtime compatibility matter?**

*Hint:* --release targets API/bytecode level; preview requires matching compiler/runtime release and enablement; do not treat preview APIs as stable contracts.

<a id="section-05"></a>

## 05. JVM, memory, garbage collection, and performance

**Q061 ★ Priority. Explain heap, thread stacks, metaspace, and native memory.**

*Hint:* Objects mainly on heap; stack frames per thread; class metadata in metaspace; direct buffers/JIT/native allocations consume additional process memory.

**Q062 ★ Priority. How do you choose Xms and Xmx on an EC2 instance or container?**

*Hint:* Measure working set/allocation under load; reserve native/thread/OS/agent headroom; heap is only part of process memory; respect container limit.

**Q063 ★ Priority. How does an object become eligible for garbage collection?**

*Hint:* Unreachable from GC roots; cycles alone do not prevent collection; roots include live thread references and static reachability.

**Q064. What is a memory leak in a garbage-collected application?**

*Hint:* Unneeded objects remain reachable; caches, listeners, ThreadLocal values, queues; inspect retained heap/dominator paths.

**Q065. How do minor, major, and full GC terms differ?**

*Hint:* Collector-specific terminology; young versus wider collection; avoid assuming every collector uses identical generations or log labels.

**Q066. Compare Serial, Parallel, G1, and ZGC at a high level.**

*Hint:* Small/simple workloads, throughput, balanced pauses, low latency respectively; validate overhead and pause goals with actual workload.

**Q067. What is the Java 8 versus Java 17/21 default GC?**

*Hint:* Server-class HotSpot Java 8 typically Parallel; Java 9+ generally G1. JVM implementation and ergonomics/platform constraints can affect defaults.

**Q068. How would you investigate frequent GC with stable post-GC heap usage?**

*Hint:* High allocation churn rather than retained leak; allocation profiling, large temporary objects, serialization, request volume.

**Q069. How would you investigate continuously growing post-GC heap usage?**

*Hint:* Retained objects/live-set growth; heap dump comparison; reference chains; distinguish legitimate cache warmup from leak.

**Q070 ★ Priority. Thread dump versus heap dump versus GC logs: when do you use each?**

*Hint:* Thread states/locks; retained objects; allocation/collection pauses; correlate timestamps with request latency and deployment changes.

**Q071. What is stop-the-world, and can a concurrent collector still pause?**

*Hint:* Application threads stop for some phases; concurrent does not mean pause-free; analyze pause frequency and tail latency.

**Q072. Why can a process be killed without Java OutOfMemoryError?**

*Hint:* OS/container memory limit exceeded; inspect exit reason/events/RSS/native usage; increasing Xmx may worsen the problem.

**Q073. What are strong, weak, soft, and phantom references?**

*Hint:* Different reachability/cleanup semantics; weak-key cache caveats; soft references are not predictable cache sizing policies.

**Q074. How would you use JFR to investigate a slow service?**

*Hint:* Time-bounded recording; CPU samples, allocations, locks, I/O; correlate with traffic; assess production overhead and sensitive data.

**Q075. What do P95 and P99 latency mean, and can you average them across instances?**

*Hint:* Percentile thresholds over a defined population/window; P99 includes more tail; do not average instance percentiles—aggregate compatible distributions.

<a id="section-06"></a>

## 06. Multithreading and synchronization

**Q076 ★ Priority. What is the difference between concurrency and parallelism?**

*Hint:* Overlapping progress versus simultaneous execution; I/O-bound and CPU-bound workloads require different designs.

**Q077 ★ Priority. What guarantees does synchronized provide?**

*Hint:* Mutual exclusion on the same monitor plus visibility through unlock/lock happens-before; instance and class monitors differ.

**Q078 ★ Priority. volatile versus synchronized: when is volatile insufficient?**

*Hint:* Visibility and ordering for the variable; compound operations like count++ remain non-atomic; coordinate multi-field invariants with locks.

**Q079. Why must wait, notify, and notifyAll be called while owning the monitor?**

*Hint:* Monitor protocol; otherwise IllegalMonitorStateException; wait releases that monitor and reacquires it before returning.

**Q080 ★ Priority. wait versus sleep: how do lock ownership and wakeup differ?**

*Hint:* wait releases its monitor; sleep does not release held locks; both can be interrupted; wait requires condition-loop checks.

**Q081. notify versus notifyAll: which would you choose?**

*Hint:* One arbitrary waiter versus all waiters; condition predicates determine correctness; notifyAll can avoid waking only the wrong kind of waiter.

**Q082. Why must wait be inside a while loop rather than an if?**

*Hint:* Spurious wakeups and another thread consuming the condition; recheck predicate after reacquiring the lock.

**Q083. What is a deadlock, and how would you prevent it?**

*Hint:* Circular lock dependency; consistent lock order, shorter critical sections, timed lock acquisition; diagnose with thread dumps.

**Q084. How do deadlock, livelock, and starvation differ?**

*Hint:* No progress due to waiting, repeated reactions without progress, or one participant never getting resources; remedies depend on cause.

**Q085. What does Thread.interrupt() actually do?**

*Hint:* Cooperative cancellation signal; interruptible operations may throw; preserve/propagate interruption appropriately; it does not forcibly stop arbitrary code.

**Q086. How does ReentrantLock differ from synchronized?**

*Hint:* Timed/interruptible acquisition, optional fairness, multiple Conditions; always unlock in finally; extra flexibility has complexity cost.

**Q087. What is safe publication, and how can you achieve it?**

*Hint:* Expose fully initialized objects through volatile, locks, static initialization, or concurrent containers; avoid constructor this escape.

**Q088. What does happens-before mean in the Java Memory Model?**

*Hint:* Visibility/ordering relationship, not wall-clock order; examples: unlock→lock, volatile write→read, thread start/join relationships.

**Q089. How can ThreadLocal cause leakage in thread pools?**

*Hint:* Worker threads outlive requests; stale values retain objects/context; remove in finally; asynchronous work needs explicit context strategy.

**Q090. How would you implement even/odd printing using two threads?**

*Hint:* Shared counter, same lock, while-loop predicate, wait/notifyAll, termination wakeup and interruption handling; avoid busy waiting.

<a id="section-07"></a>

## 07. Executors, CompletableFuture, and concurrency design

**Q091 ★ Priority. How does ThreadPoolExecutor use core size, queue, maximum size, and rejection?**

*Hint:* Create to core, then queue, then grow to max when queue fills, then reject; unbounded queues often make max size ineffective.

**Q092 ★ Priority. How would you size a thread pool?**

*Hint:* CPU-bound near available cores as starting point; I/O uses measured wait/compute ratio; cap by DB/API capacity and load-test.

**Q093 ★ Priority. Future versus CompletableFuture: what changes in application design?**

*Hint:* Future mainly wait/cancel/result; CompletableFuture supports composition and error stages; blocking get/join still blocks the caller.

**Q094. thenApply versus thenCompose versus thenCombine?**

*Hint:* Transform a result; flatten a dependent future; combine independent futures. Distinguish dependency from concurrency.

**Q095. What is the difference between synchronous and Async completion stages?**

*Hint:* Non-Async may run on completing/calling thread; Async uses executor/common pool unless provided; choose explicit executors for blocking work.

**Q096. How do handle, exceptionally, and whenComplete differ?**

*Hint:* Transform success/failure, recover failures, observe completion; throwing from a stage can change downstream outcome.

**Q097. How would you call seven external APIs concurrently with a shared deadline?**

*Hint:* Bound concurrency; overall and per-call timeouts; compose futures; failure policy; cancellation/cleanup; propagate tracing context.

**Q098. Does CompletableFuture.orTimeout stop the underlying operation?**

*Hint:* No guarantee; completes future exceptionally but underlying work may continue; configure HTTP timeouts and cancellation at the client level.

**Q099 ★ Priority. How would you prevent an unbounded queue from exhausting memory?**

*Hint:* Bound queues; backpressure/rejection policy; admission control; queue-depth/age metrics; caller behavior when capacity is exhausted.

**Q100. CountDownLatch versus CyclicBarrier versus Semaphore?**

*Hint:* One-shot completion coordination; reusable rendezvous; permits for scarce resources. Use matching lifecycle semantics.

**Q101. AtomicInteger versus LongAdder: when would you choose each?**

*Hint:* Atomic read-modify-write for exact coordination; LongAdder reduces contention for metrics; sum is not an atomic global snapshot.

**Q102. What is CAS, and what is the ABA problem?**

*Hint:* Conditional update based on expected value; value changes away and back can hide history; stamped/versioned references when relevant.

**Q103. How do you shut down an ExecutorService safely?**

*Hint:* Stop submissions; await bounded completion; escalate interruption; preserve interrupt status; explain tasks that ignore interrupts.

**Q104. Why can nested tasks deadlock on a fixed thread pool?**

*Hint:* All workers block waiting for queued child tasks; avoid blocking dependencies on the same exhausted executor or redesign composition.

**Q105. How do you make concurrency bugs reproducible in tests?**

*Hint:* Latches/barriers to force interleavings, repeated stress tests, time bounds and invariants; sleep-based timing tests are unreliable.

<a id="section-08"></a>

## 08. Spring core, dependency injection, and AOP

**Q106 ★ Priority. Explain IoC and dependency injection using a service and repository.**

*Hint:* Container owns construction/wiring; service declares dependencies; constructor injection makes required dependencies explicit and testable.

**Q107 ★ Priority. @Component, @Service, @Repository, and @Bean: when do you use each?**

*Hint:* Scanned stereotypes versus factory method; repository exception translation; @Bean suits third-party or customized construction.

**Q108. @Primary versus @Qualifier: what happens with multiple implementations?**

*Hint:* Primary is default candidate preference; qualifier narrows selection; explicit business-role qualifiers avoid accidental wiring.

**Q109 ★ Priority. What are Spring bean scopes, and is a singleton bean automatically thread-safe?**

*Hint:* Singleton per container, prototype, web scopes; shared mutable fields need coordination; prefer stateless services.

**Q110. How do you inject a fresh prototype into a singleton?**

*Hint:* ObjectProvider/provider or scoped proxy; direct constructor injection resolves only once during singleton creation.

**Q111. What happens during the Spring bean lifecycle?**

*Hint:* Instantiation, dependency population, post-processors, initialization callbacks, use, destruction; prototype destruction is not automatically managed like singleton destruction.

**Q112. How do BeanFactory and ApplicationContext differ?**

*Hint:* Core bean container versus richer application facilities including events/resources; ApplicationContext is typical application entry point.

**Q113 ★ Priority. How do Spring AOP proxies work, and why does self-invocation matter?**

*Hint:* Calls must cross proxy for advice; JDK interface versus subclass proxies; this.method() bypasses normal proxy interception.

**Q114. How do @Configuration and @Component differ for @Bean method calls?**

*Hint:* Full configuration can intercept inter-bean calls; lite mode or proxyBeanMethods=false uses normal Java calls; prefer method-parameter injection.

**Q115. Can Spring advise private or final methods?**

*Hint:* Proxy-type limitations; subclass proxies cannot override final/private methods; interface proxy behavior differs; design interceptable boundaries.

**Q116. Why do circular dependencies indicate a design problem?**

*Hint:* Tangled responsibilities and initialization order; refactor orchestration/interfaces; @Lazy is a workaround, not automatic architectural repair.

**Q117. How would you validate strongly typed configuration?**

*Hint:* @ConfigurationProperties with binding/validation; fail startup for invalid required settings; avoid scattered @Value strings.

**Q118. What is the difference between a filter, interceptor, and AOP advice?**

*Hint:* Servlet boundary, MVC handler lifecycle, method invocation; choose security/tracing/business concerns at appropriate layer.

**Q119. How do Spring application events behave by default?**

*Hint:* Typically synchronous in-process dispatch; not automatically durable; async configuration changes thread/error/transaction behavior.

**Q120. How would you apply Strategy or Factory in Spring without a large switch?**

*Hint:* Inject implementations keyed by supported type; validate uniqueness/unsupported keys; separate selection from behavior.

<a id="section-09"></a>

## 09. Spring Boot configuration and operations

**Q121 ★ Priority. What does Spring Boot add on top of Spring?**

*Hint:* Auto-configuration, starters, dependency management, executable packaging and operational integration; it does not replace Spring's DI container.

**Q122 ★ Priority. How does auto-configuration decide which beans to create?**

*Hint:* Classpath, properties, existing beans and conditional annotations; inspect condition evaluation report; user beans can cause configuration to back off.

**Q123. What does @SpringBootApplication combine?**

*Hint:* Configuration, component scanning and auto-configuration; package placement affects scan scope; avoid accidentally scanning unrelated modules.

**Q124. How do you investigate a bean missing only in one environment?**

*Hint:* Profiles, property values, scan paths, conditions, dependency/classpath differences; compare effective configuration without revealing secrets.

**Q125 ★ Priority. How would you manage environment-specific config and secrets?**

*Hint:* Externalized configuration, typed binding, secret manager; verify precedence for actual property sources; never commit credentials.

**Q126. How does a starter differ from a BOM?**

*Hint:* Starter assembles dependencies; BOM manages compatible versions; neither makes an arbitrary override safe.

**Q127. What are important concerns in a Boot 2 to Boot 3 migration?**

*Hint:* Java 17 baseline, javax-to-jakarta changes for relevant APIs, Spring Security configuration and Hibernate behavior; integration tests and dependency compatibility.

**Q128 ★ Priority. How do liveness and readiness checks differ?**

*Hint:* Restart decision versus traffic eligibility; avoid making liveness depend on a temporary DB outage and causing restart storms.

**Q129. What should and should not be exposed through Actuator?**

*Hint:* Selected health/metrics endpoints under access control; avoid public config/heap/management exposure; sanitize sensitive information.

**Q130. How do graceful shutdown and load-balancer draining work together?**

*Hint:* Stop new traffic, allow in-flight work within a deadline, drain consumers/executors, then terminate; ensure deployment timeouts align.

**Q131. When would you use @Async, and what pitfalls must you handle?**

*Hint:* Proxy entry, explicit executor, error handling, context propagation; transaction does not automatically travel to new thread; self-invocation bypasses advice.

**Q132. What can go wrong with @Scheduled in multiple replicas?**

*Hint:* Every replica may run the job; use distributed coordination/scheduler ownership plus idempotency; handle lock expiry and failover.

**Q133. How would you diagnose slow application startup?**

*Hint:* Startup profiling, bean initialization, classpath scanning, migrations and remote calls; do not hide failures with indiscriminate lazy initialization.

**Q134. How would you reload configuration without restarting safely?**

*Hint:* Explicit supported reload mechanism; validate new values, atomic swap, rollback/audit; not every bean or property can be safely refreshed.

**Q135. How do you separate application metrics, logs, and traces?**

*Hint:* Metrics identify patterns; logs provide events; traces follow requests; correlate IDs; Micrometer/Prometheus metrics are not application logs.

<a id="section-10"></a>

## 10. Spring MVC, REST APIs, and HTTP

**Q136 ★ Priority. Explain the request flow through a Spring MVC application.**

*Hint:* Servlet filters, DispatcherServlet, handler mapping/adapter, argument binding/validation, controller, serialization; security can reject before controller execution.

**Q137 ★ Priority. How do you design a resource-oriented REST endpoint?**

*Hint:* Resource nouns, appropriate verbs/statuses, stable identifiers, pagination; DTO contract distinct from persistence model.

**Q138. PUT versus PATCH versus POST: what are the semantics?**

*Hint:* Replacement, partial change, creation/action; PUT idempotent by semantics, PATCH depends on operation; POST may implement idempotency keys.

**Q139 ★ Priority. How would you make a create-payment endpoint idempotent?**

*Hint:* Client key scoped to operation/user; unique constraint and durable result; same key/payload validation; concurrent requests and timeout ambiguity.

**Q140. Which status codes would you use for common API outcomes?**

*Hint:* 201 with location, 202 for accepted async work, 204, 400, 401, 403, 404, 409, 429, 500/503; distinguish client and transient server failures.

**Q141 ★ Priority. How do @RestControllerAdvice and @ExceptionHandler work?**

*Hint:* Central MVC exception mapping; consistent safe response schema; correlation ID; do not assume they catch all security-filter errors.

**Q142. How do you validate request DTOs and nested objects?**

*Hint:* Bean Validation, @Valid, nested validation, groups when justified; distinguish syntactic checks from domain rules and DB constraints.

**Q143. How would you represent validation errors consistently?**

*Hint:* Field/global errors, stable machine codes, readable detail and trace ID; ProblemDetail where supported; exclude stack traces and secret data.

**Q144. How would you version an API without breaking clients?**

*Hint:* Additive compatibility first; explicit breaking version boundary; deprecation window, consumer contracts, rollout/usage tracking.

**Q145. Offset versus cursor pagination: when does each fit?**

*Hint:* Offset simplicity versus deep-scan/concurrent-change issues; cursor over deterministic indexed sort with unique tie-breaker.

**Q146. How do ETag and If-Match help prevent lost updates?**

*Hint:* Version-aware conditional write; reject stale version with precondition failure; compare-and-update atomically.

**Q147. How would you secure and scale a file-upload endpoint?**

*Hint:* Size/type/content checks, safe storage names, ownership, malware scanning as needed; stream rather than buffer entire files; avoid path traversal.

**Q148. What is CORS, and why can an API work in Postman but fail in a browser?**

*Hint:* Browser-origin policy/preflight; allowed origin/method/header/credentials configuration; CORS is not server authorization.

**Q149. Spring MVC versus WebFlux: how would you choose?**

*Hint:* Blocking servlet model versus reactive nonblocking execution; end-to-end dependencies, team skill, workload; blocking JPA can undermine reactive benefits.

**Q150. How would you expose a long-running bulk import over REST?**

*Hint:* 202 plus job ID/status endpoint; durable queue, progress/failure state, idempotency, cancellation semantics; no request thread held for minutes.

<a id="section-11"></a>

## 11. Hibernate and JPA persistence

**Q151 ★ Priority. How do JPA, Hibernate, and Spring Data JPA differ?**

*Hint:* Specification, implementation, repository abstraction; understand which layer owns mapping, persistence context, and repository generation.

**Q152 ★ Priority. Explain transient, managed, detached, and removed entity states.**

*Hint:* Persistence-context lifecycle; managed changes are tracked; detachment ends tracking; merge copies state into a managed instance.

**Q153. What is the first-level cache, and how does it differ from second-level cache?**

*Hint:* Persistence-context identity cache versus optional shared entity cache; staleness/invalidation and transaction scope matter.

**Q154 ★ Priority. What is dirty checking, and when is SQL executed?**

*Hint:* Managed state changes detected during flush; flush may precede queries or commit; flush is not commit.

**Q155. Persist versus merge: what happens to the passed object?**

*Hint:* Persist makes a new entity managed; merge returns managed copy/instance; passed detached instance does not automatically become managed.

**Q156 ★ Priority. What is N+1, and how do you prove and fix it?**

*Hint:* Count executed SQL; fetch join, entity graph, DTO projection or batch fetching; choose by required data and pagination needs.

**Q157. LAZY versus EAGER: what are the defaults and trade-offs?**

*Hint:* JPA to-one EAGER, to-many LAZY by default; EAGER can still cause extra selects; explicit fetch plans beat blanket eager loading.

**Q158. What causes LazyInitializationException, and why is OSIV controversial?**

*Hint:* Lazy access after context closes; load/project within service boundary; OSIV can hide late queries and performance problems.

**Q159. What do mappedBy and the owning side mean?**

*Hint:* Owning side writes relationship mapping; inverse side mirrors; keep both Java sides synchronized; schema mapping must match ownership.

**Q160. CascadeType.REMOVE versus orphanRemoval: how do they differ?**

*Hint:* Parent removal cascade versus disassociation-driven child removal; avoid cascading deletes across shared many-to-many entities.

**Q161. Why is collection fetch-join pagination risky?**

*Hint:* Joined rows multiply parents; provider may paginate in memory or reject; page IDs first then fetch, or use suitable projection.

**Q162. How would you batch-insert thousands of rows efficiently?**

*Hint:* JDBC batching, compatible ID strategy, periodic flush/clear, bounded transactions; saveAll alone does not guarantee SQL batching.

**Q163. How do JPQL bulk updates differ from managed-entity updates?**

*Hint:* Bypass normal per-entity dirty checking/callbacks; persistence context may become stale; clear/refresh; handle versioning explicitly.

**Q164. Why are equals/hashCode/toString tricky on JPA entities?**

*Hint:* Generated IDs change lifecycle assumptions; proxies/lazy relationships; recursion; avoid including mutable collections in equality or logging.

**Q165. How would you map a self-referencing hierarchy or many-to-many with extra fields?**

*Hint:* Self-reference can use one table; association entity for join attributes; table counts are modeling choices, not rigid universal minima.

<a id="section-12"></a>

## 12. Transactions, isolation, and locking

**Q166 ★ Priority. What does @Transactional actually do in a typical Spring application?**

*Hint:* Proxy advice opens/joins transaction around invocation; transaction manager coordinates resources; commit/rollback after method outcome.

**Q167 ★ Priority. Why does a transactional method called from another method in the same class not behave as expected?**

*Hint:* Normal proxy self-invocation bypasses advice; separate bean boundary or explicit transaction template; confirm actual transaction activity.

**Q168 ★ Priority. Which exceptions trigger rollback by default?**

*Hint:* Normally RuntimeException and Error, not checked exceptions; configured rules can change defaults; caught errors may never reach interceptor.

**Q169. REQUIRED versus REQUIRES_NEW: what happens to commit and rollback?**

*Hint:* Join existing transaction versus suspend/use independent physical transaction; outer rollback does not undo already committed inner work.

**Q170. Why can REQUIRES_NEW exhaust a connection pool?**

*Hint:* Outer transaction retains resources while inner needs another connection; concurrent nesting can stall all callers; size/design deliberately.

**Q171. NESTED versus REQUIRES_NEW: what is the difference?**

*Hint:* Savepoint within same physical transaction versus independent transaction; support depends on transaction manager and database integration.

**Q172. What do SUPPORTS, MANDATORY, NOT_SUPPORTED, and NEVER mean?**

*Hint:* Join if present, require one, suspend one, reject one; explain concrete use rather than memorizing names alone.

**Q173. What causes UnexpectedRollbackException?**

*Hint:* An inner participating operation marked shared transaction rollback-only; outer caught exception but cannot safely commit.

**Q174 ★ Priority. What does readOnly=true guarantee—and what does it not guarantee?**

*Hint:* Optimization/read-only hint with provider/DB-dependent enforcement; not a universal prohibition on writes or substitute for permissions.

**Q175. Explain dirty reads, nonrepeatable reads, phantoms, and write skew.**

*Hint:* Use concurrent transaction timelines; stronger isolation trades contention/retries; database implementation differs from standard minimum guarantees.

**Q176. Optimistic versus pessimistic locking: how would you choose?**

*Hint:* Version-checked conflict/retry versus lock-and-wait; contention, transaction duration and user experience determine choice.

**Q177. How do you prevent overselling the last inventory item?**

*Hint:* Atomic conditional decrement or appropriate locking/version checks; unique/idempotency constraints; test simultaneous requests, not only sequential calls.

**Q178. Does @Transactional propagate into @Async or a manually created thread?**

*Hint:* Imperative transactions are thread-bound; new task needs its own explicit transaction boundary; do not share EntityManager across threads.

**Q179. Why should remote API calls usually stay outside a DB transaction?**

*Hint:* Long resource/lock holding and uncertain external outcome; separate steps with durable state and reconciliation/compensation.

**Q180. How do you publish an event reliably after a database update?**

*Hint:* Transactional outbox for durable handoff; after-commit callback alone has crash gap; consumer idempotency and replay handling still required.

<a id="section-13"></a>

## 13. SQL, indexing, and database design

**Q181 ★ Priority. INNER JOIN versus LEFT JOIN: how can a WHERE clause accidentally change the result?**

*Hint:* Filtering right-side columns after LEFT JOIN can remove null-extended rows; choose ON versus WHERE by intended semantics.

**Q182 ★ Priority. WHERE versus HAVING versus GROUP BY: explain execution intent.**

*Hint:* Filter rows, group/aggregate, filter groups; nonaggregated selected columns need valid grouping or supported functional dependency.

**Q183. How do NULL, COUNT(*), COUNT(column), and NOT IN interact?**

*Hint:* Three-valued logic; count rows versus non-null values; NULL in NOT IN can invalidate expectations; consider NOT EXISTS.

**Q184 ★ Priority. How does a B-tree index help, and why might the optimizer ignore it?**

*Hint:* Selective access/order; sequential scan may be cheaper; stale statistics, casts/functions, low selectivity, large result fraction.

**Q185. How do you order columns in a composite index?**

*Hint:* Match equality/range/sort/access patterns; leading columns often matter most; engine/version optimizations such as skip scans prevent absolute rules.

**Q186. What are covering and partial indexes?**

*Hint:* Cover required columns versus index only selected rows; balance read benefit, space and write cost; engine-specific implementation.

**Q187. Why can too many indexes hurt a write-heavy application?**

*Hint:* Every mutation maintains indexes; extra I/O, WAL/logging, storage and contention; remove redundant indexes based on measured usage.

**Q188 ★ Priority. How do you investigate a slow query using an execution plan?**

*Hint:* Estimated/actual rows, scan/join type, buffers, sorts/spills and lock waits; EXPLAIN ANALYZE executes the query—use safely.

**Q189. How do normalization and denormalization trade off?**

*Hint:* Consistency and update simplicity versus read cost; denormalized data needs explicit ownership/update strategy.

**Q190. Partitioning versus sharding: what changes operationally?**

*Hint:* Partitioning divides a logical table; sharding distributes data across independently managed nodes/databases; routing, rebalancing and cross-shard operations.

**Q191. How do you choose a shard key?**

*Hint:* Distribution, query locality, growth and hot keys; avoid concentrating all traffic on one tenant/time bucket.

**Q192. What is a database deadlock, and how do you handle it?**

*Hint:* Cycle of resource waits; DB aborts victim; consistent update order, short transactions, bounded safe retry.

**Q193. Stored procedure versus application business logic: when would you use either?**

*Hint:* Data-local set operations versus portability/testability/deployment ownership; avoid assuming procedures are always faster.

**Q194. How do replicas affect consistency and read-after-write behavior?**

*Hint:* Asynchronous lag can return stale data; route critical reads to primary or use consistency mechanism; replicas do not automatically scale writes.

**Q195. What database constraints should supplement application validation?**

*Hint:* NOT NULL, foreign key, unique and check constraints; concurrent requests bypass check-then-act application assumptions.

<a id="section-14"></a>

## 14. SQL exercises: explain the query approach

**Q196 ★ Priority. Find the second-highest distinct salary.**

*Hint:* DENSE_RANK over salary descending or MAX below MAX; state behavior with ties, nulls and fewer than two distinct salaries.

**Q197 ★ Priority. Find the top three salaries in each department, including ties.**

*Hint:* DENSE_RANK partitioned by department, ordered salary descending; filter rank <= 3; ROW_NUMBER means a different requirement.

**Q198. Find each department's highest-paid employees, including ties.**

*Hint:* Window rank or join to grouped MAX; do not select arbitrary name alongside grouped salary.

**Q199. Find departments with at least three employees earning above 50,000.**

*Hint:* WHERE salary > 50000; GROUP BY department; HAVING COUNT(*) >= 3.

**Q200. Find duplicate email addresses.**

*Hint:* GROUP BY normalized email HAVING COUNT(*) > 1; decide case/null/whitespace semantics first.

**Q201. Delete duplicate rows while retaining the most recent row.**

*Hint:* ROW_NUMBER by business key ordered timestamp and unique ID; inspect candidates, then controlled deletion; unique constraint prevents recurrence.

**Q202 ★ Priority. Find customers who have never placed an order.**

*Hint:* NOT EXISTS correlated by customer ID; include indexing; compare with LEFT JOIN and IS NULL.

**Q203. Find employees whose salary exceeds their department's average.**

*Hint:* AVG OVER partition by department or correlated/grouped subquery; avoid filtering before computing intended average.

**Q204. Find the latest order for every customer.**

*Hint:* ROW_NUMBER partition by customer ORDER BY created_at DESC, id DESC; define tie behavior.

**Q205. Calculate a running total of daily sales.**

*Hint:* Aggregate per day then SUM OVER ordered days with explicit frame; distinguish ROWS versus peer-sensitive RANGE.

**Q206. Calculate month-over-month revenue change.**

*Hint:* Monthly aggregation and LAG; handle missing months and zero denominator; specify percentage versus absolute change.

**Q207. Implement cursor pagination over created_at and id.**

*Hint:* Stable composite ordering and matching tuple/range predicate; parameterize cursor and limit; align index.

**Q208. Find consecutive days when a user logged in.**

*Hint:* Distinct dates per user; gaps-and-islands using date minus row number or lag-based groups; dialect-specific date arithmetic.

**Q209. Find orders containing every required product.**

*Hint:* Relational division: grouped COUNT DISTINCT of matching products or double NOT EXISTS; duplicate line items must not inflate counts.

**Q210. How would you atomically claim pending jobs for multiple workers?**

*Hint:* Transaction with row locks and supported SKIP LOCKED pattern, then status update; lease/crash recovery and fairness need design.

<a id="section-15"></a>

## 15. Spring Security, authentication, and authorization

**Q211 ★ Priority. Authentication versus authorization: where does Spring Security enforce them?**

*Hint:* Identity versus permissions; servlet filter chains plus optional method security; controller checks alone are insufficient.

**Q212 ★ Priority. Explain SecurityFilterChain and the authentication flow.**

*Hint:* Matching chain, authentication filter/manager/provider, authenticated context, authorization; filter order and endpoint matchers matter.

**Q213. 401 versus 403: what is the distinction?**

*Hint:* Missing/invalid authentication versus authenticated but forbidden; configure entry point/access-denied handling for consistent API responses.

**Q214 ★ Priority. How do you validate a JWT securely?**

*Hint:* Signature with trusted algorithm/key; issuer, audience, expiry and relevant claims; decoding is not validation; signed is not encrypted.

**Q215. How does JWKS support signing-key rotation?**

*Hint:* kid selects trusted published key; caching/refresh and overlapping keys; unknown key/fetch outage policy; avoid trusting arbitrary token-provided URLs.

**Q216. Can you immediately revoke a stateless JWT after logout?**

*Hint:* Not with local signature checks alone; short lifetime, denylist/version check/introspection or stateful sessions; revoke refresh tokens separately.

**Q217. Access token versus refresh token: how should each be protected?**

*Hint:* Short-lived API credential versus renewal credential; rotation/reuse detection; secure storage and revocation; audience/scope separation.

**Q218. OAuth2 versus OpenID Connect versus JWT?**

*Hint:* Authorization framework, identity layer, token format; they solve different concerns and are not interchangeable.

**Q219 ★ Priority. When is CSRF protection required for a REST API?**

*Hint:* Browser-automatically attached credentials such as cookies create risk; REST/stateless/JWT labels alone do not remove it; evaluate credential transport.

**Q220. How do XSS and CSRF differ, and what does HttpOnly protect?**

*Hint:* Script execution versus forged authenticated requests; HttpOnly blocks JS reading cookie, not all actions through compromised page; layered protection.

**Q221. How would you store and verify passwords?**

*Hint:* Adaptive one-way password hashing with salt via PasswordEncoder; no reversible encryption/plaintext; rate limiting and safe account-recovery flows.

**Q222. How would you prevent users from accessing another user's order by changing its ID?**

*Hint:* Server-side ownership/tenant authorization on every access; object-level checks, negative tests; opaque IDs are not authorization.

**Q223. How do @PreAuthorize, roles, and authorities fit together?**

*Hint:* Enable method security; expressions on intercepted calls; authority mapping/prefix conventions; enforce domain permissions near service boundary.

**Q224. What are risks of logging tokens or returning raw exceptions?**

*Hint:* Credential/PII leakage and internals exposure; redaction, structured safe fields, restricted access/retention; preserve useful correlation IDs.

**Q225. How would you secure uploads and outbound URL fetching?**

*Hint:* Validate content/size/path and object ownership; SSRF controls for destinations/redirects/DNS; block access to sensitive internal endpoints by policy.

<a id="section-16"></a>

## 16. Microservices and distributed systems

**Q226 ★ Priority. How do you choose service boundaries?**

*Hint:* Business capability/bounded context, data ownership and change cadence; avoid entity-per-service splits and excessive synchronous coupling.

**Q227 ★ Priority. When would you choose synchronous REST versus asynchronous messaging?**

*Hint:* Immediate response needs versus decoupling/buffering; availability, consistency, ordering, failure handling and operational complexity.

**Q228 ★ Priority. How does a Saga maintain business consistency?**

*Hint:* Sequence of local transactions with compensations; orchestration/choreography; intermediate states visible; compensation is business action, not database rollback.

**Q229. What if a Saga compensation repeatedly fails?**

*Hint:* Persist progress, retry with backoff/jitter, idempotent compensation, DLQ/manual reconciliation; alert with business impact and audit history.

**Q230 ★ Priority. How do you implement an idempotent message consumer?**

*Hint:* Durable message/business key with unique constraint; deduplication and state change in same transaction; acknowledge after successful durable processing.

**Q231. What happens if a consumer commits to DB but crashes before acknowledging?**

*Hint:* Message can be delivered again; deduplication must recognize committed outcome; at-least-once delivery is expected.

**Q232. How does the transactional outbox solve the dual-write problem?**

*Hint:* Business row and outbox row commit together; publisher retries; consumers deduplicate; publisher crash can still produce duplicate sends.

**Q233. What do circuit breaker, retry, timeout, and bulkhead each do?**

*Hint:* Stop calls temporarily, retry transient failures, bound wait, isolate resources; coordinate budgets to avoid retry amplification.

**Q234. How would you retry an HTTP payment request after a timeout?**

*Hint:* Outcome is unknown; use idempotency key/status lookup/reconciliation; never blindly create another payment.

**Q235. What does eventual consistency mean for the UI and business workflow?**

*Hint:* Temporary stale states with convergence assumptions; pending status, progress, compensation and user expectations; define acceptable lag.

**Q236. How would you trace a request across services and asynchronous consumers?**

*Hint:* Trace/correlation propagation, structured logs, spans, queue metadata; no secrets in baggage; handle sampling and asynchronous causality.

**Q237. What does CAP explain, and what does it not explain?**

*Hint:* During partition, trade strict consistency against availability for operations; not a universal pick-two menu or latency-free design rule.

**Q238. How would you evolve an event schema safely?**

*Hint:* Additive changes, defaults, tolerant readers, versioning strategy, contract tests; old events/replay and multiple consumer versions matter.

**Q239. How would you use SQS for long-running bulk processing?**

*Hint:* Visibility timeout/extension, delete after success, bounded concurrency, idempotency, DLQ; long polling wait differs from processing timeout.

**Q240. What ordering and exactly-once assumptions must you challenge?**

*Hint:* Define scope: partition/group versus global order, broker dedup versus business side effects; end-to-end exactly-once needs stronger coordinated guarantees.

<a id="section-17"></a>

## 17. Modular monoliths and architecture boundaries

**Q241 ★ Priority. What is a modular monolith, and how is it different from a distributed monolith?**

*Hint:* One deployable with enforced internal boundaries versus distributed services that require coordinated changes/releases; deployment alone does not create modularity.

**Q242 ★ Priority. Why might a modular monolith be better than microservices for a team?**

*Hint:* Simpler operations, local calls/transactions, faster refactoring; balance independent scaling/deployment requirements and team ownership.

**Q243. How do you organize packages by business capability?**

*Hint:* Order, inventory, payment modules with public APIs and hidden internals; avoid only controller/service/repository global layers.

**Q244 ★ Priority. How do you enforce module boundaries?**

*Hint:* Package visibility, explicit APIs, dependency rules and architecture tests; Spring Modulith/ArchUnit can detect invalid dependencies/cycles.

**Q245. Can modules share one database without becoming tightly coupled?**

*Hint:* Possible with table/schema ownership and restricted access; cross-module writes undermine boundaries; shared DB still couples operations.

**Q246. Should one module call another module's repository directly?**

*Hint:* Prefer exposed application/domain API; repository access leaks persistence assumptions and bypasses business invariants.

**Q247. When should modules communicate through method calls versus events?**

*Hint:* Immediate results/invariants versus decoupled reactions; decide transactional and delivery guarantees explicitly.

**Q248 ★ Priority. Are in-process application events durable?**

*Hint:* Not inherently; process crash may lose work; durable publication/outbox or supported event registry when business delivery matters.

**Q249. How would you handle transactions spanning two modules?**

*Hint:* Single local transaction can preserve atomicity; weigh coupling; asynchronous module interaction needs separate consistency design.

**Q250. How do Maven modules differ from business modules and JPMS modules?**

*Hint:* Build organization, domain boundary, runtime/compiler encapsulation are different dimensions; multiple Maven modules alone do not guarantee architecture.

**Q251. How would you detect and remove a circular module dependency?**

*Hint:* Identify responsibility crossing; extract orchestration or stable contract; events only if semantics fit; avoid dumping everything in shared module.

**Q252. What belongs in a shared module?**

*Hint:* Small stable primitives/contracts with explicit ownership; avoid shared entities/services that tie every feature together.

**Q253. How would you extract a module into a microservice later?**

*Hint:* Stabilize API/data ownership first; remove hidden DB coupling; add remote failure semantics; migrate traffic/data incrementally.

**Q254. What is the Strangler pattern, and when is it useful?**

*Hint:* Incrementally route capabilities from legacy to replacement; coexistence, contracts, data migration and rollback; avoid big-bang replacement.

**Q255. How do you test a modular monolith efficiently?**

*Hint:* Module-focused integration tests, boundary verification, API contracts, a few cross-module workflows; keep fixtures from bypassing public APIs.

<a id="section-18"></a>

## 18. JavaScript fundamentals and asynchronous behavior

**Q256 ★ Priority. var versus let versus const: what differs?**

*Hint:* Function versus block scope, temporal dead zone and reassignment; const does not make referenced objects immutable.

**Q257 ★ Priority. What is a closure, and where can it cause bugs?**

*Hint:* Function retains lexical bindings; callbacks capture surrounding state; stale React closures and loop captures are practical examples.

**Q258 ★ Priority. Explain the event loop, task queue, and microtask queue.**

*Hint:* Synchronous stack first; promise continuations run as microtasks before next task; long JS tasks block responsiveness.

**Q259. Predict the order of sync logs, Promise.then, and setTimeout(..., 0).**

*Hint:* Sync logs first, then queued promise callbacks, then timer task; zero delay does not mean immediate execution.

**Q260. What is the difference between == and ===?**

*Hint:* Coercive versus strict equality; prefer explicit conversions and strict comparison; know null/undefined and NaN edge cases.

**Q261. How does this differ in regular functions versus arrow functions?**

*Hint:* Regular this depends on invocation/binding; arrows inherit lexical this; call/apply/bind do not rebind arrow this.

**Q262. How do shallow and deep copies differ?**

*Hint:* Spread/Object.assign copy top level; nested references remain shared; structuredClone supports many but not all values; JSON copying is lossy.

**Q263 ★ Priority. Promise.all versus allSettled versus race versus any?**

*Hint:* All succeed, collect all outcomes, first settlement, first fulfillment; failure does not automatically cancel remaining requests.

**Q264. How does async/await handle exceptions?**

*Hint:* Await rejected promise throws; try/catch around awaited operation; forgetting await can let rejection escape surrounding synchronous catch.

**Q265. Does fetch reject for HTTP 404 or 500?**

*Hint:* Normally no; inspect response.ok/status; network/abort errors reject; JSON parsing can fail independently.

**Q266. Debounce versus throttle: when would you use each?**

*Hint:* Wait for quiet period versus limit execution frequency; search input versus scroll tracking; cleanup timers and define leading/trailing behavior.

**Q267. What does AbortController cancel, and what does it not guarantee?**

*Hint:* Cooperating client operation such as fetch; server may already process request; handle AbortError separately from failure UI.

**Q268. How do ES modules, default exports, and named exports differ?**

*Hint:* Explicit export/import contracts; module evaluation and tooling; exporting a function differs from exporting its invocation result.

**Q269. How do prototypes and classes relate in JavaScript?**

*Hint:* Prototype delegation under class syntax; instance methods typically on prototype; distinguish instance versus static behavior.

**Q270. What are common frontend memory-leak sources?**

*Hint:* Unremoved listeners/timers/subscriptions, retained DOM references, unbounded caches; reproduce navigation loop and inspect heap/retainers.

<a id="section-19"></a>

## 19. React components, rendering, and hooks

**Q271 ★ Priority. Props versus state: how does one-way data flow work?**

*Hint:* Parent supplies props; component state is updated through setters; child requests changes via callbacks; state should not be mutated directly.

**Q272 ★ Priority. What causes a component to render, and does render mean the DOM changes?**

*Hint:* State/context/parent updates; render computes output, commit applies needed changes; memoization may skip work under applicable conditions.

**Q273. Why do stable keys matter when rendering lists?**

*Hint:* Identity across insert/reorder/delete; index keys can associate state with wrong item; random keys force remounting.

**Q274 ★ Priority. Why use functional state updates such as setCount(c => c + 1)?**

*Hint:* Updates based on pending previous value; avoids stale snapshot issues and preserves queued increments; state reads do not change immediately.

**Q275. How do useEffect dependency arrays and cleanup work?**

*Hint:* Synchronize external systems; reactive dependencies; cleanup before changed effect setup and on unmount; development Strict Mode may replay setup/cleanup.

**Q276. Why can useEffect create an infinite loop?**

*Hint:* Effect updates state that changes dependency, often unstable object/function; rethink derived state and dependencies instead of disabling lint rules.

**Q277 ★ Priority. How do you avoid stale API responses overwriting newer search results?**

*Hint:* Cancel requests and/or use request identity/ignore guard; handle loading/error per active request; debounce alone does not solve response races.

**Q278. useMemo versus useCallback versus React.memo?**

*Hint:* Cache calculation, function identity, component rendering respectively; dependencies and prop identity matter; measure before adding complexity.

**Q279. Why does useCallback not stop a function from being executed?**

*Hint:* It retains identity across renders when dependencies match; call sites still invoke function; child memoization is separate.

**Q280. Controlled versus uncontrolled inputs: where does useRef fit?**

*Hint:* State+value/onChange versus DOM-owned value read via ref; useRef does not implement two-way binding or trigger renders.

**Q281. How do you lift state up for sibling communication?**

*Hint:* Move shared source of truth to common parent; pass value/callbacks; avoid duplicate unsynchronized copies.

**Q282. What makes a custom hook reusable, and does it share state across callers?**

*Hint:* Extract hook-based logic with use prefix; each call has independent state unless connected to shared external/context state.

**Q283. What do error boundaries catch, and what do they miss?**

*Hint:* Descendant rendering/lifecycle failures; ordinary event-handler and async fetch failures need own handling; boundary cannot catch its own rendering failure.

**Q284. How do lazy and Suspense work together?**

*Hint:* Load component module on demand, display fallback while suspended; ordinary useEffect fetch does not activate Suspense; chunk failures need error boundary.

**Q285. When would you use useLayoutEffect instead of useEffect?**

*Hint:* Pre-paint layout measurement/adjustment when needed; blocks paint and can hurt performance; effects are not for pure derived calculations.

<a id="section-20"></a>

## 20. React state management, frontend architecture, and performance

**Q286 ★ Priority. useState versus useReducer versus Context versus Redux Toolkit?**

*Hint:* Local simple state, local transition logic, value distribution, centralized state/tooling; Context transports state rather than replacing all state logic.

**Q287 ★ Priority. How does a Redux Toolkit slice work, and why does mutation-like syntax work?**

*Hint:* Reducers/actions generated; Immer drafts allow mutation syntax in supported reducers; normal React state must still be treated immutably.

**Q288. createAsyncThunk and extraReducers: what problems do they solve?**

*Hint:* Pending/fulfilled/rejected lifecycle actions; loading/error/data transitions; cancellation/request IDs and rejected payload handling.

**Q289. How do you prevent a slower old thunk response replacing newer data?**

*Hint:* Track requestId/current arguments; ignore stale completion or cancel superseded request; scope state by query when needed.

**Q290 ★ Priority. Server state versus client state: why separate them?**

*Hint:* Remote cache freshness/retries/dedup/invalidation versus local UI state; RTK Query or equivalent handles server lifecycle with explicit policy.

**Q291. Why does changing a Context provider value rerender consumers?**

*Hint:* Identity change notifies consumers; split concerns, stabilize value where useful; React.memo does not block subscribed context changes.

**Q292. How would you avoid unnecessary renders in a large table?**

*Hint:* Profile; stable props/selectors; row memoization where valuable; virtualization/pagination; avoid processing all rows per keystroke.

**Q293. How do useTransition and useDeferredValue improve responsiveness?**

*Hint:* Mark nonurgent updates or defer value-driven rendering; they do not reduce server work or replace debounce automatically.

**Q294. How would you design reusable form components?**

*Hint:* Controlled value/error contract, labels/accessibility, validation timing, submit states; keep domain rules outside generic input components.

**Q295. How do nested routes and Outlet work?**

*Hint:* Parent layout renders matched child through Outlet; route parameters and error/loading boundaries; preserve expected navigation state.

**Q296. Why are frontend protected routes insufficient for security?**

*Hint:* They control UI/navigation only; backend must authenticate/authorize every request and resource; bundle contents are observable.

**Q297. How do you handle token expiry without a refresh storm?**

*Hint:* Coordinate one refresh, queue/retry eligible requests once, detect refresh failure, clear session; prevent loops and duplicate unsafe mutations.

**Q298. How would you implement optimistic updates safely?**

*Hint:* Temporary state plus rollback/reconciliation; operation identity, conflict handling, retry policy; avoid optimistic confirmation of irreversible outcomes.

**Q299. How would you diagnose a production-only frontend performance regression?**

*Hint:* Browser profiling/network waterfall, bundle diff, source maps under policy, Core Web Vitals, real-user telemetry; compare affected devices/routes.

**Q300. How do you make a React screen accessible and resilient?**

*Hint:* Semantic elements, labels, keyboard/focus support, meaningful loading/error/empty states; test user behavior, not just component internals.

<a id="section-21"></a>

## 21. Testing, build pipelines, and release confidence

**Q301 ★ Priority. Unit versus integration versus contract versus end-to-end tests?**

*Hint:* Isolated rules, real component interaction, API compatibility, user workflow; put most checks at smallest reliable level.

**Q302 ★ Priority. What should you mock in a service test, and what should you keep real?**

*Hint:* External boundaries; real domain logic; assert behavior/results rather than implementation call sequences unless meaningful.

**Q303. How do @Mock and @MockBean-style Spring test replacements differ?**

*Hint:* Mockito object versus bean replacement in application context; use version-supported Spring test replacement APIs; avoid unnecessary full-context tests.

**Q304 ★ Priority. When would you use @WebMvcTest, @DataJpaTest, or @SpringBootTest?**

*Hint:* Controller slice, persistence slice, full integration; explicit included dependencies; test slicing can miss cross-layer behavior.

**Q305. Why use Testcontainers instead of relying only on H2?**

*Hint:* Real DB dialect, constraints/locking/query behavior; reproducibility; startup/resource cost; pin compatible images.

**Q306. How can transactional tests hide production bugs?**

*Hint:* Automatic rollback, unflushed SQL and no commit callbacks; explicitly flush/commit where behavior requires; test actual transaction boundaries.

**Q307. How do you test concurrent inventory updates?**

*Hint:* Coordinate simultaneous transactions; assert invariant and conflict behavior; use real database semantics and deterministic barriers.

**Q308. How do you test JWT authorization and object ownership?**

*Hint:* Valid/expired/wrong issuer/audience/roles plus cross-user/tenant access; do not test only successful authenticated calls.

**Q309. How do you test a React component that fetches data?**

*Hint:* Mock network boundary; loading/success/error/empty/race/unmount behavior; query accessible UI and await user-visible changes.

**Q310 ★ Priority. What checks belong in a CI/CD pipeline for a service?**

*Hint:* Compile, unit/integration tests, dependency/security checks, artifact build, migration validation, staged rollout and health verification.

**Q311. How do you make database migrations backward compatible?**

*Hint:* Expand-contract, dual compatibility window, backfill validation, later removal; application rollback may not undo schema/data changes.

**Q312. What should a code review focus on beyond style?**

*Hint:* Correctness, data consistency, failure modes, security, testability, observability, API compatibility and operational impact.

**Q313. How do you reduce flaky tests?**

*Hint:* Control clocks/randomness/concurrency; isolate data; avoid sleeps/shared state; investigate root cause instead of repeatedly rerunning.

**Q314. How do you handle a vulnerable transitive dependency?**

*Hint:* Trace dependency tree; compatible upgrade/BOM; assess exposure, regression tests and release; avoid unsupported overrides without validation.

**Q315. What evidence is needed before calling a release successful?**

*Hint:* Functional checks, error/latency/throughput comparisons, business outcome and dependency health over representative window; rollback triggers agreed upfront.

<a id="section-22"></a>

## 22. Production bugs and incident scenarios

**Q316 ★ Priority. P99 latency doubled after a release but average latency barely changed. What do you do?**

*Hint:* Assess impact/time window; compare versions/routes; trace tail requests, GC, pool waits and dependencies; mitigate/rollback safely; verify recovery.

**Q317 ★ Priority. CPU is 95%, memory is stable, and requests time out. How do you investigate?**

*Hint:* CPU profile/JFR and runnable threads; hot loops, serialization, GC or contention; avoid assuming more threads solve CPU saturation.

**Q318. CPU is low, but all request threads are busy. What could explain it?**

*Hint:* Blocked I/O, locks, pool acquisition, slow downstreams; thread dumps and wait metrics; timeouts and bounded concurrency.

**Q319 ★ Priority. Hikari active connections reach maximum and pending requests grow. What next?**

*Hint:* Slow/long transactions, leaks, DB locks and demand; inspect acquisition/usage times and DB capacity; increasing pool may worsen contention.

**Q320. A scheduled ingestion job times out after data volume increases. How would you fix it?**

*Hint:* SQL count/plan, N+1, paging, fetch strategy, batch size, external calls; reproduce at realistic volume; bound memory/transaction scope.

**Q321. The service restarts repeatedly with container OOM kills. How do you investigate?**

*Hint:* Exit reason, RSS versus heap, direct buffers/thread count/agents; memory limit/headroom; heap dump only if appropriate and safe.

**Q322. An API returns 500 only for some users. How do you narrow it down?**

*Hint:* Correlation IDs, affected tenant/data/roles, request shape and recent changes; sanitize reproduction data; avoid leaking PII in debug logs.

**Q323 ★ Priority. Payments or orders are duplicated after retries. How do you stop and repair it?**

*Hint:* Contain repeat processing; durable idempotency/unique constraints; reconcile existing records; check timeout/ack gaps and replay behavior.

**Q324. Database CPU is high after adding an index. Why might it happen?**

*Hint:* Write amplification, changed execution plan, maintenance/build effects or unrelated load; compare plans and metrics; validate benefit before keeping index.

**Q325. SQS backlog grows while consumers appear healthy. What do you check?**

*Hint:* Arrival versus completion rate, oldest-message age, visibility/redelivery, downstream limits, poison messages; scale within dependency capacity.

**Q326. Some users are logged out after signing-key rotation. What do you check?**

*Hint:* kid/JWKS cache, overlap window, issuer/audience/time skew, network fetch; never bypass signature validation to restore service.

**Q327. A deployment passes health checks but real requests fail. Why?**

*Hint:* Shallow readiness check, missing permissions/config, migrations, dependency route or incompatible contract; synthetic critical-path checks and rollout gates.

**Q328. How do you investigate missing logs or broken distributed traces?**

*Hint:* Sampling, ingestion failures, label cardinality, time range/clock, context propagation across executors/messages; distinguish no telemetry from no failures.

**Q329. A new frontend version calls an old backend endpoint incorrectly. What do you do?**

*Hint:* Mitigate route/feature/version mismatch; compatibility window, cache/CDN behavior; release contract tests and controlled rollout.

**Q330 ★ Priority. What is your incident response sequence from alert to prevention?**

*Hint:* Impact/severity, ownership, safe mitigation, communication, evidence, hypothesis tests, fix/verification, blameless RCA and tracked preventive actions.

<a id="section-23"></a>

## 23. Ownership, delivery, and senior-engineer behavior

**Q331 ★ Priority. Describe a feature you owned end to end.**

*Hint:* Real example: requirement → design/trade-offs → implementation/tests → rollout/monitoring → outcome; distinguish personal contribution from team work.

**Q332 ★ Priority. How do you handle a deadline that is no longer realistic?**

*Hint:* Surface risk early with evidence; split must-have scope, options/dependencies and revised estimate; agree trade-offs; avoid silent quality cuts.

**Q333 ★ Priority. Tell me about a production issue you caused or helped resolve.**

*Hint:* Own specific decisions; impact, mitigation, root cause, permanent fix and prevention; avoid blaming others or inventing numbers.

**Q334. How do you estimate a task with unfamiliar dependencies?**

*Hint:* Break down work; identify assumptions/unknowns; time-box spike; ranges and checkpoints; update estimate as evidence changes.

**Q335. How do you disagree with a senior engineer's design?**

*Hint:* Clarify goals, compare options with evidence, small experiment if needed, document decision; commit to agreed direction and review outcomes.

**Q336. How do you handle ambiguous requirements?**

*Hint:* Examples and acceptance criteria; edge cases, nonfunctional requirements, stakeholder confirmation; document assumptions before costly implementation.

**Q337 ★ Priority. What does done mean beyond merging code?**

*Hint:* Tests, migration, security, observability, documentation, rollout/recovery, production verification and ownership handover.

**Q338. How do you balance technical debt against feature delivery?**

*Hint:* Describe user/operational cost; prioritize by risk/frequency; incremental improvements tied to current work; avoid blanket rewrite proposals.

**Q339. How do you review a junior developer's flawed implementation?**

*Hint:* Explain risk and alternatives respectfully; ask for reasoning, pair on key fix, separate blocking correctness issues from preferences.

**Q340. How do you handle a dependency team that is delaying delivery?**

*Hint:* Clarify contract/date, mock boundary, parallel work, escalate impact/options early; maintain shared tracking and avoid surprise escalation.

**Q341. How do you prove your optimization improved performance?**

*Hint:* Before/after under comparable load/data/config; throughput and tail latency plus resources/errors; do not manufacture percent improvements.

**Q342. How do you respond when a bug cannot be reproduced locally?**

*Hint:* Collect minimal safe evidence, compare environments/data/concurrency, staging replay or targeted instrumentation; avoid dismissing customer reports.

**Q343. How do you prioritize several urgent issues?**

*Hint:* Customer impact, security/data loss, severity, workaround and dependencies; align owner/communication; revisit priorities as facts change.

**Q344. What would you do if asked to deliver a risky change without sufficient testing?**

*Hint:* Make concrete risk visible; propose reduced scope, feature flag/canary, targeted checks and rollback; escalate irreversible risk appropriately.

**Q345. How do you explain a technology you know conceptually but have not used in production?**

*Hint:* State experience boundary honestly; connect known concepts; describe how you would validate choices; no invented project claims.

<a id="section-24"></a>

## 24. Coding and system-design follow-ups

**Q346 ★ Priority. Design a thread-safe bounded in-memory cache with TTL.**

*Hint:* Clarify eviction/expiration, clock, size and concurrency; atomic operations, cleanup and cache stampede; explain why a mature cache may be safer.

**Q347. Implement a producer-consumer pipeline with graceful shutdown.**

*Hint:* Bounded BlockingQueue, backpressure, interruption/termination signal, failure propagation, executor shutdown and no dropped accepted work.

**Q348. Implement a thread-safe singleton and explain its limitations.**

*Hint:* Enum or initialization-on-demand holder; if double-checked locking, volatile required; classloader scope differs from distributed singleton.

**Q349. Find the first nonrepeating character in a string.**

*Hint:* Frequency map preserving order; define case and Unicode code point semantics; O(n) time and bounded alphabet/map space.

**Q350. Merge overlapping time intervals.**

*Hint:* Sort by start then scan; define touching endpoints/inclusive boundaries; O(n log n); empty and nested cases.

**Q351. Find the top K frequent elements efficiently.**

*Hint:* Frequency map plus size-K heap or bucket strategy; tie rules; complexity relative to unique keys and K.

**Q352. Implement debounce in JavaScript and explain cleanup.**

*Hint:* Closure-held timer, clear previous timeout, preserve args/context as needed; cancel on unmount; leading/trailing requirements.

**Q353 ★ Priority. Build a reusable useUsers hook with loading/error/cancellation.**

*Hint:* Effect dependencies, response.ok check, AbortController/stale-result guard, cleanup; independent hook state; expose retry if required.

**Q354. Build a React searchable, paginated table.**

*Hint:* Controlled filter, debounced query, request race handling, stable keys, accessible states; backend pagination and caching trade-offs.

**Q355 ★ Priority. Design bulk ingestion of thousands of bookings for one portCallId.**

*Hint:* 202/job ID, durable queue, bounded fan-out to APIs, idempotent DB writes, progress state, retry/DLQ and operational metrics.

**Q356 ★ Priority. Design an order-placement workflow with inventory and payment.**

*Hint:* Requirements/load/SLO first; ownership, reservations, idempotency, local transactions/Saga/outbox; failure/reconciliation and rollout.

**Q357. Design a rate limiter for an API deployed on several instances.**

*Hint:* Token/sliding-window choice; per-tenant/user key; shared atomic state or coordinated allocation; expiry, failure policy and 429 semantics.

**Q358. Design a high-throughput Spring Boot application.**

*Hint:* Measure bottleneck; stateless scale-out, efficient SQL/cache, bounded async work, resource budgets, load test, tail-latency/error SLOs.

**Q359. Design a file-upload and download flow for large files.**

*Hint:* Authorize metadata, presigned object-storage upload, size/content checks, processing status, ownership on download, expiry and cleanup.

**Q360. Choose a design pattern for interchangeable notification channels.**

*Hint:* Strategy for behavior; factory/registry for selection; decorators for cross-cutting additions; clarify simple factory versus GoF Factory Method.

## Common answers that need correction

| Avoid saying | More accurate direction |
|---|---|
| “Java passes objects by reference.” | Java passes values; an object reference is itself passed by value. |
| “A final reference makes the object immutable.” | It prevents reassignment; the object may still be mutable. |
| “volatile makes count++ thread-safe.” | Visibility does not make a compound read-modify-write atomic. |
| “ConcurrentHashMap makes any multi-step operation atomic.” | Use appropriate atomic per-key APIs or explicit coordination for wider invariants. |
| “Virtual threads make CPU work faster.” | They help scale blocking concurrency; CPU and downstream limits remain. |
| “More threads or DB connections always means more throughput.” | Saturated dependencies, contention and queueing can make performance worse. |
| “@Transactional works on every internal call.” | Normal proxy-based interception requires the call to cross the proxy. [S3] |
| “readOnly=true universally prevents writes.” | Its enforcement depends on the transaction manager/provider/database setup. |
| “Flush means commit.” | Flush synchronizes SQL; transaction outcome is still pending. |
| “EAGER loading fixes N+1.” | It can still issue additional selects; use and verify an explicit fetch plan. |
| “Pagination with a collection fetch join is always efficient.” | Joined-row multiplication may force in-memory pagination or rejection. |
| “A signed JWT is encrypted and can always be revoked at logout.” | Signing protects integrity; immediate revocation generally needs state/checks. |
| “Every stateless REST API can disable CSRF.” | Assess whether a browser automatically attaches authentication credentials. [S5] |
| “Exactly-once broker delivery guarantees one business side effect.” | Database/external side effects still require correct processing semantics. |
| “An in-process event is a durable message.” | Delivery persistence and recovery must be designed explicitly. |
| “A modular monolith is just a project with several Maven modules.” | Business boundaries and enforced dependencies matter, not folder count. |
| “fetch throws for a 500 response.” | Check response.ok/status yourself. |
| “Suspense automatically handles useEffect fetch loading.” | Ordinary effect-based fetching does not activate Suspense. [S6] |
| “An Error Boundary catches all API failures.” | Ordinary asynchronous/event-handler failures require explicit handling. |
| “useRef creates two-way binding.” | Refs provide mutable storage/DOM access without triggering a render. |
| “State is mutable because React lets us update it.” | Treat existing state as immutable; supply new values through update APIs. |
| “P99 is the average of the slowest 1%.” | It is a percentile threshold, not that group's average. |

## Final self-check before the interview

- Can I explain one real feature from request to database, including security and failures?
- Can I diagnose a slow API without guessing the cause?
- Can I explain transaction boundaries, concurrent updates and duplicate processing?
- Can I compare two reasonable designs and state why I chose one?
- Can I write a small Java/SQL/React solution while explaining edge cases?
- Can I describe my individual ownership, release checks and what I learned from a mistake?
- Can I clearly separate hands-on experience from concepts I have studied?

## Selected official references

These are targeted references for version-sensitive points and commonly confused behavior. The questions are an original study checklist; the links are not a claim that any company asks this exact set. Follow the matching version of documentation for your project.

- **S1 — Java 21 changes:** https://docs.oracle.com/en/java/javase/21/migrate/significant-changes-jdk-release.html
- **S2 — Java 21 virtual threads:** https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html
- **S3 — Spring transaction annotations and proxy limitations:** https://docs.spring.io/spring/reference/6.2/data-access/transaction/declarative/annotations.html
- **S4 — Spring Security servlet architecture:** https://docs.spring.io/spring-security/reference/7.0/servlet/architecture.html (architecture reference; use the corresponding version for configuration code)
- **S5 — Spring Security CSRF:** https://docs.spring.io/spring-security/reference/servlet/exploits/csrf.html
- **S6 — React Suspense:** https://react.dev/reference/react/Suspense
- **S7 — React effect lifecycle:** https://react.dev/reference/react/useEffect
- **S8 — PostgreSQL transaction isolation:** https://www.postgresql.org/docs/current/transaction-iso.html
- **S9 — PostgreSQL multicolumn indexes:** https://www.postgresql.org/docs/current/indexes-multicolumn.html
- **S10 — Hibernate ORM guidance:** https://docs.hibernate.org/orm/6.2/introduction/html_single/
- **S11 — Spring Modulith fundamentals:** https://docs.spring.io/spring-modulith/reference/fundamentals.html
- **S12 — React lazy:** https://react.dev/reference/react/lazy
