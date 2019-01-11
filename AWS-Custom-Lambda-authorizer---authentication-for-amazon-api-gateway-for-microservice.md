Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily. Amazon Cognito scales to millions of users and supports sign-in with social identity providers, such as Facebook, Google, and Amazon, and enterprise identity providers via SAML 2.0.


#### Advantages for using Cognito:

- Managed service, less components to implement/monitor/scale
- Easily configurable via portal, CLI and templates
- Supports multiple flows for authentication (client side, server side, OAuth2, custom)
- Supports Lambda triggered functions on authentication/registration events
- Uses JWT signed tokens which can be passed directly to clients in session cookies and used to verify requests and passed in related API calls so a single authentication/authorisation method can be used through your stack statelessly
Group membership, supplied in access token can be used for authorisation (e.g. users in group “Admin” can perform admin functions)

- Handles:
- User group membership and attribute storage
- Email/Phone verification
- User invitation
- Login/Signup UI forms (customisable)
- Password reset

#### Disadvantages:

- Less control over authentication/authorisation (limits to UI/flow customisation)
- Potential for lock-in (cannot export users with passwords for migration)


##### Amazon Cognito has three type of authorizer 

1. Amazon Cognito user pool                  - User pool authorizer.
2. Amazon Cognito federated identities       - AWS IAM Authorization.
3. Custom Lambda identity providers                 - Custom Autorizer


##### Custom Lambda identity providers has two different options.
1. Token
2. Request    

![Alt Text](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2017/09/27/Screen-Shot-2017-09-13-at-10.13.18-AM-215x300.png)


Previously, custom authorizers received only the bearer token included in the request and the ARN of the API Gateway method being called.

Enhanced request authorizers receive all of the headers, query string, and path parameters as well as the request context. This enables you to make more sophisticated authorization decisions based on parameters such as the client IP address, user agent, or a query string parameter alongside the client bearer token.

So Authorizers can use token and request.

### TOKEN :
TOKEN type Lambda authorizers grant a caller permissions to invoke a given request using an authorization token passed in a header. The token could be, for example, an OAuth token.

Token-based lambda authorizers receive the below object as its event to process the authorization request:

     `{`
         `"type":"TOKEN",`
         `"authorizationToken":"<caller-supplied-token>",`
         `"methodArn":"arn:aws:execute-api:<regionId>:<accountId>:<apiId>/<stage>/<method>/<resourcePath>"`
       `}`


### REQUEST :

REQUEST type Lambda authorizers grant a caller permissions to invoke a given request using request parameters, including headers, query strings, stage variables, or context parameters.

For request-based lambda authorizers, lambda receives the event object as below:

     `{`
         `"type":"REQUEST",`
         `"methodArn":"arn:aws:execute-api:us-east-1:xxxx:xxxx/test/GET/request",`
         `"resource":"/request",`
        `"path":"/request",`
        `"httpMethod":"GET",`
        `"headers":{`
         `...`
         `},`
         `"queryStringParameters":{`
         `"QueryString1":"queryValue1"`
         `},`
         `"pathParameters":{`
         `},`
          `"stageVariables":{`
          `"StageVar1":"stageValue1"`
         `},`
        `"requestContext":{`
         `"path":"/request",`
         `"accountId":"xxxxx",`
         `"resourceId":"xxxx",`
         `"stage":"test",`
         `"requestId":"...",`
        `"identity":{`
         `"apiKey":"...",`
         `"sourceIp":"..."`
         `},`
         `"resourcePath":"/request",`
         `"httpMethod":"GET",`
         `"apiId":"xxxx"`
         `}`
      `}`



-----------------------------------------------------------------------------------------

