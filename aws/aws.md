# IAM :

- IAM consists of the following : Users, Groups (a way to group users and apply
policies to them collectively ), Roles, Policy documents
- Policy documents are in JSON format.
- IAM is universal. It does not apply to regions at this time.
- root account -> account created when you first setup ur aws account.it has
Admin access.
- new users have NO permissions when first created
- new users are assigned access key id & secret access
- you can't login in to aws console using access key & secret only via api or
command line.
- access key id & secret access are viewed one time, if you loose them you
need to regenerate them.
- always set up MFA (multi factor authentification)
- you can create and customise your own password rotation policies
- "Power User" -> has access to all AWS Services expect for management of
groups and users within IAM.
- "root" -> Administrator access

# S3 :

- S3 provides developers and IT Teams with secure, highly-scalable object
storage. Easy to use with a simple web services interface to store and retrieve
andy amount of data from anywhere on the web.
- S3 is object based (allow to upload files)
- Files can be from 0 bytes to 5 TB
- Unlimited storage.
-  Files are stored in Buckets.
- S3 in a universal namespace, names have to be unique globally (bucket names)
- when you upload a file to S3 you will get a HTTP 200 response if the upload
was successful.
- valid url : https://s3-eu-west-1 .com/acloudguru
- Read after write consistency for PUTS of new objects.
- Eventual consistency for overwrite PUTS and DELETES ( can take some time to
propogate)
- S3 Storage classes / tiers : S3 (99,99 availability and 99.99999 durability) stored redundantly across multipe devices in multiple facilities, S3-IA (infrequetly accessed) lower fee than S3 but you are charged a retrival fee (RRS), S3 One zone- IA, Glacier
(Archival 3 to 5 hours retrival).
- S3 object : key (name), value(data), version id, metadata, subresources
(ACL,Torrent)
- not suitable to store OS
- by default buckets are private and all objects stored inside them are private
- control access to bucket using ACL or using bucket policies
- When you create a bucket : encryption is not enabled and no bucket policy exists, Transfer acceleration and versinoning are also disabled.
- S3 bucket properties : versioning, server access logging, object level logging, static website hosting, tags, transfer acceleration, events.
- You can access to files in s3 from your data center using a File Gateway. those files can be accessed via NFS and SMB.
- Request rate and performance : S3 can manage up to 3500 PUT/POST/DELETE per second and 5500 GET per second.
- You can upload files up to 5 GB in size. Use multi-part upload for larger file uploads.
- You can track object-level action by cloudTrail logging.
- System meta data for objects in S3 : x-amz-server-side-encryption , x-amz-version-id , Content-Length

## Versioning:
- stores all versions of an object (including all wirtes and even if you delete an object)
- great backup tool
- once enabled can't be disabled, but can be suspended
- integrates with lifecyle rules
- can have additional level of security by adding MFA for delete operations.

## Cross region replication:
- Versioning must be enabled on both the source and destination buckets.
- Regions must be unique.
- Files in an existing bucket are not replicated automatically. All subsequent
updated files will be replicated automatically.
- You cannot replicate to multiple buckets or use daisy chaining
- Delete markers are replicated
- Deleting individual versions or delete markers will not be replicated

## Life cycle management :
configured within a bucket
- can be used in conjunction with versioning (with or without versioning)
- can be applied to current versions and previous versions
- following action can be done : transition to S3 - IA after certain time then to Glacier after certain period
- you can program when to delete objects (permanently delete)
goal : reduce cost of storage if objects are not used / accessed

## Security & Encryption :
- by default all newly created buckets are private
- you can setup access control to your buckets using :
  bucket policies
  access control lists (ACL)
- S3 buckets can be configured to create access logs which log all requests
made to the S3 bucket. this can be done to another bucket.

* Encryption :
- in transit; (sending info from and to ur bucket) uses SSL/TLS  (HTTPS)
- at rest :
    server side encryption :
      - S3 Managed Keys - SSE-S3 ( each object with unique key)
      - SSE-KMS (Amws key management service, Managed keys)
      - SSE-C Server side encryption with customer provided keys
- client side encryption   

# Glacier

- Expedited : 1-5 minutes
- Standard : 3-5 hours
- Bulk : 5-12 hours  


- Vault lock : allow you to lock your vault within  a variety of compliance controls that are designed to support such long-term records retention.

# CloudFront :

 Definition : A content delivery network (CDN) is a system of distributed servers (network) that deliver webpages and other web content to a user based
 on the geographic locations of the user, the origin of the webpage and a content delivery server.

 Terminology :
 - Edge location : this is the location where content will be cached. separate to region or availability zone.
 - Origin : the origin of all the files that the CDN will distribute, this can be either an S3 bucket, an EC2 instance, ELB or Route 53.
 - Distribution: this is the name given to the CDN which consiste of a collection of edge locations.
 - Web Distribution - Used for websites
 - RTMP - used for media streaming
 - Edge locations are not read only, you can put objects on edge location
 - Objects are cached for the life of the TTL
 - you can clear cached objects, but you will be charged

# Storage Gateway :

 4 types : NFS (file gateway), Volumes Gateway : stored volumes / cached volumes, Tape Gateway
 - File Gateway (in exam) : files are stored as objects in your S3 buckets. Accessed through a network file (nfs) mount point. Ownership, permissions and timestamps are durably stored in S3 in the user-metada of the object associated with the file.Once objects are transferred to S3, they can be managed as native S3 objects, and bucket policies such as versioning, lifecycle management, and cross-regions replication apply directly to objects stored in your bucket. For flat files, stored directly on S3.

 - Volumes Gateway :
    - stored volumes: entire dataset is stored on site and is asynchrously backed up to S3.
    - cached volumes : entire dataset is stored on s3 and the most frequently accessed data is cached on site.

 - Gateway virtual tape libray :
    used for backup and uses populer backup applications like NetBackUp, Backup Exec, Veeam etc.

    use cases:
    - store flat files storage cost min -> file gateway
    - data center / backup for servers, physical tapes -> Gateway virtual tape
    - media company : much  storage not all on site (cached volumes), financial to keep latency (stored volumes)

