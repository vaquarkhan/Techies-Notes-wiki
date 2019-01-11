- https://github.com/aws-samples/aws-developer-workshop
- https://github.com/dwyl/learn-aws-lambda
- https://github.com/dwyl/learn-aws-lambda/tree/master/examples

- https://www.youtube.com/watch?v=IiWyPb389UU

- https://www.youtube.com/watch?v=_UiyIJqDXXQ

-----------------------------
- https://www.youtube.com/watch?v=y_whCBFKlj0
- https://www.youtube.com/watch?v=IiWyPb389UU
- https://gist.github.com/fcavalcantirj
- https://stackoverflow.com/questions/43954325/why-does-my-jwt-token-from-amazon-cognito-have-an-invalid-signature
- https://aws.amazon.com/premiumsupport/knowledge-center/decode-verify-cognito-json-token/
- https://community.blackboard.com/thread/4359-using-postman-to-try-out-the-rest-apis
- https://www.youtube.com/watch?v=VZqG7HjT2AQ&t=2169s
- https://www.youtube.com/watch?v=_UiyIJqDXXQ

## cognito 
- https://www.youtube.com/watch?v=KWjgiNgDfwI



-----------------------------
VK-PRE-SIGNUP-Lambda

#python 2.7

`def lambda_handler(event,context):`
    `event['response'] = {`
        `"autoConfirmUser" : True`
        
    `}`
    `return event`



-------------------------
VK-PRE-SIGNIN-Lambda


`from __future__ import print_function`
`import boto3`
`import botocore.exceptions`
`import hmac`
`import hashlib`
`import base64`
`import json`
`import uuid`
`import logging`
`#Demo pool`
`USER_POOL_ID='ca-central-1_XXXXX'`
`CLIENT_ID ='vkXXXXXXXXXXXXXXXXXXXXX'`
`CLIENT_SECRET='XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'`


`logger = logging.getLogger()`
`logger.setLevel(logging.INFO)`

`client = None`

`def get_secret_hash(username):`
    `msg = username + CLIENT_ID`
    `dig = hmac.new(str(CLIENT_SECRET).encode('utf-8'), `
        `msg = str(msg).encode('utf-8'), digestmod=hashlib.sha256).digest()`
    `d2 = base64.b64encode(dig).decode()`
    `return d2`

`ERROR = 0`
`SUCCESS = 1`
`USER_EXISTS = 2`


`def sign_up(username,password):`
    `try:`
        `resp = client.sign_up(`
            `ClientId=CLIENT_ID ,`
            `SecretHash= get_secret_hash(username),`
            `Username=username,`
            `Password=password )`
        `print(resp)`
    `#except client.exceptions.UserNotFoundException as e:`
    `except client.exceptions.UsernameExistsException as e:`
        `return USER_EXISTS`
    `except Exception as e:`
        `print(e)`
        `logger.error(e)`
        `return ERROR`
    `return SUCCESS`

`def initiate_auth(username, password):`
    `try:`
      `# AdminInitiateAuth`
        `resp = client.admin_initiate_auth(`
            `UserPoolId=USER_POOL_ID,`
            `ClientId=CLIENT_ID,`
            `AuthFlow='ADMIN_NO_SRP_AUTH',`
            `AuthParameters={`
                `'USERNAME': username,`
                `'SECRET_HASH': get_secret_hash(username),`
                `'PASSWORD': password`
            `},`
            `ClientMetadata={`
                `'username': username,`
                `'password': password`
            `})`
    `except client.exceptions.NotAuthorizedException as e:`
        `return None, "The username or password incorrect"`
    `except client.exceptions.UserNotFoundException as e:`
        `return None, "Unauthorized"`
    `except Exception as e:`
        `print(e)`
        `logger.error(e)`
        `return None, "Unknown error"`
    `return resp, None`

`def lambda_handler(event, context):`
    `global client`
    `if client == None:`
        `client = boto3.client('cognito-idp')`

    `print(event)`
    `body = event`
    `username = body['username']`
    `password = body['password']`
    
    `is_new ="false"`
    `user_id=str(uuid.uuid4())`
    `signed_up = sign_up(username,password)`
    `if signed_up == ERROR :`
        `return {'status':'fail','msg':'failed to sign up'}`
    `if signed_up == SUCCESS:`
        `is_new="true"`
    
    `resp,msg =initiate_auth(username,password)    `
    
    `if msg != None :`
        `logger.info('failed signIN with username={}'.format(username))`
        `return {'status':'fail','msg':msg}`
        
    `id_token =resp['AuthenticationResult']['IdToken']`
    `print('id token: '+id_token)`
    `return {'status': 'success', 'id_token': id_token, 'user_id':user_id,'is_new':is_new}`
    `#resp, msg = sign_up(username, password)`
    
   `# if msg != None:`
    `#    return {'status': 'fail', 'message': msg}`
    `#    logger.info('failed signIN with username={}'.format(username))`
    `#    raise Exception(msg)`

    `#logger.info('successful signIN with username={}'.format(username))`
    `#id_token = resp['AuthenticationResult']['IdToken']`
    `#access_token = resp['AuthenticationResult']['AccessToken']`
    `#expires_in = resp['AuthenticationResult']['ExpiresIn']`
    `#refresh_token = resp['AuthenticationResult']['RefreshToken']`
    `#return {'status': 'success', 'id_token': id_token, 'user_id':user_id,'is_new':is_new}`
    
    `#return {'status': 'success', 'id_token': id_token, 'user_id':user_id,'is_new':is_new}`



