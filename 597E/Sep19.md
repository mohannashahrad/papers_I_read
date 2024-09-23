# RedLeaf: 

## Notes From the paper
- A new operating system developed from scratch in Rust to explore the impact of language safety on operating system organization.

- Only uses type and memory safety instead of hardware address spaces for isolation

- The goal is to get design space of systems that embrace lightweight fine-grained isolation. [instead of costly hardware isolation]

- Building on RedLeaf isolation mechanisms, they show the possibility to implement
end-to-end zero-copy, fault isolation, and transparent recovery of device drivers.

- Unfortunately, despite many attempts to introduce fine-grained isolation to the kernel, modern systems remain monolithic

- Traditionally, safe languages require a managed runtime, and specifically, garbage collection, to implement safety -> the overhead of garbage collection is too high

- The historical balance of isolation and performance is changing with the development of Rust, arguably, the first practical language that achieves safety without garbage collection

- Main idea of rust -> linear types

- Rust enforces type and memory safety through a restricted ownership model allowing only one unique reference to each live object in memory -> so we can track the lifetime of object and deallocate it without garbage collecting

- Previous works: Use Rust as a dropin replacement for C

- Their goal: enable practical, lightweight, fine-grained isolation and ... 

- RedLeaf: A new operating system aimed at exploring the impact of language safety on operating system organization, and specifically the ability to utilize fine-grained isolation and its benefits in the kernel

- It does not rely on hardware mechanisms for isolation and instead uses only type and memory safety of the Rust language.

- To protect the execution of the entire system, the kernel needs a mechanism that isolates faults, i.e., provides a way to terminate a faulting or misbehaving computation in such a way that it leaves the system in a clean state.

- After the subsystem is isolated the isolation mechanisms should provide a way to: 
1. deallocate all resources that were in use by the subsystem,
2. preserve the objects that were allocated by the subsystem but then were passed to other subsystems through communication channels
3. ensure that all future invocations of the interfaces exposed by the terminated subsystem do not violate safety or block the caller, but instead return an error

- Our work develops principles and mechanisms of fault isolation in a safe language

- They introduce an abstraction of a language-based isolation domain that serves as a unit of information hiding, loading, and fault isolation

- The core principals of the paper: 
1. Heap Isolation
    - domains never hold pointers into private heaps of other domains
    - the key to termination and unloading of crashing domains
    - for cross-domain communication, we introduce a special shared heap that allows allocation of objects that can be exchanged between domains
2. Exchangable Types
    - this is introduced to enforce heap isolation
    - types that can be safely exchanged across domains without leaking pointers to private heaps
    - statically enforce that objects allocated on the shared heap cannot have pointers into private domain heaps, but can have references to other objects on the shared heap
3. Ownership Tracking:
    - To deallocate resources owned by a crashing domain on the shared heap, we track ownership of all objects on the shared heap.
    - Rely on Rust’s ownership discipline to enforce that domains lose ownership when they pass a reference to a shared object in a crossdomain function call
4. Interface validation: 
    - enforcing the invariant that interfaces are restricted to exchangeable types and hence preventing them from breaking the heap isolation invariants.
    - developed an interface definition language (IDL) that statically validates definitions of cross-domain interfaces and generates implementations for them.
5. Cross-domain call proxy-ing
    - mediate all crossdomain invocations with invocation proxies: a layer of trusted code that interposes on all domain’s interfaces
    - The IDL generates implementations of the proxy objects from interface definitions.
    - hte proxies are generated statically to avoid the run-time overhead.

- The goal of the principals -> to enable fault-isolation

- To test the principals -> they implement RedLeaf as a microkernel system in which a collection of isolated domains implement functionality of the kernel

- Building on RedLeaf isolation mechanisms, we demonstrate the possibility to transparently recover crashing device drivers
    - lightweight shadow domains that mediate access to the device driver and restart it replaying its initialization protocol after the crash

- They also implemented Rv6, a POSIX-subset operating system on top of RedLeaf [to test generality]

- Finally, to demonstrate that Rust and finegrained isolation introduces a non-prohibitive overhead, we develop efficient versions of 10Gbps Intel Ixgbe network and PCIe-attached solid state-disk NVMe drivers

- Their argue: argue that a combination of practical language safety and ownership discipline allows us to enable many classical ideas of operating system research for the first time in an efficient way.

- they develop principles for enforcing fault isolation in a safe language that enforces ownership

- A collection of isolated domains implement device drivers, personality of an operating system,

- Domains communicate via normal, typed Rust function invocations

## Notes From Other Papers

- security guarantees of programming languages, including systems that can be built on top of them

- re-implementing device drivers in safe programming language

- Idea: rely on language safety that provides isolation between processes, thereby eliminating the need for expensive hardware context switches

- Language-based isolation, where a program is accepted in the form of a safe language like Rust and validated by the type checker and compiler.

- it performs competitively to their Linux counterparts

- Modules written in a type- and ownership-safe language such as Safe Rust are immune to entire classes of bugs, from NULL pointer dereferences to buffer overruns to memory leaks to data races, and are still able to perform complicated work with competitive performance

- ownership reasoning is not a barrier to performance

# Verve

## Notes from the paper

