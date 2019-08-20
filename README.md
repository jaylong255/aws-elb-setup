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
9. Click the "Create Record Set" button.
10. Paste the value of the name into the name field.
11. Now copy the entire "Value" field from the Certificate Manager page into the "Value" field in the record set form and click the "Create" button.
12. On the Certificate Manager page, click the "Continue" button. It may take a few minutes to validate. Just keep refreshing until it's ready. It shouldn't take long.

### IV. Create an Elastic Load Balancer
>[Elastic Load Balancing (ELB)](https://aws.amazon.com/elasticloadbalancing/) automatically distributes incoming application traffic across multiple targets, such as Amazon EC2 instances, containers, IP addresses, and Lambda functions. It can handle the varying load of your application traffic in a single Availability Zone or across multiple Availability Zones. Elastic Load Balancing offers three types of load balancers that all feature the high availability, automatic scaling, and robust security necessary to make your applications fault tolerant.
1. From the EC2 menu, click on the "Load Balancers" tab.
2. Click the "Create Load Balancer" button.
3. On the "Application Load Balancer" card, click the "Create" button.
4. Give your ELB a descriptive name.
5. Make it internet-facing, ipv4.
6. Select HTTPS protocol. Make sure the port is 443. Click the "Add Listener" button.
7. Add HTTP as well.
8. Select your VPC from the drop down.
9. Check the boxes beside your availability zones and select your public subnets from the drop down lists.
10. Click the "Next: Configure Security... " button.
11. Select "Choose a certificate from ACM".
12. Select your certificate from the drop down list.
13. Click the "Next: Configure Security Groups" button.
14. Select your VPC security group and click the "Next:..." button.

### V. Create a Target Group
>Each [target group](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html) is used to route requests to one or more registered targets. When you create each listener rule, you specify a target group and conditions. When a rule condition is met, traffic is forwarded to the corresponding target group. You can create different target groups for different types of requests. For example, create one target group for general requests and other target groups for requests to the microservices for your application. For more information, see Application Load Balancer Components.
1. Continuing from your ELB creation, name your application target group descriptively
2. Select "Instance"
3. Use HTTPS Protocol for the group and the health checks.
4. Click the "Next: Register Targets" button.
5. Go ahead and continue for now, we'll add instances in the next step.
6. Click the "Create" button.

### VI. Add Instances
> [Amazon Elastic Compute Cloud (EC2)](https://aws.amazon.com/ec2/) is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers.
1. Create an EC2 instance for the bastion host. Use the Amazon Linux 2 AMI. You can make it a micro, it's not going to serve public traffic. Select the VPC. Select a public subnet. Enable public IP.
2. Create an EC2 instance for the application. Use the same AMI. Use your own judgement as to what size to make this one. It's going to be handling public load, but the ELB will enable you to spread the load over multiple instances.
3. Select the VPC. Select a private subnet. Keep public IP disabled. All traffic will go through the ELB.
4. Install a web server stack on the application instance. You'll need to ssh into the bastion using it's public ip, then ssh into the application from the bastion using it's private ip.
5. Add the application instance to the target group.


### VII. DNS Records
>
1. Go to the menu for the new hosted zone in Route53.
2. Click on the A record (or add a new one, if there's not one already)
3. Point the A record to the Load Balancer.
4. Select "yes" for alias.
5. Save it.
6. Your domain should now point to the application instance. 
7. 

Next, you're going to want to set up deployments and autoscaling. But those are for another doc.


 
