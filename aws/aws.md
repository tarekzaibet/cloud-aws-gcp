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

    - SSD :
      - General purpose SSD : balances price and performance for a wide variety of workloads.
      - Provisioned IOPS SSD : Highest-performance SSD volumes for missions-critical low-latency or high-throughput workloads  

    - Magnetic :
      - Throughout optimized HDD : Low cost HDD volumes designed for frequently accessed, thoughput-intensive workloads.
      - Cold HDD : Lowest cost HDD volume designed for less frequently accessed workloads
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
- Read the ELB FAQ for classic load balancers

# Cloudwatch EC2 :

- Standard monitoring : 5 minutes
- Detailed monitoring : 1 minute
- What can you do with Cloudwatch ? :
    - Dashboards : creates awesome dashboards to see what is happening with your aws environment.
    - Alarms : Allows you to ser alarms that notify you when particular thresholds are hit.
    - Events : cloud watch events helps you respond to stats changes in your AWS ressources.
    - Logs : cloudwatch logs helps you to aggregate, monitor, and store logs.

- Cloudwatch is for logging / monitoring where is cloud trail is for auditing.  

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
- There are other SERVERLESS Services in amazon (S3, SNS etc) NEED TO KNOW
- Lmabda functions can trigger other lambda functions, 1 event can = x functions if functions trigger other functions.
- Architectures can get extremely complicated, AWS X-ray allows you to debug what is happening.
- Lambda can do things globally, you can use it to back up S3 buckets to other S3 buckets etc.
- Know your triggers.

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


-- EVS & Instance Store :  

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


## Route 53

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

# Register a domain name  :

- Route 53 is global.  
- Routing policies available on AWS :
  - simple Routing
  - weighted routing
  - latency-based routing
  - failover routing
  - geolocation routing
  - multivalue answer routing.

# Simple Routing Policy :

- you can only have one record with multiple IP addresses.
- if you specify multiple values in a record, Route 53 returns all values to the user in a random order.

# Weighted Routing Policy :

- weighted routing policies let you split your traffic based on different weights assigned. For example you can set 10% of your traffic to go to US-EAST-1 and 90% to go to EU-WEST-1.

# Latency Routing Policy :

- latency based routing allows you to route your traffic based on the lowest network for your end user. To use latency-based routing, you create a latency resource record set for the Amazon EC2 (or ELB) resource in each region that hosts your website. When Amazon Route 53 receives a query for your site, it selects the latency resource record set for the region that gives the user the lowest latency. Route 53 then responds with the value associated with that resource record set. Gives back the region that gives the fastest response time.


# Failover Routing Policy :

- Failover routing policiers are used when you want to create an active/passive set up. You may want your primary site to be in EU-WEST-2 and your secondary DR Site in AP-SOUTHEAST-2

- Route 53 will monitor the health of your primary site using a health check.

# Geolocation Routing Policy :

- lets you choose where your traffic will be sent based on the geographic location of your users.

# Multivalue Routing Policy :

- if you want to route traffic approximately randomly to multiple resources, such as web servers, you can create one multivalue answer record for each resource and, optionally, associate an Amazon Route 53 health check with each record. For example, suppose you manage an HTTP web service with a dozen web servers that each have their own IP address. No one web server could handle all of the traffic, but if you create a dozen multivalue answer records, Amazon Route 53 responds to DNS queries with up to eight healthy records in response to each DNS query. Amazon Route 53 gives differents answers to differents DNS resolvers. If a web server becomes unavailable after a resolver caches a response, client sofwtare can try another IP address in the response.  

!! there is a limit to the number of domain names that you can manages using route 53. its true and false, there is a limit of 50 domain names however this limit can be raised by contacting AWS support. !!

## Datbases on AWS

# RDS

- to connect rds instance to ec2, you need to ( in the security group of rds ) open MySQL on port 3306 to security group of your EC2 instance. 
