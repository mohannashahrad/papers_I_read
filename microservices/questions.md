1. Resource allocation or deployment/running strategies [like co-locating]?  should we do batching/re-ordering of requests also?
for resource works like Sinan only focus on CPU [because microservices are mostly stateless], but are there a class of microservices with memory allocations also important for them?
2. Does the networking/coomunication between services matter? [in most of the things I read it's usually Apache Thrift that is used for inter-service communication] for highly interactive services, can we potentially start some porcessing over the network going from one service to the other? 
3. Hybrid ways of hosting/deploying microservice applications, like both on serverless and also on servers? [EC2 and lambda together] / placement problem it would be
    https://www.datadoghq.com/knowledge-center/serverless-architecture/serverless-microservices/#:~:text=Microservices%20can%20be%20hosted%20on,down%20into%20many%20independent%20components. 

    given a micro-service can you detect which services run on where? [this is one level higher than resource allocation I guess, how to do placement of microservices to begin with? which ones to co-locate on the same node and stuff? what if when the dependancy graphs starts to change?]

    Papers to look at: 
    
    - "Improving microservice-based applications with runtime placement adaptation"" 

    - https://www.cs.ubc.ca/~bestchai/papers/icse23-seip-smart-tuning.pdf -> they do placement and shifting but without considering the dependencies between microservices

    - definitely should read this for that regard -> https://dl.acm.org/doi/pdf/10.1145/3627703.3629587 