# AWS solution architect associate certificate preparation study notes

core concept: Region, availability zone. global services (route 53, IAM, cloudfront, WAF).

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

### EC2  Pricing
ID|Type|Scenarios
------ | ------- | ----------
1 | On demand | On-Demand Instances are ideal for short-term, irregular workloads that cannot be interrupted. No upfront costs or minimum contracts apply. The instances run continuously until you stop them, and you pay for only the compute time you use.On-Demand Instances are not recommended for workloads that last a year or longer because these workloads can experience greater cost savings using Reserved Instances.
2 | EC2 saving plans |  enable you to reduce your compute costs by committing to a consistent amount of compute usage for a 1-year or 3-year term. This term commitment results in savings of up to 66% over On-Demand costs.Any usage up to the commitment is charged at the discounted plan rate (for example, $10 an hour). Any usage beyond the commitment is charged at regular On-Demand rates.
3 | Reserved instances | Reserved Instances are a billing discount applied to the use of On-Demand Instances in your account. You can purchase Standard Reserved and Convertible Reserved Instances for a 1-year or 3-year term, and Scheduled Reserved Instances for a 1-year term. You realize greater cost savings with the 3-year option.
4 | Spot instances | Spot Instances are ideal for workloads with flexible start and end times, or that can withstand interruptions. Spot Instances use unused Amazon EC2 computing capacity and offer you cost savings at up to 90% off of On-Demand prices.
5 | Dedicated hosts | Dedicated Hosts are physical servers with Amazon EC2 instance capacity that is fully dedicated to your use. 

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

### Placement group
Sometimes you want control over the EC2 Instance placement strategy.
* Cluster—clusters instances into a low-latency group in a single Availability Zone  --on the same Rack, great network 10G, big data job, low latency and high network throughput.
* Spread—spreads instances across underlying hardware (max 7 instances per group per AZ)  --instances on different hardware, can across AZ and region, maximize high availability. 
* Partition—spreads instances across many different partitions (which rely on different sets of racks) within an AZ. Scales to 100s of EC2 instances per group (Hadoop, Cassandra, Kafka)  -up to 7 partitions per AZ, can span across AZ in the same region. up to 100 instances.instances in the same partition does not share racks. HDFS, HBase, Cassandra,Kafka

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

## Network
### Global Accelerator
AWS Global Accelerator - AWS Global Accelerator utilizes the Amazon global network, allowing you to improve the performance of your applications by lowering first-byte latency (the round trip time for a packet to go from a client to your endpoint and back again) and jitter (the variation of latency), and increasing throughput (the amount of time it takes to transfer data) as compared to the public internet.
AWS Global Accelerator optimizes the path to your application to keep packet loss, jitter, and latency consistently low.

With Global Accelerator, you are provided two global static public IPs that act as a fixed entry point to your application, improving availability. On the back end, add or remove your AWS application endpoints, such as Application Load Balancers, Network Load Balancers, EC2 Instances, and Elastic IPs without making user-facing changes. Global Accelerator automatically re-routes your traffic to your nearest healthy available endpoint to mitigate endpoint failure.

Global Accelerator improves performance for a wide range of applications over TCP or UDP by proxying packets at the edge to applications running in one or more AWS Regions. Global Accelerator is a good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP, as well as for HTTP use cases that specifically require static IP addresses or deterministic, fast regional failover.

while ELB provides load balancing within one Region, AWS Global Accelerator provides traffic management across multiple Regions.

###Cloudfront
Amazon CloudFront - Amazon CloudFront is a fast content delivery network (CDN) service that securely delivers data, videos, applications, and APIs to customers globally with low latency, high transfer speeds, all within a developer-friendly environment.

AWS Global Accelerator and Amazon CloudFront are separate services that use the AWS global network and its edge locations around the world. CloudFront improves performance for both cacheable content (such as images and videos) and dynamic content (such as API acceleration and dynamic site delivery), while Global Accelerator improves performance for a wide range of applications over TCP or UDP.

###Route 53
Amazon Route 53 - Amazon Route 53 is a highly available and scalable cloud Domain Name System (DNS) web service. It is designed to give developers and businesses an extremely reliable and cost-effective way to route end users to Internet applications by translating names like www.example.com into the numeric IP addresses like 192.0.2.1 that computers use to connect to each other.


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



###S3
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

####Unsupported lifecycle transitions
Amazon S3 does not support any of the following Lifecycle transitions.
You can't transition from the following:
*Any storage class to the S3 Standard storage class.
**Any storage class to the Reduced Redundancy storage class.
*The S3 Intelligent-Tiering storage class to the S3 Standard-IA storage class.
*The S3 One Zone-IA storage class to the S3 Standard-IA or S3 Intelligent-Tiering storage classes.


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

### How can users access AWS?
* AWS management console (protected by password + MFA)
* CLI: access keys
* SDK: access keys
* users manage their own access keys access key id == user name secret access key = password
* 

##Kinesis Data Streams (KDS)
 Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can continuously capture gigabytes of data per second from hundreds of thousands of sources such as website clickstreams, database event streams, financial transactions, social media feeds, IT logs, and location-tracking events. However, the user is expected to manually provision an appropriate number of shards to process the expected volume of the incoming data stream. The throughput of an Amazon Kinesis data stream is designed to scale without limits via increasing the number of shards within a data stream. Therefore Kinesis Data Streams is not the right fit for this use-case.
 
 