![Alt Text](https://docs.aws.amazon.com/apigateway/latest/developerguide/images/custom-auth-workflow.png)

-----------------------------------------------------------------------------------------


## Cognito vs Custom lambda authorizer pros and cons 

### AWS Cognito:

Pros

AWS SDK handles everything for you and you cannot make much mistake in your authentication process.
Fine grained access control for AWS resources via IAM.
An extra lambda function in front of every API is not required for authentication.

Cons

Need to use AWS SDK specifically on client side. Programmers have to add this into their toolchain and make use if it during development. Adds extra complexity.
Fine grained access control for resources is not really required since the only access that is required is for API gateway.

###  Custom Lambda authorizer

An API Authorizer is a Lambda function that performs authentication and authorization on requests prior to AWS API Gateway execution. The Lambda returns an IAM policy that either permits or blocks the API requests that contain a particular authorization token. The returning IAM policy is then cached by the API Gateway so it can be later reused for up to 1 hour.


Pros

You can have your authentication mechanism the way you want it. Ultimate control over authentication and authorization.
You can have the UI call the APIs with a standard token (JWT) and the flow for developers remains same. No extra consideration of AWS SDK.

Cons

Authentication requires a lot of thinking and effort to build.
Chances of missing some crucial aspects are always there.
Its like reinventing the wheel. Why do it when Amazon has already done it for you.


Note : The authorizer is an intercepting mechanism provided so that you can add custom logic into lambda function and call in authorize calls. The custom logic may use rules based authorization.


### ***
The Cognito Authorizer is great for quickly getting things going and utilizing powerful out of the box authentication and authorization. This would be my first choice as it's the easiest to get going without needing to understand the smaller details first. Just handle sending back the id token after logging the user in then pass that id token to your protected API routes in the header of your requests (e.g. "X-Auth-Token": "[id_token]").

The Lambda Custom Authorizer with AWS_IAM is great if you need to take the validation a step further than the built in solution API Gateway provides with Cognito Authroizer. If you need to allow users to sign in with their Google, Facebook, Amazon, Twitter or any other identity provider account. Then Lambda Custom Authorizer with AWS_IAM would be your choice as it allows for this type of functionality.



-----------------------------------------------------------------------------------------


![Alt Text](https://cdn-images-1.medium.com/max/1000/1*b46gRLzVry1kr8ikUOrXMg.jpeg)
![Alt Text](https://i.stack.imgur.com/1cq56.png)

[Control a Lambda Function of a Lambda Authorizer](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html#api-gateway-lambda-authorizer-types)


Examples:

1. **Example using a self-encoded access token**<br/>
[Introducing custom authorizers in Amazon API Gateway](https://aws.amazon.com/jp/blogs/compute/introducing-custom-authorizers-in-amazon-api-gateway) ([AWS Compute Blog](https://aws.amazon.com/blogs/compute/))

2. **Example using an unrealistic access token**<br/>
[Enable Amazon API Gateway Custom Authorization](http://docs.aws.amazon.com/apigateway/latest/developerguide/use-custom-authorizer.html) ([AWS Documentation](https://aws.amazon.com/documentation/))

3. **Example using an external authorization server**<br/>
[Amazon API Gateway Custom Authorizer + OAuth](https://www.authlete.com/documents/article/custom_authorizer) ([Authlete](https://www.authlete.com/))



## Sample pre post trigger Function python 2.7 and AWS blue print for lambda authorizer 

- https://github.com/vaquarkhan/aws-lambda-example


-----------------------------------------------------------------------------------------------------

Above notes i have created using  aws doc,image and following links

-------------------------------------------------------------------------------------------------------
- https://markpollmann.com/lambda-authorizer/
- https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html
- https://blog.codecentric.de/en/2018/04/aws-lambda-authorizer/
- https://github.com/awslabs/aws-apigateway-lambda-authorizer-blueprints/blob/master/blueprints/java/src/io/TokenAuthorizerContext.java
- https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html
- https://aws.amazon.com/blogs/compute/using-enhanced-request-authorizers-in-amazon-api-gateway/
- https://dzone.com/articles/reuse-authorizers-across-aws-apis
- https://stackoverflow.com/questions/40656761/custom-authorizer-vs-cognito-authentication-for-amazon-api-gateway-web-appli
- https://auth0.com/docs/integrations/aws-api-gateway/custom-authorizers/part-3
- https://blog.codecentric.de/en/2018/04/aws-lambda-authorizer/
- https://docs.aws.amazon.com/apigateway/latest/developerguide/call-api-with-api-gateway-lambda-authorization.html
- https://www.slideshare.net/AmazonWebServices/security-best-practices-for-serverless-applications-july-2017-aws-online-tech-talks
- https://medium.com/@awskarthik82/part-1-securing-aws-api-gateway-using-aws-cognito-oauth2-scopes-410e7fb4a4c0
- https://github.com/UWFosterIT/aws-cognito/wiki/Step-1:-AWS-AuthN-using-Cognito-User-Pool-with-SAML-Federation-at-the-UW
- https://hackernoon.com/full-guide-to-building-a-serverless-api-with-zero-code-c4f7871998f5
- https://dzone.com/articles/the-brief-summary-of-hashiconf-2018-insights
- https://aws.amazon.com/blogs/mobile/building-fine-grained-authorization-using-amazon-cognito-user-pools-groups/
- https://www.slideshare.net/AmazonWebServices/securing-serverless-workloads-with-cognito-and-api-gateway-part-i-aws-security-day
- https://www.slideshare.net/AmazonWebServices/securing-serverless-workloads-with-cognito-and-api-gateway-part-ii-aws-security-day