# Snowball

    - AWS Import / Export disk (old snowball) : accelerates moving large amounts of data into and out of the aws cloud using portable sotrage devices for transport. AWS Import / Export disk transfers your data directly onto and off of storage devices using amazone's high-speed internal network and bypassing the internet.

    - AWS Snowball is a service that accelerates transferring large amounts of data into and out of AWS using physical storage devices, bypassing the Internet. Each AWS Snowball device type can transport data at faster-than internet speeds. This transport is done by shipping the data in the devices through a regional carrier. The devices are rugged shipping containers, complete with E Ink shipping labels.

    - With a Snowball, you can transfer hundreds of terabytes or petabytes of data between your on-premises data centers and Amazon Simple Storage Service (Amazon S3). AWS Snowball uses Snowball appliances and provides powerful interfaces that you can use to create jobs, transfer data, and track the status of your jobs through to completion. By shipping your data in Snowballs, you can transfer large amounts of data at a significantly faster rate than if you were transferring that data over the Internet, saving you time and money.

    - Note
    There are many options for transferring your data into AWS. Snowball is intended for transferring large amounts of data. If you want to transfer less than 10 terabytes of data between your on-premises data centers and Amazon S3, Snowball might not be your most economical choice.
    Snowball uses Snowball appliances shipped through your region's carrier. Each Snowball is protected by AWS Key Management Service (AWS KMS) and made physically rugged to secure and protect your data while the Snowball is in transit. In the US regions, Snowballs come in two sizes: 50 TB and 80 TB. All other regions have 80 TB Snowballs only.

    storage capacity (usable capacity)	Snowball	Snowball Edge
    50 TB (42 TB) - US regions only	     ✓
    80 TB (72 TB)	                       ✓
    100 TB (83 TB)		                                ✓
    100 TB Clustered (45 TB per node)		              ✓


        Use case	                       Snowball	Snowball Edge
    Import data into Amazon S3	               ✓	✓
    Export from Amazon S3	                     ✓	✓
    Durable local storage		                      ✓
    Local compute with AWS Lambda		              ✓
    Amazon EC2 compute instances		              ✓
    Use in a cluster of devices		                ✓
    Use with AWS Greengrass (IoT)		              ✓
    Transfer files through NFS with a GUI		      ✓

READ S3 FAQ BEFORE THE EXAM


## ---- EC2 -----

# EC2

- 4 pricing models :

    - On demand : allows you to pay a fixed rate by the hour (or by second) with no commitment

    - Reserved  : provides you with a capacity reservation, and offer a significant discount on the hourly charge for an instance. 1 year or 3 years terms.

    - Spot : enables you to bid whatever price you want for instance capacity, providing for even greater savings if your applications have flexible start and end times. ! if a spot instance is termniated by amazon EC2, you will not be charged for a partial hour of usage. However if you terminate the instance yourself, you will be charged for the complete hour in which the instance ran.

    - Dedicated Hosts : physical EC2 server dedicated for your use. Dedicated Hosts can help you reducs costs by allowing you to use your existing server-bound software licenses.

- instance types :
    FIGHT DR MC PX

- EBS Volume types :

    - SSD-backed volumes optimized for transactional workloads involving frequent read/write operations with small I/O size, where the dominant performance attribute is IOPS
    - HDD-backed volumes optimized for large streaming workloads where throughput (measured in MiB/s) is a better performance measure than IOPS

    - SSD :
      - General purpose SSD : balances price and performance for a wide variety of workloads.
          - Use cases : recommended for most workloads, System boot volumes, Virtual Desktops, Low-Latency interactive apps, Dev & Test environments.
      - Provisioned IOPS SSD : Highest-performance SSD volumes for missions-critical low-latency or high-throughput workloads
          - Use cases :    Critical business applications that require sustained IOPS performance, or more than 16,000 IOPS or 250 MiB/s of throughput per volume,  Large Database Workloads, such as : MongoDB, Cassandra, Microsoft SQL Server, mySQL, postgreSQL, Oracle.

    - Magnetic :
      - Throughout optimized HDD : Low cost HDD volumes designed for frequently accessed, thoughput-intensive workloads.
          - Use cases : Streaming workloads requiring consistent, fast throughput at a low price. Big Data, Data Warehouse, Log processing, Cannot be a boot volume.
      - Cold HDD : Lowest cost HDD volume designed for less frequently accessed workloads.
          - Use cases : Throughput-oriented storage for large volumes of data that is infrequently accessed, Scenarios where the lowest storage cost is important, Cannot be a boot volume.
      - Magnetic : Previous generation.Can be a boot volume.

# Security group basics :

- All inbound traffic is blocked by default.
- All outbound traffic is allowed.
- Changes to security groups take effect immediately.
- You can have any number of EC2 instances within a security group.
- Security groups are stateful :
    - if you create an inbound rule allowing traffic in, that traffic is automatically allowed back out again.
- You cannot block specific IP addresses using Security Groups, instead use Network Access Control Lists.
- You can specify allow rules, but not deny rules.


# EBS Volumes and snapshots :

- Volumes exist on EBS : Virtual Hard Disk
- Snapshots exist on S3.
- Snapshots are point in time copies of Volumes.
- Snapshots are incremental - this means that only the blocks that have change since you last snapshot are moved to S3.
- If its the first snapshot it may take time to create.

- To create a snapshot for Amazon EBS volumes that serve as root devices, you should stop the instance before taking the snapshot.
- However you can take a snap while the instance is running.
- IMPORTANT : you can change EBS volumes sizes on the fly, including changing the size and storage type.
- You can create AMI'S from EBS-backed instances and snapshots.  
- IMPORTANT : volumes will ALWAYS be in the same AZ as the EC2 instance.
- To move an EC2 volume from on AZ/Region to another, take a snap or an image of it, then copy it to the new AZ/Region.
- Snapshots of encrypted volumes are encrypted automatically.
- Volumes restored from encrypted snapshots are encrypted automatically.
- You can share snapshots, but only if thet are unencrypted.
   - these snapshots can be shared with other AWS accounts or made public.
- By default the root volume will be deleted if instance is terminated, but we can uncheck that.

# RAID, Volumes & Snapshots
- RAID = Redundant Array of Independent Disks.
    RAID 0 : Stripped, No Redundancy, Goof performance
    RAID 1 : Mirrored, Redundancy
    RAID 5 : NEVER USE IT. Good for reads, bad for writes
    RAID 10 : Stripped & Mirrored, Good Redundancy, Good performance

- How can I take a Snapshot of a RAID Array :
Problem : take a snapshots, the snapshot excludes data held in the cache by applications and the OS. This tend not to matter on a single volume, however using multiple volumes in a RAID array, this can be a problem due to interdependencies of the array.

Solution : Take an application consistent snapshot.
    - stop the app from writing to disk
    - flush all caches to the disk
      - how can we do this ?
        - freeze the file system
        - unmount the RAID Array
        - shutting down the associated EC2 instance.

# Volume vs Snapshot

- to create a snapshot for amazon EBS volumes that serve as root devices, you should stop the instance before taking the snapshot.
- Snapshots of ENCRYPTED volumes are AUTOMATICALLY ENCRYPTED.
- Volumes restored from encrypted snapshots are encrypted automatically.
- You can share snapshots, but only if they are unencrypted.
    - these snapshots can be shared with other aws accounts or made public.

# EBS vs Instance Store

- All AMI are either backed by : EBS or instance store.
- EBS Volumes : the root device for an instance launched from the AMI is an Amazon EBS volumes created from an Amazon EBS snapshot.
- Instance store : the root device for an instance launched from the AMI is an instance store volume created from a template stored in Amazon S3.

