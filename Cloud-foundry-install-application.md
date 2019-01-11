### Deploy the Sample App

Now that you have the cf CLI installed and a Pivotal Web Services (PWS) account, you are really close to deploying the sample app.

This sample app is built with Spring Boot to get you up and running as quickly as possible.

Download the app with git:

git clone https://github.com/cloudfoundry-samples/cf-sample-app-spring.git

If you don't have Git installed, you can download a zip file of the app at https://github.com/cloudfoundry-samples/cf-sample-app-spring/archive/master.zip

Navigate to the app directory:

     cd cf-sample-app-spring

Sign in to PWS:

      cf login -a https://api.run.pivotal.io

Push the app to PWS:

   cf push
   
   Open the sample app in your browser:

![](https://d1fto35gcfffzn.cloudfront.net/images/products/gettingstartedwithpcf/example-url.png)