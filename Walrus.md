# Walrus/S3
What it is, What it does
![Walrus haz many bukkits!](http://www.funny-pics-space.com/wp-content/uploads/2012/04/walrus-ma-bucket-5.jpg)

## DesignDoc for the Cloud Admin
### Backing Store (where the buckets are)
Currently, Walrus requires a POSIX filesystem to store buckets and objects. It uses, by default, the filesystem at $EUCALYPTUS/var/lib/eucalyptus/bukkits and creates a directory per bucket and each object is stored as a single file within that bucket directory.

### HA (DRBD and more)
failover, setup, DRBD info etc ...
### How is the Backing Store Used
Bucket directories are named exactly as the bucket itself, so bukkits/bucket01 is the directory for the 'bucket01' bucket, as expected. However, object files do not retain their name on the filesystem for several reasons and are instead stored as files which have a random hash as the name. This prevents name conflicts due to object versioning and allows the system to handle object names which are not valid POSIX filenames (as the S3 API allows '/' in object names, for example).

This does of course, mean that the number of buckets in a single Walrus installation is limited by the number of subdirectories allowed by the filesystem. For ext3 that number is 32,000 and for ext4 it is 64,000. XFS and btrfs have much higher limits (i.e. > 1M). XFS also has many other advantageous features such as full metadata journalling that allows repair of the FS without a full fsck run. There is plenty of info online for all the filesystems, but we suggest XFS or ext4. For XFS info see [XFS documentation](http://xfs.org/index.php/XFS_Papers_and_Documentation)

Walrus does not utilize the filesystem for storing metadata, that is all persisted in the database managed by the Cloud Controller (CLC). Therefore, Walrus does not require extended metadata support from the filesystem itself.

### Lifecycle of a Request
Get and Put examples
### Walrus API
Walrus implements much of the Amazon Web Services Simple Storage Service (S3) API: [S3 Documentation](http://aws.amazon.com/documentation/s3/)

Specifically, Walrus supports: get/put/head/post of objects and buckets, object/bucket ACLs, and object/bucket versioning. Walrus does not yet implement static websites, bucket-policies, degraded durability modes, or multi-part uploads. We are, of course, working on many of those features, but can't give a timeline just yet. Walrus supports the S3 SOAP API as well as REST and supports normal S3 authorization-header-based request authentication or query-string authentication.

### Walrus and the Database
Walrus uses a database to manage metadata for buckets and objects as well as ACLs and policies. Walrus has its own database but is currently co-hosted by the Cloud Controller (CLC) with all of the other databases that Eucalyptus uses. The current implementation leverages a PostGreSQL database although version prior to Eucalyptus 3.1 ran on MySQL.

Walrus, like all Eucalyptus components written in Java, utilizes Hibernate to interact with the db layer and database HA is handled via a replicating Hibernate JDBC connection. For more info on how the DB is managed and synchronized for HA, see the CLC documentation.

### Authentication, ACLs, Permissions
See the [S3 Documentation](http://aws.amazon.com/documentation/s3/). Walrus implements and supports S3-style ACLs and IAM policies. It does not support bucket-policies or bucket-lifecycles (yet!).

## DesignDoc for the Developers
code is the ultimate truth here

*****

[[category.components]]