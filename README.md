# AWS ELB Setup

## Instructions on how to set up an Elastic Load Balancer behind a secure VPC.
*NOTE: You'll probably want to start with this doc on [AWS VPC Setup](https://github.com/jaylong255/aws-vpc-setup). All of these instructions assume that you already have a secure VPC.*
### So what good is all this?
- 

### Ultimately, we're going to set up these items:
|Service|Usage|
|--|--|
|Hosted Zone|Manages the DNS records forwarded to the AWS nameservers from the domain registrar for the domain.|
|Secure Sockets Layer (SSL) Certificate|Encrypts requests to secure data transers between the client and server.|
|Domain Name Server (DNS) A Record|Points requests for the domain to the elastic IP.|
|Elastic Load Balancer (ELB)|Analyzes the request and sends traffic to the correct target group.|
|Target Group|Describes which instances should be targeted by the load balancer for a particular request.|
|Amazon Elastic Compute Cloud (EC2) Instances|These are the actual server instances.|
