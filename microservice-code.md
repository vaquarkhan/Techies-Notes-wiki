https://gitbrowse.com/repos/ewolff/microservice


https://www.infoq.com/news/2016/12/netflix-conductor

https://netflix.github.io/conductor/


http://www.simplicityitself.io/getting/started/with/microservices/2015/06/10/service-discovery-overview.html


https://docs.google.com/document/d/15G8ew0qEDqpuBTWH9YGHKhda6HaLvfKuS4pnB-CPm50/mobilebasic

http://netflix.github.io/

# https://fernandoabcampos.wordpress.com/2016/03/16/config-server/

# http://callistaenterprise.se/blogg/teknik/2015/03/25/an-operations-model-for-microservices/

---------------------------------------------------------------------------------------------------------

![http://callistaenterprise.se/assets/blogg/build-microservices-part-1/mapping-table.png](http://callistaenterprise.se/assets/blogg/build-microservices-part-1/mapping-table.png)


# Background


When we started the design and development of our own SaaS, naturally our overarching goal was also to achieve continuous delivery for ourselves. We wanted our SaaS to have the following characteristics

* Service Oriented architecture to optimize application delivery
* Stateless services
* Uniform REST APIs
* Natively multi-tenant
* Cloud agnostic (as much as possible)
* Scalable
* Resilient
* Secure

We knew that adopting a microservices architecture was required to achieve continuous delivery but we also knew that it is was not a free lunch. This architecture comes with its own set of challenges. In this post I will describe some of these, and how we addressed them.

# Application “Dockerization”

Deploying & monitoring more than a handful of services  in a cloud agnostic way requires significant tooling. In our case, we got a nice break since it is exactly one of the problems our product is solving. The goal for us became to deploy our SaaS using … our SaaS!

We developed our own layer of service orchestration on top of Docker. Orchestration of microservices, combined with the choice of Docker as the delivery vehicle for microservices, proved to be a winning solution.

Writing Docker files to produce our service images was fairly easy. We integrated this step to our Continuous Delivery loop. We use Jenkins to build our services and then an additional step is executed to produce the Docker images and to store them in our private Docker registry. Finally Jenkins invokes our SaaS APIs to deploy the new images into our test environment and then into our staging environment.

Docker also helped us largely be cloud agnostic. Once a service is containerized, it can run on an AWS instance, a VMWare Virtual Machine or an OpenStack Virtual Machine without even knowing it. Docker is absolutely brilliant in achieving true runtime portability at a service level.

There is also a very interesting side effect to using containerization: cost saving. We use AWS to deploy our various SaaS environments: development, test, staging and production. Nirmata’s orchestration can use a placement policy to pack multiple containers on one single AWS instance. It represents hundreds of dollars of saving at the end of each month. Quite interesting for any cost sensitive business.

# Common Infrastructure Components

Another challenge to consider carefully when adopting a microservices architecture is that it requires more infrastructure components than regular 3-tier applications. We initially identified the following needs for our own SaaS:

* A service registry
* A traffic gateway to control the traffic to the services
* A framework to develop uniform REST API

# Service configuration framework

* HTTP client
* A caching mechanism for all the REST calls between mid-tier services
* Load balancing between mid-tier services
* Asynchronous framework/message bus/message queues
* Logging service

# Analytics service
After researching what was available we focused our attention on the work done by Netflix for several reasons:

Netflix architecture was aligned with what we wanted to achieve
They open sourced most of their platform code. These components were designed ground up as cloud native components
Unlike many open source components, Netflix infrastructure has been battle tested at scale … real scale.
Netflix developers were extremely open about their work. We had access to numerous presentations and papers. We could also interact and engage with them directly in the meetups they organized.
Netflix OSS Components used in our SaaS
From the 40+ Netflix OSS components available today, we started with 4:

* Gateway Service: Zuul
* Registry Service: Eureka
* Dynamic Properties: Archaius
* REST client: Ribbon

We consider these components as a good starting point for development of any application based on a microservices architecture. There are several others you should consider using, but in this post I will focus on these.

# Archaius

Archaius [1] is a Java library used for configuration management. It was designed to facilitate the implementation of dynamic properties.

Here is how you can retrieve a String property, the name of a Kafka topic in this case:

DynamicStringProperty value = DynamicPropertyFactory.getInstance().getStringProperty(    “nirmata.orchestrator.topic.config”, null);

The actual property value can be stored in a property file, an environment variable, a database accessible through JDBC or even a service accessible through a REST API.

Archaius uses a predefined hierarchy between these data sources to retrieve the correct value. You can also use cascading property files to provide default values to your services and override some of them at runtime.

Archaius main features include:

* Support for dynamic and strongly type properties
* Rich set of configuration source: JDB, REST API, Environment variables, Property files.
* Cascading configuration sources
* Hierarchical configuration sources
* Customizable polling mechanism
* Callback invoked upon property change

When developing a new application, it is easy to underestimate this area as we initially need few environment variables and property files. Using a framework like Archaius may seem overkill, at that time we quickly learnt that its important to build in the flexibility upfront. At Nirmata, we started using Archaius early on after selecting Zuul and Eureka. It allowed us to easily handle the differences between the developer environments (laptop or AWS sandbox) and our AWS staging environment. We defined one set of property files for the developer sandbox and a different set of property files for a staging environment.

Later on, it allowed us to add properties very quickly. Moreover it helped us to deploy our SaaS in multiple environments while sharing common services such as Kafka. We achieve logical separation between these SaaS instances by setting the Kafka topic names through Archaius properties.

# Eureka

Eureka [2] is Netflix service registry providing service discovery, middle tier load balancing and failover. It plays a critical role in our SaaS architecture. Most of our services register with Eureka so they can find each other dynamically. Our services use the Java based Eureka client library to interact with the Registry. Only services shared across environments such as Kafka and Elasticsearch do not register with Eureka.

Multiple instances of the same service can be registered at the same time with Eureka. In this case the Eureka client applies a basic round robin algorithm to select a specific instance to talk to. Service instances can come and go dynamically, other services won’t notice it. A service such as Eureka is critical when you start scaling out your micro services. It also makes deployment of microservices application easiers as services don’t need to be preconfigured with ip addresses of other services.

We adopted a model in which Eureka is deployed in each environment. All service instances running in our production environment register to a dedicated Eureka service, same for our staging environment, or any sandbox environment used by developers. The deployment itself was made very fast and easy by containerizing Eureka. We can then easily deploy our entire SaaS or just a single service very quickly to any environment.

The flexibility provided by Eureka also helps developers be more productive. A typical use case for us is to run a copy of the SaaS in a staging environment and debug a particular service directly on our laptop in Eclipse. The service instance running on the laptop registers to the Eureka service in the Cloud and then everything works pretty much as if our service was running in the cloud. In doing so, we can avoid painful remote debugging while using the full power of Eclipse in a production-like environment.

The only wrinkle we had to deal with was related to service dependencies. Since there is one Eureka service per environment, we have to inject dynamically the IP address in each service belonging to the same environment. Eureka is started first then when its public IP is determined, we can inject it in the other services which in turn can be started. This information is provided as environment variables and other services uses the Archaius API to retrieve it.

The next step for us will be to leverage the high availability and replication functionalities provided by Eureka. We will deploy a cluster of 3 Eureka instances across 3 AWS availability zones for our production environment.

# Ribbon

Ribbon [3] is the client library used along with Eureka to make REST calls between services. It is built on top of the Apache HTTP client library. Ribbon provides client side load balancing between our mid-tier services. It works in tandem with the Eureka client to determine the service instances available to receive an HTTP/HTTPS. The default load balancing algorithm is a simple round robin that can be customized if desired.

# Zuul

Zuul [4] is the last piece of the puzzle. This edge gateway service provides a rich and powerful feature set. For the time being, we are only using a small subset of its features, mainly the dynamic routing capabilities. The gateway is the entry point to our SaaS. Base on the prefix of a URI, external REST calls are routed to the appropriate microservice.

We found another interesting use for Zuul. Our web UI required access to cloud providers APIs like those from AWS, OpenStack or vCenter to discover entities already configured. The gateway exposes a simple REST API for each provider and these calls are translated into a JAVA SDK call to the cloud provider within the gateway.

Other features provided by Zuul consist of:

* Authentication and security
* Insights and monitoring
* Stress testing
* Load shedding
* Static response handling
* Multi region resiliency
* Putting Everything Together

The following diagram depicts a simplified version of the sequence of calls and messages flowing through the system.

netflix2-470x228

In this example we have 3 microservices A, B and C. Service A exposes an external REST API through the Zuul gateway. It communicates asynchronously with the Service B through using notifications. Service processes the notification can makes REST call to Service B. Service C exposes a REST API that is only accessible to the mid-tier services.

# Summary

The four Netflix components we used to start the development of our SaaS have helped us to deliver and Enterprise grade solution. This helped us focus on solving other challenges related microservices based applications. We will keep blogging on that to share our experience.

There are other Netflix OSS components you may want to consider depending on the problem you have to solve. But for us the four component, combined with Docker helped us greatly in achieving the speed of development and deployment we desired.

One area we want to improve is the implementation of some of our asynchronous services. We are just using plain Java, Java multi-threading and Java Futures to handle asynchronous requests. We noticed that the code is becoming more and more unwieldy. We want to experiment with reactive programming (RxJava and Java 8 Completable Futures) to see how we can simplify our code and also avoid any scalability issues in the future.

-------------------------------------------------------------------------
https://github.com/peterbourgon/go-microservices

