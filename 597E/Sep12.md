RedBlue notes: 

- they distinguish their work to some prior work that the focus of their work is not on adapting the consistency levels of particular data items at runtime, but instead on systematically partitioning the space of operations according to their actions and the desired system semantics.

- the plots are so hard to read [no color and stuff]
- they mention that they don't tolerate faults in this work 

- they have a follow up work that uses static and dynamic code analysis and developer should not be worried about choosing the right consistency level. [In a follow-up work, Li et al. [2014] implement and evaluate a system that would relieve the programmer from having to choose the right consistency level for each operation by exploiting a combination of automatic static and dynamic code analysis. but in this version of the work the consistency level of such operations can be manually assigned in Gemini.]

- red/blue decision is based on commutativity and the respect of invariants.

- cost reduction was not a goal here but in another work called SPANStore they do different consistency levels with the goal of cost efficiency. 

- globally red tokens are given out via a a coordinator to make logical clocks between the sites

-------------------------------------------------------------------------

RedBlue Questions: 

- how can shadow operations be compared to CRDTs? [for each operation, we may generate different shadow operations based on the specifics of the execution.]

- Is there any conflict solution? 

- Gemini uses Optimistic concurrency control

-------------------------------------------------------------------------
Quelea notes: 

- There is no mention of faults or fault tolerence. [I guess because this is just the language and not system]

- There is specific levels of consistenct that they support: eventual consistency, causal consistency, session guarantees, and timeline consistency. How easy it would be to incorporate other types of consistency into the language? And also does Gemini also supports different consistency levels?  

- QUELEA language maps operations to a fine-grained consistency levels such as eventual, causal, and ordering and transaction isolation levels like read committed (RC), repeatable read (RR), and monotonic atomic view (MAV).

- In some cases contracts could be more low-level than our invariants, since they typically constrain the happens-before relation. 

- It automatically chooses consistency levels for the programmer, driving the choice of mixed consistency via program invariants rather than explicit consistency annotations

- Quelea lets the programmer declare consistency contracts for operations of a replicated object using primitive consistency relations such as visibility and session orders. It defines axiomatic semantics for consistency notions using the same primitives. It then automatically maps a contract to the weakest consistency notion that satisfies the contract

- it's basically using the fact that invariants are a powerful way for developers to precisely specify what guarantees are necessary at application level.

- conflicting updates are handeled differently than consistency ones

- writing the contracts seems to be hard for the user [store semantics should also be provided in the contract by the programmer]

-------------------------------------------------------------------------

Quelea questions: 

- In some other works I saw they referenced to Quela and said "the contracts in it are lower-level than integrity invariants and translating invariants to contracts is
non-trivial". Kinda not sure about integrity invariants!!
[However, weak notions can be enough for certain operations to preserve the integrity properties. For example, consider a bank account object with the integrity property that its balance is non-negative. The deposit operation can be executed without any coordination as it cannot make the balance negative]

- Is is that Quelea can only execute a given transaction at a single consistency level? In these systems, all data in the Message Groups example would effectively be upgraded to linearizability. There would be no performance benefit from having a weakly consistent inbox and log; message delivery performance would likely be unacceptable [could we have different consistency levels for a transaction by breaking it to smaller ones? (I guess idea of MixT)]

- Quelea use user-provided data annotations to infer appropriate consistency levels for operations within a single program. The system then automatically chooses an appropriate consistency model for each operation. 
The Quelea approach -> using Cassandra to store compressed logs of all system events,
MixT -> transaction partitioning based on static information flow analysis.

- there seems to be some sort of conflict resolution in quelea but there is nothing mentioned in Gemini? 

- in both a single operations/transaction is being colored [like deposit, but what if there are different levels of actions in the same action?]

-----------------------------------------------------

Comparison Notes: 

quela: 
Consistency levels: Weak (eventual, causal, ordering, and transactional), RC, RR, MAV
Conflict Solution: LWW
Transaction Type: N/A

Gemini: 
Consistency levels: eventual, strong
Conflict Solution: N/A [I guess they also use LWW]
Transaction Type: RO/WO

user-side tradeoffs [more control or simplicity]

-----------------------------------------------------