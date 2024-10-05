- The focus of this work seems to be on microservices for AI/ML applications or workloads.
- Their methodology cannot be applied directly to microservice types outside of the AI and ML space (because they study this specific group of microservices for their latency requirements).
- The final goal here is to maximize server throughput (this is done by sharing microservices and batching requests running on a common microservice) without violating the end-to-end SLAs. To achieve this, they have an algorithm that predicts the runtime of a request on a microservice and then chooses the extent to which they do batching and sharing.

- **Grandslam** is a microservice execution framework that improves utilization of datacenters hosting microservices.
- It is a runtime framework that enables consolidated execution of requests belonging to multiple jobs in a microservice-based computing framework.
- It estimates the time of completion of requests propagating through individual microservice stages within an application.
- Prior studies have proposed co-locating high-priority latency-sensitive applications with other low-priority batch applications. (However, for microservices, this could be fundamentally different.)
- What they do is:
    - Accurately estimate completion time at individual microservice stages.
    - Guarantee end-to-end SLAs by using stage-level SLAs, allowing them to identify opportunities for increasing throughput without violating the end-to-end SLA.

## Background Section on Microservices
- A motivating class of applications that would benefit from the microservice paradigm is AI and ML applications because many of the stages present in the execution pipeline of AI applications are common across other AI applications.
- One aspect of microservice applications is to focus on the components that are common across many applications.
- In their work, they focus only on sequential DAGs and not on parallelism or branching. Essentially, a long chain of microservices calling each other.

- Sharing microservices could create contention, resulting in the violation of end-to-end latency for individual applications.

## Opportunities in Microservice Applications
- The microservice execution framework enables a new degree of visibility into an application’s structure and behavior, since an application is comprised of microservice building blocks. [Identifying interference between co-running applications is easier than with traditional datacenter applications.]
- The granularity of multi-tenancy and consolidation in a microservice framework is different from traditional datacenter systems. Application consolidation in microservice platforms is performed at a fine granularity: batching multiple requests of different tenants to the same microservice.
- Now the key challenge is how to safely consolidate microservices without violating SLAs, which translates into accurately predicting how long each request spends in a microservice. This enables sharing microservices across different jobs of different tenants while still meeting SLAs.

## Analysis of Performance of Microservices
- Three critical factors determine the execution time of a request at each microservice stage:
    1. Sharing degree: how many requests run on a single microservice. This increases throughput but decreases latency (as long as SLAs are met, this can be done).
    2. Input size.
    3. Queuing delay: requests waiting for the previous ones to finish.

## Their Design
- **Offline:** Create the DAG of the microservice application.
- Calculate microservice stage slack (the maximum amount of time a request can spend at a particular microservice stage).
- **Online:** Dynamic batching with request re-ordering to (1) meet SLAs and (2) maximize throughput.
