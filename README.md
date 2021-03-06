# AWS solution architect associate certificate preparation study notes

core concept: Region, availability zone. global services (route 53, IAM, cloudfront, WAF).

## Availability Zone
An Availability Zone is represented by a region code followed by a letter identifier; for example, us-east-1a. To ensure that resources are distributed across the Availability Zones for a region, AWS maps Availability Zones to names for each AWS account. For example, the Availability Zone us-west-2a for one AWS account might not be the same location as us-west-2a for another AWS account.

To coordinate Availability Zones across accounts, you must use the AZ ID, which is a unique and consistent identifier for an Availability Zone. For example, usw2-az2 is an AZ ID for the us-west-2 region and it has the same location in every AWS account.

Viewing AZ IDs enables you to determine the location of resources in one account relative to the resources in another account. For example, if you share a subnet in the Availability Zone with the AZ ID usw2-az2 with another account, this subnet is available to that account in the Availability Zone whose AZ ID is also usw2-az2.

You can view the AZ IDs by going to the service health section of the EC2 Dashboard via your AWS Management Console.

## Compute

EC2- Elastic Compute Cloud = Infrastructure As a Service
* EC2
* EBS (Elastic Blck Storage)
* ELB (Elastic Load Balancing)
* ASG (Auto-scaling Group)

EC2 - OS includes Linux, Widnows, and Mac OS

EC2 User Data: Run only once at the instance firt start as root to automate boot tasks like installing updates/software...


### EC2 Instance Types
group by cpu, memory, storage and network.
A typical name has three parts: a letter describing the instance class (R, M, C, T, G, D, I, P, X), a number describing the generation (1, 2, 3, 4, 5), and a string describing the size within that instance class and generation (small, medium, large, xlarge, 2xlarge, 4xlarge, 8xlarge, 10xlarge, 16xlarge, 32xlarge).
* General Purpose – (T2, M5, M4, M3)
* Compute Optimized – (C4, C3)
* Memory Optimized – (X1, R4, R3)
* Accelerated Computing (P2, G4, G3, F1)
* Storage Optimized – (I3, D2)

ID|Purpose|Scenarios
------------ | ------------- | ----------
1  | General purpose instances | Application Server, Gaming Servers,backend servers,small and medium databases
2  | Compute optimized instances |  ideal for high-performance web servers, compute-intensive applications servers, and dedicated gaming servers. batch processing
3  | Memory optimized instances |  high-performance database or a workload that involves performing real-time processing of a large amount of unstructured data. 
4 | Accelerated computing instances | use hardware accelerators, or coprocessors, to perform some functions more efficiently than is possible in software running on CPUs. Examples of these functions include floating-point number calculations, graphics processing, and data pattern matching.
5 | Storage Optimzied instances |  are designed for workloads that require high, sequential read and write access to large datasets on local storage. i.e OLTP high IOPS 

### Instance metadata and user data
Instance metadata is data about your instance that you can use to configure or manage the running instance. Instance metadata is divided into categories, for example, host name, events, and security groups.

You can also use instance metadata to access user data that you specified when launching your instance. For example, you can specify parameters for configuring your instance, or include a simple script. You can build generic AMIs and use user data to modify the configuration files supplied at launch time. For example, if you run web servers for various small businesses, they can all use the same generic AMI and retrieve their content from the Amazon S3 bucket that you specify in the user data at launch. 

EC2 instances can also include dynamic data, such as an instance identity document that is generated when the instance is launched.

Although you can only access instance metadata and user data from within the instance itself, the data is not protected by authentication or cryptographic methods. Anyone who has direct access to the instance, and potentially any software running on the instance, can view its metadata. Therefore, you should not store sensitive data, such as passwords or long-lived encryption keys, as user data.

