## Cache at Network Level - 
   as Bruno said it's Persisted Queries and of course you can cache on a client and nobody stops you to use caching on 
   database level or even Redis. But of course as GraphQL has only one endpoint and each query is different 

- it's much more complicated to do this type of caching than with REST.
- Nested queries in GraphQL leads to circular queries and can crash the server - not a problem anymore with a vast variety of solutions. Some of them are listed here
- Handling File Upload - we have already lots of solutions for it
But there are couple more cases which can be counted as disadvantages:

- Boilerplate excessiveness ( by this I mean, for the creating for example new query you need to define schema, resolver and inside the resolver to explicitly say GraphQL how to resolve your data and fields inside, on the client side create query with exactly fields related to this data)

- Error handling - I need to say that it's more related to comparison with REST. It's possible here with apollo but at the same time it's much more complicated than in REST

- Authentication and authorization - but as I said community is increasing with outstanding speed and there are already couple of solutions for this goal.
To sum up, GraphQL is just a tool for specific goals and for sure it's not a silver bullet to all problems and of course not a replacement for REST.

- https://labs.getninjas.com.br/pain-points-of-graphql-7e83ba5ddef7



## Disadvantages:

* You need to learn how to set up GraphQL. The ecosystem is still rapidly evolving so you have to keep up.
* You need to send the queries from the client, you can just send strings but if you want more comfort and caching you'll use a client library -> extra code in your client
* You need to define the schema beforehand => extra work before you get results
* You need to have a graphql endpoint on your server => new libraries that you don't know yet
* Graphql queries are more bytes than simply going to a REST endpoint
* The server needs to do more processing to parse the query and verify the parameters

But, those are more than countered by these:

* GraphQL is not that hard to learn
* The extra code is only a few KB
* By defining a schema, you will prevent much more work afterwards fixing bugs and enduring hairy upgrades
* There are a lot of people switching to GraphQL so there is a rich ecosystem developing, with excellent tooling
* When using persistent queries in production (replacing GraphQL queries with simply an ID and parameters), you actually send less bytes than with REST
* The extra processing for incoming queries is negligible
* Providing a clean decoupling of API and backend allows for much faster iteration on backend improvenments

- https://stackoverflow.com/questions/40689858/are-there-any-disadvantages-to-graphql
