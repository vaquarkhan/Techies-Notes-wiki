- JWT are mostly used to secure web APIs (request data),It is a token that only the server can generate, and can contain a payload of data.

- JWT tokens are included in the Authorization HTTP header as part of the bearer authentication scheme. The main advantages of using bearer scheme authentication is that it's not vulnerable to CSRF attacks because your script needs to explicitly attach the token to the request and can be used cross-domain


- Bearer scheme authentication does require HTTPS connections as anyone who manages to steal the token can use it to access the API for as long as the token is valid.

- Security protocols like OAuth2 use JWT tokens to secure APIs. OpenID Connect uses JWT tokens to authenticate web applications, but stores the token in a cookie.

- Since JWT tokens are digitally signed by the issuer (server doing the authentication), they can be validated without talking to the server again.

--------------------------------------------------------------------------------

## Authentication flow using JWT

1. User credentials sent to /signin
2. /signin returns a JWT
3. JWT is stored in localStorage
4. JWT is sent on every request (to API?)
5. The server decrypts JWT and extracts user ID out of it
6. The server sends response given the authenticated user.





- https://scotch.io/tutorials/the-anatomy-of-a-json-web-token
- https://stackoverflow.com/questions/25392562/jwt-authentication-concept?rq=1
- https://blog.gds-gov.tech/our-considerations-on-token-design-session-management-c2fa96198e6d
- https://stormpath.com/blog/the-ultimate-guide-to-mobile-api-security
- https://www.baeldung.com/java-json-web-tokens-jjwt