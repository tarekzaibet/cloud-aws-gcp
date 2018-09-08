
## Serverless :

Serverless Architecture is basically a cloud based execution model, where you dont have to manage manually your servers, the cloud provider automatically alocates and scales servers for your application.

A serverless application is an event driven app where the server is trigered each time the app is invoced.

#Where to use Serverless ?
- web services
- bot dev
- batch processing
- event driven processing

# What are the advantages of Serverless ?

- scalability : very fast scalability, no need to scale in advance, ability to scale instantly unlike the traditional IaaS platforms.
- on demand billing : based "on zero-use zero-cost" model. This means that users pay only for compute ressources that are usefully consumed. This means that users dont pay for unused ressources.
- no infra to manage : you dont have to manager your infrastructure which leads to more focus on the product quality and more ressources affcted to enhance the product.
- security: As per the Shared Responsibility Model, the cloud provider is
responsible for the security of the underlying serverless execution
environment and are able to devote significant resources and
expertise to this, developers can benefit from the managed
security and are free to concentrate on application-level security

# What are the cons of serverless ?
- stateless : it is possible that a particular execution sandbox may continue to execute on the same underlying compute resource, this is by no means guaranteed and must not be relied upon. Typical serverless architectures make use of in-memory stores such as Redis, SQL/NoSQL databases or message queues to hold state
- artefact size : limited compressed / uncompressed artefact size including dependencies ( 50 mb compressed / 250 uncompressed for lambda )
- limited native runtime support : not all runtimes are supported generally it's java - node js - python - .net with specific versions
- cold start and pinging : this is tricky since it depends on the runtime https://medium.com/@nathan.malishev/lambda-cold-starts-language-comparison-%EF%B8%8F-a4f4b5f16a62