- Instance store volumes are sometimes called epehemeral storage.
- Instance store volumes cannot be stopped. if the underlying host fails, you will lose your data.
- EBS backed instances can be stopped. You will not lose the data of this instance if it is stopped.
- You can reboot both, you will not lose your data.
- By default, both ROOT volumes will be deleted on termination, however with EBS volumes, you can tell AWS to keep the root device volume.

# Elastic Load Balancer ELB :

- 3 types of load balancer :
      - Application Load Balancers
      - Network Load Balancers
      - Classic Load Balancers
- 504 error means the gateway has timed out. This means that the application not responding within the idle timeout period.
  - Trouble shoot the application. Is the web server or database server ?
- If you need the IPv4 address of your end user, look for the X-Forwarded-For Header.

- Instances monitored by ELB are reported as : inService or OutOfService
- Health Checks check the instance health by talking to it
- Have their own DNS name. You are never given an IP address.
- You need at least two subnets to create a load balancers
- Read the ELB FAQ for classic load balancers
- For monitor Application Load Balancer use :  CloudWatch metrics, VPC Flow Logs, CloudTrail logs.

# Auto Scaling Group VS Target Group

- Auto Scaling Group is just a group of identical instances that AWS can scale up(add a new one) or down(remove) automatically based on some configurations you've specified. You use this to ensure at any point in time, there is the specific number of instances running your application, and when a threshold is reached(like CPU utilization), it scales up or down.

- Auto Scaling group launches new instances based on the configuration defined in Launch Configuration. AMI ID is one of the configuration parameter which defines the type of instance to be launched when auto scaling logic is executed. AMI ID is set during the creation of launch configuration and cannot be modified. Auto scaling group will not launch new instance based on the latest image of main instance.

- Target Group is a way of getting network traffic routed via specified protocols and ports to specified instances. It's basically load balancing on a port level. This is used mostly to allow accessing many applications running on different ports but the same instance.

# Networking

- The instance retains its private ipv4 addresses and any ipv6 addresses when stopped and started. AWS releases the public ipv4 address and assigns a new one when it is stopped & started


# Cloudwatch EC2 :

- Standard monitoring : 5 minutes
- Detailed monitoring : 1 minute
- What can you do with Cloudwatch ? :
    - Dashboards : creates awesome dashboards to see what is happening with your aws environment.
    - Alarms : Allows you to ser alarms that notify you when particular thresholds are hit.
    - Events : cloud watch events helps you respond to stats changes in your AWS ressources.
    - Logs : cloudwatch logs helps you to aggregate, monitor, and store logs.

- Cloudwatch is for logging / monitoring where is cloud trail is for auditing.

# CloudTrail :

- for auditing purpose
- by default cloudtrail event log are encrypted using Amazon S3 server-side encryption  

# AWS Command Line & EC2 :

1 - created and launched EC2 Instance
2 - added new user in IAM for programatic access, attached existing policies to user (admin access) then create user that giver secret and key id.
3 - we connect to the machine with my key.
4 - aws s3 ls : here we have unable to locat credentials
5 - aws configue to put acces + secret id
6 - we have access to s3
7 - !!! CREDENTIALS ARE STORED WITHIN THE MACHINE WHICH MAKES IT VULNERABLE TO ACCESS THE AWS ACCOUNT VIA COMMAND LINE WITH THE CREDENTIALS, CAN START PROVISION MACHINE USING YOUR ACCOUNT TO MAKE BITCOIN MINING !!!

SOLUTION : use IAM Roles. Attached to EC2 instances.

# S3 CLI & Regions :

if you want to copy a file from a specific region to EC2 instance directory it's better to put the --region param to specifiy from which region you are copying.

example : aws s3 cp -- recursive S3://acloudguru-euwest2 /home/ec2-user --region eu-west-2.

# Bootstrap scripts to provision EC2 :

you can add a bash script in the advanced details then user data of the Configure instance section when creating EC2 Instance.

that script will automatically provision the machine with specific modules such as an apache server (httpd)

# EC2 Metadata :

curl http://169.254.169.254/latest/meta-data/  

curl http://169.254.169.254/latest/user-data/ (this will give you the bootstrap script)

# EC2 autoscaling :

We use this to ensure that a service will not have failure. The purpose is to have different machines in different AZ, the load balancer will then root the traffic to a specific machine.

We can configure how to autoscale. for example if CPU >80 we provision another machine if it's less we delete a machine.
In case of a failure in an AZ it will still work because the ELB will guide us to the healthy instance.

# What is Placement Group ? :

if they dont say the type it's Clustered !!!

- Two types : Clustered / Spread
- Clustered :  grouping of instances within a single AZ. Placement groups are recommended for applications that need low network latency, high network throughput, or both. Only certain instances can be launched in to a Clustered Placement Group.
- Spread : a spread Placement group is a group of instances that are each placed on distinct underlying hardware. Spread placement groups are recommended for applications that have a small number of critical instances that should be kept separate from each other.

- Clustered placement group can't span multiple AZ.
- A spread placement group can.
- The name you specifiy for a placement group must be unique within ur AWS account.
- Only certain types of instances can be launched in a placement group (compute optimized, GPU, Memory Optimized, Storage optimized)
- AWS recommend homogenous instances wihtin placement groups.
- you cant merge placement groups.
- you cant move an existing instance into a placement group. You can create AMI from your existing instance, and then launch a new instance from the AMI into a placement group.

# Lambda :

- Lambda scales out (not up) automatically
- Lambda functions are Independent, 1 event = 1 function.
- Lambda is serverless
- There are other SERVERLESS Services in amazon (S3, SNS, SQS, DynamoDB, Lamdba, API Gateway, Kinesis, CloudWatch events) NEED TO KNOW
- Lmabda functions can trigger other lambda functions, 1 event can = x functions if functions trigger other functions.
- Architectures can get extremely complicated, AWS X-ray allows you to debug what is happening.
- Lambda can do things globally, you can use it to back up S3 buckets to other S3 buckets etc.
- Know your triggers.
- Handling Errors In Lambda :
    - if the execution of Lambda function is getting abruptly terminated (non-processed or failed payloads also in case of asynchronous request to a function), you can send notification by : Configuring Dead-Letter queue and send it to SNS or SQS
    - Max memory limit and max execution time limit gets terminated without being caught in the handler exception.
- Lambda supports the following : Java, Node JS, Python, C#
- Maximum execution duration per request is 300 seconds or 5 minutes
- There is a limit of 1000 concurrent executions

# EC2 - Exam Tips - Summary.

- Know the difference between : on demand, spot, reserved, dedicated host.
- With SPOT instances : if you terminate thes instance, you pay for the hour. if AWS terminates the sport instance, you get the hour it was terminated in for free.
- EC2 Instance types : FIGHT DR MC PX
- Termination protection is turned off by default, you must turn it on.
- On an EBS-Backed instance, the default action is for the root EBS volume to be deleted when the instance is terminated.
- EBS-Backed root volumes can now be encrypted using AWS API or console, or you can use a third party pool (such as bit locker etc) to encrypt the root volumes.

