Thesis statement is that you can identify latent application bugs before deploying the MS application by using user-dentric approaches. 

## Microservices Dependencies

This classification of dependencies is based on how faults are handeled.

Hard Dependency: A dependency where a failed invocation forces the request that triggered the dependency to return an error to its caller.

Soft Dependency: A dependency where a failed invocation is handled in the application to avoid the request that triggered the dependency from returning an error.
    - When soft dependencies can be used, this is typically called graceful degradation.

Soft is prefered over hard: 

developers often prefer soft dependencies as they enable the “graceful degradation” of microservice applications: the ability for applications to reduce their functionality in the event of failure to provide still (some) service to the customer.
 
In essence, it is always preferred to give the customer a slightly incorrect or incomplete answer over giving them an error, where they may turn to different vendors of the same consumer service. (switching from Uber to Lyft for example)
