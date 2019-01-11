The advantage of using Lambda Function is that it can perform authorization processing other than verification of IdToken. For example, you can write processing according to your application, such as IP restrictions and allowing only specific user agents.

### Create Cognito user pool:
  - 1. Create a new user pool
  - 2. Setting attribute of user pool
  - 3. Application client registration for user pool (This time, I will remove client secret. Also, auth - flow selects ADMIN_NO_SRP_AUTH)
  - 4. User registration after user pool creation
 

#### if you choose forse password change not not creted custom UI following script help to change password.

The script when changing the password looks something like this. Please fill in the necessary items accordingly. After execution, I think that I can confirm that IdToken etc is output to the console.

----------------------------------------------------------------------------------------------

       #!/bin/sh
 
      user_pool_id=""
      client_id=""
      user_name=""
      password=""
      new_password=""
 
     session="$(aws cognito-idp admin-initiate-auth --user-pool-id ${user_pool_id} --client-id ${client_id} --auth-flow 
     ADMIN_NO_SRP_AUTH --auth-parameters USERNAME=${user_name},PASSWORD=${password} | jq -r .Session)"
     aws cognito-idp admin-respond-to-auth-challenge --user-pool-id  ${user_pool_id} --client-id ${client_id} --challenge- 
     name NEW_PASSWORD_REQUIRED --challenge-responses USERNAME=${user_name},NEW_PASSWORD=${new_password} --session 
     ${session}

----------------------------------------------------------------------------------------------

  - 5. Acquisition of IdToken

IdToken etc will be returned after resetting the password of the user in step 4, but IdToken will expire in 1 hour by default, so we need to reacquire it. In such a case, you can reacquire with the following command


----------------------------------------------------------------------------------------------

        aws cognito-idp admin-initiate-auth --user-pool-id <user_pool_id> --client-id <client_id> --auth-flow 
        ADMIN_NO_SRP_AUTH --auth-parameters USERNAME=<user_name>,PASSWORD=<password>

----------------------------------------------------------------------------------------------

### Building an API

 
- https://dev.classmethod.jp/cloud/aws/verify_cognit_idtoken_by_apig_custom_auth/
- https://dev.classmethod.jp/server-side/serverless/lambda-authorizer/




#### Reference site

- https://aws.amazon.com/jp/premiumsupport/knowledge-center/decode-verify-cognito-json-token/
- https://github.com/awslabs/aws-support-tools/tree/master/Cognito/decode-verify-jwt
- https://github.com/awslabs/aws-apigateway-lambda-authorizer-blueprints
- https://blog.kdgregory.com/2016/12/server-side-authentication-with-amazon.html