-- Snapshots & Volumes :

- Additional volumes can be encrypted.
- Volumes exist on EBS : virtual hard disk.
- Snapshots exist on S3
- you can take a snapshot of a volumes, this will store that volume os S3.
- Snapshots are point in tome copies of volumes.
- Snapshots are incremental. This means that only the blocks that have changed since your last snapshot are moved to S3.
- if this is your first snapshot, it may take some time to create.
- Snapshots of encrypted volumes are encrypted automatically.
- Volumes restored from encrypted snapshots are encrypted automatically.
- You can share snapshots, but only if they are unencrypted : these snapshots can be shared with other AWS accounts or made public.
- To create a snapshot for amazon EBS volumes that serve as root devices, you should stop the instance before taking the snapshot.


-- EBS & Instance Store :  

- Instance store volumes are sometimes called epehemeral storage.
- Instance store volumes cannot be stopped. If the underlying host fails, you will lose your data.
- EBS backed instances can be stopped. You will not lose the data of the instance if it is stopped.
- you can reboot both, you will not lose your data.
- by default, both root volumes will be deleted on termination. However with EBS volumes, you can tell AWS to keep the root device volume.

- How can I take a snapshot of RAID Array :
     - Stop the application from writing the disk.
     - Flush all caches to the disk.
     - How can we do this ?
          - Freeze the file system.
          - Unmount the RAID Array.
          - shutting down the associated EC2 instance.

-- Amazon Machine Images (AMI)

- AMI are regional. You can only launch an AMI from the region in which it is stoed. However you can copy AMIs to other regions using the console, command line, or the amazon EC2 API.

-- Monitoring :

- standard monitoring = 5 minutes.
- detailed monitoring = 1 minute.
- CloudWatch is for performance monitoring.
- CloudTrail is for auditing.

- Dashboards - CloudWatch creates awesome dashboards to see what is happening with your AWS environment.
- Alarms - Allows you to set alarms that notify you when particular thresholds are hit.
- Events - CloudWatch Events helps you to respond to state changes in your AWS ressources.
- Logs - CloudWatch logs help you to aggregate, monitor, and store logs.

-- Roles :
- Roles are more secure than storing your access key and secret access key on individual EC2 instances.
- Roles are easier to manage.
- Roles can be assigned to an EC2 instance AFTER it has been provisionned using both the CL and the AWS console.
- Roles are universal - you can use them in any region

- EBS Consists of :
  - SSD, General Purpose - GP2 - (up to 10000 IOPS)
  - SSD, Provisioned IOPS - IO1 - (More than 10000 IOPS)
  - HDD, Throughout Optimized - ST1 - frequently accessed workloads.
  - HDD, Cold - SC1 - less frequently accessed data.
  - HDD, Magnetic - Standard - cheap, infrequently accessed storage

  - You cannot mount 1 EBS volume to multiple EC2 instances; instead use EFS.


# Route 53

WHY 53 -> beacuse the DNS is on port 53

- DNS 101 :

  - DNS is sued to convert human friendly domain name into an IP address. Works like a phone book.
  - 2 types : IPv4 (32 bit field) / IPv6 (46 bit field)
  - Top Level Domaines:
     - the last work in the domain name represents the "the top level domain"
     .com : top level domain name
     .co.uk : second level domain name (uk is top level)
     - controlled by the Internet assigned numbers authority (IANA). it's a database of all available domain names.
     - Domain Registras : make sure that a domain name is unique.  
  - Start of authority Record (SOA) :
      stores :
       - the name of the server that supplied the data for the zone.
       - the Administrator of the zone.
       - the current version of the data file.
       - the default number of seconds for the TTL on ressource records.

  - NS Records : NS stands for Name server records. they are used by Top Level Domain servers to direct traffic to the content DNS server which contains the authoritative DNS records.
  - A Records : Fundemental type of DNS. The A record is used by a computer to translate the name of the domain to an IP address.
  - TTL : the length a DNS record is cached on either the resolving server or the user own pc.
  CNAMES : used to resolve a domain name to another.
  ALIAS Records : used to map to ressource record sets in your hosted zone to Elastic load balancers. DIFFERENCE CNAME CANT BE USED FOR NAKE DOMAIN NAMES.

  - ELBs do not have pre-defined IPv4 addresses; you resolve to them using a DNS name.
  - Given the choice always choose an A record over a CNAME.  
  - Alias Records : SAVE TIME. Amazon route 53 automatically recognizes changes in the record sets that the alias ressource record set refers to.
  - Commond DNS types : SOA, NS, A, CNAMES, MX Records.

## Register a domain name  :

- Route 53 is global.  
- Routing policies available on AWS :
  - simple Routing
  - weighted routing
  - latency-based routing
  - failover routing
  - geolocation routing
  - multivalue answer routing.

## Simple Routing Policy :

- you can only have one record with multiple IP addresses.
- if you specify multiple values in a record, Route 53 returns all values to the user in a random order.

## Weighted Routing Policy :

- weighted routing policies let you split your traffic based on different weights assigned. For example you can set 10% of your traffic to go to US-EAST-1 and 90% to go to EU-WEST-1.

## Latency Routing Policy :

- latency based routing allows you to route your traffic based on the lowest network for your end user. To use latency-based routing, you create a latency resource record set for the Amazon EC2 (or ELB) resource in each region that hosts your website. When Amazon Route 53 receives a query for your site, it selects the latency resource record set for the region that gives the user the lowest latency. Route 53 then responds with the value associated with that resource record set. Gives back the region that gives the fastest response time.


## Failover Routing Policy :

- Failover routing policiers are used when you want to create an active/passive set up. You may want your primary site to be in EU-WEST-2 and your secondary DR Site in AP-SOUTHEAST-2

- Route 53 will monitor the health of your primary site using a health check.

## Geolocation Routing Policy :

- lets you choose where your traffic will be sent based on the geographic location of your users.

## Multivalue Routing Policy :

- if you want to route traffic approximately randomly to multiple resources, such as web servers, you can create one multivalue answer record for each resource and, optionally, associate an Amazon Route 53 health check with each record. For example, suppose you manage an HTTP web service with a dozen web servers that each have their own IP address. No one web server could handle all of the traffic, but if you create a dozen multivalue answer records, Amazon Route 53 responds to DNS queries with up to eight healthy records in response to each DNS query. Amazon Route 53 gives differents answers to differents DNS resolvers. If a web server becomes unavailable after a resolver caches a response, client sofwtare can try another IP address in the response.  

!! there is a limit to the number of domain names that you can manages using route 53. its true and false, there is a limit of 50 domain names however this limit can be raised by contacting AWS support. !!

## Summary of A , CNAME, ALIAS, AAAA, MX Record

