## Amazon SES and lambda instance in different regions

SES not available in all region However, there is no reason you cannot connect cross-region to an SES service from your existing region. You do not need to change your app in your existing region to do this. While SES and networking best practices suggest you would want to choose an endpoint closest to your application, to reduce network latency, there is no SES requirement for your app instance to be in the same region as your SES service. Assuming you are using SMTP/S to send email, the email server can be anywhere on the internet.

You can configure your app in Asia-Pacific to send email via the SMTP/S endpoint provided by SES in any region. Again, network latency may be an issue, but depending on your mail volume, I would not expect network latency to be prohibitive. In fact I believe this setup is quite common for users in regions where SES is not supported.

I would suggest you setup the SES service in any of the available regions (say EU-Ireland), and run some basic load testing and see how the latency affects your application, if at all.

For more info, see Connecting to the Amazon SES SMTP Endpoint


#### Error 👍 

       `{`
          `"errorMessage": "Inaccessible host: `email.us-east-2.amazonaws.com'. This service may not be available in the `us- 
           east-2' region.",`
          `"errorType": "UnknownEndpoint",`
          `"stackTrace": [`
          `"Request.ENOTFOUND_ERROR (/var/runtime/node_modules/aws-sdk/lib/event_listeners.js:481:46)",`
          `"Request.callListeners (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:105:20)",`
          `"Request.emit (/var/runtime/node_modules/aws-sdk/lib/sequential_executor.js:77:10)",`
          `"Request.emit (/var/runtime/node_modules/aws-sdk/lib/request.js:683:14)",`
           `"ClientRequest.error (/var/runtime/node_modules/aws-sdk/lib/event_listeners.js:320:22)",`
          `"ClientRequest.<anonymous> (/var/runtime/node_modules/aws-sdk/lib/http/node.js:89:19)",`
           `"emitOne (events.js:116:13)",`
           `"ClientRequest.emit (events.js:211:7)",`
           `"TLSSocket.socketErrorListener (_http_client.js:387:9)",`
           `"emitOne (events.js:116:13)"`
            `]`
         `}`


Logs 👍 

      `START RequestId: acab6449-0e1b-11e9-a486-ab69638cb57b Version: $LATEST`
      `2019-01-01T23:19:24.047Z	acab6449-0e1b-11e9-a486-ab69638cb57b	Received event: { 'name-input': 'Vaquar',`
      `'phone-input': '2244360783',`
      `email: 'vaquar.cna@gmail.com',`
      `'description-input': 'test' }`
      `2019-01-01T23:19:25.038Z	acab6449-0e1b-11e9-a486-ab69638cb57b	{"errorMessage":"Inaccessible host: `email.us- 
      east-2.amazonaws.com'. This service may not be available in the `us-east-2'  
      region.","errorType":"UnknownEndpoint","stackTrace":["Request.ENOTFOUND_ERROR (/var/runtime/node_modules/aws- 
      sdk/lib/event_listeners.js:481:46)","Request.callListeners (/var/runtime/node_modules/aws- 
      sdk/lib/sequential_executor.js:105:20)","Request.emit (/var/runtime/node_modules/aws- 
      sdk/lib/sequential_executor.js:77:10)","Request.emit (/var/runtime/node_modules/aws- 
      sdk/lib/request.js:683:14)","ClientRequest.error (/var/runtime/node_modules/aws- 
      sdk/lib/event_listeners.js:320:22)","ClientRequest.<anonymous> (/var/runtime/node_modules/aws- 
      sdk/lib/http/node.js:89:19)","emitOne (events.js:116:13)","ClientRequest.emit 
      (events.js:211:7)","TLSSocket.socketErrorListener (_http_client.js:387:9)","emitOne (events.js:116:13)"]}`
     `END RequestId: acab6449-0e1b-11e9-a486-ab69638cb57b`
     `REPORT RequestId: acab6449-0e1b-11e9-a486-ab69638cb57b	Duration: 1013.79 ms	Billed Duration: 1100 ms 	 
      Memory Size: 128 MB	Max Memory Used: 58 MB	`


#### How to solve 👍 

- https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SES.html#endpoint-property
- https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/ses-examples-sending-email.html


     "endpoint (String) — The endpoint URI to send requests to. The default endpoint is built from the configured 
         region. The endpoint should be a string like 'https://{service}.{region}.amazonaws.com'. "

     ` // Load the SDK for JavaScript`
     `var AWS = require('aws-sdk');`

     `// Set the region `
     `AWS.config.update({region: 'us-west-2'});`


#### Results 👍 

           START RequestId: 4bb51cd7-0e25-11e9-9f39-17c2f92fd31c Version: $LATEST
           2019-01-02T00:28:16.108Z	4bb51cd7-0e25-11e9-9f39-17c2f92fd31c	Received event: { 'name-input': 'Vaquar',
           'phone-input': '2244360783',
            email: 'vaquar.cna@gmail.com',
           'description-input': 'test' }
            END RequestId: 4bb51cd7-0e25-11e9-9f39-17c2f92fd31c
            REPORT RequestId: 4bb51cd7-0e25-11e9-9f39-17c2f92fd31c	Duration: 1193.88 ms	Billed Duration: 1200 ms 	 
             Memory Size: 128 MB	Max Memory Used: 29 MB	


#### Note : find your near SES available region and set in AWS SDK
---------------------------------------------------------
#### Use GMail with your own domain for free thanks to Amazon SES & Lambda

---------------------------------------------------------

- http://www.daniloaz.com/en/use-gmail-with-your-own-domain-for-free-thanks-to-amazon-ses-lambda/
- https://dev.to/adnanrahic/building-a-serverless-contact-form-with-aws-lambda-and-aws-ses-4jm0
- https://docs.aws.amazon.com/sdkforruby/api/Aws/SES/Client.html
- https://aws.amazon.com/blogs/architecture/create-dynamic-contact-forms-for-s3-static-websites-using-aws-lambda-amazon-api-gateway-and-amazon-ses/
- https://github.com/vaquarkhan/static-website-s3-dynamic-contactus
- https://vmokshagroup.com/blog/setting-up-a-secure-email-engine-using-amazon-ses/
