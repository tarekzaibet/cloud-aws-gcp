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