- A Record :
  - A record value is always an IP Address. The IP Should be static.
  - Use case : A record will be used to map your root/naked domain-name viz example.com  to  Elastic IP say 198.51.100.77.

- CNAME :
  - CNAME record maps a name to another name. It can never be an IP address.
  - Use case : An A record for example.com (i.e. root/naked domain) points to the server IP address
               A CNAME record for www.example.com points to example.com  

- ALIAS :
  - Alias record is an Amazon Route 53-specific virtual record.  So, it works only with Amazon Route 53. Whenever you come across questions related to mappings of Amazon specific resources/ services say Amazon ELBs (Elastic Load Balancers), Amazon CloudFront distributions, AWS Elastic Beanstalk environments, or Amazon S3 buckets that are configured as websites, it should ring a bell for you to consider Alias record in Route 53.

- AAAA : record is similar to an A record but it is for IPv6 addresses (whereas A record is for IPv4).
- MX records  : (Mail Exchange records) is used for setting up Email servers. MX records must be mapped correctly to deliver email to your address.

# Databases on AWS

## RDS

- to connect rds instance to ec2, you need to ( in the security group of rds ) open MySQL on port 3306 to security group of your EC2 instance.

- Backups  :
  - Automated backups are enabled by default. You get free storage matching the size of the DB
  - Automated backups are recoverable at any time within the configurable retention period (between 1 and 35 days)
  - When restoring from an automated backup or manual snapshot, the restored instance will be a new RDS instance with a new DNS endpoint.

- Encryption :
  - Encryption at rest is supported for : MySQL, Oracle, SQL Server, PostgreSQL, MariaDB, AuroraDB
  - Encrypting existing DB is not supported. To do this, you will need to create a new encrypted instance and migrate data to it. The encryption key can be stored in KMS.

- Failover :
  - When Multi-AZ failover is enabled, AWS creates a primary RDS instance, and asynchronously replicates data to a standby instance in a different AZ.
  - if the primary DB instance fails, the following happens :
    - The standby replica (which is stored in a different AZ to the primary) is promoted to become the new primary.
    - The DNS record of the DB instance is changed to point to the new primary.
    - The original primary DB instance is terminated, and a new standby replica is created.
    - Failover is for disaster recovery only.
    - Failover and Multi-AZ is only supported for : MySQL, Oracle, Maria DB, PostgreSQL
  - You can convert existing DB instances to Multi AZ deployments by modifying the DB instance and specifiying the Multi-AZ option.
  - The failover mechanism automatically changes the DNS record of the DB instance to point to the standby DB instance. As a result, you need to re-establish any existing connections to your DB instance.

- Read Replicas :
    - Supported by : mySQL, PostgreSQL, MariaDB
    - Are not Multi-AZ
    - Have their own DNS address and are not Mulit-AZ
    - Can be promoted to a standalone, writable DB
    - For a DB up to 5 read replica are supported
    - Possible to have Read replicas of read replicas, but problem is Latency
    - Read replicas are supported in seperate regions from the source DB for both MySQL and MariaDB but not for PostgreSQL  

## AWS Database type
 - RDS - OLTP : sql, mysql, postgresSQL, Oracle, MariaDB, Aurora
 - DynamoDB - No Sql
 - Redshift - OLAP
 - Elasticache : in memory caching : Memecached - Redis

## Aurora Scaling :
- 2 copies of your data is contained is each AZ, with minimum of 3 AZ. 6 copies of your data.
- Aurora is designed to transparently handle the loss of up to two copies of data without affecting database write availability and up to three copies without affecting read availability.
- Aurora storage is also self-healing. Data blocks and disks are continusously scanned for errors and repaired automatically.

## Aurora Replicas
- 2 types of replicas are available.
- Aurora Replicas (currently 15)
- MySQL Read Replicas (currently 15)

## DynamoDB vs RDS
- DynamoDB offers "push buttons" scaling, meaning that you can scale your database on the fly, without any down time.
- RDS is not so easy and you usually have to use a bigger instance size or to add a read replica.

## Dynamo DB
- Stored on SSD Storage.
- Spread across 3 geographically distinct data centers.
- Eventual consistent reads (default)
- Strongly consistent reads

## Redshift

- Suitable for OLAP
- Single Node (160Gb)
- Multi-Node
    - Leader Node (manages client connections and receives queries)
    - Compute Node (store data and perform queries and computations) Up to 128 computes nodes.
- Data Transfer : only within a VPC not outside it
- Security :
      - Encrypted in transit using SSL
      - Encrypted at rest using AES-256 encryption
      - By default RedShift takes care of key management
- Availability :
      - Currently available in only 1 AZ
      - Can restore snapshots to new AZs in the event of outage.
- Backups :
      - Enabled by default within a 1 day retention period
      - Maximum retention period is 35 days
      - Always attempts to maintain at least three copies of your data (the original and replica on the compute nodes and
        backup in Amazon S3)
      - Automated snapshots are deleted within the period of 1(Least) to 35 (Max) days (based on the retention period settings)
      - Amazon Redshift never deletes Manual snapshots automatically.

## Elasticache :
- supports : memecached, Redis
- web service that makes it easy to deploy, operate and scale an in-memory cache in the cloud.


# VPC
- think of a VPC as a logical datacenter in AWS.
- Consists of IGWs (ot Virtual Private Gateways), Route Tables, Network Access Control Lists, Subnets, and Security Groups.
- 1 Subnet = 1 AZ
- Security Groups are stateful (inbound <=> outbound). Network access control lists are stateless.
- no transitive peering.

## NAT instances
- When creating a NAT instance, disable source/destination check on the instance.
- NAT instances must be in a public subnet.
- There must be a route out of the private subnet to the NAT instance in order for this to work.
- The amount of traffic that NAT instances can support depends on the instance size. if you are bottlenecking, increase the instance size.
- You can create high availability using Autoscaling Groups, multipe subnets in differents AZs, and a script to automate failover.
- Behind a security group.

## NAT Gateways
- Preferred by the enterprise.
- Scale automatically up to 10Gbps.
- No need to patch
- Not associated with security groups.
- Automatically assigned a public ip address
- Remember to update your route tables.
- No need to disable Source/ Destination Checks.
- More secure than a NAT instance.
- You can't share nat gateways accross VPC's
- NAT Gateways don't have security groups

## Network ACLs
- Your VPC automatically comes a default network ACL, and by default it allows all outbound and inbound traffic.
- You can create custom network ACLs. By default, each custom network ACL denies all inbound and outbound traffic until you add rules.
- Each subnet in your VPC must be associated with a network ACL. if you don't explicitly associate a subnet with a network ACL, the subnet is automatically associated with the default network ACL.
- You can associate a network ACL with multiple subnets; however, a subnet can be associated with only one network ACL at a time. When you associate a network ACL with a subnet, the previous association is removed.
- Network ACLs contain a numbered list of rules that is evaluated in order, starting with the lowest numbered rule.
- Network ACLs are stateless, responses to allowed inbound traffic are subject to the rules for outbound traffic (and vice versa)
- You can Block IP Addresses using network ACLs not security groups.

