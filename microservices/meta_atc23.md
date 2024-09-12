1. Production traces used in their paper having Meta’s microservice topology and request workflows: https://github.com/facebookresearch/distributed_traces.
2. Main findings: 
- Workload characteristics: Traces are small in size and wide in number of communication calls.
- Service and endpoint names do not predict number of communication calls or their concurrency.
3. Meta’s microservice architecture: 
- Interconnected, replicated software services running in dozens of datacenters
- Load balancers [Requests can be load balanced to instances within the same datacenter or to instances in different datacenters. Only the datacenter load balancer is shown in the figure.]
- Observability framework [monitoring the topology and creating traces] 
- A globally-federated scheduler for running services on host machines within containers
 ![](./figures/meta_arc.png)
4. How do applications use Meta’s microservice architecture? 
- Apps issue requests -> that are load balanced by DNS to specific datacenters -> then processed by a subset of the architecture’s software services
5. In this architecture the notion of an application is ill-defined.
5. Not so many papers studies microservice topologies: like how the topology evolves
or the velocity of changes. These aspects are apparently only studied in this work and also [ByteDance](https://onlinelibrary.wiley.com/doi/abs/10.1002/smr.2467)
6. Each service in the application could be stateless or statefull [like databases].
7. Most services at Meta use two-way Thrift RPCs.
8. The set of services involved in a request workflow depends on: business logic + whether any requested data is cached.[the specific set of instances involved in a request workflow depends on the current load and the load-balancing policy though.]
9. Parent-child request calls among services:
- Sequentially: blocking calls or data dependencies between services [all of them are in the critical path]
- Concurrently: only the slowest service is in the critical path 
10. Meta’s microservice topology contains three types of software entities that communicate within and amongst one another
- those that represent a single, well-scoped business use case.
- those that serve many different business cases but are deployed as a signle service
- ill-suited to the microservice architecture’s expectations [not a single service?]
11. Ill-fitting services were those that had unique names (each service) but they were essentially one big platform that were creating those sub-services! (make sure this is correct though) [in the whole data there is much of ill-fitting data that makes the analysis kinda hard, and the previous works also it's not clear if the same behaviour is seen]
12. Topology of services is very complex [and the overall topology of connected services does not show a power-law relationship typical of many large-scale networks.] but the number of endpoints services expose does show a power-law relationship.
13. They reported that the topology has scaled rapidly. this is an increase in number of services (i.e., new functionality) rather than increased replication of existing ones (i.e., additional instances). 
14. ill-fitting services -> skew instance counts so much! [a high number of services counted diffeernt but essentially the same thing]
15. Services are sparsely interconnected.
16. Services are called by services more than they call other services.
17. Most services are simple, exposing only a few endpoints
18. Service complexity follows a power-law distribution: Meaning: most services are simple with a long tail of more complex services.
19. The service dependency diagram does not follow a power law distribution -> Meaning: services with more endpoints are not proportionally more connected to the topology than services with fewer endpoints. 
20. Instances’ rate of increase is due to new business use cases rather than increased scale.[more users really getting to add services]
21. Lots of churn in services, with both long-lived and ephemeral ones: rates of quick deprecations. 
22. Request workflows characteristics: 
- Trace sizes are mostly small [only a few service blocks]
- Traces are wide and not deep [calling many services at once instead of a caller/callee thing] 
- Root Ingress IDs do not predict trace properties. [like given an id of a service, you can't well-predict the RPCs that it's gonna make? ]
- Many call paths in the traces are prematurely terminated due to rate limiting, dropped records, or non-instrumented services. [unrecoverable call paths]
23. There is significant diversity in trace sizes. In general, traces are shallow and wide: Large traces are a result of the number of calls made by services, not depth of calls. -> Reason: could be data sharding, where retrieving a collection of items requires fanning out requests across many storage service instances.
24. Service reuse within traces is high and occurs at many different call depths


Implications for testbeds and benchmarks: 
- Future testbeds should include concurrency, number of children, and set of children that are executed as dimensions of variability in request workflows.

Implications for microservice tooling: 
- Services and the topology it self are very dynamic and changing: Periodic retraining may be necessary; mechanisms are needed to identify when predictions diverge from the ground truth due to stale topological information. 
- Many workflow properties can be predicted when they are broken down into fundamental building blocks (like parent/child relationships).

Need for artificial microservice topology & workflow generators: 
- there is only one right now doing this [Characterizing microservice dependency
and performance: Alibaba trace analysis](https://dl.acm.org/doi/abs/10.1145/3472883.3487003)  
- potential research directions: 
   (a) which dimensions of microservice architectures are best explored in testbeds versus artificial topology or workflow generators? 
   (b) how to ensure these dimensions are representative of a variety of large-scale organizations’ characteristics?

How to better incorporate ill-fitting software entities into microservice architecture? [I need to read more about the ill-fitting things again]
- Should infrastructure platforms provide richer interfaces to allow scheduling, scaling, and observability based on additional dimensions rather than only one?


- they found that organizations’ architectures have similar architectural
diagrams and use custom versions of the same architectural components or open-source versions. 
- overall, they found that more detailed quantitative comparisons is impossible due to divergent (or ill-specified) definitions in previous studies and because different studies use custom measurement techniques specific to their observability
frameworks. 
- There is a need to develop rich, well-accepted methodologies for collecting data about microservice architectures to systematize similarities and differences across them. [like IMC]