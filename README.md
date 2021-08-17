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

## Network

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

### EC2 Instance Store
* EBS volumes are network drives with good but “limited” performance
* If you need a high-performance hardware disk, use EC2 Instance Store
* Better I/O performance
* EC2 Instance Store lose their storage if they’re stopped (ephemeral)
* Good for buffer / cache / scratch data / temporary content
* Backups and Replication are your responsibility

## Security


