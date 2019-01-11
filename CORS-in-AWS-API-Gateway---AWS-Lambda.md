- https://stackoverflow.com/questions/43567284/cors-in-aws-api-gateway-aws-lambda
- https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-cors.html

-----------------------------------------
#### What is CORS

Cross Origin Resource Sharing is something that is declared by the w3c on communication between different domains. By CORS, communications between the same domain will be allowed to users and the communications that are cross-originated will be restricted to a few techniques. We can see this when we are talking to APIs mostly. The REST call may give us an error. This is because the server and the client sides are on different domains and the communication between them are restricted by CORS rules.


#### What went wrong?

The response to the CORS request is missing the required Access-Control-Allow-Origin header, which is used to determine whether or not the resource can be accessed by content operating within the current origin.

If the server is under your control, add the origin of the requesting site to the set of domains permitted access by adding it to the Access-Control-Allow-Origin header's value.

For example, to allow a site at https://amazing.site to access the resource using CORS, the header should be:

Access-Control-Allow-Origin: https://amazing.site

You can also configure a site to allow any site to access it by using the "*" wildcard. You should only use this for public APIs. Private APIs should never use "*", and should instead have a specific domain or domains set. In addition, the wildcard only works for requests made with the crossorigin attribute set to "anonymous".

Access-Control-Allow

Warning: Using the wildcard to allow all sites to access a private API is a bad idea for what should be obvious reasons.

 

For example, in Apache, add a line such as the following to the server's configuration (within the appropriate <Directory>, <Location>, <Files>, or <VirtualHost> section). The configuration is typically found in a .conf file (httpd.conf and apache.conf are common names for these), or in an .htaccess file.

Header set Access-Control-Allow-Origin 'origin-list'

For Nginx, the command to set up

this header is:

add_header 'Access-Control-Allow-Origin' 'origin-list'