- Verve consists of a 
    - “Nucleus” that provides primitive access to hardware and memory, (written in verified assembly language)
    - a kernel that builds services on top of the Nucleus, 
        - written in C# and compiled to TAL, builds higher-level services, such as preemptive threads, on top of the Nucleus
    - applications that run on top of the kernel

- A TAL checker verifies the safety of the kernel and applications. 
- A Hoare-style verifier with an automated theorem prover verifies both the safety and correctness of the Nucleus

- The first operating system mechanically verified to guarantee both type and memory safety

- their goal: mixing high-level typed code with low-level untyped code in a verifiably safe manner.
    - typing the hardware is much difficult that higher-level programs 
    - typing is moer flexible for specifying the behaviour

- Unfortunately, today’s low-level software still suffers from a steady stream of bugs, often leaving computers vulnerable to attack until the bugs are patched.

- You can use safe languages to increase the reliability and security of low-level systems

- Safe languages ensure type safety and memory safety: accesses to data are guaranteed to be well-typed and guaranteed not to overflow memory boundaries or dereference dangling pointers

- Unfortunately, even if a language is safe, implementations of the language’s underlying run-time system might have bugs that undermine the safety.

- Verve: An operating system and run-time system that we have verified to ensure type and memory safety.

- Every assembly language instruction in the software stack must be mechanically verified for safety
    - every piece of software except the boot loader

- The key idea is to split a traditional OS kernel into two layers: 
    - a critical low-level “Nucleus,” which exports essential runtime abstractions of the underlying hardware and memory 
    - a higher-level kernel, which provides more fully-fledged services

- because there are two layers -> Leverage two distinct automated technologies to
verify Verve
    - TAL (typed assembly language)  -> Kernel 
    - automated SMT theorem provers  -> Nucleos (the core of the verification)
        - using Boogie that relies on Z3

- Idea: two-layer approach should be applied more generally to systems that want to mix lower-level untyped code with higher-level typed code in a verifiably safe way.

- Writing the assertions requires human effort, but once they are written, Boogie and Z3 verify them completely automatically

- It demonstrates that automated techniques (TAL and automated theorem proving) are powerful enough to verify the safety of the complex, low-level code that makes up an operating system and run-time system

- In its current implementation, Verve is a small system and has many limitations
    Like exception handling in C#
    - Only on a single processor -> this is a fundamental lack

- Although it protects applications from each other using type safety, it lacks a more comprehensive isolation mechanism between applications, such as Java Isolates, C# AppDomains, or Singularity SIPs

- They use the Bartok compiler to generate TAL code. [converting C# to TAL is easy and scalable]

- only the functionality that TAL could not verify as safe went into the Nucleus; all other code went into the kernel.

- they encoded assembly language instructions as sequences of calls to BoogiePL procedures

- Besides the boot loader, the only trusted components are the tools used to verify, assemble, and link the verified Nucleus and kernel.

- Note: none of our compilers are part of our trusted computing base: we do not need to trust the compilers to ensure the correctness of the Nucleus and safety of the Verve system as a whole

- all access to low-level functionality must occur through the Nucleus — the kernel’s TAL code and application’s TAL code can only access low-level functionality indirectly, through the Nucleus.

- The Nucleus consists of a minimal set of functions necessary to support the TAL code that runs above it

- They tried to keep the Nucleus API as small as possible (overhead of verification) consisting of just 20 functions. 
    - they did this by:  no Nucleus function ever blocks. In other words, every Nucleus function performs a finite (and usually small) amount of work and then returns.

- As more devices are added to the system, more Nucleus functions may be required

- Verve keeps interrupts disabled throughout the execution of any single Nucleus function. (On the other hand, interrupts may be enabled during the TAL kernel’s execution, with no loss of safety.) 
    - Since Nucleus functions do not block, Verve still guarantees that eventually, interrupts will always be re-enabled, and usually will be re-enabled very quickly.

- The nucleus specification: preconditions and postconditions for each of the 20 functions

- The Nucleus interacts with five components: memory, hardware devices, the boot loader, interrupt handling, and TAL code (kernel and application code)

- The kernel implements round-robin preemptive threading on top of Nucleus stacks, allowing threads to block on semaphores

Evaluation Section
- We believe our measurements are, along with seL4, the first measurements showing efficient execution of realistic, verified kernels on real-world hardware

- The small size of the annotated Nucleus implementation and the relatively small amount of time for automated verification suggests that this is feasible as a general approach for developing verified low-level systems.

- Verve currently supports four hardware devices: a programmable interrupt controller (PIC), a programmable interval timer (PIT), a VGA text screen, and a keyboard

## Notes From Other Papers

- The Verve project proved correctness of a minimal lan- guage runtime, including garbage collection, to achieve provable type and memory safety on the assembly level for the kernel and its applications, which must run in the same language framework. 

- Verve is a kernel written in a C#-like language that sits upon a hardware abstraction layer that has been verified to maintain the heap properties needed for memory safety.

- Yang and Hawblitzel used binary verification on a mini- mal type safe language runtime, including garbage collection, to implement an OS kernel. The verification establishes type safety for user-level programs, but forces all applications into the same language framework

- has applied formal verification to elimi- nate entire classes of bugs within OS kernels, by constructing a machine-checkable proof that the behavior of an imple- mentation adheres to its specification



Trusting compilers: 
- 2012 paper -> validation of compiler is needed [they verified it and found bugs that were not caught in their test cases]
- it is rare that compiler has a malicisous code path