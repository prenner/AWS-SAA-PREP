# AWS Certified Solutions Architect Study Guide

[Global Infrastructure](#global-infrastructure)  

[Networking](#networking)

[Compute](#compute)

[Storage](#storage)

[Databases](#databases)

[Analytics](#analytics)

[Security/Identity](#security--identity)

[Management Tools](#management-tools)

[Application Services](#application-services)

[Acronyms](#acronyms)


# Global Infrastructure

**Description** : global organization of resources in AWS.

**Regions** : geographical area consisting of 2 or more availability zones (12)

**Availability Zone** : a data center (depends on the region)
**Edge Location** : CDN endpoints for CloudFront (50+)

# Networking

**VPC (Virtual Private Cloud)**

**Brief Description:** virtual data centers in the cloud.

**Detailed Description:** allows you to provision a logically isolated section of the AWS Cloud where you can launch resources in a virtual network that you define. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways. You can easily customize the network configuration for your VPC. For example, you can create a public-facing subnet for your web servers that have access to the Internet, and place your backend systems such as databases or application servers in a private-facing subnet with no Internet access. You can leverage multiple layers of security, including security groups and network access control lists, to help control access to EC2 instances in each subnet. Additionally, you can create a Hardware Virtual Private Network (VPN) connection between your corporate datacenter and your VPC and leverage the cloud as an extension of your corporate datacenter.

**Critical Terms:**

- **Network Access Control List –** a virtual firewall for a subnet that allows you to put down network rules for an entire subnet. These rules overrule the rules of any security groups on your instances. It is a numbered list of rules that are evaluated in order (starting with the lowest numbered rule) to determine whether to allow traffic in and out of a subnet associated with that NACL.
- **Route Table –** a set of rules, called routes, which are used to determine where network traffic is directed. Each subnet in your VPC must be associated with a route table, and a subnet can only be associated with one route table at a time, but you can associate one route table with multiple subnets.

**What can you do with VPC?**

- Launch instances into a subnet of your choosing
- Assign custom IP address ranges in each subnet
- Configure route tables between subnets
- Create internet gateways and attach them to subnets (or not)
- Have better security control over your resources
- Use instance security groups
- Use subnet network access control lists (NACL)

**Default VPC vs Custom VPC:**

- Default VPC is user friendly, allowing you to immediately deploy instances
- All subnets in default VPC have an internet gateway attached, and each instance will have both a public and private IP
- If you delete the default VPC, the only way to get it back is to contact AWS.

**VPC Peering:**

- Allows you to connect one VPC with another via a direct network route using private IPs
- Instances behave as if they were on the same private network
- You can peer VPC&#39;s with other accounts as well as with other VPCs in the same account **within a single region.**
- Transitive peering is not allowed.
- AWS uses the existing infrastructure of a VPC to create a VPC peering connection; it is neither a gateway nor a VPN connection, and does not rely on a single piece of hardware. There is no single point of failure for communication, nor a bandwidth bottleneck.
- If two VPCs have an overlapping CIDR block, peering won&#39;t work.


**Exam Tips:**

- AWS recommends that you number your NACL rules in increments of 100 so that it&#39;s easy to add new ones in anywhere later.
- When you create a subnet, it comes with a default, modifiable NACL that allows all inbound and outbound traffic.
- You can also create your own custom NACLs, that by default deny all inbound and outbound traffic.
- All subnets must be associated with a NACL. If you don&#39;t associate one explicitly, the subnet will be associated with the default NACL.
- Understand the difference between the stateless NACLs and the stateful SGs:
  - **oo** With SGs, any traffic allowed in will allow responses out even if the response isn&#39;t explicitly allowed by a rule in the SG.
  - **oo** With NACLs, all outbound traffic is subject to the outbound traffic rules. Responses to traffic allowed in aren&#39;t automatically allowed out (and vice versa.)
- NACL&#39;s can span across multiple subnets, but each subnet can only be associated with one NACL.
- You can choose to have your VPC on dedicated servers, which means that any instances in that VPC, they will be on dedicated servers, even if you click that it should be on shared servers or multi-tenant hardware. It is more expensive.
- When you create a VPC, you automatically create a default route table.
- When using a NAT instance, remember to turn off source/destination checks. If you don&#39;t, the NAT won&#39;t be able to pass on requests to the instances in your private subnet.
- You can only have one internet gateway per VPC.
- You can only have 5 VPCs (and therefore 5 IGWs) per region.
- Each subnet is matched with one AZ. You cannot have a subnet span across multiple AZs.


**Direct Connect**

**Brief Description:** a way to connect into AWS without using the Internet.

nac

**Detailed Description:** makes it easy to establish a dedicated network connection from your premises to AWS. Using Direct Connect, you can establish private connectivity between AWS and your datacenter, office, or colocation environment, which in many cases can reduce your network costs, increase bandwidth throughout, and provide a more consistent network experience than Internet-based connections.

**Benefits:**

- Reduce costs when using large volumes of traffic
- Increased reliability
- Increased bandwidth

**Difference Between Direct Connect and VPN –** VPN connections can be configured in minutes and are a good solution if you have an immediate need, have low to modest bandwidth requirements, and can tolerate the inherent variability in Internet-based connectivity. Direct Connect does not involve the Internet; instead, it uses dedicated, private network connections between your intranet and your VPC.

**Types of Connections:**

- 10 Gbps
- 1 Gbps
- \&lt; 1 Gbps can be purchased through AWS Direct Connect partners


**Route53**

**Description:** AWS&#39;s DNS service.

**Exam Tips:**

- ELB&#39;s do not have pre-defined public IPv4 addresses. They have DNS endpoints that you can use to resolve to them.
- Understand the difference between an alias record and a CNAME:
  - **oo**** Alias Record –** nly exists within AWS. Used to map resource record sets in your hosted zone to ELBs, CloudFront distributions, or S3 buckets that are configured as websites. Alias records work like CNAMEs in that you can map one DNS name (www.example.com) to another target DNS name (elb1234.elb.amazonaws.com). They save you time because Route53 automatically recognizes changes in the record sets that the alias resource record set refers to. (In other words, if the IP address of an ELB changes, Route53 will automatically reflect those changes in DNS answers for &#39;www.example.com&#39; without any changes to the hosted zone that contains the resource record sets.)
  - **oo**** CNAME –** canonical name. Can be used to resolve one domain name to another. (Like &#39;http://m.example.com&#39; to &#39;http://mobile.example.com&#39;.)
- You are billed for CNAME requests to Route53, but not for alias record requests.
- When you have a naked domain name (which is just a web URL without the &#39;www&#39;. Also known as a &#39;zone apex record&#39;) you want to use an alias record, because you can&#39;t use a CNAME on the naked domain name.
- Given the choice, always choose &#39;alias record&#39; over &#39;CNAME&#39;.


**Routing Policies:**

- **Simple –** default policy when you create a new record set. This is most commonly used when you have a single resource that performs a given function for your domain. For example, one web server that serves a site. There is no intelligence built in to the response.


- **Weighted –** allows you to split your traffic based on different weights assigned. For example, you could direct 10% of your traffic to one AZ and 90% to another.


- **Latency –** allows you to route your traffic based on the lowest network latency for your end user (i.e. which region will give them the fastest response time.**)** To use this policy, you create a latency resource record set for the EC2 / ELB in each region that hosts your website. When Route53 receives a query for your site, it selects the latency resource record set for the region that gives the user the lowest latency. Route53 then responds with the value associated with that resource record set.


- **Failover –** used when you want to create an active / passive set up. For example you may want your primary site to be one AZ and your secondary disaster recovery site in another. With this policy, Route53 will monitor the health of your primary site using a health check. The health check monitors the health of your end points.


- **Geolocation –** allows you to choose where your traffic will be sent based on the geographic location of your users (i.e. the locations from which the DNS queries originate.) For example, you might want all queries from Europe to be routed to a fleet of EC2 instances that are specifically configured form your European customers. These servers may have the local language of your European customers and all prices are displayed in Euros.


# Compute

**EC2 (Elastic Cloud Compute)**

**Description** : virtual servers in the cloud.

**Detailed Description:** EC2 is a web service that provides resizable compute capacity in the cloud. It reduces the time required to obtain and boot new server instances to minutes, allowing you to quickly scale capacity, both up and down, as your computing requirements change. It changes the economics of computing by allowing you to pay only for capacity that you actually use. EC2 provides developers the tools to build failure resilient applications and isolate themselves from common failure scenarios.

**Critical Terms:**

- **Elastic Load Balancer (ELB) –** automatically distribute incoming application traffic across multiple EC2 instances in the cloud. It enables you to achieve greater levels of fault tolerance in your applications, seamlessly providing the required amount of load balancing capacity needed to distribute application traffic.
- **Auto-scaling Group (ASG) –** contains a collection of EC2 instances that share similar characteristics and are treated as a logical grouping for the purposes of instance scaling and management.
- **Launch Configuration –** template that an Auto Scaling group uses to launch EC2 instances. When you create a launch configuration, you specify information for the instances such as the ID of the Amazon Machine Image (AMI), the instance type, a key pair, one or more security groups, and a block device mapping.
- **Elastic Block Store (EBS) –** allows you to create storage volumes and attach them to EC2 instances. Once attached, you can create a file system on top of these volumes, run a database, or use them in any other way you would use a block device. EBS volumes are placed in a specific AZ, where they are automatically replicated to protect you from the failure of a single component (but not the failure of the entire AZ.)
- **Volume --  ** virtual hard disk that exists on EBS
- **Snapshot –** point-in-time copies of EBS volumes that are stored in S3
  - **oo** Snapshots are incremental (only the blocks that have changed since your last snapshot are moved to S3.)
  - **oo** Initial snapshots may take some time to create.
  - **oo** Snapshots of encrypted volumes are encrypted automatically
  - **oo** Volumes restored from encrypted snapshots are encrypted automatically.
  - **oo** You can share snapshots, but only if they are unencrypted. They can be shared with other AWS accounts or be made public.
- **Root Device Volume –** the EBS volume that is attached to an instance when it is first created.
  - **oo** To create a snapshot of a root device volume, first stop the instance.
- **Security Group –** acts as a virtual firewall for instances, to control inbound and outbound traffic.
  - **oo** All inbound traffic is blocked by default
  - **oo** All outbound traffic is allowed by default
  - **oo** Changes to SG&#39;s take effect immediately
  - **oo** SG&#39;s can apply to any number of EC2 instances
  - **oo** SG&#39;s are stateful (if you create an inbound rule allowing traffic in, that traffic is automatically allowed back out)
  - **oo** SG&#39;s act at the instance level (as opposed to subnet level.)
- **Amazon Machine Image (AMI) –** provides the information required to launch a virtual server in the cloud. You specify an AMI when you launch an instance, and you can launch as many instances from the AMI as you need. You can also launch instances from as many different AMIs as you need. Basically, it is a template for an EC2 instance.
  - **oo** Consists of three components:
    - A template for the root volume for the instance (i.e., an OS, an application server, and applications)
    - Launch permissions that control which AWS accounts can use the AMI to launch instances
    - A block device mapping that specifies the volumes to attach to the instance when it&#39;s launched
  - **oo** An **AMI** is simply a **snapshot** of a **root device volume**.
  - **oo** AMIs are regional. You can only launch from an AMI into the region in which it is stored. However, you can copy AMIs to other regions using the console, CLI, or AWS EC2 API.
  - **oo** There are two types of AMIs, based on the type of storage for the root device:
    - **EBS backed –** the root device for an instance launched from the AMI is an EBS volume created from an EBS snapshot
      - Can be stopped. You will not lose the data on this instance if it is stopped.
    - **Instance store (ephemeral) –** the root device for an instance launched from the AMI is an instance store volume created from a template stored in S3
      - Take longer to provision
      - Cannot be stopped. If the underlying host fails, you will lose your data.
    - With both types, you can reboot, and you will not lose your data.
    - By default, both root volumes will be deleted on termination, but with EBS backed, you can instruct AWS to keep the root device volume.
  - **oo** You can select your AMI based on a variety of factors, such as region, OS, architecture (32 bit vs 64 bit), launch permissions, and the type of storage for the root device
- **Placement Group –** a logical grouping of instances within a single AZ. Using placement groups enables applications to participate in a low-latency, 10 Gbps network. Placement groups are recommended for applications that benefit from low network latency, high network throughput, or both.
  - **oo** Cannot span multiple AZs
  - **oo** The name you specify for a PG must be unique within your account
  - **oo** Only certain types of instances can be launched in a placement group (Compute Optimized, GPU, Memory Optimized, Storage Optimized)
  - **oo** AWS recommends homogenous instances within PGs
  - **oo** Can&#39;t merge PGs
  - **oo** Can&#39;t move an existing instance into a PG. (But you can create an AMI from an existing instance and then launch a new one into a PG.)
- **Elastic IP address (EIP) –** static IP address designed for dynamic cloud computing. An Elastic IP address is associated with your AWS account. With an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account. An Elastic IP address is a public IP address, which is reachable from the Internet. If your instance does not have a public IP address, you can associate an Elastic IP address with your instance to enable communication with the Internet; for example, to connect to your instance from your local computer.
- **EC2 Compute Unit (ECU) –** provides the relative measure of the integer processing power of an instance.
- **Bastion Host** – a computer specifically configured to withstand attack, usually placed on the external/public side of a demilitarized zone (DMZ) or outside the firewall. You can set up an EC2 instance as an SSH bastion by setting up a public subnet as part of a VPC.

**RAID Arrays**

- **RAID 0 –** striped, no redundancy, good performance
- **RAID 1 –** mirrored, redundancy
- **RAID 5 –** good for reads, bad for writes, AWS does not recommend ever putting RAID 5&#39;s on EBS
- **RAID 10 –** striped &amp; mirrored, good redundancy, good performance
- Taking a snapshot of a RAID array with multiple volumes can be problematic because the snapshot will exclude data held in the cache by applications and the OS, and there may be interdependencies across the array. The solution is to take an application consistent snapshot. This means stopping the application from writing to disk, and flushing all caches to the disk. There are three ways to achieve this:
  - Freeze the file system
  - Unmount the RAID array
  - Stop the associated EC2 instance (easiest)

**Exam Tips:**

- ELBs and ASGs go together like bread and butter. ASGs scale your EC2 instances up and down to meet demand; ELBs distribute traffic evenly across the current collection of EC2 instances in the ASG.
- In order to have an ELB distribute traffic evenly across multiple AZs, enable Cross-Zone Load Balancing when creating the ELB.
- Remember that SSH uses TCP, not UDP.
- Things to remember about encrypting EBS volumes:
  - **oo** Encryption is supported on all EBS volume types.
  - **oo** Not all EC2 instance types support use of encrypted volumes.
  - **oo** There is no direct way to encrypt an existing unencrypted volume, or to remove encryption from an encrypted volume.
- Know the difference between the following instance options:
  - **oo**** On Demand –** allow you to pay a fixed rate by the hour with no commitment.
    - low cost and flexibility without up-front payment or long-term commitment
    - applications with short term, spiky, or unpredictable workloads that cannot be interrupted
    - applications being developed or tested on EC2 for the first time
  - **oo**** Spot –** enable you to bid whatever price you want for instance capacity, providing for even greater savings if your applications have flexible start and end times.
    - applications only feasible at very low compute prices
    - users with urgent computing needs for large amounts of additional capacity
  - **oo**** Reserved –** provide you with a capacity reservation, and offer a significant discount on the hourly charge for an instance. Must purchase in 1 year or 3 year terms.
    - applications with steady state or predictable usage
    - applications that require reserved capacity
    - users able to make upfront payments to reduce their total computing costs even further
    - You can have multi-AZ deployments of reserved instances.
- Remember with spot instances:
  - **oo** If you terminate the instance, you pay for that hour.
  - **oo** If AWS terminates the spot instance, you get the hour it was terminated in for free.
- EBS consists of three different types:
  - **oo** General Purpose SSD – GP2 (Up to 10,000 IOPS)
    - Designed for 99.999% availability
    - Ration of 3 IOPS per GB with up to 10,000 IOPS and the ability to burst up to 3000 IOPS for short periods of volumes under 1Gib.
  - **oo** Provisioned IOPS SSD – IO2 (More than 10,000 IOPS)
    - Designed for I/O intensive applications such as large relational or NoSQL DBs.
  - **oo** Magnetic – cheap, infrequently accessed storage
    - Lowest cost per gigabyte of all EBS volume types. Magnetic volumes are ideal for workloads where data is accessed infrequently and for applications where the lowest storage cost is important.
- You cannot mount one EBS volume to multiple EC2 instances. If you need to do that, use EFS instead.
- Root volumes cannot be encrypted by default. You need a third party tool (such as BitLocker) to encrypt the root volume.
- To get the metadata for a given EC2 instance (i.e. public IP), curl the following URL: [http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/) . This will give you a list of categories you can query by appending them to the end of the above URL.
- There is no such thing as user-data for an instance.
- Termination Protection on EC2 instances is turned off by default. You must turn it on if you want to use it.
- Xen is the underlying Hypervisor for EC2.

**EC2 Instance Types:**

| **Family** | **Specialty** | **Use cases** |
| --- | --- | --- |
| **T2** | Lowest cost, general purpose | Web servers / small DBs |
| **M4** | General purpose | Application servers |
| **M3** | General purpose | Application servers |
| **C4** | Compute optimized | CPU intensive apps / DBs |
| **C3** | Compute optimized | CPU intensive apps / DBs |
| **R3** | Memory optimized | Memory intensive apps / DBs |
| **G2** | Graphics / general purpose GPU | Video encoding / machine learning / 3D application streaming |
| **I2** | High speed storage | NoSQL DBs, data warehousing, etc. |
| **D2** | Dense storage | Fileservers / data warehousing / Hadoop |

**Mnemonic Device for Remembering the Different Instance Types:**

- **D** for Density
- **I** for IOPS
- **R** for RAM
- **T** cheap general purpose, like the T2 micro
- **M** is the main choice for general purpose apps
- **C** for Compute
- **G** for Graphics

**Elastic Beanstalk**

**Description** : easy-to-use service for deploying web applications written with common frameworks.



**Lambda**

**Brief Description** : allows you to run code without provisioning servers.

**Detailed Description:** compute service where you can upload your code and create a Lambda function. Lambda takes care of provisioning and managing the servers that you use to run the code. You don&#39;t have to worry about OSs, patching, scaling, etc. You can use Lambda in the following ways:

- Event-driven compute service where Lambda runs your code in response to events (i.e. changes to data in an S3 bucket or a DynamoDB table, etc.)
- Compute service to run your code in response to HTTP requests using API Gateway or API calls made using AWS SDKs.

**Exam Tips:**

- You only have to worry about your code – you don&#39;t have to worry about anything underneath it, including the application layer, operating system, or hardware.
- Lambda supports Node.js, Java, and Python
- Lambda is priced based on the number of requests and duration of the execution of your code. You don&#39;t have to pay for time your code isn&#39;t running, as you do with EC2 instances.
- Lambda is great because it is serverless, cheap, and scales automatically.


# Storage

**S3 (Simple Storage Service)**

**Description** : object-based storage for flat files in the cloud

**Exam Tips:**

- **S3** is object-based; allow you to upload files, but not install an OS or DB.
- Files can be between 0 bytes and 5TB.
- The largest object that can be uploaded in a single PUT is 5 gigabytes. For objects larger than 100 megabytes, customers should consider using the Multipart Upload capability.
- Unlimited storage
- Files are stored in buckets, which are basically folders
- **S3** is a universal namespace; bucket names must be unique globally.
- Bucket URL format: https://s3-{region}.amazonaws.com/{bucket}
- Example URL: [https://s3-eu-west-1.amazonaws.com/acloudguru](https://s3-eu-west-1.amazonaws.com/acloudguru)
- You can host static websites on S3
  - Static S3 site URL format: https://{bucket}.s3-website-{region}.amazonaws.com
  - Example URL: [https://acloudguru.s3-website-eu-west-1.amazonaws.com](https://acloudguru.s3-website-eu-west-1.amazonaws.com)



**Versioning:**

- Must be manually enabled on a per-bucket basis.
- Allows you to store all versions of an object, including all writes and even deletes. You pay for each version.
- Great backup tool.
- Once enabled, versioning cannot be disabled, only suspended.
- Integrates with lifecycle rules
- MFA Delete capability can be used to provide an additional layer of security.
- Cross Region Replication requires versioning to be enabled on the source bucket.

**Lifecycle Management:**

- Can be used with versioning.
- Can be applied to current and previous versions.
- Following actions supported:
  - **oo** Transition to the **S3 IA** (must be at least 128KB and at least 30 days after creation date)
  - **oo** Archive to **Glacier** (at least 30 days if after moving to **S3 IA** ; no limit if moving straight from standard)
  - **oo** Permanently delete

**Consistency Models:**

- Read after Write consistency for PUTS of new objects (can read immediately after writing)
- Eventual consistency for overwrite PUTS and DELETES (can take some time to propagate)

**Storage Classes/Tiers:**

- **S3 Standard –** durable, immediately available, frequently accessed
- **S3 IA (Infrequently Accessed) –** durable, immediately available, infrequently accessed, cheaper than S3 Standard **.**
- **S3 RRS (Reduced Redundancy Storage) –** data that is easily reproducible.
- **Glacier –** archived data, where you can wait 3-5 hours before accessing

|   | **Availability** | **Durability** |
| --- | --- | --- |
| **Standard** | 99.99% | 99.999999999% |
| **IA** | 99.99% | 99.999999999% |
| **RRS** | 99.99% | 99.99% |
| **Glacier** | N/A | 99.999999999% |



**Core Attributes of an object in S3:**

- **Key –** name

- **Value –** data
- **Version ID**
- **Metadata**
- **Access control lists**

**Security:**

- By default, all newly created buckets are private.
- You can setup access control to buckets using the following:
  - Bucket Policies (bucket level)
  - Access Control Lists (object level)
    - To identify an AWS account to grant access to S3, use the e-mail address associated with the account or the canonical user ID.
- S3 buckets can be configured to create access logs which log all requests made to the bucket (logs can be directed to another bucket in your account or in another account.)
- In transit encryption uses SSL/TLS (in other words, you must use HTTPS)
- At rest encryption:
  - Server side encryption:
    - S3 managed keys (SSE-S3). Each object is encrypted with a unique key using MFE. As an additional safeguard, it encrypts the key itself with a master key that regularly rotates. This is AES-256 (Advanced Encryption Standard) and AWS does it themselves.
    - AWS KMS (SSE-KMS) is similar to SSE-S3 but there are some additional benefits including cost, separate permissions for the use of an envelope key (a key that protects your data&#39;s encryption key) which provides additional protection against unauthorized access of S3 objects. Also allows you to have an audit trail of when your key was used and by whom.
    - Encryption with customer-provided keys (SSE-C.) This is where you managed the encryption keys yourself, and S3 manages the actual encryption as it writes to disk and decryption when you read
  - Client side encryption – encrypt the data yourself, and when you upload it to S3, it is treated as encrypted data.

**CloudFront**

**Description** : AWS&#39;s CDN service.

**Critical Terms:**

- **Content Delivery Network –** a system of distributed servers (network) that delivers webpages and other web content to a user based on the geographic location of the user, the origin of the webpage, and a content delivery server (edge location).
- **Edge Location –** the location where content will be cached. Separate from a region or AZ.
- **Origin –** the origin of all files that the CDN will distribute (i.e. S3 bucket, EC2 instance, ELB, or Route53.)
- **Distribution –** name given to the CDN which consists of a collection of edge locations.
  - **Web distribution –** used for websites.
  - **RTMP –** used for media streaming (in particular, for Adobe Flash.)


**Exam Tips:**

- Edge locations are not just for reading; you can write to them too. The data written will be replicated back up to the origin server.
- Objects are cached for the TTL (time to live.) Default is 24 hours.
- There is a charge to clear cached objects.

**Without a CDN:**


**With a CDN:**


**Glacier**

**Description** : secure, durable, and extremely low cost storage solution for data archiving and long-term backup. Very cheap, but used for archival only. Stores data for as little as $0.01 per gigabyte per month, but retrieval times are between 3 and 5 hours. (See the S3 section for more information.)



**EFS (Elastic File System)**

**Brief Description** : block-level file storage for EC2 instances.

**Detailed Description:** file storage service for EC2. Easy to use and provides a simple interface that allows you to create and configure file systems quickly and easily. With EFS, storage capacity is elastic, growing and shrinking automatically as you add and remove files, so your applications have the storage they need, when they need it.

**Exam Tips:**

- Supports the Network File System version 4 (NFSv4) protocol
- You only pay for the storage you use (no pre-provisioning required)
- Can scale up to the petabytes
- Can support thousands of concurrent NFS connections
- Data is stored across multiple AZs within a region
- Read After Write consistency



**Snowball**

**Description** : allows you to rent a Snowball (a large hard disk product developed by AWS), load data onto it, send it to Amazon, and they will load the data into AWS. Bypasses Internet. Petabyte scale data transfer solution.

**Import / Export Disk:** is the deprecated method of Import / Export in which you could send in your own hard disks, as opposed to the Snowball product created by AWS, to load your data into AWS without going through the Internet. At this point in time, AWS recommends that you always choose Snowball over Import / Export Disk.

**Exam Tips:**

- Import / Export Disk supports data movement to S3, EBS, and Glacier, but only supports data retrieval from S3.
- Snowball only supports data movement and retrieval to and from S3.



**Storage Gateway**

**Brief Description:** service used to backup on-premises data to AWS storage services such as S3 or Glacier.

**Detailed Description** : service connecting an on-premise software appliance with cloud-based storage to provide seamless and secure integration between an organization&#39;s on-premise IT environment and AWS&#39;s storage infrastructure. It allows you to securely store data in AWS for scalable and cost-effective storage. The software appliance is available for download as a virtual machine that you install on a host in your datacenter. Once it is installed, you can select a Storage Gateway option that is right for you (i.e. S3 versus Glacier)




**Three Types of Storage Gateways:**

- **Gateway Stored Volumes –** keep your entire data set on-premise, and back it all up asynchronously to AWS. Provides durable and inexpensive offsite backups that you can recover locally or to EC2.
- **Gateway Cached Volumes –** nly your most frequently accessed data is stored locally. Your entire data set is stored on AWS. You don&#39;t have to buy large SAN arrays for your office/data center, so you can get significant cost savings. If you lose Internet connectivity, however, you lose access to all of your data.
- **Gateway Virtual Tape Library –** you can have a limitless collection of virtual tapes. Each virtual tape can be stored in a Virtual Tape Library (S3) or a Virtual Tape Shelf (Glacier). The Virtual Tape Library exposes an industry standard iSCSI interface which provides your backup application with online access to the virtual tapes. Supported by NetBackup, Backup Exec, Veam, etc.


# Databases

**RDS (Relational Database Service)**

**Description** : consists of a number of popular databases (i.e. MySQL, PostgreSQL, and MariaDB).

**Backups:**

- Two types of backups for RDS:
  - **Automated Backups** – allow you to recover your DB to any point in time within a retention period, which can be between 1 and 35 days. Automated backups will take a full daily snapshot and will also store transaction logs throughout the day. When you do a recovery, AWS will first choose the most recent daily backup, and then apply transaction logs relevant to that day. This allows you to do a point in time recovery down to a second, within the retention period. They are enabled by default. The backup data is stored in S3 and you get free storage space equal to the size of your DB. Backups are taken within a defined window. During the backup window, storage I/O may be suspended while your data is being backed up, and you may experience elevated latency (so it&#39;s a good idea to set the backup window to a time when the DB won&#39;t be used heavily, like 3AM.)
  - **DB Snapshots** – taken manually (user initiated.) They continue to exist even after the RDS instance is deleted, unlike automated backups.
  - With both types of backups, restored versions of the DB will use a new RDS instance with a new endpoint.

**Encryption –** at rest encryption is supported for MySQL, Oracle, SQL Server, PostgreSQL, and MariaDB. Encryption is done using KMS. Once your RDS instance is encrypted, the data stored at rest in the underlying storage is encrypted, as are automated backups, read replicas, and snapshots. At present time, **encrypting an existing DB instance is not supported**. Instead, you must create a new instance with encryption enabled, and migrate your data to it.



**Multi-AZ Deployment –** allows you to have an exact copy of your DB in another AZ. AWS handles the replication for you, so when your DB is written to, the write will automatically be synchronized to the standby DB. In the event of planned DB maintenance, instance failover, or an AZ failure, RDS will automatically failover to the standby so that DB ops can resume quickly without administrative intervention. **It is for disaster recovery only. It is not for improving performance.** You can use read replicas to improve read performance. Available for SQL Server, Oracle, MySQL Server, PostgreSQL, and MariaDB (Aurora has multiple copies by default.). usually for prod!!!


**Read Replicas –** allow you to have a read-only copy of your DB. This is achieved by using asynchronous replication from the primary RDS instance to the read replica. You would use read replicas primarily for very read-heavy workloads. Available for MySQL Server, PostgreSQL, and MariaDB (and not for SQL Server or Oracle.)


**About Aurora:**

- It stores 2 copies of your data are contained in each AZ, with a minimum of 3 AZs (so at least 6 copies of your data.)
- It is designed to transparently handle the loss of up to 2 copies of data without affecting the DBs write availability and up to 3 copies without affecting read availability.
- It is self-healing. Data blocks and disks are continuously scanned for errors and repaired automatically.
- It has 2 types of read replicas:
  - Aurora replicas (up to 15)
  - MySQL read replicas (up to 5)

**Exam Tips:**

- All RDS DBs are SQL based (they operate on rows and tables.)
- RDS DBs use OLTP, which means storage is row-based (contiguous data are distinct records, or rows.)
- RDS supports SQL, MySQL, PostgreSQL, Oracle, Aurora, and MariaDB.
- Aurora is AWS&#39;s in-house DB solution and they&#39;re pretty fond of it (so, when all else fails, pick it over other DB options.)
- Understand the difference between read replicas and multi-AZ deployments.
  - Multi-AZ deployments are for disaster recovery only, NOT scaling.
  - You must have automatic backups turned on in order to deploy a read replica.
  - You can have up to 5 read replica copies of a DB.
  - You can have read replicas of read replicas, but watch out for latency (the data will first be synced to the first RR, and then would need to be synced over to the RR of the RR.)
  - Each RR has its own DNS endpoint.
  - You cannot have RRs that have Multi-AZ. However, you can have RRs of Multi-AZ source DBs.
  - RRs can be promoted to be their own DB, but this will break the replication.
  - You cannot write to a RR!
  - You can have an RR in a second region, unless the DB is PostgreSQL.
- The maximum number of days for backup retention period is 35 days.
- Automated backups are enabled by default for new RDS instances.
- Increasing storage size on a SQL Server instance is not supported.
- MySQL installations default to port 3306.
- You **do** specify an AZ for RDS



**DynamoDB**

**Brief Description** : AWS&#39;s non-relational NoSQL database service.

**Detailed Description:** Fast and flexible NoSQL DB service for all applications that need consistent, single-digit millisecond latency at any scale. It is a fully managed DB and supports both document and key-value data models. Its flexible data model and reliable performance make it a great fit for mobile, web, gaming, ad-tech, IoT, and many other applications.

**Pricing Model:**

- **Capacity Unit** – writes (or reads) per second
- Provisioned Throughput Capacity
  - Write throughput $0.0065 per hour for every 10 write capacity units
  - Read throughput $0.0065 per hour for every 50 read capacity units
- Storage costs $0.25 / Gb / month.
- Capacity Unit Example:
  - Your application needs to perform 1 million writes and 1 million reads per day
  - Calculate the number of capacity units you need
  - 1,000,000 writes / 24 hours / 60 minutes / 60 seconds = 11.6 writes per second = 12 write capacity units
  - Same number of read capacity units
  - Cost for writes per day = ($0.0065 / hour / 10 write capacity units) \* 12 write capacity units \* (24 hours / day) = $0.1872
  -  Cost for reads per day = ($0.0065 / hour / 50 write capacity units) \* 12 write capacity units \* (24 hours / day) = $0.0374

**Exam Tips:**

- DynamoDB is a NoSQL DB like MongoDB (they operate on documents and collections.)
- Offers &quot;push button scaling&quot;, which means that you can scale your DB on the fly, without any downtime. This is in contrast to RDS, where you would need to use a bigger instance size or add a read replica.
- Stored on SSD storage
- Spread across 3 geographically distinct data centers
- Automatically replicated across multiple AZs.
- Eventual Consistent Reads is the default consistency model (consistency across all copies of data is usually reached within a second. Repeating a read after a short time should return the updated data.)
- Strongly Consistent Reads (returns a result that reflects all writes that received a successful response prior to the read) is also supported, if you need data written to the DB to be available to your application in less than a second.
- You **don&#39;t** specify an AZ for a new DB.



**Elasticache**

**Brief Description** : a way of caching your database services in the cloud. Relieves stress on RDS.

**Detailed Description:** web service that makes it easy to deploy, operate, and scale an in-memory cache in the cloud. The service improves the performance of web applications by allowing you to retrieve information from fast, managed, in-memory caches, instead of relying entirely on slower disk-based DBs. Used to significantly improve latency and throughout for many read-heavy application workloads (such as social networking, gaming, media sharing, and Q&amp;A portals) or compute-intensive workloads (such as a recommendation portal.) Caching improves application performance by storing critical pieces of data in memory for low-latency access. Caching information may include the results of I/O intensive DB queries or the results of computationally-intensive calculations.

**Exam Tips:**

- Uses two different open-source in-memory caching engines:
  - **Memcached** – a widely adopted memory object caching system. Elasticache is protocol compliant with Memcached, so popular tools that you use today with existing Memcached environments will work seamlessly with the service.
  - **Redis –** popular open-source in-memory key-value store that supports data structures such as sorted sets and lists. Elasticache supports master / slave replication and Multi-AZ which can be used to achieve cross AZ redundancy.
- If you&#39;re presented with a scenario in which a DB is under a lot of stress / load, and you&#39;re asked which service you should use to alleviate this, Elasticache is a good choice if your DB is particularly read-heavy and not prone to frequent changing. (Redshift is a good answer if your DB is feeling stress because management keeps running OLAP transactions on it.)



**Redshift**

**Brief Description** : AWS&#39;s ODAP / data warehousing service.

**Detailed Description:** fast, powerful, fully-managed, petabyte-scale data warehousing service in the cloud. Customers can start small for just $0.25 per hour with no commitments or upfront costs and scale to a petabyte or more for $1000 per TB per year (less than a tenth of most other data warehousing solutions.)

**Exam Tips:**

- Redshift use OLAP, which means storage is columnar (contiguous data are distinct columns of data for different records.)
- Advanced compression! Columnar databases can be compressed much more than row-based data because similar data is stored sequentially on the disk.
- Massively Parallel Processing (MPP) – Redshift automatically distributes data and query load across all nodes. It is easy to add more nodes if necessary.
- Used for data warehousing and business intelligence
- Two different types of configuration:
  - Single node (160 Gb) for small / medium businesses.
  - Multi-node
    - Leader node manages client connections and receives queries
    - Compute nodes store data and perform queries and computations (up to 128)



**DMS (Database Migration Service)**

**Description** : a way of migrating your databases into AWS with some conversion utilities.

# Analytics

**EMR (Elastic MapReduce)**

**Description** : Big Data processing

**Exam Tips:**

- You can SSH into your EMR clusters.



**Data Pipeline**

**Description** : a way of moving data from one service to another.



**Kinesis**

**Brief Description** : platform for streaming data on AWS

**Detailed Description:** fully managed service for real-time processing of streaming data at a massive scale. You can configure hundreds of thousands of data producers to continuously put data into a Kinesis stream. For example, data from website clickstreams, application logs, and social media feeds. Within less than a second, the data will be available for your Kinesis applications to read and process from the stream.

**Exam Tips:**

- Used to consume big data
- Stream large amounts of social media, news feeds, logs, etc., into the cloud
- If you see phrases like &quot;streaming&quot;, &quot;getting big data into the cloud&quot;, or &quot;consuming big data&quot;, think Kinesis.
- If you see phrases like &quot;processing large amounts of data&quot;:
  - Think Redshift if you see &quot;business intelligence&quot; or data warehousing&quot;
  - Think EMR if you see &quot;processing big data.&quot;

# Security &amp; Identity

**IAM (Identity &amp; Access Management)**

**Description** : control users, groups, and roles.

**Critical Terms:**

- **Users –** end users (think people)
- **Groups –** a collection of users under one set of permissions
- **Roles –** assigned to AWS resources
- **Policies –** a document that defines one or more permissions. Are attached to users, groups, and roles. Written in JSON.

**Exam Tips:**

- IAM is universal; does not apply to regions
- The &quot;root account&quot; is the user created when first setting up an AWS account. It is the only user that has complete admin access by default.
- New users have NO permissions when first created.
- New users are assigned an access key ID and secret access key when first created.
- Access key IDs and secret access keys are not passwords; you can&#39;t log into the console with them. They are used to access AWS via the APIs and the command line.
- You can only view the access key ID and secret access key once, so save them in a secure location.
- You should always set up MFA on the root account.
- You can create and customize password rotation policies for users.
- Roles are more secure than storing your access key and secret access key on individual EC2 instances.
- Roles are easier to manage
- Roles can only be assigned to an EC2 instance when it is being provisioned
- Roles are universal; you can use them in any region
- Use IAM to enable MFA



**Directory Service**

**Description** : makes it easy to setup and run Microsoft Active Directory in the cloud, or connect an existing on-premises AD.

**Exam Tips:**

- When you use AD / SSO to log in, you&#39;re given a temporary security credential that enables you access the console.
- The temporary credential is a SAML (Secure Assertive Markup Language) assertion.

**KMS (Key Management Service)**

**Description** : managed service that makes it easy to create and control the encryption keys used to encrypt your data.

**For more information, see the encryption sections of S3 and RDS.**

# Management Tools

**CloudWatch**

**Description** : performance monitoring service for AWS resources.

**Exam Tips:**

- Standard monitoring – 5 minutes
- Detailed monitoring – 1 minute
- Dashboards – create awesome dashboards to see what is happening with your AWS environment
- Alarms – allows you to create that notify you when certain thresholds are hit
- Events – help you respond to state changes in your AWS resources
- Logs – help you to aggregate, monitor, and store logs
- Not to be confused with CloudTrail (which is for auditing)
- Must enable memory-based CloudWatch alarms manually



**CloudFormation**

**Brief Description** : Infrastructure as code

**Detailed Description:** Your infrastructureconsisting of various AWS resources can be described as code, which you can then launch with the push of the button. Then you can apply things like version control to your templates, so that you can continuously improve your infrastructure. It is one of the most powerful AWS services.

**CloudTrail**

**Description** : a way of auditing actions on AWS.

**Exam Tips:**

- Regional service



**OpsWorks**

**Description** : configuration management service that uses Chef.

**Exam Tips:**

- Orchestration service that uses Chef.
- Chef consists of recipes used to maintain a consistent state on servers.
- If you see &quot;chef&quot;, &quot;recipes&quot;, or &quot;cookbooks&quot;, think OpsWorks.



**Trusted Advisor**

**Description** : automated service that scans your environment and tells you ways you can increase security and save money.

# Application Services

**CloudSearch**

**Description** : managed service to set up, manage, and scale a search solution, with support for popular search features.



**Elastic Transcoder**

**Brief Description** : media transcoding service in the cloud.

**Detailed Description:** converts media files from their original source format in to different formats that will play on smartphones, tablets, PCs, etc. Provides transcoding presets for popular output formats, which means that you don&#39;t need to guess about which settings work best on particular devices. Pay based on the minutes that you transcode and the resolution at which you transcode.



**SES (Simple Email Service)**

**Description** : E-mail service that allows you to automate sending different kinds of high quality e-mails



**SQS (Simple Queue Service)**

**Brief Description** : a way of decoupling infrastructure by sending / pulling messages to queues. First service ever!

**Detailed Description:** gives you access to a message queue that can be used to store messages while waiting for a computer to process them. Is a distributed queue system that enables web service applications to quickly and reliably queue messages that one component in the application generates to be consumed by another component. A queue is a temporary repository for messages that are awaiting processing. You can decouple the components of an application so they run independently, with SQS easing message management between components. Any component of a distributed application can store messages in a fail-safe queue. Messages can contain up to 256 KB of text in any format. Any component can later retrieve the messages programmatically using the SQS API. The queue acts as a buffer between the component producing and saving data, and the component receiving the data for processing. This means the queue resolves issues that arise if the producer is producing work faster than the consumer can process it, or if the producer or consumer is only intermittently connected to the Internet. SQS ensures delivery of each message at least once, and supports multiple readers and writers interacting with the same queue. A single queue can be used simultaneously by many distributed application components, with no need for those components to coordinate with each other to share the queue. SQS is engineered to always be available and deliver messages. One of the resulting tradeoffs is that SQS does not guarantee FIFO delivery of messages. For many distributed applications, each message can stand on its own, and as long as all messages are delivered, the order is not important. If your system requires that order be preserved, you can place sequencing information in each message, so that you can reorder the messages when the queue returns them.

**Pricing Model:**

- First 1 million requests per month are free
- $0.50 per 1 million requests per month thereafter
- A single API call can have from 1 to 10 messages, up to a total maximum payload of 256kb.
- Each 64kb &#39;chunk&#39; of a payload is billed as 1 request. So a single API call with a 256kb payload will be billed as four requests.

**Exam Tips:**

- SQS never pushed messages out. You must have a system polling the queue and pulling messages down.
- SQS does not guarantee FIFO.
- You can autoscale based on queue size.
- 12 hour visibility timeout
- SQS is engineered to provide &quot;at least once&quot; delivery of all messages in its queues. Although most of the time each message will be delivered exactly once, you should design your system so that processing a message more than once does not create any errors or inconsistencies.
- Anytime you see the word &quot;decouple&quot;, think SQS.

**SWF (Simple Workflow Service)**

**Brief Description** : helps developers build, run, and scale background jobs that have parallel or sequential steps.

**Detailed Description:** web service that makes it easy to coordinate work across distributed application components. SWF enables applications for a range of use cases, including media processing, web application back-ends, business process workflows, and analytics pipelines, to be designed as a coordination of tasks. Tasks represent invocations of various processing steps in an application which can be performed by executable code, web service, calls, human actions, and scripts.

**Critical Terms:**

- **Actor –** workflow starters, deciders, and activity workers
- **Workflow Starter –** an application that can initiate (start) a workflow. (i.e., an e-commerce website when placing an order, or a mobile app searching for bus times.)
- **Decider –** controls the flow of activity tasks in a workflow execution. If something has finished in a workflow (or fails) a decider decides what to do next.
- **Activity Worker –** carries out the activity tasks.

**SWF vs SQS:**

- SQS has a retention period of 14 days; SWF does up to 1 year for workflow executions.
- SWF is a task-oriented API, whereas SQS is a message-oriented API.
- SWF ensures that a task is assigned only once and is never duplicated. With SQS, you need to handle duplicated messages and may also need to ensure that a message is processed only once.
- SWF keeps track of all the tasks and events in an application. With SQS, you need to implement your own application-level tracking, especially if your application uses multiple queues.

**SNS (Simple Notification Service)**

**Brief Description** : a web service that makes it easy to setup, operate, and send notifications from the cloud. (technically a mobile service)

**Detailed Description:** makes it easy to setup, operate, and send notifications from the cloud. It provides developers with a highly scalable, flexible, and cost-effective capability to publish messages from an application and immediately deliver them to subscribers or other applications. Besides pushing notifications directly to mobile devices, SNS can also deliver notifications by SMS text message or email, to SQS, or to any HTTP endpoint. Notifications can also trigger Lambda functions. When a message is published to an SNS topic that has a Lambda function subscribed to it, the Lambda function is invoked with the payload of the published message. The Lambda function receives the message payload as an input parameter and can manipulate the information in the message, publish the message to other SNS topics, or send the message to other services. SNS allows you to group multiple recipients using topics. A topic is an &quot;access point&quot; for allowing recipients to dynamically subscribe for identical copies of the same notification. One topic can support deliveries to multiple endpoint types – for example, you can group together iOS, Android, and SMS recipients. When you publish once to a topic, SNS delivers appropriately formatted copies of your message to each subscriber. To prevent messages from being lost, all messages published to SNS are stored redundantly across multiple AZs.

**Benefits:**

- Instantaneous, push-based delivery (no polling necessary)
- Simple APIs and easy integration with applications
- Flexible message delivery over multiple transport protocols
- Inexpensive, pay-as-you-go model with no up-front costs
- AWS console offers the simplicity of a UI

**Pricing Model:**

- Users pay $0.05 per 1 million requests
- $0.06 per 100,000 notification deliveries over HTTP
- $0.75 per 100 notification deliveries over SMS
- $2.00 per 100,000 notification deliveries over email

**WorkSpaces**

**Description** : virtual desktop in the cloud. (technically an enterprise service)

# Acronyms

**CDN** : Content Delivery Network

**DNS** : Domain Name System

**HSM** : Hardware Security Module

**MFA** : Multi-factor Authentication

**MFE** : Multi-factor Encryption

**ELB** : Elastic Load Balancer

**ASG** : Auto-scaling Group

**RTMP** : Real-time Messaging Protocol

**TTL** : Time to Live

**SSL** : Secure Sockets Layer

**TLS** : Transport Security Layer

**SSE** : Server Side Encryption

**VTL** : Virtual Tape Library

**iSCSI** : Internet Small Computer System Interface
**SSD** : Solid-state disk / drive

**IOPS** : Input / Output Operations per Second

**EBS** : Elastic Block Store

**RAID** : Redundant Array of Independent Disks

**AMI:** Amazon Machine Image

**OLTP:** Online Transaction Processing

**OLAP:** Online Analytics Processing

**ECU:** EC2 Compute Unit

**EIP:** Elastic IP address

**IGW:** Internet Gateway