## ALB's
- you will need at least 2 public subnets in order to deploy an application load balancer.  

## VPC Flow Logs
- You cannot enable flow logs for VPCs that are peered with your VPC unless the peer VPC is in your accounts.
- You cannot tag a flow log.
- After you've created a flow log, you cannot change its configuration; for example, you can"t associate a different IAM role with the flow log.

Not all IP Traffic is monitored :
- Traffic generated by instance when they contact the Amazon DNS server. if you use your own DNS server, then all traffic to that DNS server is logged.
- Traffic generated by a windows instance for Amazon Windows license activation.
- Traffic to and from 169.254.169.254 for instance metadata.
- DHCP traffic.
- Traffic to the reserved IP address for the default VPC router.


=> Look to VPC endpoints + VPC FAQ and try create a VPC with no help.

# Application Services

## SQS
- Simple Queue Service (distributed queueing system)
- Oldest AWS Service (first one)
- Using Amazon SQS, you can decouple the components of an application so they run independently, easing message management between components. Any component of a distributed application can store messages in the queue. Messages can contain up to 256 KB of text in any format. Any component can later retrieve the messages programmatically using the Amazon SQS API.
- The queue acts as a buffer between the component producing and saving data, and the component receiving the data for processing.

- SQS Visibility Timeout : the amount of time that the message is invisible in the SQS queue after a reader picks up the message. provided the job is processed before the visibility time out expires, the message will then be deleted from the Q. if the job is not processed within that time, the message will become visible again and another reader will process it. This could result in the same message being delivered twice.
- Default Visibility Timeout is 30 seconds.
- Increase it if your task takes > 30 seconds.
- Maximum is 12 hours.

- Queue Types :
    - Standard Queues (default)
    - FIFO Queues (no duplicate, in order)

- Key Facts :
  - SQL is pull-based, not pushed-based.
  - Messages are 256 KB in size.
  - Messages can be kept in the queue from 1 minute to 14 days.
  - Default retention period is 4 days.
  - SQS guarantees that you messages with be processed at least once.  


- SQS Long Polling
  - short polling -> returns immediately (even if the message Q being polled is empty)
  - long polling -> no return until a message arrives in the Q.
  - long polling SAVES MONEY

## SWF
Simple Workflow Service.

- SWF Workers : programs that interact with Amazon SWF to get tasks, process received tasks, and return the results.
- SWF Decider : controls the coordination of tasks, their ordering, concurrency, and scheduling according to the application logic.
- SWF Domains : workflow, activity types and the workflow execution itself are all scoped to a domain. Domains isolate a set of types, executions and task lists from others within the same account. You can register a domain by using the AWS management console or by using the RegisterDomain action in the Amazon SWF API. Parameters are in JSON

workers and deciders can run on cloud infra, such as EC2 or machines behind firewalls. SWF brokers the interactions between workers and the decider. it allows the decider to get consistent views into the progress of tasks and to initiate new tasks in an ongoing manner.

at the same time, SWF stores tasks, assigns them to workers when they are ready, and monitor their progress. it ensures that a task is assigned only once and is never duplicated. Since amazon SWF maintains the application's state durably, workers and deciders don't have to keep track of execution state. they can run independently, and scale quickly.

Maximum workflow can be 1 year and the values is always measured in seconds.

## SWF vs SQS :
-  SWF is task oriented API. SQS is messaged oriented API.
- SWF ensures that a task is assigned only once and is never duplicated. With SQS you need to handle duplicated messages and may also need to ensure that a message is processed only once.
- SWF keep track of all the tasks and events in an application. With SQS you need to implement your own application level tracking, especially if your application uses multiple Q's.

## SNS :

- a web service that set up, operate and send notifications from the cloud (SMS, email, SQL, and other http endpoint, trigger lambda).  
- a topic, is an access point, can deliver to multiple endpoint types.
- to prevent msg from being lost. all msgs published to SNS are stored redundantly across multiple AZ
- SNS Benefits :
  - Instantaneous, push-based delivery (no polling)
  - Simple and easy to integrate with applications.
  - Flexible message delivery over multiple transport protocols.
  - Inexpensive, pay as you go with no up-front costs.
  - Web-based AWS Management Console offers the simplicity of a point-and-click interface.

- SNS vs SQS :
  - Both messaging services in AWS
  - SNS - Push
  - SQS - Polls (Pulls)

- Pricing :
  - 0.5 $ / 1 Million SNS requests.
  - 0,06 $ / 100,000 notification deliveries over HTTPS.
  - 0,75 $ / 100 notification deliveries over SMS.
  - 2 $ / 100,000 notification deliveries over Email

## Elastic Transcoder

Media transcoder in the cloud.
- Convert media from original format to another.
- Pay based on the minutes you transcode.

## API Gateway

- it's a fully managed service that makes it easy for developers to publish, maintain, monitor and secure APIs at any scale. With a few clicks in the AWS Management Console, you can create an API that acts as a "front door" for applications to access data, business logic, or functionality from your back-end services, such as applications running on EC2, on lambda or any web application.

- API Caching : (has caching capa) reduce the number of call made to your endpoints, caches responses from your endpoint while the TTL.

- Low cost & efficient
- Scales Effortlessly
- You can throttle requests to prevent attacks.
- Connect cloudwatch to it.

- Use CORS to relax same-origin policy
- Valid Integration sources : HTTP-based public facing endpoint outside AWS, Lambda functions from another account, VPC Link.
- You can use API Gateway to front API hosted backedns that exist privately in your own data center using AWS Direct Connect links to your VPC.
- Use throttling and API caching to improve API performance
- Access control mechanims for AWS API Gateway: Resource policies, Lambda authorizers, Usage Plans.
- Refresh cache is not a cache setting.

## Kinesis 101

- streaming data : data from thousands of data sources.
- kinesis is a platform on AWS to send your streaming data too. kinesis makes it easy to load and analyze streaming data.

- core kinesis services :

   - Streams :

      - Consist of shards : base throughput unit of an Amazon Kinesis data stream.  
      - One shard can support up to 1000 PUT records per second
      - Shard : 5 transactions per second for read, up to a maximum total data read rate of 2 MB per second and up to 1000 records per second for writes, up to a maximum total data write rate of 1 Mb per second.
      - the data capacity of your stream is a function of the number of shards that you specify for the stream. The total capacity of the stream is the sum of the capacities of its shards.
      - 24H / 7 DAYS data retention
      - replicates data accross 3 AZ, providing high availability and data durability
      - Records : unit of data stored in Amazon Kinesis data stream.
      - Partition Key : used to seggragate and route records to different shards of a data stream.

   - Firehose :
      - Firehose =  Streams but with Automated shards etc configuration, you don't have to set all up by yourself.
      - No retention
      - Delivery stream : underlaying entity of Amazon Kinesis Data Firehose. You use Firehose by creating a delivery stream and then sending data to it.
      - Record : A record is the data of interest your data producer sends to a delivery stream. The maximum size of a record (before Base64-encoding) is 1000 KB.
      - Destination : A destination is the data store where your data will be delivered. Amazon Kinesis Data Firehose currently supports Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk as destinations.

   - Analytics :
     - Run SQL queries on the data existing on stream or firehose.

