The link to the text: https://martinfowler.com/articles/microservices.html 

- Libraries: Components that are linked into a program and called using in-memory function calls, while 
- Services: Out-of-process components who communicate with a mechanism such as a web service request, or remote procedure call.

- One main reason for using services as components (rather than libraries) is that services are independently deployable. If you have an application that consists of a multiple libraries in a single process, a change to any single component results in having to redeploy the entire application

- The microservice community favours an alternative approach: smart endpoints and dumb pipes.
    - can we have smart end points and semi-smart pipes also? [based on the roles]

- The biggest issue in changing a monolith into microservices lies in changing the communication pattern. A naive conversion from in-memory method calls to RPC leads to chatty communications which don't perform well. Instead you need to replace the fine-grained communication with a coarser -grained approach.

- 