Horizontal scaling means that you scale by adding more machines into your pool of resources whereas Vertical scaling means that you scale by adding more power (CPU, RAM) to an existing machine.

An easy way remember this is to think of a machine on a server rack, we add more machines across the horizontal direction and add more resources to a machine in the vertical direction.


![](https://i.stack.imgur.com/On3tO.png)




**Horizontal Scaling **- also referred to as "scale-out" is basically the addition of more machines or setting up a cluster or a distributed environment for your software system. This usually requires a load-balancer program which is a middle-ware component in the standard 3 tier client-server architectural model.

Load-Balancer is responsible to distribute user requests (load) among the various back-end systems/machines/nodes in the cluster. Each of these back-end machines run a copy of your software and hence capable of servicing requests. This is just one of the various functions that load balancer may be performing. Another very common responsibility is "health-check" where the load balancer uses the "ping-echo" protocol or exchanges heartbeat messages with all the servers to ensure they are up and running fine.

Load-Balancer distributes load by maintaining state of each machine -- how many requests are being served by each machine, which machine is idle, which machine is over-loaded with queued requests etc. So the load balancing algorithm considers such things before redirecting the request to an appropriate server machine. It also takes into account the network overhead and might choose the server in the nearest data-center provided it is available to service the requests.

The request-response can also be done in 2 different ways:

Load Balancer always acts as an intermediary program for every response - In this case, once the request has been handed over to the server by the load balancer, any response from the server to the user will go through the load balancer. So the server machines that are actually servicing the request will never directly interface with the user machine running the client application. The machine hosting the load balancer program will be handling all the requests/responses to and from the user.
Load Balancer does not act as an intermediary for the responses coming from the server machine - In this case, once the server has received the request from load-balancer, it bypasses the load balancer and communicates it responses directly to the client.
Setting up a cluster and load-balancer as a front-end interface to the client application does not really complete our scale-out architecture and design. There are still lots of critical questions to be answered and a number of key design decisions to be made which will affect the overall properties of our system.

We first need to identify our business goals and areas where we would like to add value. These goals will give rise to various requirements. We should then ask ourselves various questions with respect to different systemic properties.

Will such a design address our performance requirements ?
What performance characteristics do we care about ? Is it the overall system throughput where we are interested in serving maximum number of requests in any given time ? Or is it the system's response time where we design for sending back the response to client in as less time as possible ? Both these and many other types of performance characteristics are related to each other.
Will such a design address our availability requirements ? Is the system fault-tolerant ? If so, what is the degree of it ?
Is such a design reliable ? Does it impact the correctness ? We should not forget that 100% correctness is an implicit goal of any system.
Are we really meeting our scalability goals ? Might be achieving the short-term or immediate ones, but what is going to happen in the long run ?
All these kinds of requirements should have quantifiable measures associated with them.

We should then make important design decisions by questioning ourselves, developing prototypes, and refining the design.

Firstly, is using load-balancer the only approach to distribute load and horizontally scale the system ?
Do the various back-end servers or nodes communicate with each other ? If yes, then how does the system address the situation where one or more nodes go down -- permanently or temporarily ? If yes, then how does the system address the situation where the network connecting the nodes is down, but all the nodes are up and running ? Most importantly, do we have to differentiate between these two situations ? How ?
Whether or not the back-end nodes communicate with each other, does our system need to maintain consistent data across all the nodes ? What level of consistency do we care about ? Is it that At any point in time, data across all the nodes should be consistent. Or later some point in time, data across all the nodes will be consistent. If so, then what is this "later" ? When and how do all the nodes converge to a consistent state ? How will we achieve "total-order" of operations across all the nodes ? Do we have a global clock ? If we are relying on every node's local clock, then how do we synchronize the clocks of all machines. They can easily seem to regress or a machine with an out of order clock might join the cluster. As a consequence, we may ignore the latest data, and consider old/stale data as the latest one.
What cluster setup do we have to design for ? Is it a "replica" cluster, where data on each node is replicated to some or every other node. In case of former, what is the replication factor, and how do we decide it ? Or is it a sharded cluster where cluster is divided into various shards or units. A shard is a designated group of nodes. Each shard takes care of a particular partition of data. Data across shards is not replicated, but each shard can adopt replication strategy within itself. Whatever distributed system we design, it should ideally be able to answer the above and many other similar questions.
All this is what makes a distributed system so interesting and challenging to design and implement.

**Vertical Scaling -** also referred to as "scale-up" approach is an attempt to increase the capacity of a single machine : By adding more processing power By adding more storage More memory etc Summary:
***


What is important here is to understand the differences between these 2 scaling approaches, identify what suits our requirements, and see if the application really fits the model we choose.

As you would have understood by now, horizontal scaling comes with overhead in the form of cluster setup, management, and maintenance costs and complexities. The design gets increasingly complex and programming model changes.

So simply throwing in new hardware and adding more nodes or machines is not the way to start. First, see if the requirements can be met by increasing the capacity or tuning characteristics of a single machine. If not, then go with the scale-out approach or a combination of both.