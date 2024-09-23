RedLeaf: 

- this paper suggests that 
    - it is not (yet) practical to build a security mechanism solely based on Rust’s safety guarantee
    - Example: Any memory safety bugs in the trust chain can transitively break the safety boundary of such system
    Paper: Rudra SOSP'21 [https://dl.acm.org/doi/pdf/10.1145/3477132.3483570]

- significantly fewer features than Linux [can new features be added]
    - https://dl.acm.org/doi/pdf/10.1145/3458336.3465277 

- Unfortunately, the cost of switching from Linux to these clean-slate designs is prohibitive due to the established Linux and Android ecosystems. This raises an important question: can we use modern safe languages and formal verification techniques to improve OS kernel safety without resorting to a clean-slate OS design?

- It doens't target improving development velocity for a widely used commercial operating system. These approaches show that benefits can be gained by using safe languages, but all involve rewriting the entire operating system.

Their shortcommings: 
- they do not address unsafe Rust extension
    - RustBelt project provides a guide for ensuring that unsafe code is encapsulated within a safe interface
- they also assume "We trust devices to be non-malicious"
- do not protect against sidechannel attacks;

Verve: 
- We wrote the Nucleus directly in assembly language, hand-annotating it with assertions (preconditions, postconditions, and loop invariants). [how much is the annotation though]

- Writing these OS's is super manual and human effort is just too much! 

- Did verve continued? [the fundamental problem was the multi-processor support,]

-  we do not need to trust the compilers to ensure the correctness of the Nucleus and safety of the Verve system as a whole [is this the case in Redleaf also]

- how much the nucleos needs to get updated when it evolves [from paper: As more devices are added to the system, more Nucleus functions may be required]

- runtime overhead of the nuclseos verifier (Note that any property not guaranteed
by the TAL checker cannot be assumed by the Nucleus’s preconditions for functions exported to the kernel and applications; as a result, the Nucleus must occasionally perform run-time checks to validate the values passed from the kernel and applications)

- While the functional correctness of the language runtime is a strong statement, type safety alone for the rest of the system is a weaker property compared to the security and functional correctness statements we have proved for seL4. Standard type safety on its own would for instance not support further access- control proofs about the kernel. [sLe4 https://courses.cs.washington.edu/courses/cse551/17wi/readings/sel4-tocs.pdf]

- It has not addressed the important issues of concurrency, which include not just user and I/O concurrency on a single CPU, but also multicore parallelism with fine-grained locking

- a continuation of their work is Ironclad Apps: End-to-End Security via Automated Full-System Verification

- Verve currently supports four hardware devices: a programmable interrupt controller (PIC), a programmable interval timer (PIT), a VGA text screen, and a keyboard