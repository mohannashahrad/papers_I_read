Note: This was just a quick skim of the paper and the very high-level points
1. They mention that for studying microservices you should consider two things:
- Dependency graph [in shapr of a DAG]
- Call graphs, based on the different queries from users
2. They are motivated by Alibaba's trace (2021). Here are some insights from there: 
- Load dynamics: application load changes over time
- Call graph dynamics [call graph proportion changes over time]
3. Current works of microservice management
(a) Proactive methods -> most of them don't conside rthe call graph dynamics
(b) reactive methods -> they preiodically monitor the latencies of microservices, some of them are ML-based
4. They claim that current works have long QoS recovery time. 
5. Causes of the Long Recovery Time: 
- Long Monitoring Interval: in the current methods, they do real-time monitoring of the services with intervals of seconds or tens of seconds [latency monitoring interval needs to be long enough]
- Execution Blocking Effect: It means that what you monitor is not necessarily going to be processed. Because of this effect, there might be the need to adjust resources for multiple times which leads to long QoS recovery time. 
- Slow Query Draining: Queued queries need extra time to be drained under "just-enough" resources. (so you might need to give them more resources)
6. Components of Nodens are: 
- load monitor: periodically checks microservices’ network bandwidth usage and predicts the monitored loads based on it
- load updater: updates the actual "to-be-processed” load of each microservice to enable fast resource adjustmen
- query drainer: allocates just-enough excessive resources for microservices to
drain the queued queries, which can ensure the QoS recovery time target
7. Load updator has two parts:
 - Execution blocking graph -> capture all the execution blocking relationships among different microservices
 - Actual load updating -> do the actual updating based on the blocking graphs 