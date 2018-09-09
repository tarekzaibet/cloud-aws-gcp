## IAM :

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

## S3 :

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
- S3 Storage classes / tiers : S3, S3-IA (infr accessed), S3 One zone- IA, Glacier
(Archival 3 to 5 hours retrival).
- S3 object : key (name), value(data), version id, metadata, subresources
(ACL,Torrent)
- not suitable to store OS
- by default buckets are private and all objects stored inside them are private
- control access to bucket using ACL or using bucket policies

# Versioning:
- stores all versions of an object (including all wirtes and even if you delete an object)
- great backup tool
- once enabled can't be disabled, but can be suspended
- integrates with lifecyle rules
- can have additional level of security by adding MFA for delete operations.

# Cross region replication:
- Versioning must be enabled on both the source and destination buckets.
- Regions must be unique.
- Files in an existing bucket are not replicated automatically. All subsequent
updated files will be replicated automatically.
- You cannot replicate to multiple buckets or use daisy chaining
- Delete markers are replicated
- Deleting individual versions or delete markers will not be replicated
