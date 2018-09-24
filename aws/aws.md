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
