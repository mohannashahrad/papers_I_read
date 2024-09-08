# Key takeways
1. Detecting QoS violations after they occur in systems with microservices results in long recovery times, as hotspots propagate and amplify across dependent services.
2. Microservices are appealing for several reasons:
- accelerating development and deployment 
- simplifying correctness debugging
- enabling a rich software ecosystem (each microservice is written in the language or programming framework that best suits its needs)
3. Even though the QoS requirements of the end-to-end application are similar for microservices and monoliths, the tail latency required for each individual microservice is much stricter than for traditional cloud applications.
4. Dependencies between microservices mean that a single misbehaving microservice can cause cascading QoS violations across the system.
5. The complexity of modern cloud services means that manually determining the impact of each pair-wide dependency on end-to-end QoS, or relying on the user to provide this information is impractical. [you should handle considering the dependency graphs]
6. With microservices, a posteriori QoS violation detection is more impactful, as hotspots propagate and amplify across dependent services, forcing the system to operate in a degraded state for longer, until all oversubscribed tiers have been relieved, and
all accumulated queues have drained. 
7. Example: Even though the scheduler scales out all oversubscribed tiers once the violation occurs, it takes several seconds for the service to return to nominal operation. 2 reasons for this:
- By the time one tier has been upsized, its neighboring tiers have built up request backlogs, which cause them to saturate in turn. 
- **Utilization is not always a good proxy for tail latency and/or QoS violations.**
8. Seer does the following:
- Uses tracing data cloud systems collect over time to learn spatial and temporal patterns that lead to QoS violations.
- Includes a lightweight, distributed RPC-level tracing system, based on Apache Thrift’s timing interface, to collect end-to-end traces of request execution, and track permicroservice outstanding requests.
- Uses these traces to train a DNN to recognize imminent QoS violations, and identify the microservice(s) that initiated the performance degradation. 
- Once identified the culprit of a QoS violation that will occur over the next few 100s of milliseconds, it uses detailed per-node hardware monitoring to determine the reason behind the degraded performance.
- Finally, it provides the cluster scheduler with recommendations on actions required to avoid it.
9. Seer uses 2 levels of tracing: 
- First: it uses a lightweight, distributed RPC-level tracing system that 1. collects end-to-end execution traces for each user request, including per-tier latency and outstanding requests, 2. associates RPCs belonging to the same end-to-end request, and 3. aggregates them to a centralized Cassandra database.
-  These traces are used to train Seer to  recognize patterns in space (between microservices) and time that lead to QoS violations. 
- Second: When a QoS violation is expected to occur and a culprit microservice has been located -> Seer gets detailed per-node, low-level hardware monitoring primitives, such as performance counters, to identify the reason behind the QoS violation. 
10. Seer's distributed tracing instrumnetation:
- First: Reporting the time the application sees a new request [to distinguish the processing time of network requests and application computation]
- Second: systems have multiple sources of queueing in both hardware and software. these are used for measuring "queue lengths": 
a. NIC (hardware) queues: Filtering packets based on the destination microservice.
b. Software queues: Inserting probes in the application to read the number of queued requests in each case.
11. There is a "limited instrumentation" step that only uses NIC queues and no application-level queues [if we don't have access to the complete code]: 
- using network queue depths alone is enough to signal a large fraction of QoS violations 
- Polling NIC queues identifies hotspots caused by routing, incast, failures, and resource saturation, but misses QoS violations that are caused by performance and efficiency bugs in the application implementation. (Example: blocking behavior between microservices.) 
12. Queueing Networks : A popular way to model performance in cloud systems, especially when there are dependencies.
- but they require in-depth knowledge of application semantics and structure, and can become overly complex as systems scale.
13. The key idea in Seer: conditions that led to QoS violations in the past can be used to anticipate unpredictable performance in the near future. 
14. DNN configuration: what parameters should be used for best predictions?
- Resource Utilization: not a good proxy for performance.
- Latency: Leads to many false positives, or to incorrectly pinpointing computationally-intensive microservices as QoS violation culprits
- Queue Depths: per-microservice queue depths accurately capture performance bottlenecks!
15. Input and outputs of the DNN: 
- In: queue depths of each microservice
- Out: the probability for a given microservice to initiate a QoS violation
16. For Seer to be effective, inference needs to occur with enough slack for the cluster manager’s action to take effect. 
- Given that most resource partitioning decisions take effect after a few 100ms, the inference time for Seer is within the window of opportunity the cluster manager has to take action. [less than 16 ms]
17. If the application (or underlying hardware) change significantly, Seer’s detection accuracy can be impacted => It retrains incrementally in the background. 
- When the application changes in a major way, e.g., microservices on the critical path change, It also retrains from scratch in the background. 
18. For second-level of tracing [hardware monitoring of the potential candidate of the QoS] -> 2 different approaches are used depending on whether the cluster is public or private:
- private: you have access to hardware events like CPU, memory capacity and bandwidth, network bandwidth, cache contention, and storage I/O bandwidth.
- public: you don't have access to performance counters -> it uses a set of 10 tunable contentious microbenchmarks, each of them targeting a different shared resource to determine resource saturation. [each of these takes approximately 10ms to complete, avoiding prolonged degraded performance.]
19. Security Concerns: trace data is stored un-encrypted on Cassandra -> leading to potential attacks on them! [future work]
20. Many QoS violations are caused by very short, bursty events that do not have an impact on queue lengths until a few milliseconds before the violation occurs. -> so you cannot predict a very far time in the fututre [not accurate]
21. Large scale cloud study: Inference time, increased substantially from 11.4ms for the 20-server cluster to 54ms for the 100-server GCE setting.
- As the application scales further, Seer’s ability to anticipate a QoS violation within the cluster manager’s window of opportunity diminishes. [So they ran Seer on Google's TPU to accelerate]
22. Source of QoS Violations: The most frequent culprits: in-memory caching tiers in memcached, and Thrift services with high request fanout. 
- memcached: it is on the critical path for almost all query types, and it is additionally very sensitive to resource contention in compute. [violations caused by resource contention]
- Microservices with high fanout -> they have to synchronize multiple inbound requests before proceeding. If processing for any incoming requests is delayed, end-to-end performance is likely to suffer. [violations caused by long synchronization times]
23. Microservice design decisions that lead to hotspots: 
- microservices with a lot of backand-forth communication between them, or 
- microservices forming cyclic dependencies, or
- microservices using blocking primitives