### Using IAM Roles with Amazon EC2 Instances
![image](https://user-images.githubusercontent.com/85909185/132144216-9010ac15-d26a-4ff3-a111-624f243e4655.png)
Trusty --- which component/service can use the credential . usually EC2 instances as our code runs on it.
Permission --- what actions can the trusted/selected service do. like read file from S3.
![image](https://user-images.githubusercontent.com/85909185/132144287-a0c12417-a003-4594-b211-d8c13d8b8a63.png)

then we can launch EC2 instance with the IAM role
![image](https://user-images.githubusercontent.com/85909185/132144335-06c6966f-d506-49c0-ac91-59c30d228109.png)

then in application, the harcoded credential can be removed and then AWS S3 SDK can automatically query meta-data to get temporary credential before call.

## root account
below tasks can be performed by root account only
* change account name or root password or root email address
* change AWS support plan, close AWS account
* enable MFA on S3 bucket delete
* create Cloudfront key pair, register for GovCloud.

### EC2  Pricing
ID|Type|Scenarios
------ | ------- | ----------
1 | On demand | On-Demand Instances are ideal for short-term, irregular workloads that cannot be interrupted. No upfront costs or minimum contracts apply. The instances run continuously until you stop them, and you pay for only the compute time you use.On-Demand Instances are not recommended for workloads that last a year or longer because these workloads can experience greater cost savings using Reserved Instances.
2 | EC2 saving plans |  enable you to reduce your compute costs by committing to a consistent amount of compute usage for a 1-year or 3-year term. This term commitment results in savings of up to 66% over On-Demand costs.Any usage up to the commitment is charged at the discounted plan rate (for example, $10 an hour). Any usage beyond the commitment is charged at regular On-Demand rates.
3 | Reserved instances | Reserved Instances are a billing discount applied to the use of On-Demand Instances in your account. You can purchase Standard Reserved and Convertible Reserved Instances for a 1-year or 3-year term, and Scheduled Reserved Instances for a 1-year term. You realize greater cost savings with the 3-year option.
4 | Spot instances | Spot Instances are ideal for workloads with flexible start and end times, or that can withstand interruptions. Spot Instances use unused Amazon EC2 computing capacity and offer you cost savings at up to 90% off of On-Demand prices.
5 | Dedicated hosts | Dedicated Hosts are physical servers with Amazon EC2 instance capacity that is fully dedicated to your use. 

![image](https://user-images.githubusercontent.com/85909185/132094414-d26b75af-5c09-4675-b53a-2a42d88947f0.png)


### EC2 Scalability
Scalability involves beginning with only the resources you need and designing your architecture to automatically respond to changing demand by scaling out or in. As a result, you pay for only the resources you use. You don’t have to worry about a lack of computing capacity to meet your customers’ needs.
Amazon EC2 Auto Scaling.Amazon EC2 Auto Scaling enables you to automatically add or remove Amazon EC2 instances in response to changing application demand. By automatically scaling your instances in and out as needed, you are able to maintain a greater sense of application availability.

Within Amazon EC2 Auto Scaling, you can use two approaches: dynamic scaling and predictive scaling.

* Dynamic scaling responds to changing demand. 
* Predictive scaling automatically schedules the right number of Amazon EC2 instances based on predicted demand.
![image](https://user-images.githubusercontent.com/85909185/129497947-1b7e565e-eccc-4ede-ab1a-01e943290e66.png)

### Elastic Load Balancing
Elastic Load Balancing is the AWS service that automatically distributes incoming application traffic across multiple resources, such as Amazon EC2 instances.
ELB can work with auto scaling to route traffic dynamically to available instances. 
* Class Load balancer
* Network Load balancer (TCP)
* Application Load Balancer (HTTP)

By default, cross-zone load balancing is enabled for Application Load Balancer and disabled for Network Load Balancer

### Security Groups
security groups are the fundational of network security in AWS as they control how traffic is allowd into or out of EC2 instances.
securiy groups only contain allow rules
security groups rules can reference by IP or by security group
it actualy acts as 'firewall' on EC2 instances. the regulate:
access to ports
authorized IP ranges (both IPv4 and 6)
control of outbound network
* can be attached to multiple instances
* locked down to a region/vpc combination
* does 'live' outside the EC2, if traffic is blocked, instance won't see it.
* if your application is not accessible (time out), it's security group issue
* all inbound traffic is blocked by default 
* all outbound traffic is authorised by default 

A security group acts as a virtual firewall that controls the traffic for one or more instances. When you launch an instance, you can specify one or more security groups; otherwise, we use the default security group. You can add rules to each security group that allows traffic to or from its associated instances. You can modify the rules for a security group at any time; the new rules are automatically applied to all instances that are associated with the security group. When we decide whether to allow traffic to reach an instance, we evaluate all the rules from all the security groups that are associated with the instance. The following are the characteristics of security group rules: By default, security groups allow all outbound traffic. Security group rules are always permissive; you can't create rules that deny access. Security groups are stateful

Security Groups are stateful, so allowing inbound traffic to the necessary ports enables the connection. Network ACLs are stateless, so you must allow both inbound and outbound traffic

### Using an IAM role to grant permissions to applications running on Amazon EC2 instances
Applications that run on an EC2 instance must include AWS credentials in their AWS API requests. You could have your developers store AWS credentials directly within the EC2 instance and allow applications in that instance to use those credentials. But developers would then have to manage the credentials and ensure that they securely pass the credentials to each instance and update each EC2 instance when it's time to rotate the credentials. That's a lot of additional work.

Instead, you can and should use an IAM role to manage temporary credentials for applications that run on an EC2 instance. When you use a role, you don't have to distribute long-term credentials (such as a user name and password or access keys) to an EC2 instance. Instead, the role supplies temporary permissions that applications can use when they make calls to other AWS resources. When you launch an EC2 instance, you specify an IAM role to associate with the instance. Applications that run on the instance can then use the role-supplied temporary credentials to sign API requests.

Using roles to grant permissions to applications that run on EC2 instances requires a bit of extra configuration. An application running on an EC2 instance is abstracted from AWS by the virtualized operating system. Because of this extra separation, an additional step is needed to assign an AWS role and its associated permissions to an EC2 instance and make them available to its applications. This extra step is the creation of an instance profile that is attached to the instance. The instance profile contains the role and can provide the role's temporary credentials to an application that runs on the instance. Those temporary credentials can then be used in the application's API calls to access resources and to limit access to only those resources that the role specifies. Note that only one role can be assigned to an EC2 instance at a time, and all applications on the instance share the same role and permissions.

Using roles in this way has several benefits. Because role credentials are temporary and rotated automatically, you don't have to manage credentials, and you don't have to worry about long-term security risks. In addition, if you use a single role for multiple instances, you can make a change to that one role and the change is propagated automatically to all the instances.

![image](https://user-images.githubusercontent.com/85909185/132126818-01e37e6a-4b1b-450c-becc-ea3f7479a5c0.png)


### IP addresses
private IP: Machines connect to WWW using a NAT + internet gateway (a proxy)
by default, When you stop and then start an EC2 instance, it can change its public IP . use elastic IP if you want to have a fixed public IP which can only be attache to one instance at a time.
With an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account.
You can only have 5 Elastic IP in your account (you can ask AWS to increase that).
Overall, try to avoid using Elastic IP:
• They often reflect poor architectural decisions
• Instead, use a random public IP and register a DNS name to it
• Or, as we’ll see later, use a Load Balancer and don’t use a public IP

by default, EC2 instance comes with a private ip and public ip. and if machine restarted, public IP can change.

### Retrieve instance metadata
To view all categories of instance metadata from within a running instance, use the following IPv4 or IPv6 URIs:

http://169.254.169.254/latest/meta-data/
http://[fd00:ec2::254]/latest/meta-data/

### Placement group
Sometimes you want control over the EC2 Instance placement strategy.
* Cluster—clusters instances into a low-latency group in a single Availability Zone  --on the same Rack, great network 10G, big data job, low latency and high network throughput.
* Spread—spreads instances across underlying hardware (max 7 instances per group per AZ)  --instances on different hardware, can across AZ and region, maximize high availability. 
* Partition—spreads instances across many different partitions (which rely on different sets of racks) within an AZ. Scales to 100s of EC2 instances per group (Hadoop, Cassandra, Kafka)  -up to 7 partitions per AZ, can span across AZ in the same region. up to 100 instances.instances in the same partition does not share racks. HDFS, HBase, Cassandra,Kafka

Partition placement groups - Partition placement groups help reduce the likelihood of correlated hardware failures for your application. When using partition placement groups, Amazon EC2 divides each group into logical segments called partitions. Amazon EC2 ensures that each partition within a placement group has its own set of racks. Each rack has its own network and power source. No two partitions within a placement group share the same racks, allowing you to isolate the impact of a hardware failure within your application.

The following image is a simple visual representation of a partition placement group in a single Availability Zone. It shows instances that are placed into a partition placement group with three partitions—Partition 1, Partition 2, and Partition 3. Each partition comprises multiple instances. The instances in a partition do not share racks with the instances in the other partitions, allowing you to contain the impact of a single hardware failure to only the associated partition.

Partition placement groups can be used to deploy large distributed and replicated workloads, such as HDFS, HBase, and Cassandra, across distinct racks. When you launch instances into a partition placement group, Amazon EC2 tries to distribute the instances evenly across the number of partitions that you specify. You can also launch instances into a specific partition to have more control over where the instances are placed.

A partition placement group can have partitions in multiple Availability Zones in the same Region. A partition placement group can have a maximum of seven partitions per Availability Zone. The number of instances that can be launched into a partition placement group is limited only by the limits of your account.

Partition placement groups:  via - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#placement-groups-partition

Spread placement groups - A spread placement group is a group of instances that are each placed on distinct racks, with each rack having its own network and power source. Spread placement groups are recommended for applications that have a small number of critical instances that should be kept separate from each other. Launching instances in a spread placement group reduces the risk of simultaneous failures that might occur when instances share the same racks. Spread placement groups provide access to distinct racks, and are therefore suitable for mixing instance types or launching instances over time. As the use-case talks about running large distributed and replicated workloads, so it needs more instances, therefore this option is not the right fit for the given use-case.

A spread placement group can span multiple Availability Zones in the same Region. You can have a maximum of seven running instances per Availability Zone per group.

### Elastic Network Interface (ENI)
Logical component in a VPC that represents a virtual network card
The ENI can have the following attributes:
* Primary private Ipv4 , one or more secondary IPv4.
* One or more security groups
* One public IPv4
* A MAC address
* Bound to a specific AZ
* can attach them on the fly (move them) 

### EC2 Hiberate
Stop: the data on disk (EBS) is kept intact in the next start
Terminate: any EBS volumes (root) also set-up to be destroyed is lost

On Start:
First start: the OS boots & the EC2 User Data script is run
Following starts: the OS boots up
Then your application starts, caches get warmed up, and that can take time!

During Hiberate:
* The in-memory (RAM) state is preserved
* The instance boot is much faster!(the OS is not stopped / restarted)
* Under the hood: the RAM state is written to a file in the root EBS volume which must be encrypted
* Root volume: must be EBS, encrypted, not instance store, and large
* An instance cannot be hiberated more than 60 days.

When you hibernate an instance, AWS signals the operating system to perform hibernation (suspend-to-disk). Hibernation saves the contents from the instance memory (RAM) to your Amazon EBS root volume. AWS then persists the instance's Amazon EBS root volume and any attached Amazon EBS data volumes. When you start your instance: The Amazon EBS root volume is restored to its previous state The RAM contents are reloaded The processes that were previously running on the instance are resumed Previously attached data volumes are reattached and the instance retains its instance ID

### AMI Overview
* built for specific region   (can be copied across regions)
* EC2 instance can be launched from : 
** public AMI
** Your own AMI
** An AWS Marketplace AMI

### Standby
You can put an instance that is in the InService state into the Standby state, update or troubleshoot the instance, and then return the instance to service. Instances that are on standby are still part of the Auto Scaling group, but they do not actively handle load balancer traffic.
This feature helps you stop and start the instances or reboot them without worrying about Amazon EC2 Auto Scaling terminating the instances as part of its health checks or during scale-in events.
Amazon EC2 Auto Scaling does not perform health checks on instances that are in a standby state. While the instance is in a standby state, its health status reflects the status that it had before you put it on standby. Amazon EC2 Auto Scaling does not perform a health check on the instance until you put it back in service.

### Auto Scaling Group
The ReplaceUnhealthy process terminates instances that are marked as unhealthy and then creates new instances to replace them. Amazon EC2 Auto Scaling stops replacing instances that are marked as unhealthy.
The scheduled action tells the Amazon EC2 Auto Scaling group to perform a scaling action at specified times. To create a scheduled scaling action, you specify the start time when the scaling action should take effect, and the new minimum, maximum, and desired sizes for the scaling action.
An Auto Scaling group contains a collection of Amazon EC2 instances that are treated as a logical grouping for the purposes of automatic scaling and management. An Auto Scaling group also enables you to use Amazon EC2 Auto Scaling features such as health check replacements and scaling policies.

With target tracking scaling policies, you select a scaling metric and set a target value. Amazon EC2 Auto Scaling creates and manages the CloudWatch alarms that trigger the scaling policy and calculates the scaling adjustment based on the metric and the target value. The scaling policy adds or removes capacity as required to keep the metric at, or close to, the specified target value.

For example, you can use target tracking scaling to:
![image](https://user-images.githubusercontent.com/85909185/131781081-9e3362c6-5e72-4325-b893-4bb6ff03bb41.png)


Configure a target tracking scaling policy to keep the average aggregate CPU utilization of your Auto Scaling group at 50 percent. This meets the requirements specified in the given use-case and therefore, this is the correct option.

#### default termination policy
It selects the Availability Zone with two instances, and terminates the instance that was launched from the oldest launch template or launch configuration. If the instances were launched from the same launch template or launch configuration, Amazon EC2 Auto Scaling selects the instance that is closest to the next billing hour and terminates it.

#### Dynamic scaling policy types
*Target tracking scaling—Increase or decrease the current capacity of the group based on a target value for a specific metric. This is similar to the way that your thermostat maintains the temperature of your home—you select a temperature and the thermostat does the rest.
*Step scaling—Increase or decrease the current capacity of the group based on a set of scaling adjustments, known as step adjustments, that vary based on the size of the alarm breach.
*Simple scaling—Increase or decrease the current capacity of the group based on a single scaling adjustment.

#### auto scaling termination policy
Per the default termination policy, the first priority is given to any allocation strategy for On-Demand vs Spot instances. As no such information has been provided for the given use-case, so this criterion can be ignored. The next priority is to consider any instance with the oldest launch template unless there is an instance that uses a launch configuration. So this rules out Instance A. Next, you need to consider any instance which has the oldest launch configuration. This implies Instance B will be selected for termination and Instance C will also be ruled out as it has the newest launch configuration. Instance D, which is closest to the next billing hour, is not selected as this criterion is last in the order of priority.

On-demand /spot instances > oldest launch template /oldest launch configuration > next billing hour
![image](https://user-images.githubusercontent.com/85909185/132090470-4b60ee8c-3a61-40c8-ade2-3798be934b58.png)

#### instance scale-in protection
To control whether an Auto Scaling group can terminate a particular instance when scaling in, use instance scale-in protection. You can enable the instance scale-in protection setting on an Auto Scaling group or on an individual Auto Scaling instance. When the Auto Scaling group launches an instance, it inherits the instance scale-in protection setting of the Auto Scaling group. You can change the instance scale-in protection setting for an Auto Scaling group or an Auto Scaling instance at any time.
If all instances in an Auto Scaling group are protected from termination during scale in, and a scale-in event occurs, its desired capacity is decremented. However, the Auto Scaling group can't terminate the required number of instances until their instance scale-in protection settings are disabled.
Instance scale-in protection does not protect Auto Scaling instances from the following:
* Manual termination through the Amazon EC2 console, the terminate-instances command, or the TerminateInstances action. To protect Auto Scaling instances from manual termination, enable Amazon EC2 termination protection.
* Health check replacement if the instance fails health checks. For more information, see Health checks for Auto Scaling instances. To prevent Amazon EC2 Auto Scaling from terminating unhealthy instances, suspend the ReplaceUnhealthy process. 
* Spot Instance interruptions. A Spot Instance is terminated when capacity is no longer available or the Spot price exceeds your maximum price.



### Application Load Balancer ALB
![image](https://user-images.githubusercontent.com/85909185/131936223-1d7729e2-287c-40c0-ab50-7831299b0251.png)
You can't specify publicly routable IP addresses as values for IP target type, so both these options are incorrect.

![image](https://user-images.githubusercontent.com/85909185/132146172-9e0a96e2-70bd-4b80-b722-ac184369b2c1.png)

### NAT gateway vs NAT instance
![image](https://user-images.githubusercontent.com/85909185/132929543-092ccf08-16ec-4953-8b05-8de255a668f9.png)

### Rebalancing activities
Rebalancing activities fall into two categories: Availability Zone rebalancing and capacity rebalancing.
#### Availability Zone rebalancing
After certain actions occur, your Auto Scaling group can become unbalanced between Availability Zones. Amazon EC2 Auto Scaling compensates by rebalancing the Availability Zones. The following actions can lead to rebalancing activity:
You change the Availability Zones for your group.
You explicitly terminate or detach instances and the group becomes unbalanced.
An Availability Zone that previously had insufficient capacity recovers and has additional capacity available.
An Availability Zone that previously had a Spot price above your maximum price now has a Spot price below your maximum price.
When rebalancing, Amazon EC2 Auto Scaling launches new instances before terminating the old ones, so that rebalancing does not compromise the performance or availability of your application.
Because Amazon EC2 Auto Scaling attempts to launch new instances before terminating the old ones, being at or near the specified maximum capacity could impede or completely halt rebalancing activities. To avoid this problem, the system can temporarily exceed the specified maximum capacity of a group by a 10 percent margin (or by a 1-instance margin, whichever is greater) during a rebalancing activity. The margin is extended only if the group is at or near maximum capacity and needs rebalancing, either because of user-requested rezoning or to compensate for zone availability issues. The extension lasts only as long as needed to rebalance the group (typically a few minutes).

#### Capacity Rebalancing
You can enable Capacity Rebalancing for your Auto Scaling groups when using Spot Instances. When you turn on Capacity Rebalancing, Amazon EC2 Auto Scaling attempts to launch a Spot Instance whenever Amazon EC2 notifies that a Spot Instance is at an elevated risk of interruption. After launching a new instance, it then terminates an old instance. 

Auto Scaling creates a new scaling activity for terminating the unhealthy instance and then terminates it. Later, another scaling activity launches a new instance to replace the terminated instance.



## Network
### Global Accelerator
AWS Global Accelerator - AWS Global Accelerator utilizes the Amazon global network, allowing you to improve the performance of your applications by lowering first-byte latency (the round trip time for a packet to go from a client to your endpoint and back again) and jitter (the variation of latency), and increasing throughput (the amount of time it takes to transfer data) as compared to the public internet.
AWS Global Accelerator optimizes the path to your application to keep packet loss, jitter, and latency consistently low.

With Global Accelerator, you are provided two global static public IPs that act as a fixed entry point to your application, improving availability. On the back end, add or remove your AWS application endpoints, such as Application Load Balancers, Network Load Balancers, EC2 Instances, and Elastic IPs without making user-facing changes. Global Accelerator automatically re-routes your traffic to your nearest healthy available endpoint to mitigate endpoint failure.

Global Accelerator improves performance for a wide range of applications over TCP or UDP by proxying packets at the edge to applications running in one or more AWS Regions. Global Accelerator is a good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP, as well as for HTTP use cases that specifically require static IP addresses or deterministic, fast regional failover.

while ELB provides load balancing within one Region, AWS Global Accelerator provides traffic management across multiple Regions.

can be used to provide single entry points for multipe region /applications.
![image](https://user-images.githubusercontent.com/85909185/132972233-1bf9ad8c-438e-4f4c-aba9-0f73ea11852c.png)


###Cloudfront
Amazon CloudFront - Amazon CloudFront is a fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency, high transfer speeds, all within a developer-friendly environment.

AWS Global Accelerator and Amazon CloudFront are separate services that use the AWS global network and its edge locations around the world. CloudFront improves performance for both cacheable content (such as images and videos) and dynamic content (such as API acceleration and dynamic site delivery), while Global Accelerator improves performance for a wide range of applications over TCP or UDP.

dynamic content determined at request time and proxy methods send to origin directly.

![image](https://user-images.githubusercontent.com/85909185/131621889-0717e582-3ec7-423f-ba64-e89682870987.png)

Field-level encryption allows you to enable your users to securely upload sensitive information to your web servers. The sensitive information provided by your users is encrypted at the edge, close to the user, and remains encrypted throughout your entire application stack. This encryption ensures that only applications that need the data—and have the credentials to decrypt it—are able to do so.

To use field-level encryption, when you configure your CloudFront distribution, specify the set of fields in POST requests that you want to be encrypted, and the public key to use to encrypt them. You can encrypt up to 10 data fields in a request. (You can’t encrypt all of the data in a request with field-level encryption; you must specify individual fields to encrypt.)

#### Configuring secure access and restricting access to content
* Configure HTTPS connections
* Prevent users in specific geographic locations from accessing content
* Require users to access content using CloudFront signed URLs or signed cookies
* Set up field-level encryption for specific content fields
* Use AWS WAF to control access to your content

Require that your users access your private content by using special CloudFront signed URLs or signed cookies.

Require that your users access your content by using CloudFront URLs, not URLs that access content directly on the origin server (for example, Amazon S3 or a private HTTP server). Requiring CloudFront URLs isn't necessary, but we recommend it to prevent users from bypassing the restrictions that you specify in signed URLs or signed cookies.

Restricting access to files in Amazon S3 buckets
You can optionally secure the content in your Amazon S3 bucket so that users can access it through CloudFront but cannot access it directly by using Amazon S3 URLs. This prevents someone from bypassing CloudFront and using the Amazon S3 URL to get content that you want to restrict access to. This step isn't required to use signed URLs, but we recommend it.

To require that users access your content through CloudFront URLs, you do the following tasks:

Create a special CloudFront user called an origin access identity and associate it with your CloudFront distribution.

Give the origin access identity permission to read the files in your bucket.

Remove permission for anyone else to use Amazon S3 URLs to read the files.

To restrict access to content that you serve from Amazon S3 buckets, follow these steps:

Create a special CloudFront user called an origin access identity (OAI) and associate it with your distribution.

Configure your S3 bucket permissions so that CloudFront can use the OAI to access the files in your bucket and serve them to your users. Make sure that users can’t use a direct URL to the S3 bucket to access a file there.



###Route 53
Amazon Route 53 is a highly available and scalable Domain Name System (DNS) web service. You can use Route 53 to perform three main functions in any combination: domain registration, DNS routing, and health checking.
#### Register domain names
Your website needs a name, such as example.com. Route 53 lets you register a name for your website or web application, known as a domain name.
#### Route internet traffic to the resources for your domain
When a user opens a web browser and enters your domain name (example.com) or subdomain name (acme.example.com) in the address bar, Route 53 helps connect the browser with your website or web application.
#### Check the health of your resources
Route 53 sends automated requests over the internet to a resource, such as a web server, to verify that it's reachable, available, and functional. You also can choose to receive notifications when a resource becomes unavailable and choose to route internet traffic away from unhealthy resources.


Amazon Route 53 - Amazon Route 53 is a highly available and scalable cloud Domain Name System (DNS) web service. It is designed to give developers and businesses an extremely reliable and cost-effective way to route end users to Internet applications by translating names like www.example.com into the numeric IP addresses like 192.0.2.1 that computers use to connect to each other.
Use Route 53 based geolocation routing policy to restrict distribution of content to only the locations in which you have distribution rights
Geolocation routing lets you choose the resources that serve your traffic based on the geographic location of your users, meaning the location that DNS queries originate from. For example, you might want all queries from Europe to be routed to an ELB load balancer in the Frankfurt region. You can also use geolocation routing to restrict the distribution of content to only the locations in which you have distribution rights.
TLD is the abbreviation for Top Level Domain
![image](https://user-images.githubusercontent.com/85909185/131756367-f7021678-2ea3-43b8-806b-a0d548904722.png)

#### How Amazon Route 53 routes traffic for your domain
After you configure Amazon Route 53 to route your internet traffic to your resources, such as web servers or Amazon S3 buckets, here's what happens in just a few milliseconds when someone requests content for www.example.com:

![image](https://user-images.githubusercontent.com/85909185/132937582-6ddc695c-40cf-4774-9078-1c2cb2127516.png)

#### Choosing a routing policy
When you create a record, you choose a routing policy, which determines how Amazon Route 53 responds to queries:

*Simple routing policy – Use for a single resource that performs a given function for your domain, for example, a web server that serves content for the example.com website.
*Failover routing policy – Use when you want to configure active-passive failover.
*Geolocation routing policy – Use when you want to route traffic based on the location of your users.
*Geoproximity routing policy – Use when you want to route traffic based on the location of your resources and, optionally, shift traffic from resources in one location to resources in another.You can also optionally choose to route more traffic or less to a given resource by specifying a value, known as a bias. A bias expands or shrinks the size of the geographic region from which traffic is routed to a resource.
*Latency routing policy – Use when you have resources in multiple AWS Regions and you want to route traffic to the Region that provides the best latency with less round-trip time.
*Multivalue answer routing policy – Use when you want Route 53 to respond to DNS queries with up to eight healthy records selected at random.
*Weighted routing policy – Use to route traffic to multiple resources in proportions that you specify.

#### Comparison of alias and CNAME records
Alias records are similar to CNAME records, but there are some important differences. The following list compares alias records and CNAME records.

Resources that you can redirect queries to
Alias records
An alias record can only redirect queries to selected AWS resources, such as the following:

Amazon S3 buckets

CloudFront distributions

Another record in the same Route 53 hosted zone

For example, you can create an alias record named acme.example.com that redirects queries to an Amazon S3 bucket that is also named acme.example.com. You can also create an acme.example.com alias record that redirects queries to a record named zenith.example.com in the example.com hosted zone.

CNAME records
A CNAME record can redirect DNS queries to any DNS record. For example, you can create a CNAME record that redirects queries from acme.example.com to zenith.example.com or to acme.example.org. You don't need to use Route 53 as the DNS service for the domain that you're redirecting queries to.

Creating records that have the same name as the domain (records at the zone apex)
Alias records
In most configurations, you can create an alias record that has the same name as the hosted zone (the zone apex). The one exception is when you want to redirect queries from the zone apex (such as example.com) to a record in the same hosted zone that has a type of CNAME (such as zenith.example.com). The alias record must have the same type as the record you're routing traffic to, and creating a CNAME record for the zone apex isn't supported even for an alias record.

CNAME records
You can't create a CNAME record that has the same name as the hosted zone (the zone apex). This is true both for hosted zones for domain names (example.com) and for hosted zones for subdomains (zenith.example.com).

Pricing for DNS queries
Alias records
Route 53 doesn't charge for alias queries to AWS resources. For more information, see Amazon Route 53 Pricing.

CNAME records
Route 53 charges for CNAME queries.

Note
If you create a CNAME record that redirects to the name of another record in a Route 53 hosted zone (the same hosted zone or another hosted zone), each DNS query is charged as two queries:

Route 53 responds to the first DNS query with the name of the record that you want to redirect to.

Then the DNS resolver must submit another query for the record in the first response to get information about where to direct traffic, for example, the IP address of a web server.

If the CNAME record redirects to the name of a record that is hosted with another DNS service, Route 53 charges for one query. The other DNS service might charge for the second query.

Record type specified in the DNS query
Alias records
Route 53 responds to a DNS query only when the name of the alias record (such as acme.example.com) and the type of the alias record (such as A or AAAA) match the name and type in the DNS query.

CNAME records
A CNAME record redirects DNS queries for a record name regardless of the record type specified in the DNS query, such as A or AAAA.

How records are listed in dig or nslookup queries
Alias records
In the response to a dig or nslookup query, an alias record is listed as the record type that you specified when you created the record, such as A or AAAA. (The record type that you specify for an alias record depends on the resource that you're routing traffic to. For example, to route traffic to an S3 bucket, you specify A for the type.) The alias property is visible only in the Route 53 console or in the response to a programmatic request, such as an AWS CLI list-resource-record-sets command.

CNAME records
A CNAME record is listed as a CNAME record in response to dig or nslookup queries.

### records 
* A CNAME record maps DNS queries for the name of the current record, such as acme.example.com, to another domain (example.com or example.net) or subdomain (acme.example.com or zenith.example.org).
* A record - Used to point a domain or subdomain to an IP address. 'A record' cannot be used to map one domain name to another.
* PTR record - A Pointer (PTR) record resolves an IP address to a fully-qualified domain name (FQDN) as an opposite to what A record does. PTR records are also called Reverse DNS records. 'PTR record' cannot be used to map one domain name to another.
* Alias Record - Alias records let you route traffic to selected AWS resources, such as CloudFront distributions and Amazon S3 buckets. They also let you route traffic from one record in a hosted zone to another record. 3rd party websites do not qualify for these as we have no control over those. 'Alias record' cannot be used to map one domain name to another.

### Single Region Mutil-VPC Connectivity
![image](https://user-images.githubusercontent.com/85909185/132146506-267b125d-b4fc-472a-8f92-2a0e42ef4233.png)
![image](https://user-images.githubusercontent.com/85909185/132146538-0c552a85-bc3b-4653-89bf-bd30c7c01d70.png)
![image](https://user-images.githubusercontent.com/85909185/132146588-a5a3241b-43da-4065-aaad-0a31cca1d77d.png)

![image](https://user-images.githubusercontent.com/85909185/132146542-3eb75029-3d3e-413a-9a56-b7184b52fbf4.png)
![image](https://user-images.githubusercontent.com/85909185/132146562-e37fc4ec-7a4a-4087-8a19-447b16aa918e.png)
![image](https://user-images.githubusercontent.com/85909185/132146569-690e8092-a2fe-44a8-91fb-5e275a0521ee.png)

![image](https://user-images.githubusercontent.com/85909185/132146624-06e2ab78-004e-4530-9410-03e36c53ef7f.png)

![image](https://user-images.githubusercontent.com/85909185/132146658-e739a4b0-56a3-486f-80ae-696366e6c499.png)

### Multi Regions Mutil-VPC Connectivity
![image](https://user-images.githubusercontent.com/85909185/132146719-17b1794a-a787-4f69-8af7-a9199cdf38f1.png)
![image](https://user-images.githubusercontent.com/85909185/132146737-82de8492-c5d6-42fd-96c6-6652e3c1b393.png)


### Transit gateway
AWS Transit Gateway is a service that enables customers to connect their Amazon Virtual Private Clouds (VPCs) and their on-premises networks to a single gateway. With AWS Transit Gateway, you only have to create and manage a single connection from the central gateway into each Amazon VPC, on-premises data center, or remote office across your network. Transit Gateway acts as a hub that controls how traffic is routed among all the connected networks which act like spokes. So, this is a perfect use-case for the Transit Gateway.
![image](https://user-images.githubusercontent.com/85909185/132094525-620dbbd7-4e35-4a77-920e-852cdd72852a.png)

### VPC Peering 
- A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them using private IPv4 addresses or IPv6 addresses. Instances in either VPC can communicate with each other as if they are within the same network. You can create a VPC peering connection between your VPCs, or with a VPC in another AWS account. The VPCs can be in different regions (also known as an inter-region VPC peering connection). VPC Peering helps connect two VPCs and is not transitive. It would require to create many peering connections between all the VPCs to have them connect. This alone wouldn't work, because we would need to also connect the on-premises data center through Direct Connect and Direct Connect Gateway, but that's not mentioned in this answer.

### VPN Gateway
A virtual private gateway (also known as a VPN Gateway) is the endpoint on the VPC side of your VPN connection. You can create a virtual private gateway before creating the VPC itself. VPN Gateway is a distractor here because we haven't mentioned a VPN.

### Private Link 
- AWS PrivateLink simplifies the security of data shared with cloud-based applications by eliminating the exposure of data to the public Internet. AWS PrivateLink provides private connectivity between VPCs, AWS services, and on-premises applications, securely on the Amazon network. Private Link is utilized to create a private connection between an application that is fronted by an NLB in an account, and an Elastic Network Interface (ENI) in another account, without the need of VPC peering, and allowing the connections between the two to remain within the AWS network.

### VPC endpoint
A VPC endpoint allows you to privately connect your VPC to supported AWS services without requiring an Internet gateway, NAT device, VPN connection, or AWS Direct Connect connection. Endpoints are virtual devices that are horizontally scaled, redundant, and highly available VPC components. They allow communication between instances in your VPC and services without imposing availability risks or bandwidth constraints on your network traffic.

VPC endpoints enable you to reduce data transfer charges resulting from network communication between private VPC resources (such as Amazon Elastic Cloud Compute—or EC2—instances) and AWS Services (such as Amazon Quantum Ledger Database, or QLDB). Without VPC endpoints configured, communications that originate from within a VPC destined for public AWS services must egress AWS to the public Internet in order to access AWS services. This network path incurs outbound data transfer charges. Data transfer charges for traffic egressing from Amazon EC2 to the Internet vary based on volume. With VPC endpoints configured, communication between your VPC and the associated AWS service does not leave the Amazon network. If your workload requires you to transfer significant volumes of data between your VPC and AWS, you can reduce costs by leveraging VPC endpoints.


### VPN CloudHub
If you have multiple AWS Site-to-Site VPN connections, you can provide secure communication between sites using the AWS VPN CloudHub. This enables your remote sites to communicate with each other, and not just with the VPC. Sites that use AWS Direct Connect connections to the virtual private gateway can also be part of the AWS VPN CloudHub. The VPN CloudHub operates on a simple hub-and-spoke model that you can use with or without a VPC. This design is suitable if you have multiple branch offices and existing internet connections and would like to implement a convenient, potentially low-cost hub-and-spoke model for primary or backup connectivity between these remote offices.
![image](https://user-images.githubusercontent.com/85909185/132929742-85f91247-0184-4008-b5ff-711396879fae.png)

### Internet Gateway
An Internet Gateway is a horizontally scaled, redundant, and highly available VPC component that allows communication between your VPC and the internet.

An Internet Gateway serves two purposes: to provide a target in your VPC route tables for internet-routable traffic and to perform network address translation (NAT) for instances that have been assigned public IPv4 addresses. 

#### Enable internet access
To enable access to or from the internet for instances in a subnet in a VPC, you must do the following.
*Create an internet gateway and attach it to your VPC.
*Add a route to your subnet's route table that directs internet-bound traffic to the internet gateway.
*Ensure that instances in your subnet have a globally unique IP address (public IPv4 address, Elastic IP address, or IPv6 address).
*Ensure that your network access control lists and security group rules allow the relevant traffic to flow to and from your instance.

#### Public and private subnets
If a subnet is associated with a route table that has a route to an internet gateway, it's known as a public subnet. If a subnet is associated with a route table that does not have a route to an internet gateway, it's known as a private subnet.

In your public subnet's route table, you can specify a route for the internet gateway to all destinations not explicitly known to the route table (0.0.0.0/0 for IPv4 or ::/0 for IPv6). Alternatively, you can scope the route to a narrower range of IP addresses; for example, the public IPv4 addresses of your company’s public endpoints outside of AWS, or the Elastic IP addresses of other Amazon EC2 instances outside your VPC.

#### IP addresses and NAT

To enable communication over the internet for IPv4, your instance must have a public IPv4 address or an Elastic IP address that's associated with a private IPv4 address on your instance. Your instance is only aware of the private (internal) IP address space defined within the VPC and subnet. The internet gateway logically provides the one-to-one NAT on behalf of your instance, so that when traffic leaves your VPC subnet and goes to the internet, the reply address field is set to the public IPv4 address or Elastic IP address of your instance, and not its private IP address. Conversely, traffic that's destined for the public IPv4 address or Elastic IP address of your instance has its destination address translated into the instance's private IPv4 address before the traffic is delivered to the VPC.

To enable communication over the internet for IPv6, your VPC and subnet must have an associated IPv6 CIDR block, and your instance must be assigned an IPv6 address from the range of the subnet. IPv6 addresses are globally unique, and therefore public by default.

In the following diagram, Subnet 1 in the VPC is a public subnet. It's associated with a custom route table that points all internet-bound IPv4 traffic to an internet gateway. The instance has an Elastic IP address, which enables communication with the internet.
![image](https://user-images.githubusercontent.com/85909185/132931120-bff8317e-fbc6-4a8a-9292-d569c7c05efc.png)

#### Internet access for default and nondefault VPCs
The following table provides an overview of whether your VPC automatically comes with the components required for internet access over IPv4 or IPv6.

Component!Default VPC!Nondefault VPC
---------|-----------|--------------
Internet gateway	| Yes	| Yes, if you created the VPC using the first or second option in the VPC wizard. Otherwise, you must manually create and attach the internet gateway.
Route table with route to internet gateway for IPv4 traffic (0.0.0.0/0)	| Yes	| Yes, if you created the VPC using the first or second option in the VPC wizard. Otherwise, you must manually create the route table and add the route.
Route table with route to internet gateway for IPv6 traffic (::/0)	| No	| Yes, if you created the VPC using the first or second option in the VPC wizard, and if you specified the option to associate an IPv6 CIDR block with the VPC. Otherwise, you must manually create the route table and add the route.
Public IPv4 address automatically assigned to instance launched into subnet	| Yes (default subnet)	| No (nondefault subnet)
IPv6 address automatically assigned to instance launched into subnet |	No (default subnet)	| No (nondefault subnet)

### Network ACLs
A network access control list (ACL) is an optional layer of security for your VPC that acts as a firewall for controlling traffic in and out of one or more subnets. You might set up network ACLs with rules similar to your security groups in order to add an additional layer of security to your VPC.
*Your VPC automatically comes with a modifiable default network ACL. By default, it allows all inbound and outbound IPv4 traffic and, if applicable, IPv6 traffic.
*You can create a custom network ACL and associate it with a subnet. By default, each custom network ACL denies all inbound and outbound traffic until you add rules.


## Storage

### EBS 
* An EBS (Elastic Block Store) Volume is a network drive you can attach to your instances while they run.
* It allows your instances to persist data, even after their termination
* They can only be mounted to one instance at a time (at the CCP level)
* They are bound to a specific availability zone
* Free tier: 30 GB of free EBS storage of type General Purpose (SSD) or Magnetic per month
* can increase capacity of the drive over time
* It can be detached from an EC2 instance and attached to another one quickly
* Delete on termination attribute --checked by default.
![image](https://user-images.githubusercontent.com/85909185/129647972-e472d39b-2946-4979-b5be-c25a0a6da359.png)

 * **Only gp2/gp3 and io1/io2 can be used as boot volumes SSD only** 

#### EBS Multi-Attache only io1/io2 family
Attach the same EBS volume to multiple EC2 instances in the same AZ
Each instance has full read & write permissions to the volume
* Applications must manage concurrent write operations
* must use a file system that's cluster-aware (not XFS..)

#### EBS Encryption
* Data at rest is encrypted inside the volume
* All the data in flight moving between the instance and the volume is encrypted
* All snapshots are encrypted
* All volumes created from the snapshot
* leverage key from KMS
* how to encrypt an unencrypted EBS volume
** create a snapshot
** encrypt snapshot (using copy)
** create new ebs vlume from the snapshot 
** attach the encrypted volume to the original instance

### EC2 Instance Store
* EBS volumes are network drives with good but “limited” performance
* If you need a high-performance hardware disk, use EC2 Instance Store
* Better I/O performance
* EC2 Instance Store lose their storage if they’re stopped (ephemeral)
* Good for buffer / cache / scratch data / temporary content
* Backups and Replication are your responsibility

### pricing differences among EBS, EFS and S3
With Amazon EFS, you pay only for the resources that you use. The EFS Standard Storage pricing is $0.30 per GB per month. Therefore the cost for storing the test file on EFS is $0.30 for the month.

For EBS General Purpose SSD (gp2) volumes, the charges are $0.10 per GB-month of provisioned storage. Therefore, for a provisioned storage of 100GB for this use-case, the monthly cost on EBS is $0.10*100 = $10. This cost is irrespective of how much storage is actually consumed by the test file.

For S3 Standard storage, the pricing is $0.023 per GB per month. Therefore, the monthly storage cost on S3 for the test file is $0.023.
S3 < EFS < EBS

![image](https://user-images.githubusercontent.com/85909185/131790214-9ed0258e-9f31-4bd1-9765-caf0021b2dc8.png)

Amazon EBS Multi-Attach enables you to attach a single Provisioned IOPS SSD (io1 or io2) volume to multiple instances that are in the same Availability Zone. You can attach multiple Multi-Attach enabled volumes to an instance or set of instances. Each instance to which the volume is attached has full read and write permission to the shared volume. Multi-Attach makes it easier for you to achieve higher application availability in clustered Linux applications that manage concurrent write operations.

### EFS
Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic NFS file system for use with AWS Cloud services and on-premises resources.

Amazon EFS is a regional service storing data within and across multiple Availability Zones (AZs) for high availability and durability. Amazon EC2 instances can access your file system across AZs, regions, and VPCs, while on-premises servers can access using AWS Direct Connect or AWS VPN.

You can connect to Amazon EFS file systems from EC2 instances in other AWS regions using an inter-region VPC peering connection, and from on-premises servers using an AWS VPN connection. So this is the correct option.

Max I/O performance mode is used to scale to higher levels of aggregate throughput and operations per second. This scaling is done with a tradeoff of slightly higher latencies for file metadata operations. Highly parallelized applications and workloads, such as big data analysis, media processing, and genomic analysis, can benefit from this mode.
![image](https://user-images.githubusercontent.com/85909185/132090340-a6ac7546-55a4-45ec-aa4a-22fd306c098f.png)

#### EFS Infrequent Access
Amazon EFS Infrequent Access (EFS IA) is a storage class that provides price/performance that is cost-optimized for files, not accessed every day, with storage prices up to 92% lower compared to Amazon EFS Standard. Therefore, this is the correct option.


### S3
#### performance 
For example, your application can achieve at least 3,500 PUT/COPY/POST/DELETE or 5,500 GET/HEAD requests per second per prefix in a bucket.
There are no limits to the number of prefixes in a bucket. You can increase your read or write performance by parallelizing reads. For example, if you create 10 prefixes in an Amazon S3 bucket to parallelize reads, you could scale your read performance to 55,000 read requests per second.

Amazon S3 Transfer Acceleration is a bucket-level feature that enables fast, easy, and secure transfers of files over long distances between your client and an S3 bucket. Transfer Acceleration takes advantage of the globally distributed edge locations in Amazon CloudFront. As the data arrives at an edge location, the data is routed to Amazon S3 over an optimized network path.

When you use Transfer Acceleration, additional data transfer charges might apply. 
*Your customers upload to a centralized bucket from all over the world.
*You transfer gigabytes to terabytes of data on a regular basis across continents.
*You can't use all of your available bandwidth over the internet when uploading to Amazon S3.

#### Retention
retention period - can specify retain util date for the object version
different vresion of a single object can have different retention mode and period
When you use bucket default settings, you don't specify a Retain Until Date. Instead, you specify a duration, in either days or years, for which every object version placed in the bucket should be protected.
You can extend a retention period after you've applied a retention setting to an object version. To do this, submit a new lock request for the object version with a Retain Until Date that is later than the one currently configured for the object version. 

####Versioning
Once you version-enable a bucket, it can never return to an unversioned state. Versioning can only be suspended once it has been enabled.
![image](https://user-images.githubusercontent.com/85909185/131612638-e96abad7-bd29-4da3-844e-75977c7e6c59.png)

####S3 transfer acceleration
Amazon S3 Transfer Acceleration enables fast, easy, and secure transfers of files over long distances between your client and an S3 bucket. Transfer Acceleration takes advantage of Amazon CloudFront’s globally distributed edge locations. As the data arrives at an edge location, data is routed to Amazon S3 over an optimized network path.

Multipart upload allows you to upload a single object as a set of parts. Each part is a contiguous portion of the object's data. You can upload these object parts independently and in any order. If transmission of any part fails, you can retransmit that part without affecting other parts. After all parts of your object are uploaded, Amazon S3 assembles these parts and creates the object. If you're uploading large objects over a stable high-bandwidth network, use multipart uploading to maximize the use of your available bandwidth by uploading object parts in parallel for multi-threaded performance. If you're uploading over a spotty network, use multipart uploading to increase resiliency to network errors by avoiding upload restarts.

 In general, when your object size reaches 100 MB, you should consider using multipart uploads instead of uploading the object in a single operation.
####Transitioning objects using Amazon S3 Lifecycle
*When you know that objects are infrequently accessed, you might transition them to the S3 Standard-IA storage class.
*You might want to archive objects that you don't need to access in real time to the S3 Glacier storage class.
![image](https://user-images.githubusercontent.com/85909185/131614088-06297bf7-6bcb-4c36-9e82-7a2276f94284.png)
You can transition from the following:
*The S3 Standard storage class to any other storage class.
*Any storage class to the S3 Glacier or S3 Glacier Deep Archive storage classes.
*The S3 Standard-IA storage class to the S3 Intelligent-Tiering or S3 One Zone-IA storage classes.
*The S3 Intelligent-Tiering storage class to the S3 One Zone-IA storage class.
*The S3 Glacier storage class to the S3 Glacier Deep Archive storage class.

To summarize again, S3 Intelligent-Tiering, S3 Standard-IA, and S3 One Zone-IA have a minimum storage duration charge of 30 days (so instead of 24 hours, you end up paying for 30 days). S3 Standard-IA and S3 One Zone-IA also have retrieval charges (as the results are heavily referenced by other parts of the analytics pipeline, so the retrieval costs would be pretty high). Therefore, these 3 storage classes are not cost optimal for the given use-case.

#### Unsupported lifecycle transitions
Amazon S3 does not support any of the following Lifecycle transitions.
You can't transition from the following:
*Any storage class to the S3 Standard storage class.
**Any storage class to the Reduced Redundancy storage class.
*The S3 Intelligent-Tiering storage class to the S3 Standard-IA storage class.
*The S3 One Zone-IA storage class to the S3 Standard-IA or S3 Intelligent-Tiering storage classes.

#### bucket policy
A bucket policy is a type of resource-based policy that can be used to grant permissions to the principal that is specified in the policy. Principals can be in the same account as the resource or in other accounts. For cross-account permissions to other AWS accounts or users in another account, you must use a bucket policy.

#### S3 website endpoints
When you configure your bucket as a static website, the website is available at the AWS Region-specific website endpoint of the bucket.
Depending on your Region, your Amazon S3 website endpoints follow one of these two formats.
*s3-website dash (-) Region ‐ http://bucket-name.s3-website.Region.amazonaws.com
*s3-website dot (.) Region ‐ http://bucket-name.s3-website-Region.amazonaws.com
These URLs return the default index document that you configure for the website.




## Security
### Identity and Access Management , Global Servcie
* Root account created by default, shouldn’t be used or shared
* Users are people within your organization, and can be grouped
* Groups only contain users, not other groups
* Users don’t have to belong to a group, and user can belong to multiple groups
#### IAM Permissions
* users or groups can be assigned json documents called policies
* thses policies define the permissions of the users/groups
* apply least privilege principle
json:
 version:
    statement:[
      {
         sid: 1 optional
         effect: deny/allow
         principal: {aws:account/user/roleto which the policy applied to}
         action: ec2:Describe* or list of [cloudWatch:ListMetrics,cloudWatch:Describe*]
         resource: *
         condition: when the policy is in effect
    ]
Policy inheritance if user belongs to multiple groups.
#### IAM password policy
can setup password policy like min password length, require specific character types,allow change password, password expiration,prevent password reuse
MFA-multi Factor Authentication

### Policy types
AWS supports six types of policies: identity-based policies, resource-based policies, permissions boundaries, Organizations SCPs, ACLs, and session policies.
* Identity-based policies – Attach managed and inline policies to IAM identities (users, groups to which users belong, or roles). Identity-based policies grant permissions to an identity.
* Resource-based policies – Attach inline policies to resources. The most common examples of resource-based policies are Amazon S3 bucket policies and IAM role trust policies. Resource-based policies grant permissions to the principal that is specified in the policy. Principals can be in the same account as the resource or in other accounts.
* Permissions boundaries – Use a managed policy as the permissions boundary for an IAM entity (user or role). That policy defines the maximum permissions that the identity-based policies can grant to an entity, but does not grant permissions. Permissions boundaries do not define the maximum permissions that a resource-based policy can grant to an entity.
* Organizations SCPs – Use an AWS Organizations service control policy (SCP) to define the maximum permissions for account members of an organization or organizational unit (OU). SCPs limit permissions that identity-based policies or resource-based policies grant to entities (users or roles) within the account, but do not grant permissions.
* Access control lists (ACLs) – Use ACLs to control which principals in other accounts can access the resource to which the ACL is attached. ACLs are similar to resource-based policies, although they are the only policy type that does not use the JSON policy document structure. ACLs are cross-account permissions policies that grant permissions to the specified principal. ACLs cannot grant permissions to entities within the same account.
* Session policies – Pass advanced session policies when you use the AWS CLI or AWS API to assume a role or a federated user. Session policies limit the permissions that the role or user's identity-based policies grant to the session. Session policies limit permissions for a created session, but do not grant permissions. For more information, see Session Policies.

### IAM Role Scenarios
IAM roles with temporary credentials are useful in the following situations:

Temporary IAM user permissions – An IAM user can assume an IAM role to temporarily take on different permissions for a specific task.

Federated user access – Instead of creating an IAM user, you can use existing identities from AWS Directory Service, your enterprise user directory, or a web identity provider. These are known as federated users. AWS assigns a role to a federated user when access is requested through an identity provider. For more information about federated users, see Federated users and roles in the IAM User Guide.

Cross-account access – You can use an IAM role to allow someone (a trusted principal) in a different account to access resources in your account. Roles are the primary way to grant cross-account access. However, with some AWS services, you can attach a policy directly to a resource (instead of using a role as a proxy). To learn the difference between roles and resource-based policies for cross-account access, see How IAM roles differ from resource-based policies in the IAM User Guide.

Cross-service access – Some AWS services use features in other AWS services. For example, when you make a call in a service, it's common for that service to run applications in Amazon EC2 or store objects in Amazon S3. A service might do this using the calling principal's permissions, using a service role, or using a service-linked role.

Principal permissions – When you use an IAM user or role to perform actions in AWS, you are considered a principal. Policies grant permissions to a principal. When you use some services, you might perform an action that then triggers another action in a different service. In this case, you must have permissions to perform both actions. To see whether an action requires additional dependent actions in a policy, see Actions, Resources, and Condition Keys for AWS Lambda in the Service Authorization Reference.

Service role – A service role is an IAM role that a service assumes to perform actions on your behalf. An IAM administrator can create, modify, and delete a service role from within IAM. For more information, see Creating a role to delegate permissions to an AWS service in the IAM User Guide.

Service-linked role – A service-linked role is a type of service role that is linked to an AWS service. The service can assume the role to perform an action on your behalf. Service-linked roles appear in your IAM account and are owned by the service. An IAM administrator can view, but not edit the permissions for service-linked roles.

Applications running on Amazon EC2 – You can use an IAM role to manage temporary credentials for applications that are running on an EC2 instance and making AWS CLI or AWS API requests. This is preferable to storing access keys within the EC2 instance. To assign an AWS role to an EC2 instance and make it available to all of its applications, you create an instance profile that is attached to the instance. An instance profile contains the role and enables programs that are running on the EC2 instance to get temporary credentials. For more information, see Using an IAM role to grant permissions to applications running on Amazon EC2 instances in the IAM User Guide.

#### How IAM roles differ from resource-based policies
For example, assume that you manage AccountA and AccountB. In AccountA, you have the Amazon S3 bucket named BucketA. You attach a resource-based policy to BucketA that allows all principals in AccountB full access to objects in your bucket. They can create, read, or delete any objects in that bucket. In AccountB, you attach a policy to the IAM user named User2. That policy allows the user read-only access to the objects in BucketA. That means that User2 can view the objects, but not create, edit, or delete them.
![image](https://user-images.githubusercontent.com/85909185/132144807-cfaa63f4-751a-40d6-a957-94700764ab36.png)

AccountA gives AccountB full access to BucketA by naming AccountB as a principal in the resource-based policy. As a result, AccountB is authorized to perform any action on BucketA, and the AccountB administrator can delegate access to its users in AccountB.

The AccountB root user has all of the permissions that are granted to the account. Therefore, the root user has full access to BucketA.

The AccountB administrator does not give access to User1. By default, users do not have any permissions except those that are explicitly granted. Therefore, User1 does not have access to BucketA.

The AccountB administrator grants User2 read-only access to BucketA. User2 can view the objects in the bucket. The maximum level of access that AccountB can delegate is the access level that is granted to the account. In this case, the resource-based policy granted full access to AccountB, but User2 is granted only read-only access.

### How can users access AWS?
* AWS management console (protected by password + MFA)
* CLI: access keys
* SDK: access keys
* users manage their own access keys access key id == user name secret access key = password

### Amazon Cognito User Pools
 A user pool is a user directory in Amazon Cognito. You can leverage Amazon Cognito User Pools to either provide built-in user management or integrate with external identity providers, such as Facebook, Twitter, Google+, and Amazon. Whether your users sign-in directly or through a third party, all members of the user pool have a directory profile that you can access through a Software Development Kit (SDK).
 User pools provide: 1. Sign-up and sign-in services. 2. A built-in, customizable web UI to sign in users. 3. Social sign-in with Facebook, Google, Login with Amazon, and Sign in with Apple, as well as sign-in with SAML identity providers from your user pool. 4. User directory management and user profiles. 5. Security features such as multi-factor authentication (MFA), checks for compromised credentials, account takeover protection, and phone and email verification. 6. Customized workflows and user migration through AWS Lambda triggers.

After creating an Amazon Cognito user pool, in API Gateway, you must then create a COGNITO_USER_POOLS authorizer that uses the user pool.
![image](https://user-images.githubusercontent.com/85909185/132145761-33c64294-b376-496a-9cfd-957cdf3bf09d.png)

### Access Control
![image](https://user-images.githubusercontent.com/85909185/132146039-c0b7a8a9-1614-45f4-80a8-e7d6776454e0.png)

### differences between Cognito User Pools and Cognito Identity Pools:
![image](https://user-images.githubusercontent.com/85909185/132146223-2dfa8444-2595-426f-8c94-4fad5efa9cf8.png)



### CMK vs KMS
Deleting a customer master key (CMK) in AWS Key Management Service (AWS KMS) is destructive and potentially dangerous. Therefore, AWS KMS enforces a waiting period. To delete a CMK in AWS KMS you schedule key deletion. You can set the waiting period from a minimum of 7 days up to a maximum of 30 days. The default waiting period is 30 days. During the waiting period, the CMK status and key state is Pending deletion. To recover the CMK, you can cancel key deletion before the waiting period ends. After the waiting period ends you cannot cancel key deletion, and AWS KMS deletes the CMK.
![image](https://user-images.githubusercontent.com/85909185/131619815-06636f83-0f8c-4a8c-a927-100b796fd398.png)


## Kinesis Data Streams (KDS)
![image](https://user-images.githubusercontent.com/85909185/132930171-01fb6835-4c2d-4120-ac23-26a89fa9d8c2.png)

 Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events.The throughput of an Amazon Kinesis data stream is designed to scale without limits via increasing the number of shards within a data stream.

### When should I use Amazon Kinesis Data Streams, and when should I use Amazon SQS?

We recommend Amazon Kinesis Data Streams for use cases with requirements that are similar to the following:

Routing related records to the same record processor (as in streaming MapReduce). For example, counting and aggregation are simpler when all records for a given key are routed to the same record processor.
Ordering of records. For example, you want to transfer log data from the application host to the processing/archival host while maintaining the order of log statements.
Ability for multiple applications to consume the same stream concurrently. For example, you have one application that updates a real-time dashboard and another that archives data to Amazon Redshift. You want both applications to consume data from the same stream concurrently and independently.
Ability to consume records in the same order a few hours later. For example, you have a billing application and an audit application that runs a few hours behind the billing application. Because Amazon Kinesis Data Streams stores data for up to 365 days, you can run the audit application up to 365 days behind the billing application.
We recommend Amazon SQS for use cases with requirements that are similar to the following:

Messaging semantics (such as message-level ack/fail) and visibility timeout. For example, you have a queue of work items and want to track the successful completion of each item independently. Amazon SQS tracks the ack/fail, so the application does not have to maintain a persistent checkpoint/cursor. Amazon SQS will delete acked messages and redeliver failed messages after a configured visibility timeout.
Individual message delay. For example, you have a job queue and need to schedule individual jobs with a delay. With Amazon SQS, you can configure individual messages to have a delay of up to 15 minutes.
Dynamically increasing concurrency/throughput at read time. For example, you have a work queue and want to add more readers until the backlog is cleared. With Amazon Kinesis Data Streams, you can scale up to a sufficient number of shards (note, however, that you'll need to provision enough shards ahead of time).
Leveraging Amazon SQS’s ability to scale transparently. For example, you buffer requests and the load changes as a result of occasional load spikes or the natural growth of your business. Because each buffered request can be processed independently, Amazon SQS can scale transparently to handle the load without any provisioning instructions from you

### Enhanced Fanout feature of Kinesis Data Streams
By default, the 2MB/second/shard output is shared between all of the applications consuming data from the stream. You should use enhanced fan-out if you have multiple consumers retrieving data from a stream in parallel. With enhanced fan-out developers can register stream consumers to use enhanced fan-out and receive their own 2MB/second pipe of read throughput per shard, and this throughput automatically scales with the number of shards in a stream.
![image](https://user-images.githubusercontent.com/85909185/135000155-b8952989-b4e0-4d8e-b0f7-d1f50886673b.png)


## Kinesis Data Firehose
Amazon Kinesis Data Firehose is the easiest way to load streaming data into data stores and analytics tools. It can capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk, enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today. It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration. It can also batch, compress, and encrypt the data before loading it, minimizing the amount of storage used at the destination and increasing security.
![image](https://user-images.githubusercontent.com/85909185/131784743-660c62f7-83c9-4daa-bd24-a663d6624995.png)

Kinesis Data Firehose can only write to S3, Redshift, Elasticsearch or Splunk. You can't have applications consuming data streams from Kinesis Data Firehose, that's the job of Kinesis Data Streams. 

## GuardDuty 
Amazon GuardDuty is a threat detection service that continuously monitors for malicious activity and unauthorized behavior to protect your AWS accounts, workloads, and data stored in Amazon S3. With the cloud, the collection and aggregation of account and network activities is simplified, but it can be time-consuming for security teams to continuously analyze event log data for potential threats. With GuardDuty, you now have an intelligent and cost-effective option for continuous threat detection in AWS. The service uses machine learning, anomaly detection, and integrated threat intelligence to identify and prioritize potential threats.

GuardDuty analyzes tens of billions of events across multiple AWS data sources, such as AWS CloudTrail events, Amazon VPC Flow Logs, and DNS logs.

With a few clicks in the AWS Management Console, GuardDuty can be enabled with no software or hardware to deploy or maintain. By integrating with Amazon CloudWatch Events, GuardDuty alerts are actionable, easy to aggregate across multiple accounts, and straightforward to push into existing event management and workflow systems.

## CloudTrail 
AWS CloudTrail is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account. With CloudTrail, you can log, continuously monitor, and retain account activity related to actions across your AWS infrastructure. CloudTrail provides event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command line tools, and other AWS services. This event history simplifies security analysis, resource change tracking, and troubleshooting. In addition, you can use CloudTrail to detect unusual activity in your AWS accounts. These capabilities help simplify operational analysis and troubleshooting.



## Lambda
AWS Lambda currently supports 1000 concurrent executions per AWS account per region. If your Amazon SNS message deliveries to AWS Lambda contribute to crossing these concurrency quotas, your Amazon SNS message deliveries will be throttled. You need to contact AWS support to raise the account limit. Therefore this option is correct.


## AWS WAF
AWS WAF is a web application firewall service that lets you monitor web requests and protect your web applications from malicious requests. Use AWS WAF to block or allow requests based on conditions that you specify, such as the IP addresses. You can also use AWS WAF preconfigured protections to block common attacks like SQL injection or cross-site scripting.
Configure AWS WAF on the Application Load Balancer in a VPC

You can use AWS WAF with your Application Load Balancer to allow or block requests based on the rules in a web access control list (web ACL). Geographic (Geo) Match Conditions in AWS WAF allows you to use AWS WAF to restrict application access based on the geographic location of your viewers. With geo match conditions you can choose the countries from which AWS WAF should allow access.

Geo match conditions are important for many customers. For example, legal and licensing requirements restrict some customers from delivering their applications outside certain countries. These customers can configure a whitelist that allows only viewers in those countries. Other customers need to prevent the downloading of their encrypted software by users in certain countries. These customers can configure a blacklist so that end-users from those countries are blocked from downloading their software.

## Amazon MQ
Amazon MQ is a managed message broker service for Apache ActiveMQ and RabbitMQ that makes it easy to set up and operate message brokers on AWS. Amazon MQ reduces your operational responsibilities by managing the provisioning, setup, and maintenance of message brokers for you. Because Amazon MQ connects to your current applications with industry-standard APIs and protocols, you can easily migrate to AWS without having to rewrite code.
it supports mqtt.


## SQS
Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. SQS offers two types of message queues - Standard queues vs FIFO queues.

For FIFO queues, the order in which messages are sent and received is strictly preserved (i.e. First-In-First-Out). On the other hand, the standard SQS queues offer best-effort ordering. This means that occasionally, messages might be delivered in an order different from which they were sent.

By default, FIFO queues support up to 300 messages per second (300 send, receive, or delete operations per second). When you batch 10 messages per operation (maximum), FIFO queues can support up to 3,000 messages per second. Therefore you need to process 4 messages per operation so that the FIFO queue can support up to 1200 messages per second, which is well within the peak rate.
![image](https://user-images.githubusercontent.com/85909185/131991239-f3ad1471-2384-4a51-948e-7d3f10b956b6.png)

Amazon SQS provides short polling and long polling to receive messages from a queue. By default, queues use short polling. With short polling, Amazon SQS sends the response right away, even if the query found no messages. With long polling, Amazon SQS sends a response after it collects at least one available message, up to the maximum number of messages specified in the request. Amazon SQS sends an empty response only if the polling wait time expires.

Long polling makes it inexpensive to retrieve messages from your Amazon SQS queue as soon as the messages are available. Using long polling can reduce the cost of using SQS because you can reduce the number of empty receives.

![image](https://user-images.githubusercontent.com/85909185/132929652-67398128-b8b5-4054-b053-4dd006973652.png)

### Delay queue
Delay queues let you postpone the delivery of new messages to a queue for several seconds, for example, when your consumer application needs additional time to process messages. If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period. The default (minimum) delay for a queue is 0 seconds. The maximum is 15 minutes.
![image](https://user-images.githubusercontent.com/85909185/132938412-f40a8e6a-9a00-4253-9852-5f5d20b1e948.png)

Delay queues are similar to visibility timeouts because both features make messages unavailable to consumers for a specific period of time. The difference between the two is that, for delay queues, a message is hidden when it is first added to queue, whereas for visibility timeouts a message is hidden only after it is consumed from the queue. The following diagram illustrates the relationship between delay queues and visibility timeouts.

### SNS
With SQS, you can use FIFO (First-In-First-Out) queues to preserve the order in which messages are sent and received, and to avoid that a message is processed more than once.
![image](https://user-images.githubusercontent.com/85909185/132145170-8e318b46-b382-4130-89cb-5c31799ea487.png)

* Ordering – You configure a message group by including a message group ID when publishing a message to a FIFO topic. 
* Deduplication – Distributed systems (like SNS) and client applications sometimes generate duplicate messages. You can avoid duplicated message deliveries from the topic in two ways: either by enabling content-based deduplication on the topic, or by adding a deduplication ID to the messages that you publish. If you subscribe a FIFO queue to a FIFO topic, the deduplication ID is passed to the queue and it is used by SQS to avoid duplicate messages being received.
* You can use FIFO topics and queues together to simplify the implementation of applications where the order of operations and events is critical, or when you cannot tolerate duplicates.
![image](https://user-images.githubusercontent.com/85909185/132145261-6c7032a2-771a-42ae-94f0-de7bc172c105.png)



## Cloudwatch
You can use CloudWatch Alarms to send an email via SNS whenever any of the EC2 instances breaches a certain threshold.

## RDS
If your workload is unpredictable, you can enable storage autoscaling for an Amazon RDS DB instance. With storage autoscaling enabled, when Amazon RDS detects that you are running out of free database space it automatically scales up your storage. Amazon RDS starts a storage modification for an autoscaling-enabled DB instance when these factors apply:
*Free available space is less than 10 percent of the allocated storage.
*The low-storage condition lasts at least five minutes.
*At least six hours have passed since the last storage modification.
The maximum storage threshold is the limit that you set for autoscaling the DB instance. You can't set the maximum storage threshold for autoscaling-enabled instances to a value greater than the maximum allocated storage.

![image](https://user-images.githubusercontent.com/85909185/132113363-5d47d776-2879-4a63-a839-99336e1a1cc9.png)

When you provision an RDS Multi-AZ DB Instance, Amazon RDS automatically creates a primary DB Instance and synchronously replicates the data to a standby instance in a different Availability Zone (AZ). Each AZ runs on its own physically distinct, independent infrastructure, and is engineered to be highly reliable.
the standby is not a read replica as it does not accept read request. 
![image](https://user-images.githubusercontent.com/85909185/132929205-6b281d8b-e91b-469c-a74d-fe1467769b79.png)

### Aurora global database
An Aurora global database provides more comprehensive failover capabilities than the failover provided by a default Aurora DB cluster. By using an Aurora global database, you can plan for and recover from disaster fairly quickly. Recovery from disaster is typically measured using values for RTO and RPO.

global database uses storage replication while read replica uses db replica.

![image](https://user-images.githubusercontent.com/85909185/132970969-83ad6e11-3a95-44b5-9dda-0210b62e2563.png)



## DynamoDB
DynamoDB - Amazon DynamoDB is a key-value and document database that delivers single-digit millisecond performance at any scale. It's a fully managed, multi-region, multi-master, durable database with built-in security, backup and restore, and in-memory caching for internet-scale applications. DynamoDB is a NoSQL database and it's best suited to store data in key-value pairs.

## RedShift
Redshift - Amazon Redshift is a fully-managed petabyte-scale cloud-based data warehouse product designed for large scale data set storage and analysis. 

## Redshift Spectrum
Amazon Redshift is a fully-managed petabyte-scale cloud-based data warehouse product designed for large scale data set storage and analysis.

Using Amazon Redshift Spectrum, you can efficiently query and retrieve structured and semistructured data from files in Amazon S3 without having to load the data into Amazon Redshift tables.

Amazon Redshift Spectrum resides on dedicated Amazon Redshift servers that are independent of your cluster. Redshift Spectrum pushes many compute-intensive tasks, such as predicate filtering and aggregation, down to the Redshift Spectrum layer. Thus, Redshift Spectrum queries use much less of your cluster's processing capacity than other queries.

Redshift Spectrum Overview
![image](https://user-images.githubusercontent.com/85909185/134999317-59b15aec-ce22-49db-b8db-7e1f3b1472ae.png)


## ElasticCache
ElastiCache - Amazon ElastiCache allows you to seamlessly set up, run, and scale popular open-Source compatible in-memory data stores in the cloud. Build data-intensive apps or boost the performance of your existing databases by retrieving data from high throughput and low latency in-memory data stores. Amazon ElastiCache is a popular choice for real-time use cases like Caching, Session Stores, Gaming, Geospatial Services, Real-Time Analytics, and Queuing. Elasticache is used as a caching layer in front of relational databases. It is not a good fit to store data in key-value pairs from the IoT sources

## Lambda
### access to S3 bucket in the same AWS account
I want my AWS Lambda function to be able to access my Amazon Simple Storage Service (Amazon S3) bucket. How can I do that?
To give your Lambda function access to an Amazon S3 bucket in the same AWS account, do the following:
1.    Create an AWS Identity and Access Management (IAM) role for the Lambda function that also grants access to the S3 bucket.
2.    Configure the IAM role as the Lambda function's execution role.
3.    Verify that the S3 bucket policy doesn't explicitly deny access to your Lambda function or its execution role.

Important: If your S3 bucket and the function's IAM role are in different accounts, then you must also grant the required permissions on the S3 bucket policy.

### cross-account access to AWS S3 bucket
I want to grant another AWS account access to an object that is stored in an Amazon Simple Storage Service (Amazon S3) bucket. How can I provide cross-account access to Amazon S3 buckets?
Use one of the following methods to grant cross-account access to objects that are stored in S3 buckets:

*Resource-based policies and AWS Identity and Access Management (IAM) policies for programmatic-only access to S3 bucket objects
*Resource-based Access Control List (ACL) and IAM policies for programmatic-only access to S3 bucket objects
*Cross-account IAM roles for programmatic and console access to S3 bucket objects


## Kinesis data firehose
Amazon Kinesis Data Firehose is the easiest way to reliably load streaming data into data lakes, data stores, and analytics services. It can capture, transform, and deliver streaming data to Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, generic HTTP endpoints, and service providers like Datadog, New Relic, MongoDB, and Splunk. It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing administration. It can also batch, compress, transform, and encrypt your data streams before loading, minimizing the amount of storage used and increasing security.

You can easily create a Firehose delivery stream from the AWS Management Console, configure it with a few clicks, and start ingesting streaming data from hundreds of thousands of data sources to your specified destinations.
You can also configure your data streams to automatically convert the incoming data to open and standards based formats like Apache Parquet and Apache ORC before the data is delivered.
With Amazon Kinesis Data Firehose, there is no minimum fee or setup cost. You pay for the amount of data that you transmit through the service, if applicable, for converting data formats, and for Amazon VPC delivery and data transfer

## Kinesis Data Streams
Kinesis Data Streams - Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. The throughput of an Amazon Kinesis data stream is designed to scale without limits via increasing the number of shards within a data stream. With Amazon Kinesis Data Streams, you can scale up to a sufficient number of shards (note, however, that you'll need to provision enough shards ahead of time). As it requires manual administration of shards, it's not the correct choice for the given use-case.

## AWS Organizations
AWS Organizations helps you centrally manage and govern your environment as you grow and scale your AWS resources. Using AWS Organizations, you can programmatically create new AWS accounts and allocate resources, group accounts to organize your workflows, apply policies to accounts or groups for governance, and simplify billing by using a single payment method for all of your accounts.

In addition, AWS Organizations is integrated with other AWS services so you can define central configurations, security mechanisms, audit requirements, and resource sharing across accounts in your organization. AWS Organizations is available to all AWS customers at no additional charge.
![image](https://user-images.githubusercontent.com/85909185/132112354-d70e072c-f08e-47f2-89e1-e8b146b380ad.png)

## AWS Resource Access Manager
AWS Resource Access Manager (RAM) helps you securely share your resources across AWS accounts, within your organization or organizational units (OUs) in AWS Organizations, and with IAM roles and IAM users for supported resource types. You can use AWS RAM to share transit gateways, subnets, AWS License Manager license configurations, Amazon Route 53 Resolver rules, and more resource types.
![image](https://user-images.githubusercontent.com/85909185/132112393-098a0b51-1f4d-4304-87cd-37bb59121f86.png)

## Aurora
Aurora Replicas have two main purposes. You can issue queries to them to scale the read operations for your application. You typically do so by connecting to the reader endpoint of the cluster. That way, Aurora can spread the load for read-only connections across as many Aurora Replicas as you have in the cluster. Aurora Replicas also help to increase availability. If the writer instance in a cluster becomes unavailable, Aurora automatically promotes one of the reader instances to take its place as the new writer.

While setting up a Multi-AZ deployment for Aurora, you create an Aurora replica or reader node in a different AZ.

Multi-AZ for Aurora:
![image](https://user-images.githubusercontent.com/85909185/132112473-017185cc-9458-45ff-a959-d5383fcc3b9c.png)

You use the reader endpoint for read-only connections for your Aurora cluster. This endpoint uses a load-balancing mechanism to help your cluster handle a query-intensive workload. The reader endpoint is the endpoint that you supply to applications that do reporting or other read-only operations on the cluster. The reader endpoint load-balances connections to available Aurora Replicas in an Aurora DB cluster.
![image](https://user-images.githubusercontent.com/85909185/132112485-3ccaafb1-9f2c-43ef-833a-bacacde2e841.png)

### Amazon Aurora Serverless
Amazon Aurora Serverless is an on-demand, auto-scaling configuration for Amazon Aurora (MySQL-compatible and PostgreSQL-compatible editions), where the database will automatically start-up, shut down, and scale capacity up or down based on your application's needs. It enables you to run your database in the cloud without managing any database instances. It's a simple, cost-effective option for infrequent, intermittent, or unpredictable workloads. You pay on a per-second basis for the database capacity you use when the database is active and migrate between standard and serverless configurations with a few clicks in the Amazon RDS Management Console.


## Secrets Manager
AWS Secrets Manager helps you protect secrets needed to access your applications, services, and IT resources. The service enables you to easily rotate, manage, and retrieve database credentials, API keys, and other secrets throughout their lifecycle. Users and applications retrieve secrets with a call to Secrets Manager APIs, eliminating the need to hardcode sensitive information in plain text. Secrets Manager offers secret rotation with built-in integration for Amazon RDS, Amazon Redshift, and Amazon DocumentDB. The correct answer here is Secrets Manager

## SSM Parameter store
"SSM Parameter Store" - AWS Systems Manager Parameter Store (aka SSM Parameter Store) provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, EC2 instance IDs, Amazon Machine Image (AMI) IDs, and license codes as parameter values. You can store values as plain text or encrypted data. You can reference Systems Manager parameters in your scripts, commands, SSM documents, and configuration and automation workflows by using the unique name that you specified when you created the parameter.

SSM Parameter Store can serve as a secrets store, but you must rotate the secrets yourself, it doesn't have an automatic capability for this.

## Resource Access Manager
AWS Resource Access Manager (RAM) is a service that enables you to easily and securely share AWS resources with any AWS account or within your AWS Organization. You can share AWS Transit Gateways, Subnets, AWS License Manager configurations, and Amazon Route 53 Resolver rules resources with RAM. RAM eliminates the need to create duplicate resources in multiple accounts, reducing the operational overhead of managing those resources in every single account you own. You can create resources centrally in a multi-account environment, and use RAM to share those resources across accounts in three simple steps: create a Resource Share, specify resources, and specify accounts. RAM is available to you at no additional charge.

## Database MIgration Service DMS
Use AWS Database Migration Service to replicate the data from the databases into Amazon Redshift

AWS Database Migration Service helps you migrate databases to AWS quickly and securely. The source database remains fully operational during the migration, minimizing downtime to applications that rely on the database. With AWS Database Migration Service, you can continuously replicate your data with high availability and consolidate databases into a petabyte-scale data warehouse by streaming data to Amazon Redshift and Amazon S3.


https://d2908q01vomqb2.cloudfront.net/b6692ea5df920cad691c20319a6fffd7a4a766b8/2020/09/08/streaming-s3-to-kinesis-21.jpg

## Acive Directory
### AWS Managed Microsoft AD

AWS Directory Service provides multiple ways to use Amazon Cloud Directory and Microsoft Active Directory (AD) with other AWS services.

AWS Directory Service for Microsoft Active Directory (aka AWS Managed Microsoft AD) is powered by an actual Microsoft Windows Server Active Directory (AD), managed by AWS. With AWS Managed Microsoft AD, you can run directory-aware workloads in the AWS Cloud such as SQL Server-based applications. You can also configure a trust relationship between AWS Managed Microsoft AD in the AWS Cloud and your existing on-premises Microsoft Active Directory, providing users and groups with access to resources in either domain, using single sign-on (SSO).

### AD Connector - Use AD Connector if you only need to allow your on-premises users to log in to AWS applications and services with their Active Directory credentials. AD Connector simply connects your existing on-premises Active Directory to AWS. You cannot use it to run directory-aware workloads on AWS, hence this option is not correct.

### Simple AD - Simple AD provides a subset of the features offered by AWS Managed Microsoft AD. Simple AD is a standalone managed directory that is powered by a Samba 4 Active Directory Compatible Server. Simple AD does not support features such as trust relationships with other domains. Therefore, this option is not correct.

### Amazon Cloud Directory - Amazon Cloud Directory is a cloud-native directory that can store hundreds of millions of application-specific objects with multiple relationships and schemas. Use Amazon Cloud Directory if you need a highly scalable directory store for your application’s hierarchical data. You cannot use it to establish trust relationships with other domains on the on-premises infrastructure. Therefore, this option is not correct.

## AWS Config
AWS Config is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources. With Config, you can review changes in configurations and relationships between AWS resources, dive into detailed resource configuration histories, and determine your overall compliance against the configurations specified in your internal guidelines. You can use Config to answer questions such as - “What did my AWS resource look like at xyz point in time?”.
![image](https://user-images.githubusercontent.com/85909185/132971119-a7db35af-52be-4366-9a6f-a73f4136b077.png)

*Think resource performance monitoring, events, and alerts; think CloudWatch.
*Think account-specific activity and audit; think CloudTrail.
*Think resource-specific history, audit, and compliance; think Config.


## Trusted Advisor
AWS Trusted Advisor is an online tool that draws upon best practices learned from AWS’s aggregated operational history of serving hundreds of thousands of AWS customers. Trusted Advisor inspects your AWS environment and makes recommendations for saving money, improving system performance, or closing security gaps. It scans your AWS infrastructure and compares it to AWS Best practices in five categories (Cost Optimization, Performance, Security, Fault Tolerance, Service limits) and then provides recommendations.

How Trusted Advisor Works:
![image](https://user-images.githubusercontent.com/85909185/134999592-d934ac11-357a-4025-8c7c-3de018218b3c.png)


## API Gateway
Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor, and secure APIs at any scale. APIs act as the "front door" for applications to access data, business logic, or functionality from your backend services.

How API Gateway Works:
![image](https://user-images.githubusercontent.com/85909185/135000341-11a04389-2ea5-4673-991c-547b7eebf306.png)

## DataSync 
AWS DataSync is an online data transfer service that simplifies, automates, and accelerates copying large amounts of data to and from AWS storage services over the internet or AWS Direct Connect.

AWS DataSync fully automates and accelerates moving large active datasets to AWS, up to 10 times faster than command-line tools. It is natively integrated with Amazon S3, Amazon EFS, Amazon FSx for Windows File Server, Amazon CloudWatch, and AWS CloudTrail, which provides seamless and secure access to your storage services, as well as detailed monitoring of the transfer. DataSync uses a purpose-built network protocol and scale-out architecture to transfer data. A single DataSync agent is capable of saturating a 10 Gbps network link.

DataSync fully automates the data transfer. It comes with retry and network resiliency mechanisms, network optimizations, built-in task scheduling, monitoring via the DataSync API and Console, and CloudWatch metrics, events, and logs that provide granular visibility into the transfer process. DataSync performs data integrity verification both during the transfer and at the end of the transfer.

How DataSync Works:
![image](https://user-images.githubusercontent.com/85909185/135007686-29868e6e-d637-43fc-be60-55006f9c4cbb.png)


## AWS Storage Gateway
AWS Storage Gateway is a hybrid cloud storage service that gives you on-premises access to virtually unlimited cloud storage. The service provides three different types of gateways – Tape Gateway, File Gateway, and Volume Gateway – that seamlessly connect on-premises applications to cloud storage, caching data locally for low-latency access. File gateway offers SMB or NFS-based access to data in Amazon S3 with local caching.

The combination of DataSync and File Gateway is the correct solution. AWS DataSync enables you to automate and accelerate online data transfers to AWS storage services. File Gateway then provides your on-premises applications with low latency access to the migrated data.


##S3
Amazon S3 supports the following destinations where it can publish events:

* Amazon Simple Notification Service (Amazon SNS) topic
* Amazon Simple Queue Service (Amazon SQS) queue
* AWS Lambda
Currently, the Standard SQS queue is only allowed as an Amazon S3 event notification destination, whereas the FIFO SQS queue is not allowed




## SCP
Service control policies (SCPs) are a type of organization policy that you can use to manage permissions in your organization. SCPs offer central control over the maximum available permissions for all accounts in your organization. SCPs help you to ensure your accounts stay within your organization’s access control guidelines.

An SCP restricts permissions for IAM users and roles in member accounts, including the member account's root user. Any account has only those permissions permitted by every parent above it. If a permission is blocked at any level above the account, either implicitly (by not being included in an Allow policy statement) or explicitly (by being included in a Deny policy statement), a user or role in the affected account can't use that permission, even if the account administrator attaches the AdministratorAccess IAM policy with / permissions to the user.

SCPs don't affect users or roles in the management account. They affect only the member accounts in your organization.

Running a DB instance as a Multi-AZ deployment can further reduce the impact of a maintenance event because Amazon RDS applies operating system updates by following these steps:

Perform maintenance on the standby.

Promote the standby to primary.

Perform maintenance on the old primary, which becomes the new standby.

A Multi-AZ standby cannot serve read requests. Multi-AZ deployments are designed to provide enhanced database availability and durability, rather than read scaling benefits. As such, the feature uses synchronous replication between primary and standby. AWS implementation makes sure the primary and the standby are constantly in sync, but precludes using the standby for read or write operations.

## AWS PrivateLink
AWS PrivateLink provides private connectivity between VPCs, AWS services, and your on-premises networks, without exposing your traffic to the public internet. AWS PrivateLink makes it easy to connect services across different accounts and VPCs to significantly simplify your network architecture.
You can connect services across different accounts and Amazon VPCs, with no need for firewall rules, path definitions, or route tables. There is no need to configure an Internet gateway, VPC peering connection, or manage VPC Classless Inter-Domain Routing (CIDRs). Because AWS PrivateLink simplifies your network architecture, it is easier for you to manage your global network.
AWS PrivateLink enables you to securely connect your VPCs to supported AWS services: to your own services on AWS, to services hosted by other AWS accounts, and to third-party services on AWS Marketplace. Since traffic between your VPC and any one of these services does not leave the Amazon network, an Internet gateway, NAT device, public IP address, or VPN connection is no longer needed to communicate with the service.
To use AWS PrivateLink, create an interface VPC endpoint for a service in your VPC. This creates an Elastic Network Interface (ENI) in your subnet with a private IP address that serves as an entry point for traffic destined to the service. Service endpoints available over AWS PrivateLink will appear as ENIs with private IPs in your VPCs.
![image](https://user-images.githubusercontent.com/85909185/136873746-0775280e-8924-4ced-806c-030f4b21a683.png)
Let's understand this by a real-life use case

Suppose You have your Own VPC (created by you using your own AWS Account) in which you have few EC2 instances that wants to communicate with instances running in your Client's VPC - obviously this VPC is created by your client using his/her AWS Account - Use VPC Peering to achieve this communication requirement

Now consider you have your OWN VPC (created by you using your own AWS Account) with EC2 Instance running inside it, and using the same AWS account you uploaded some files in S3. And your EC2 Instance now wants to read content of the file in S3.

In this case you will configure VPC Endpoint - which uses PrivateLink technology - AWS PrivateLink allows you to privately access services hosted on the AWS network in a highly available and scalable manner, without using public IPs and without requiring the traffic to traverse the internet.


## Spot block
A Spot Instance is an unused EC2 instance that is available for less than the On-Demand price. Because Spot Instances enable you to request unused EC2 instances at steep discounts, you can lower your Amazon EC2 costs significantly. The hourly price for a Spot Instance is called a Spot price.

Spot Instances with a defined duration (also known as Spot blocks) are designed not to be interrupted and will run continuously for the duration you select. This makes them ideal for jobs that take a finite time to complete, such as batch processing, encoding and rendering, modeling and analysis, and continuous integration.

## Instance store lifetime
You can specify instance store volumes for an instance only when you launch it. You can't detach an instance store volume from one instance and attach it to a different instance.

The data in an instance store persists only during the lifetime of its associated instance. If an instance reboots (intentionally or unintentionally), data in the instance store persists. However, data in the instance store is lost under any of the following circumstances:

The underlying disk drive fails

The instance stops

The instance hibernates

The instance terminates

Therefore, do not rely on instance store for valuable, long-term data. Instead, use more durable data storage, such as Amazon S3, Amazon EBS, or Amazon EFS.

When you stop, hibernate, or terminate an instance, every block of storage in the instance store is reset. Therefore, your data cannot be accessed through the instance store of another instance.

If you create an AMI from an instance, the data on its instance store volumes isn't preserved and isn't present on the instance store volumes of the instances that you launch from the AMI.

## AWS Firewall manager
AWS Firewall Manager is a security management service which allows you to centrally configure and manage firewall rules across your accounts and applications in AWS Organizations. As new applications are created, Firewall Manager makes it easy to bring new applications and resources into compliance by enforcing a common set of security rules. Now you have a single service to build firewall rules, create security policies, and enforce them in a consistent, hierarchical manner across your entire infrastructure.

Using AWS Firewall Manager, you can centrally configure AWS WAF rules, AWS Shield Advanced protection, Amazon Virtual Private Cloud (VPC) security groups, AWS Network Firewalls, and Amazon Route 53 Resolver DNS Firewall rules across accounts and resources in your organization. It does not support Network ACLs as of today.

## Multi-AZ deployments, multi-region deployments, and read replicas

![image](https://user-images.githubusercontent.com/85909185/137125477-6cc1eb17-69e4-46dc-8d5e-4e3d18e3ddf6.png)
![image](https://user-images.githubusercontent.com/85909185/137125550-a268c7bb-0619-4a15-9aef-63fd75f4411a.png)
![image](https://user-images.githubusercontent.com/85909185/137125587-928f72bc-f267-4c7b-8959-3f901a092e61.png)


## Choosing between Redis and Memcached
![image](https://user-images.githubusercontent.com/85909185/137126129-218e4a0c-94a6-49f5-8c87-351da481b361.png)
![image](https://user-images.githubusercontent.com/85909185/137126150-c9b7ebae-f83d-47f0-a066-43caf1acc647.png)













