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

This will set up everything we need to secure application instances behind a VPC with a single bastion instance that is publicly accessible for outside access. 

### I. Create a Virtual Private Cloud (VPC)
>A [VPC](https://docs.amazonaws.cn/en_us/vpc/latest/userguide/what-is-amazon-vpc.html) is an isolated portion of the AWS cloud populated by AWS objects, such as Amazon EC2 instances. You must specify an IPv4 address range for your VPC. Specify the IPv4 address range as a Classless Inter-Domain Routing (CIDR) block; for example, 10.0.0.0/16. You cannot specify an IPv4 CIDR block larger than /16. You can optionally associate an Amazon-provided IPv6 CIDR block with the VPC.

If you don't already have a VPC for your system, follow this guide: https://github.com/jaylong255/aws-vpc-setup

### II. DNS Records
> I like to manage my DNS records with Route 53. Amazon [Route 53](https://aws.amazon.com/route53/) is a highly available and scalable cloud Domain Name System (DNS) web service.
1. From the Route 53 menu, click the "Create Hosted Zone" button.
2. Add the domain name and a comment if you want.
3. Click the "Create" button.
4. Click on your new hosted zone to enter it's menu.
5. Route 53 generates nameservers. Copy each of these over to your DNS records in your domain registrar, replacing the registrar's nameservers. This moves management of your domain over to AWS.

### III. SSL Certificate
> You can use LetsEncrypt if you want to. I like to let AWS handle the certificate if I'm going to be using their ELB service. It just makes everything so much quicker and easier. AWS [Certificate Manager](https://aws.amazon.com/certificate-manager/) is a service that lets you easily provision, manage, and deploy public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates for use with AWS services and your internal connected resources.
1. From the Certificate Manager menu, click the "Request a certificate" button. (I'm not covering how to import a certificate, but you can use a LetsEncrypt or other cert if you want to.)
2. Select "Request a public certificate" from the list and then click the button.
3. Enter your domain in the text field.
4. If you need to add subdomains, click the "Add another name..." button and add a wildcard. `*.mydomain.com`
5. Since you're using Route53, select "DNS Validation" to get your cert back quicker. Then click the "Review" button.
6. Review it and click 
7. Expand the details by clicking the arrow below the domain name. Copy everything before `.mydomain.com` in the name field.
8. Open up the Route53 menu in another tab and go to your hosted zone menu.
9. 

### IV. Create an Elastic Load Balancer
>[Elastic Load Balancing (ELB)](https://aws.amazon.com/elasticloadbalancing/) automatically distributes incoming application traffic across multiple targets, such as Amazon EC2 instances, containers, IP addresses, and Lambda functions. It can handle the varying load of your application traffic in a single Availability Zone or across multiple Availability Zones. Elastic Load Balancing offers three types of load balancers that all feature the high availability, automatic scaling, and robust security necessary to make your applications fault tolerant.
1. 

### V. Create a Target Group
>Each [target group](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html) is used to route requests to one or more registered targets. When you create each listener rule, you specify a target group and conditions. When a rule condition is met, traffic is forwarded to the corresponding target group. You can create different target groups for different types of requests. For example, create one target group for general requests and other target groups for requests to the microservices for your application. For more information, see Application Load Balancer Components.
1. 

### VI. Add Instances
> 
1.

### VII. DNS Records
>
1.
