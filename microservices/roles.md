## API Gateway / Edge Services

- Entry point to the system
- handling client requests
- Routing requests to the proper backend service 
- they also do some rate limiting and monitoring

## Business Logic / Core Services

## Storage Services
- data persistence and retrieval
- data consistency and managing transactions

## Communication / Messaging Services
- handle the coordination and communication between other microservices, often via messaging systems or event buses.
- Example: RabbitMQ or Kafka

##  Third-Party Integration Services
- Communication with third-party vendors or external APIs
- integration logic like retries and error handling for external requests
## Orchestrator / Coordination Services
- coordinate complex workflows or long-running transactions that involve multiple microservices.
- Orchestrating calls to multiple services
- managing distributed transactions

## Utility / Shared Services
- general-purpose services that provide reusable functionalities used by multiple other services.
- Shared logic such as logging, metrics, or authentication
- caching or session management
- Monitoring and observability

## Security Services
- manage security-related tasks like authentication, authorization, and encryption.
- Enforcing access control rules across services
- Managing user sessions and security policies
- Issuing and validating security tokens (e.g., JWT)

## Front-End Aggregator Services
- aggregate data from multiple backend services to provide a single, unified response to front-end requests.
- Performing read-heavy operations for dashboards or summary views

## Configuration and Management Services
- manage configuration and operational settings for other microservices
- Centralized configuration management for microservices
- roviding feature flags and dynamic configuration updates
- Service discovery and load balancing
- Examples: Consul or Spring Cloud Config






# What causes hotspots? 
- routing 
- incast 
- failures  
- resource saturation
- performance and efficiency bugs in the application implementation (example: blocking behavior between microservices)

- short-lived microbursts in resource usage


# AntiPatterns in microservices

## Related work
[ bottleneck, cyclic dependencies, knots](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=10015027)

[Using dependency graph and graph theory concepts to identify anti-patterns in a microservices system: A tool-based approach](https://www.researchgate.net/profile/Indika-Perera-3/publication/353807234_Using_dependency_graph_and_graph_theory_concepts_to_identify_anti-patterns_in_a_microservices_system_A_tool-based_approach/links/6112bf501e95fe241ac26936/Using-dependency-graph-and-graph-theory-concepts-to-identify-anti-patterns-in-a-microservices-system-A-tool-based-approach.pdf)

-  bottlenecks
- cycles
- knots -> A knot is a group of services that have low cohesion, but are tightly coupled, like a bottleneck but instead of a single node, it's a set of nodes

- finding anti-patterns require both static and dynamic analysis [like cyclic ones could be detected in real-time with matrics from the services]

- 