Adaptive locks Notes: 

From the paper itself: 

- their contribution: adaptive locking technique that dynamically observes whether a
critical section would be best executed transactionally or while holding a mutex lock

- Main elements of their approcah: adaptivity logic + cost-benefit analysis,

- Evaluations: we found adaptive locks to consistently match or outperform the better of the two component mechanisms (mutexes or transactions). [compared to either mechanism alone, it provide 3-to-10x speedups]

- Aside from performance benefits, adaptive locks simplify the programming model by reducing the need for fine-grained locking. Programmer just need to provide coarse-grained locking annotations

- Transactional memory replaces mutexes and condition variables with “atomic” blocks of
code, that are meant to execute as if all other threads had stopped running during the execution of the atomic block

- Benefits of TM: (1) a higher level programming model => code is more composable. (2) Furthermore, TM does not require finegrained delineation of critical sections in order to achieve high concurrency.

- Downsides of TM: (1) Possible livelock or slower progress. (2) transactions cannot easily support irreversible operations such as I/O, (3) suffer from high overheads during the execution of atomic blocks

- their goal: ability to dynamically switch concurrency mechanisms depending on execution conditions.

- their idea: a synchronization mechanism combining locks and transactions for best performance [best of two worlds] -> executions can be done in 2 modes. 

- Programmer's job -> Specify critical sections, which can be executed either with mutual exclusion or atomically as transactions

- The decision to execute in mutex mode or in transaction mode depends on the observed behavior of the critical section, namely on the:
    - nominal contention: how many threads are blocked on the lock when in mutex mode
    - actual contention: how many times each transaction retries when in transaction mode
    - transactional overhead: how much slower is the critical section when in transaction mode compared to mutex mode.

- All critical sections associated with the same adaptive lock have to execute in the same mode at a given time.

- so the sot benefit analysis -> during runtime the 3 factors are executed and feed into the analysis

- The overall adaptive lock implementation imposes very low overhead compared to either a regular mutex lock or a transaction.

- The adaptive locks programming model resembles mutex locks more than it does transactions [kind of like a more performant locking mechanism]

- All our benchmark measurements are implemented with very coarse-grained lock annotations (often a single global lock, which trivially has good composability and deadlock-freedom properties), yet still achieve significant performance improvements

- Their user-frindly ness: adaptive locks encourage programmers to use locks at whichever level of abstraction correctness is easy to establish, and not at the granularity needed for performance.

- They had an earlier work on "non-blocking" locks -> Finally, the work in this paper is an evolution and realization of the non-blocking locks idea 

- Importantly, this removes all performance arguments against Software TM: transactions are used only when they yield benefits, and incur no overhead otherwise

- programmer's side: 
    - ensuring that the lock labels are correct
    - program is equally correct if all lock labels are dropped and all critical sections atomic(<lckLbl>)<stmt> execute as transactions, atomic <stmt>, in a conventional TM system

- The main reason for executing an adaptive lock in transaction mode is that mutex locks can exhibit false exclusion. [what is false exclusion?] -> Therefore, the performance benefit of transactions is due to higher concurrency: More threads can execute the same critical section with transactions than with mutexes. 

- At the same time, executing an adaptive lock in transaction mode incurs high overheads when there is true contention on the data. [transaction retries happen]

- another reason for overheads of TM -> in pure-software TM, there is typically a high overhead associated with executing a critical section transactionally. coming from logging actions and synchronization

--------------------------------------------------------------
Other papers: 

- This technique only applies to systems with transactional memory. 

- It adapts between optimistic (with STM) and pessimistic (via locking) execution of atomic blocks. Unfortunately, it does not map directly to best-effort HTMs, as
we showed in our evaluation (by considering Adaptive Locks, as the authors kindly provided us with their code). -> hardware transactional memory [this is from the paper https://www.usenix.org/system/files/conference/icac14/icac14-paper-diiegues.pdf ]

- Clearly at one thread, the lower latency of a lock-based runtime is best. Additionally, if transaction latency is too high and the cost of a lock moving between processors’ caches is low, the concurrency afforded by STM may not be worth its cost.

-  On systems without HTM and with few cores, TM often performs best if implemented as a single global reentrant mutex lock [like adaptive locks]. For these systems, no per-access instrumentation will ever be needed. For hypothetical future systems with robust HTM capabilities, the compiler can be equally simple, since transactions would always use HTM. On existing systems with many cores but either best-effort HTM or no HTM, the sort of instrumentation proposed by the TMTS would still be advantageous.[https://dl.acm.org/doi/pdf/10.1145/3328796]

-  For bugs that can be more simply fixed with TM, the performance overhead of TM may be too large in the absence of hardware support, because software TM is generally more expensive than locking when there is no lock contention [reference to adaptive locks] -> paper https://pages.cs.wisc.edu/~swift/papers/asplos12_tmbugs.pdf 

- It is shown that dynamic tuning of the STM runtime system depending on the workload can significantly improve the throughput
------------------------------------------------------------------------

Notes on the paper RFJP: 

- The focus of the problem is data races. 

- Because data races can be one of the most di±cult programming errors to detect, reproduce, and eliminate, many researchers have developed tools that help programmers detect or eliminate data races.
    - monitoring tools to dynamically detect data races
    - static race detection systems
    - formal type systems that ensure race-free

- Their work presents a new static type system for multi-threaded object-oriented programs; this type system guarantees that any well-typed program is free of data races.

- Every object has a protection mechanism that ensures accesses to the object never create data races. 

- RFJP: enables programmers to specify the protection mechanism for each object as part of the type of the variables that refer to that object.

How does the typing works: 
1. They type can specify 
    - the mutex that protects the object 
    - threads can safely access the object becayse 
        - 1) the object is immutable, 
        - 2) the object is accessible to a single thread and is not shared 
        - 3) the variable contains the unique reference to the object.
