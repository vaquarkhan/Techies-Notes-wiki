## SSL 

Download GoDaddy or anyother web or self sign SSL  certificate. 

STEP 1:
Unzip the file you get.
- [some string].crt: This will be “Certificate body” for ELB
- gd_bundle-g2-g1.crt: This will be “Certificate Chain” for ELB
- [your site].com.pem: This will be you “Private Key” for ELB

STEP 2: — Amazon AWS Console
Go to AWS Console -> EC2 -> Load Balancers
Click your load balancer
Click the “Listeners” tab
Click “Change” in the HTTPS row, “SSL Certificate” column
Click “Upload a certificate to IAM” radio button

- https://aws.amazon.com/blogs/security/easier-certificate-validation-using-dns-with-aws-certificate-manager/
- https://knackforge.com/blog/vishnu/aws-elastic-load-balancer-godaddy-ssl
- https://stoitschev.de/2018/09/10/enable-elastic-beanstalk-for-https-with-a-load-balancer/
------------------------------------------------------

## Route 53 

* Go to Amazon AWS - services & select Route 53
* Click on Hosted Zones & Create a hosted zone with your name.
* Give domain name which you have bought, and make sure that you don’t add www. to it.
* Select type as Public Hosted Zone.
* Make a note of the Name Space servers, usually they are of 4 different entries, because you need them in the last step.
* Now we need to bring subdomain too,so your domain should also work.

1. * In name field type www.
1. * Type as A
1. * Alias as Yes
1. * In Alias Target select your site name

Routing Policy: Simple

Add the Amazon NameSpaceServers in Control panel of Domain Provider.

* Now go to GoDaddy domain console & under my domains, select settings.
* Change the Namespace Servers (you should see 2 servers name) to custom from default.
* One by one add all the 4 namespace servers you got. Save it and wait for 2–3 minutes.
* Then type your domain name in the browser and you will be redirected to your website if you follow the entire setup perfectly.


- https://www.youtube.com/watch?v=v4RBlFOrOjA
- https://stackoverflow.com/questions/11602232/how-to-link-godaddy-domain-with-aws-elastic-beanstalk-environment
- https://blog.vizuri.com/setting-up-godaddy-and-route53-with-ghost-blogger
- https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-beanstalk-environment.html
- https://medium.com/@minhdn/how-to-use-dns-with-aws-elastic-beanstalk-7c6320e9b14f

------------------------------------------------------
## Godady Redirect 

The ELB url is a CNAME. Given this fact, You cannot point an APEX domain to a CNAME. That does not confirm to defined DNS configuration. the IP addresses behind the ELB cannot be mapped to the domain as A records. A domain without an A Record wont resolve.

The right way to do this is as follows:

1. Given a root domain example.com, go to the Godaddy DNS and point the root domain to WWW.
2. Now, any request to example.com will redirect to the www.example.com subdomain.
3. For the www subdomain, attach the AWS ELB url as the CNAME.

This will now prevent you from using a naked domain since it will redirect to www subdomain (its a 301, Permanent redirect). But this is the only way it can work with GoDaddy and other public Domain registrars.

The other alternative is to point the naked domain to an elastic IP assigned to one of the EC2 instances and the www to point to the CNAME.


Using Godaddy DNS, Alias to AWS resources such as ELB is not possible which limits using naked domain mappings to ELB. Therefore you need to delegate DNS management to Route53 hosted zone(Or atleast for the naked domain) having the name server forwarding which cost you around $0.5 per hosted zone month for the first 25 hosted zones.

Since an IP address is available for an EC2, if you directly point an A record in Godaddy, it won't cost for DNS at AWS