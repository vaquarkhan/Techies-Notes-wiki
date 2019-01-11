## API Gateway supports multiple mechanisms of access control:

- API Gateway Resource Policies
- IAM Permissions
- Enabling CORS
- Lambda Authorizers
- Amazon Cognito User Pools
- Client-Side SSL Certificates

### API Gateway Resource Policies: 
Amazon API Gateway Resource Policies are JSON policy documents attached to API to control whether a specified principal 
(typically an IAM user or role) can invoke the API. You can use the policies to allow users from different AWS accounts to securely access your API or to permit the API to be invoked only from specified source IP address ranges or CIDR blocks.

### IAM Permissions: 
IAM Permissions are standard IAM access controls. An API developer must have appropriate permissions to be able to create, deploy and manage APIs, call a deployed API, or refresh API caching. An API caller needs relevant permissions to perform required IAM actions supported by the API execution component of API Gateway.

### CORS (Cross-origin resource sharing):
The CORS mechanism is required to enable APIs receiving requests from domains other than the API’s domain. To learn how to enable it for Amazon Gateway API, go to the official AWS documentation.

### Amazon API Authorizer (custom authorizer):
Formerly known as the custom authorizer, this is a Lambda function that you provide to control access to your API methods. Go to the official AWS documentation for more details.

### Amazon Cognito User Pools:
You can use Amazon Cognito User Pools in addition to IAM permissions and Lambda authorizer to control who can access your API in Amazon API Gateway. After the API is deployed, the client must first sign the user into a user pool, obtain an identity or access token for the user, and then call the API method with one of the tokens.

### Client-Side SSL Certificates
You can also use API Gateway to generate an SSL certificate to verify that HTTP requests to your backend originated from API Gateway. This means that even if your backend systems are publicly accessible, they will accept only requests originating from API Gateway.