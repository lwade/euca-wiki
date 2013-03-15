# The Filesystem Overlay Manager for Eucalyptus 3.1.x

The Overlay Manager uses SC's local filesystem ($EUCA_HOME/var/lib/eucalyptus/volumes) to store volumes and snapshots. The advantage of this is that no dedicated disk is required and only as much disk is used as actual data is stored in the volumes and snapshots. To ensure that enough space for the full volume is available at creation time rather than at volume-write time, set `<partition>.storage.zero_fill_volumes=true`. Setting the 'zero-fill' property will ensure that volumes are zero-filled upon creation to ensure that sufficient space is available for the full volume size so that no any out-of-space issues are encountered at volume creation rather than on writes to the volume from instances that might fail due to insufficient space on the SC. This does, however, incur an overhead at volume creation time since each block must be zeroed first.

## Which filesystem should you use? 
The only requirement is that the filesystem is Posix compliant. We test the SC with ext4 most extensively, but ext3 and xfs work as well (others like btrfs and reiserfs will probably work but have not been tested). We recommend configuring the filesystem with full journaling of both data and metadata rather than just metadata journaling. Full journaling will increase the robustness of the filesystem and minimize chances of data-loss due to machine failure. Eucalyptus does not currently support a shared filesystem for hosting the volumes because the SC has the expectation that the filesystem subtree it uses is under the sole control of the SC local and updates to that subtree made by another SC could cause file conflicts that the SC does not expect. Doing so can cause name conflicts, particularly for snapshots because snapshot names (i.e. snap-abc123) are globally unique in Eucalyptus.

Where are the volumes stored on the filesystem? The SC operates on the filesystem at $EUCALYPTUS/var/lib/eucalyptus/volumes, where $EUCALYPTUS is the root of the Eucalyptus installation on the SC machine (the default is '/' if installed by packages). This point in the filesystem can be provided by a separate LVM volume and for best performance that LVM volume should be on its own high-performance disks (the more spindles the better for random I/O performance).

What is the directory structure the SC uses?All volumes and snapshots on a Storage Controller are stored in a single flat directory structure at $EUCALYPTUS/var/lib/eucalyptus/volumes.

### Lifecycle of a Request
**Create volume**

1.) User issues euca-create-volume -z cluster01 -s 1 

2.) Message sent from eucatools to the Cloud Controller (CLC) 

3.) Message processed by EC2 API components in CLC, parses the message 

4.) CLC creates its own metadata about the volume to be created and gives it a name: _vol-abc0123_ 

5.) CLC sets status of volume to 'creating' and sends a message to Storage Controller in the appropriate zone/cluster/partition to actually create the volume. 

6.) SC checks that permissions are okay, sets up some metadata of its own, and starts the asynchronous volume create operation. Then, immediately replies back to the CLC, which has been waiting for the reply. 

7.) SC creates the volume itself using these steps below for Filesystem- or SAN-backed

Assuming /dev/loop0 is open and there are no other volumes, so target-id 1 is unused as well.

7.1. Create a file: $EUCALYPTUS/var/lib/eucalyptus/volumes/vol-abc0123 of size 1GB + lvm header size (~4MB): `dd -if /dev/zero -of $EUCALYPTUS/var/lib/eucalyptus/volumes/vol-X count=1 bs=1M`

7.2. Find unused loopback: `losetup -f`

7.3. Attach file to a loopback using losetup: `losetup /dev/loop0 $EUCALYPTUS/var/lib/eucalyptus/volumes`

7.4. Create physical volume from loopback: `pvcreate /dev/loop0`.

7.5. Create volume group: `vgcreate /dev/loop0 vg-xyza` (the volume group name is a 4 char random hash).

7.6. Create logical volume: `lvcreate -n lv-jklm -l 100%FREE vg-xyza`

7.7. Create new iSCSI target: `tgtadm --lld iscsi --op new --mode target --tid 1 -T <some_prefix><SC_NAME>:store0`

7.8. Create new lun in the target: `tgtadm --lld iscsi --op new --mode logicalunit --tid 1 --lun 1 -b /dev/vg-xyza/lv-jklm`

7.9. Add eucalyptus CHAP user to target: `tgtadm --lld iscsi --op bind --mode account --tid 1 --user eucalyptus`

7.10. Bind the target to all interfaces: `tgtadm --lld iscsi --op bind --mode target --tid 1 -I ALL`

8.) Concurrently, the CLC is periodically checking the state of all volumes on every SC, so now that the volume is ready, the CLC sees it and sets the volume state to 'available' as viewable by the user via euca-describe-volumes commands.

***

**Attach volume**

1.) User runs euca-attach-volume -d /dev/sdf -i i-123abc vol-abc0123

2.) CLC gets message to attach the volume.

3.) CLC does some checks to make sure this is a valid operation (i.e. volume is not already attached)

4.) CLC finds the appropriate zone/cluster/partition where the volume is located.

5.) CLC sends message to SC telling it to prep the volume for attach and to get the connection information to send to the Node Controller.

6.) SC does attach stuff (see below) and returns the connection information necessary for clients to attach

For Filesystem-backed SC, this is essentially a No-Op.

7.) CLC gets response and sends request to Cluster Controller (CC) in proper zone/cluster/partition to do the attach of the volume to the instance.

8.) CC looks up the Node Controller (NC) hosting the instance requested

9.) CC sends the doAttach message to the proper NC including the connection info.

10.) NC uses the connection info to connect its local iSCSI initiator to the iscsi target on the SC

11.) NC has connection to the iSCSI target and has a local hypervisor block-device (i.e. /dev/sdd)

12.) NC exposes that block device to the instance via libvirt and does proper mapping of device to user-desired name (i.e. NC's /dev/sdd becomes the instance's /dev/sdf, as per the original user request).
