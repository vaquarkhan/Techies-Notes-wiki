What is Eureka?

Eureka is a REST (Representational State Transfer) based service that is primarily used in the AWS cloud for locating services for the purpose of load balancing and failover of middle-tier servers. We call this service, the Eureka Server. Eureka also comes with a Java-based client component,the Eureka Client, which makes interactions with the service much easier. The client also has a built-in load balancer that does basic round-robin load balancing. At Netflix, a much more sophisticated load balancer wraps Eureka to provide weighted load balancing based on several factors like traffic, resource usage, error conditions etc to provide superior resiliency.

What is the need for Eureka?

In AWS cloud, because of its inherent nature, servers come and go. Unlike the traditional load balancers which work with servers with well known IP addresses and host names, in AWS, load balancing requires much more sophistication in registering and de-registering servers with load balancer on the fly. Since AWS does not yet provide a middle tier load balancer, Eureka fills a big gap in the area of mid-tier load balancing.

How different is Eureka from AWS ELB?

AWS Elastic Load Balancer is a load balancing solution for edge services exposed to end-user web traffic. Eureka fills the need for mid-tier load balancing. While you can theoretically put your mid-tier services behind the AWS ELB, in EC2 classic you expose them to the outside world and there by losing all the usefulness of the AWS security groups.

AWS ELB is also a traditional proxy-based load balancing solution whereas with Eureka it is different in that the load balancing happens at the instance/server/host level. The client instances know all the information about which servers they need to talk to. This is a blessing or a curse depending on which way you look at it. If you are looking for a sticky user session based load balancing which AWS now offers, Eureka does not offer a solution out of the box. At Netflix, we prefer our services to be stateless (non-sticky). This facilitates a much better scalability model and Eureka is well suited to address this.

Another important aspect that differentiates proxy-based load balancing from load balancing using Eureka is that your application can be resilient to the outages of the load balancers, since the information regarding the available servers is cached on the client. This does require a small amount of memory, but buys better resiliency.

How different is Eureka from Route 53?

Route 53 is a naming service, like which Eureka can provide the same for the mid-tier servers, but the similarity stops there. Route 53 is a DNS service which can host your DNS records even for non-AWS data centers. Route 53 can also do latency based routing across AWS regions. Eureka is analogous to internal DNS and has nothing to do with the DNS servers across the world. Eureka is also region-isolated in the sense that it does not know about servers in other AWS regions. It's primary purpose of holding information is for load balancing within a region.

While you can register your mid-tier servers with Route 53 and rely on AWS security groups to protect your servers from public access, your mid-tier server identity is still exposed to the external world. It also comes with the drawback of the traditional DNS based load balancing solutions where the traffic can still be routed to servers which may not be healthy or may not even exist (in the case of AWS cloud where servers can disappear anytime).

How is Eureka used at Netflix?

At Netflix, Eureka is used for the following purposes apart from playing a critical part in mid-tier load balancing.

For red/black deployments with Netflix Asgard- an open source service which makes cloud deployment easier. Eureka interacts with Asgard to make switching between older/newer release of a deployment of services quick and seamless in case of problems - especially given that launching 100's of instances to deploy a release could take a long time.

For our cassandra deployments to take instances out of traffic for maintenance.

For our memcached caching services to identify the list of nodes in the ring.

Used for carrying other additional application specific metadata about services for various other reasons.

When should I use Eureka?

You typically run in the AWS cloud and you have a host of middle tier services which you do not want to register with AWS ELB or expose traffic from outside world. You are either looking for a simple round-robin load balancing solution or are willing to write your own wrapper around Eureka based on your load balancing need. You do not have the need for sticky sessions and load session data in an external cache such as memcached. More importantly, if your architecture fits the model where a client based load balancer is favored, Eureka is well positioned to fit that usage.

How does the application client and application server communicate?

The communication technology could be anything you like. Eureka helps you find the information about the services you would want to communicate with but does not impose any restrictions on the protocol or method of communication. For instance, you can use Eureka to obtain the target server address and use protocols such as thrift, http(s) or any other RPC mechanisms.

High level architecture

Eureka High level Architecture

The architecture above depicts how Eureka is deployed at Netflix and this is how you would typically run it. There is one eureka cluster per region which knows only about instances in its region. There is at the least one eureka server per zone to handle zone failures.

Services register with Eureka and then send heartbeats to renew their leases every 30 seconds. If the client cannot renew the lease for a few times, it is taken out of the server registry in about 90 seconds. The registration information and the renewals are replicated to all the eureka nodes in the cluster. The clients from any zone can look up the registry information (happens every 30 seconds) to locate their services (which could be in any zone) and make remote calls.

Non-Java services and clients

For services that are non-Java based, you have a choice of implementing the client part of eureka in the language of the service or you can run a "side car" which is essentially a Java application with an embedded eureka client that handles the registrations and heartbeats. REST based endpoints are also exposed for all operations that are supported by the Eureka client. Non-Java clients can use the REST end points to query for information about other services.

Configurability

With Eureka you can add or remove cluster nodes on the fly. You can tune the internal configurations from timeouts to thread pools. Eureka uses archaius and if you had a configuration source implementation a lot of these configurations can be tuned dynamically.

Resilience

Being in the AWS cloud, it is hard to not think about resiliency in everything we build. Eureka benefits from this experience we gained, with the resiliency built into both the client and the servers.

Eureka clients are built to handle the failure of one or more Eureka servers. Since Eureka clients have the registry cache information in them, they can operate reasonably well, even when all of the eureka servers go down.

Eureka Servers are resilient to other eureka peers going down. Even during a network partition between the clients and servers, the servers have built-in resiliency to prevent a large scale outage.

Multiple Regions

Deploying Eureka in multiple AWS regions is a fairly straightforward task. Eureka clusters between regions do not communicate with one another.

Monitoring

Eureka uses servo to track a lot information in both the client and the server for performance, monitoring and alerting.The data is typically available in the JMX registry and can be exported to Amazon Cloud Watch.