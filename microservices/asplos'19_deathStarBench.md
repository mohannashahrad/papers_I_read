# Key takeways
1. Microservices allow individual components of the end-to-end application to be elastically scaled, with microservices of complementary resources bin-packed on the same physical server. 
2. You should note the programming language and framework heterogeneity when dealing with microservices. 
3. Despite the small amount of computation per microservice, the latency requirements of each individual tier are much stricter than for typical applications, putting more pressure on predictably high single-thread performance.
4. Similarly to traditional cloud applications, microservices spend a large fraction of time in the kernel.
5. Unlike monolithic services though, microservices spend much more time sending and processing network requests over RPCs or other REST API.
6. Cluster management gets super complicated whith micro-services.
7. Dependencies between microservices introduce backpressure effects and cascading QoS violations that quickly propagate through the system, making performance unpredictable.
8. Existing cluster managers that optimize for performance and/or utilization are not expressive enough to account for the impact each pair-wise dependency has on end-to-end performance.
9. They developed a distributed tracing system that records per-microservice latencies at RPC granularity. RPCs or REST requests are timestamped upon arrival and departure from each microservice by the tracing module. We additionally track the time spent processing network requests. 
10. Across all services a large fraction of cycles, often the majority, is spent in the processor front-end. Front-end stalls occur for several reasons, including long memory accesses and i-cache misses. [consistent with studies on traditional cloud applications but to a lesser extent]
11. The challenge with microservices is that although individual application components may be well understood, the structure of the end-to-end dependency graph defines how individual services affect the overall performance. 
12. The CPU cycles breakdown is not drastically different for monoliths compared to microservices, although they experience slightly higher percentages of committed instructions, due to reduced front-end stalls, as they are less likely to wait for network requests to complete. IPC is also pretty similar.  
13. The simplicity of microservices results in better i-cache locality. (less cache misses) compared to mono.
14. Microservices offer an appealing target for simple cores, given the small amount of computation per microservice. [but not actually maybe!]
15. Microservices are much more sensitive to poor single-thread performance than traditional cloud applications. Reason: each individual microservice must meet much stricter tail latency constraints compared to an end-to-end monolith, putting more pressure on performance predictability.
16. Even though low power machines degrade performance, they can still be used for
microservices off the critical path, or those insensitive to frequency scaling.
17. A large fraction of execution is at kernel mode, skewed by the use of
memcached for in-memory caching, and the high network traffic.
18. At high loads, network processing becomes a much more pronounced factor of tail latency for all end-to-end services.
19. The large impact of network processing occured regardless of whether microservices communicate over RPCs or over HTTP. [although RPC being much faster]
20. There is a great potential in acclerating network operations though. For interactive, latency-critical services, where even a small improvement in tail latency is significant, network acceleration provides a major boost in performance.
21. Microservices complicate cluster management, because dependencies between tiers can introduce backpessure effects, leading to system-wide hotspots. 
22. Backpressure can trick the cluster manager into penalizing or upsizing a saturated microservice, even though its saturation is the result of backpressure from another, potentially not-saturated service.
23. Upsizing a wring service not only does not solve the problem, but can potentially
make it worse, by admitting even more traffic into the system. 
24. In real large microservice applications, dependencies are difficult for developers or users to describe, and also, they change frequently.
25. Hotspot propagation problem in the service: [example] once the back-end service at
the top experiences high tail latency, the hotspot propagates to its upstream services, and all the way to the front-end. Utilization in this case can be misleading.
26. There is a need for cluster managers that account for the impact dependencies between microservices have on end-to-end performance when allocating resources.
27. The fact that hotspots propagate between tiers means that once microservices experience a QoS violation, they need longer to recover than traditional monolithic applications. Reason: probably the auto-scaler scales the services with the highest utilization. microservices. However, services with the highest utilization are not necessarily the culprits of a QoS violation, taking the system much longer to identify the correct source behind the degraded performance and upsizing it. 
28. The choice of programming language affects how hotspots evolve in the system. [like high-level languages]
29. Not only bottlenecks vary across end-to-end services, despite individual microservices being same/similar, but that these bottlenecks additionally change with load => a pressure to have a dynamic and agile management. 
30. Insights on running microservices on serverless vs EC2: 
-  Latency is considerably higher for Lambda when using S3. This occurs even though the amount of data transfered between microservices is small => Adhere to the design principle that microservices should be mostly stateless. 
- The majority of this overhead disappears when using remote memory to pass state between dependent serverless functions. 
- Performance variability is higher in Lambda, as functions can be placed anywhere in the datacenter, incurring variable network latencies, and suffering interference from external functions co-scheduled on the same physical machines. 
31. Cost is almost an order of magnitude lower for Lambda.
32. For microservices to reach the potential serverless offeres, they need to remain
mostly stateless, and leverage in-memory primitives to pass data between dependent functions.
33. Even though EC2 experiences lower tail latency than Lambda during low load periods, when load increases, Lambda adjusts resources to user demand faster than EC2. [more elastic]
34. Scaling in serverless: increased number of requests translates to more Lambda functions without requiring the user to intervene.
35. Scaling in EC2: An autoscaling mechanism that examines utilization, and scales allocations by requesting extra instances, when it exceeds a pre-determined threshold.
36. [in EC2] The system waits for load to increase substantially before employing additional resources, and initializing new resources is not instantaneous. 
37. In general, the more complex an application’s microservices graph, the more impactful slow servers [on which they're running] are, as the probability that a service on the critical path will be degraded increases.


---
