[this whole document should be updated with the latest readings over the weekend!]

## Benchmark Suits for microbenchmarks
1. DeathStarBench
2. uSuite

## Characteristics and Cystem Implications of microservices
1. The Architectural Implications of Cloud Microservices. [CAL ]
2. Predicting the end-to-end tail latency of containerized microservices in the cloud [IC2E ]

## Resource Management for microservices
1. Wechat / uses overload control [matching the throughput of the upstream and downstream services]
2. PowerChief / dynamically power boosts bottleneck services in multi-phase applications
3. Sinan / uses ML [CNN and BT]
4. overload control and adopt deadline-based scheduling to improve tail latency in multi-tier workloads [paper: Distributed resource management across process boundaries]
5. Autotuning framework for microservice concurrency [Auto-tuned threading for OLDI microservice]
6. GrandSLAm -> spedifically focuses on ML microservices / improves the resources utilization by estimating the execution time of each tier, and dynamically batching and reordering requests to meet QoS.