EXAM Tips : SQS is important FAQ, go tu summary vid

# Architecting for the cloud Best practices whitepaper

## Business Benefits of Cloud
- Almost zero upront infrastructure investment
- Just-in-time infrastructure
- more efficient resource utilization
- Usage-based costing
- Reduced time to market

## Technical Benefits of Cloud
- Automation - "Scriptable infrastructure"
- Auto-scaling
- Proactive Scaling
- More efficient Dev lifecyle
- Improved testability
- Disaster Recovery and Business continuity
- "Overflow" the traffic to the cloud.

-> cloud gives "elasticity" spend money just for what you need  

## Design for failure

- Rule : be a pessimist when designing architectures in the cloud; assume things will fail. In other words, always design, implement and deploy for automated recovery from failure.

In particular, assume that your hardware will fail. Assume that outages will occur. Assume that some disaster will strike your application. Assume that you will be slammed with more than the expected number of requests per second some day. Assume that with time your application software will fail too. By being a pessimist, you end up thinking about recovery strategies during design time, which helps in designing an overall system better.

Netflix have "chaos monkey", to tests failure.

## Decouple your components

when is about decoupling think about SQS.
- the key is to build components that do not have tight dependencies on each other, so that if one component where to die(fail), sleep(not respond) or remain busy (slow to respond) for some rason, the other components in the system are built so as to continue to work as if no failure is happening.

In essence, loose coupling isolates the various layers and components of your application so that each component interacts asynchronously with the others and treats them as a "black box".

## Implement Elasticity

it can be implemented in three ways :

- Proactive Cyclic Scaling : Periodic scaling that occurs at fixed interval (daily, weekly, monthly, quarterly)

- Proactive Event-based Scaling : Scaling just when you are expecting a big surge of traffic requests due to a scheduled business event (new product launch, marketing campaigns)

- Auto-scaling based on demand. By using a monitoring service, your system can send triggers to take appropriate actions so that it scales up or down based on metrics (utilization of the servers or network i/o, for instance)

## Secure your application

- web server : allow (port 80 HTTP & 443 HTTP to internet )
- app server : Only port 22 (SSH) of App Layer developers in the corporate office network
- DB Server : external internet traffic denied

# Intro to the Well Architected Framework

released in Reinvent in 2015
https://aws.amazon.com/architecture/well-architected/

- it is a set of questions that you can use to evaluate how well your architecture is aligned to AWS best practices.

- 5 Pillars :
    - Security
    - Reliability
    - Performance Efficiency
    - Cost Optimisation
    - Operational Excellence.

- each pillar has :
  - Design Principles
  - Definition
  - Best practices
  - Key AWS services
  - Resources

## General Design Principles

- stop guessing your capacity needs.
- test systems at production scale
- automate to make architectural experimentation easier
- allows for evolutionary architecture
- Data-Driven architectures
- Improve through game days. (test loads for example on black friday)

## PILAR 1 : Security

- Design Principles :
    - Apply security at all layers.
    - Enable traceability.
    - Automate responses to security events.
    - Focus on securing your system.
    - Automate security best practices.
    - AWS Shared responsibility model : (A LOT IN THE EXAM)
      - Customer : responsible of security "in" the cloud
      - AWS : responsible of security "of" the cloud

- Definition (NEED TO KNOW FOR EXAM EACH DEFINITION)
  - Security int the cloud consists of 4 areas :
    - Data protection
    - Privilege management
    - infrastructure protection
    - Detective controls

- Best Practice :

    - Data protection :

    basic data classification should be in place. You should organise and classify your data in to segments such as publicly available, available to only members or certain members of your organisation. You should also implement at least privilege access system so that people are only able to access what they need. However most importantly, you should encrypt everything where possible whether it be at rest or in transit.

      - AWS customers maintain full control over their data
      - AWS makes it easier for you to encrypt your data and manage keys, including regular key rotation, which can be easily automated nativly by AWS or maintained by a customer.
      - Detailed logging is available that contains important content, such as file access and changes.
      - AWS has designed storage systems for exceptional resiliency. As an example, Amazon Simple Storage Service (S3) is designed for 11 nines of durability.
      - Versioning, can protect against accidental overwrites, deletes and similar harm.
      - AWS never initiates the movement of data between regions.

      Questions to ask :
      - How are you encrypting and protecting your data at rest ?
      - How are you encrypting and protecting your data in transit? (ssl)

    - Privilege Management :

    ensures that only authorized and authenticated users are able to access your resources, and only in a manner that is intended.

      Questions to ask :
      - How are you protecting access to and use of the AWS root account credentials?
      - How are you defining roles and responsibilities of system users to control human access to the AWS Management Console and APIs.
      - How are you limiting automated access (such as from application, scripts or third-party tools or services) to AWS resources?
      - How are you managing keys and credentials?

    - Infrastructure Protection :

    outside of the cloud, this is how you protect your data centre. within AWS they handle this, so really infrastructure protection exists at a VPC level.

    Questions to ask :
    - How are you enforcing network and host-level boundary protection?
    - How are you enforcing AWS service level protection ?
    - How are you protecting the integrity of the operating systems on your amazon EC2 instances.


    - Detective Controls :

    used to detect or identify a security breach. using aws services such as : cloudwatch, cloud trail, aws config, S3, glacier.

    Questions to ask :
    - How are you capturing and analyzing AWS logs?  

    KEY AWS Services for this PILLAR :
    - Data protection : you can encrypt in both rest and transit using: ELB,EBS,S3 & RDS.
    - Privilege management : IAM, MFA
    - Infrastructure protection : VPC
    - Detective controls : AWS cloud trail, AWS Config, Amazon cloud watch


    EXAM TIPS :
     - remember the 4 areas and the type of questions for each area.
     - read the whitepaper

## PILAR 2 : Reliability

   - Design Principles :
      - test recovery procedures
      - automatically recover from failure
      - scale horizontally to increase aggregate system availability
      - stop guessing capacity

   - Definition :
      Reliability in the cloud consists of 3 areas :
        - Foundations
        - Change management
        - Failure management

   - Best practices & questions to ask :
        - Foundations : with AWS, they handle most of the foundations for you
                    - questions :
                        - how are you managing AWS service limits for your account ?
                        - how are you planning your network topology on AWS ?
                        - Do you have an escalation path to deal with technical issues?

        - Change management : you need to be aware of how change affects a system so that you can plan proactively around it. Monitoring allows you to detect any changes to your env and react. With AWS you can use cloudwatch for that.
                    - questions :
                        - How does your system adapt to changes in demand ?
                        - How are you monitoring AWS resources ?
                        - How are you executing change management ?

        - Failure Management : always architect your systems with the assumptions that failure will occur.  
                     - questions :
                        - How you backing up your data ?
                        - How does your system withstand component failures ?
                        - How are you planning for recovery ?

    - Key AWS Services :
      Foundations : IAM, VPC
      Change Management : CloudTrail
      Failure Management : CloudFormation

    - Exam Tips :
      - Remember this pilar areas
      - Remember questions to ask

