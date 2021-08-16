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

## Network

## Storage

## Security


