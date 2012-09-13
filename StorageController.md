# EBS/Volumes
## DesignDoc for the Cloud Admin
The Storage Controller (SC) is the component of Eucalyptus that manages EBS volumes. Eucalyptus uses the iSCSI protocol to connect EBS volumes to instances (NCs actually), and uses standard linux commands for configuring and exporting the volumes to instances. Volumes are represented as files on the filesystem of the machine hosting the SC. The SC manages creating, deleting, snapshotting, and exporting volumes in response to both user commands (example: euca-create-volume and euca-create-snapshot) and system operations (example: euca-run-instances of a boot-from-ebs instance).

There are two broad SC configurations: filesystem-backed SC and SAN-backed SC.

For filesystem-backed SCs, the SC machine is both on the control and data path for the volumes and snapshots. This means the machine running the SC component will host the volume and snapshot data, manage the iSCSI connections to the volumes, and service iSCSI block requests as NCs (and vm instances) read and write to the volume. In this configuration it is important to understand and plan-for the I/O requirements your specific deployment will have and ensure that the machine hosting the SC is up to the task. We recommend lots of memory and as many high-speed disks as possible in a reliable and performant configuration (RAID 10,01,5,6 but not just 0 or 1). SSDs would also make an excellent addition to an SC machine due to their good IOPS capabilities. For more recommendations on hardware configurations and best-practices, see our Storage:Best Practices section.

### Backing Store (where the volumes and data are)
**Default Backing Store: Posix Filesystem**
The default configuration for the SC uses a standard Posix filesystem as the backing storage for EBS volumes. Each EBS volume is a single file and each snapshot is also a single file. The files for snapshots and volumes are not connected in any way in the filesystem (i.e. they are not symlinks to each other etc). The SC has the expectation that the filesystem it uses is under the sole control of the SC and is not a shared filesystem. The SC does not support an NFS-mounted filesystem that is shared between multiple SCs in multiple clusters (or even in an HA pair). Doing so can cause name conflicts, particularly for snapshots because snapshot names (i.e. snap-abc123) are globally unique in Eucalyptus.

**SAN Storage (paid subscriptions only)**
The SC can also be configured to interact with a SAN device to host EBS volumes. In this configuration the SC manages metadata for volumes and snapshots and issues commands to the SAN to perform required operations, but the volumes and snapshots themselves are hosted by the SAN and the SAN manages the iSCSI connections to the NCs. In this configuration the SC is not in the data path for EBS volume block traffic and therefore this configuration typically yields much better performance.

### Export Volumes (how the volumes are accessible by instances)
**SAN-backed SCs**
For SAN-backed SCs the SAN exports the volumes directly to NCs using iSCSI. This is very device specific and we will not discuss it further in this section. Eventually we will probably put up an entire section dedicated to SAN-backed SCs as well as the devices we support and how they are used, but that feature is not part of the open-source Eucalyptus (we have to save some good bits for the paying customers right?).

**Filesystem-backed SCs**
SCs using a filesystem as the backend export volumes using standard linux iSCSI servers and tools. Specifically, the SC uses [the linux ISCSI Framework (TGT)](http://stgt.sourceforge.net/) to export the volumes as iSCSI stores to the NCs. The SC creates an LVM volume from the file using a loopback device (more details below) and exports that device as a store using tgtadm.

### Snapshotting
**Filesystem-backed SCs**
A snapshot operation is a simple file-copy in this case. However, there are some complexities due to the headers added to the file when it becomes an lvm volume. For that reason, we snapshot by copying (using dd) the source file into the destination file after the destination has been created and configured as an lvm volume independently of the source. The SC only copies the final lvm volume devices so that all lvm header and metadata information stays unique to each file.

### Startup and Shutdown 
there is a lot of state in the StorageManager
### Lifecycle of a Request
Create volume
Attach volume
Snapshot
how clc is involed
### Database (same issue as walrus)

## DesignDoc for the Developers
code is the ultimate truth here
