##TrustStore :
 As the name indicates, its normally used to store the certificates of trusted entities. A process can maintain a store of certificates of all its trusted parties which it trusts.

##TrustManager: 
Determines whether the remote authentication credentials (and thus the connection) should be trusted.

##keyStore : 
Used to store the server keys (both public and private) along with signed cert.

##KeyManager: 
Determines which authentication credentials to send to the remote host.

## Difference between trustStore vs keyStore in Java SSL
 
TrustStore is used to store certificates from trusted Certificate authorities(CA) which are used to verify certificate presented by Server in SSL Connection while keyStore is used to store private key and own identity certificate which program should present to other parties (Server or client) to verify its identity. 

- https://www.cloudera.com/documentation/enterprise/5-10-x/topics/cm_sg_create_key_trust.html