## PILAR 3 : Performance Efficiency :

This pillar focuses on how to use computing resources efficiently to meet your requirements and how to maintain that efficiency as demand changes and technology evolves.

    - Design Principles :
      - Democratize advanced technologies
      - Go global in minutes
      - Use server-less architectures.
      - Experiment more often

    - Definition :
    Performance efficiency consists of 4 areas :
      - Compute
      - Storage
      - Database
      - Space-time trade off

     - Best practices & questions to ask :

      - Compute :
      you have to choose the right kind of server. with aws you can easily change the type of server in which your env is running.
          - questions to ask :
            - how do you select the appropriate instance type for your system ?
            - how do you ensure that you continue to have the most appropriate instance types and features are introduced?
            - how do you monitor your instances post launch to ensure they are performing as expected?
            - how do you ensure that the quantity of your instances matches demand

        - Storage :
        the optimal storage solutions for your env depends on a number of factors. at aws storage is virtualized.
          - questions to ask :
            - how do you select the appropriate storage solution for your system ?
            - how do you ensure that you continue to have the most appropriate storage solution as new storage solutions and features are launched ?
            - how do you monitor your storage solution to ensure it is performing as expected ?   
            - how do you ensure that the capacity and throughput of your storage solutions matches demand ?

        - Database :
        the optimal database depends on a number of factors
          - questions to ask :
            - how do you select the appropriate database solutions for your system ?
            - how do you ensure that you continue to have the most appropriate database solutions and features as new database solutions and features are launched ?
            - how do you monitor your databases to ensure performance is as expected ?
            - how do you ensure that the capacity and throughput of your databases matches demand ?

          - Space-time trade off :
            - questions to ask :
             - how do you select the appropriate proximity and caching solutions for your system ?
             - how do you monitor that you continue to have the most appropriate proximity and caching solutions as new solutions are launched ?
             - how do you monitor your proximity and caching solutions to ensure performance is as expected ?
             - how do you ensure that the proximity and caching solutions you have matches demand ?


        - Key AWS Services :
              Compute : Autoscaling
              Storage : EBS, S3, Glacier
              Database : RDS, DynamoDB, Redshift
              Space-time trade-off : CloudFront, ElastiCache, Direct Connect, RDS Read Replicas etc.

## PILAR 4 : Cost Optimization :

Use this pillar to reduce your costs to a minimum and use those savings for others parts of your business. A cost-optimized system allows you to pay the lowest price possible while still achieving your business objectives.

        - Design principles :
              Transparently attribute expenditure (know what you spend)
              Use managed services to reduce cost of ownership (remove operational ).
              Trade capital expense for operating expense.(stop ressources when they are not in use)
              Benefit from economies of scale.
              Stop spending money on data center operations.

        - Definition :
              Cost optimization in the cloud consists of 4 areas :
                - Matched supply and demand
                - Cost-effective resources
                - Expenditure awareness
                - Optimizing over time

        - Best practices & questions to ask :

                - Matched supply and demand  :
                Don't over provision or under provision, instead as demand grows, so should your supply of compute ressources. for example use lambda that only executes when request comes in. CloudWatch can help you keep track as to what your demand is.
                  - questions to ask :
                    - How do you make sure your capacity matches but does not substantially exceed what you need ?
                    - How are you optimizing your usage of AWS Services ?

                - Cost-effective resources :
                Use the correct instance type to cost save.
                  - questions to ask :
                    - Have you selected the appropriate resource types to meet your cost targets
                    - Have you selected the appropriate pricing model to meet your cost targets ?
                    - Are there managed services (higher-level services that Amazon EC2, EBS, S3) that you can use to improve ROI.

                  - Expenditure awareness :
                  with cloud you no longer have to go out and get quotes on physical devices etc you can provision things within seconds but that raises its own issue, there are many teams in an organisation and you have to be aware about what each is team is spending.
                    - questions to ask :
                      - what access controls and procedures do you have in place to govern AWS costs?
                      - how are you monitoring usage and spending ?
                      - how do you decommission resources that you no longer need, or stop resources that are temporarily not needed ?
                      - how do you consider data-transfer charges when designing your architecture.

                  - Optimizing over time :
                  since AWS moves fast, change your infra to optimize if there is an new service that increases your ROI.
                  - questions to ask :
                    - how do you manage and/or consider the adoption of new services ?  

          - Key AWS Services :
              - matched supply and demand : Autoscaling
              - cost-effective resources : EC2 (reserved), AWS Trusted Advisor
              - expenditure awareness : CloudWatch Alarms, SNS
              - Optimizing over time : AWS Blog, AWS Trusted Advisor

## PILAR 5 : Operational Excellence

includes operational practices and procedures used to manage production workloads. This includes how planned changes are executed, as well as responses to unexpected operational events. Change execution and responses should be automated. All processes and procedures of operational excellence should be documented, tested, and regularly reviewed.

      - Design principles :
        perform operations with code
        align operations processes to business objectives
        make regular, small, incremental changes
        test for responses to unexpected events
        learn from operational events and failures
        keep operations procedures current

      - Definition :
              Operational excellence in the cloud consists of 3 areas :
                - Preparation
                - Operation
                - Response

      - Best practices & questions to ask :

          - Preparation :
          effective preparation is required to drive operationel excellence. Operations checklists will ensure that workloads are reaady for production operation, and prevent unintentional production promotion without effective preparation.
          workloads should have : Runbooks, playbooks
            - questions to ask :
               - what best practices for cloud operations are you using ?
               - how are you doing configuration management for your workload ?

          - Operations :
          operations should be standardized and manageable on aroutine basis.The focus should be on automation and small frequent changes.Regular quality assurance testing and defined mechanisms to track audit roll back and review changes.Changes should not be large and infrequent.They should not require downtime and they should not require manual execution.A wide range of logs and metrics that are based on key operational indicators for a workload should be collected and reviewed to ensure continuous operations.
             - questions to ask :
              - how are you evolving your workload while minimizing the impact of change ?
              - how do you monitor your workload to ensure it is operating as expected  ?

          - Responses :
          Responses to unexpected operational events should be automated. This is not just for alerting, but also for mitigation, remediation, rollback and recovery.
          - questions to ask :
           - how do you respond to unplanned operational events ?
           - how is escalation managed when responding to unplanned operational events ?  
