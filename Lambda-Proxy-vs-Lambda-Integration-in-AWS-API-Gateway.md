- https://medium.com/@lakshmanLD/lambda-proxy-vs-lambda-integration-in-aws-api-gateway-3a9397af0e6d
- https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-proxy-integrations.html
- https://read.acloud.guru/how-you-should-and-should-not-use-the-api-gateway-proxy-integration-f9e35479b993
- https://www.stackery.io/blog/why-you-should-use-api-gateway-proxy-integration-with-lambda/



### Lambda Integration with Proxy

* Pro: One can rapidly prototype and code without worrying about all the needed configuration details (and reinventing a few wheels like generic template mappings, etc).
* Pro: It's really easy to return any status code and custom headers, while at the same time there's a generic way to read the body, headers, parameters, of the request.
* Con: Everything is done in code, so autogenerating documentation is a bit more difficult. Dependencies (headers, models, returned status codes) are "hidden" in the code.


### Lambda Integration without Proxy

* Con: Involves a lot more of work to set it up, and this configuration might be duplicated in different resources.
* Pro: It allows one to decouple what the lambda receives and returns, and how it gets mapped to different HTTP status codes, headers, and payloads.
* Pro: Very useful because it stipulates upfront what it returns, and what it requires in terms of headers and payloads.
* Pro: The hard work when setting up everything is useful in the long run because one can export everything to Swagger, so others can use this to generate different SDKs for it.