2. The type checker then uses these type specifications to statically verify that a program uses objects only in accordance with their declared protection mechanisms.


- Difference to previous type systems: they allow programmers write generic code to implement a class, then create different objects of the class that have different protection mechanisms. -> by parameterizing classes

- If this flexibility was not in place [parametizing classes] -> Programmers often must either 
    - write a program that acquires redundant locks just to satisfy the type checker,
    - create duplicate of classes with different locks

- the type system is expressive enough to verify the absence of races in many common situations where a thread accesses an object without acquiring the lock on that object. There are the following special cases [where no synchronization is required]: 

1. Thread local objects
    - If an object is accessed by only one thread, it needs no synchronization.
2. Objects contained within other objects
    - It might be redundant to acquire the lock on that object since the same lock that protects the enclosing data structure also protects that object.
3. Objects mitigating between threads
    - Serially-shared objects [accessed by a single thread at a single time]
    - Our type system uses the notion of unique pointers to support this kind of sharing. [what is unique pointer?]
4. Read-only objects

- Comparing Vector and ArrayList in Java:
- Our system enables programmers to implement a single generic resizable array class. If a program creates a resizable array object to be concurrently shared between threads, our system ensures that accesses to the array are synchronized. If an array is not concurrently shared, our system allows the program to access the array without synchronization.

- The key to our type system is the concept of object ownership. Every object has an owner. 
- An object can be owned by another object, by itself, or by a special per-thread owner called thisThread. Objects owned by thisThread, either directly or transitively, are local to the corresponding thread and cannot be accessed by any other thread.

- The ownership relationship has the following properties: 
    - The owner of an object does not change over time.
    - The ownership relation forms a forest of rooted trees, where the roots can have self loops.

- A class de¯nition in PRFJ is parameterized by a list of owners

------------------------------------------------------------------------

Notes on RFJP from other papers: [look at the follow-up works of the author]

- uses a static analysis tool 
- https://ilyasergey.net/papers/ownership-survey.pdf has a beautiful history and avolution of this work

- primarily flow insensitive type-based system

- A foolowup work of them developed an ownership type system that statically enforces object encapsulation, while supporting subtyping and constructs like iterators. 
This work did not enforce object encapsulation, it enforce weaker restrictions instead. [from https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=ea1004170596f0303f096976f87377bdc450fbf8]

-  In recent previous work we presented PRFJ [7], a type system that uses a variant of ownership types to statically prevent data races. PRFJ is the first type system to combine ownership types with unique pointers [38]. This enables PRFJ to express constructs that neither ownership types nor unique pointers alone can express. PRFJ is also the first type system to combine ownership types with effects clauses [37]. This paper extends PRFJ to prevent both data races and deadlocks. [so the previous work was not deadlock-free]

- Since annotation writing creates significant overhead, techniques [3] have been proposed to infer type annotations by looking at concurrent executions [http://web4.cs.columbia.edu/~junfeng/09fa-e6998/papers/racefuzz.pdf]

- This language-level approach eliminates data races altogether, but requires a substantial amount of code annotation and also restricts the kinds of synchronization that can be expressed.

- In general, systems based on type checking perform very well, but require a significant number of programmer annotations, which can be time consuming when checking large code bases 

- Boyapati and Rinard [2001] have defined a type system much like RACEFREEJAVA but with a notion of object ownership. Their system allows a single class declaration to yield both thread-local and thread-shared instances. Tracking thread locality on a per-object basis in this manner seems to be a promising alternative to the notion of thread-local classes presented in this article (which forbid a class to yield both thread-local and thread-shared instances). In the system of Boyapati and Rinard, synchronization is expressed only at the object level, and not at the field level. They have extended their analysis to identify deadlocks [Boyapati et al. 2002] by using a partial ordering over the lock names manipulated by the program, much as in Flanagan and Abad. [https://dl.acm.org/doi/pdf/10.1145/1119479.1119480]

- Boypati and Rinard [12] employ object ownership to enable race-free Java programs, and more recently (with Lee) for preventing deadlocks [11]. Their type systems offer only shallow ownership and a different form of effect, which is sufficient for the problem they address, but not enough to obtain the results presented here. [https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=9137b46b85b90bcaa487bd66f0de8148746142a8] -> their goal: reasoning about OO programs
