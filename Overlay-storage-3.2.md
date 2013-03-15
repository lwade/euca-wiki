# Filesystem Overlay Manager EBS provider for Eucalyptus 3.2.x

The core filesystem overlay system for Eucalyptus 3.2.1 uses the same filesystem concepts as from that of 3.1.x. For that info see: [Eucalyptus Overlay Manager 3.1](Overlay-storage-3.1)

The significant change from 3.1.x to 3.2.x is how and when volumes are exported via iSCSI and how loopback devices are used. Volumes are only exported as iSCSI targets on an 'attach-volume' command from the user, and loopbacks are only in use when the volume is attached. This frees up loopbacks and removes the total-volume limit on loopbacks from the SC. In 3.2.x the loopback limitation is only for _attached_ volumes, not extant volumes.

### Lifecycle of a Request (3.2.x)
**Create volume**

1.) User issues euca-create-volume -z cluster01 -s 1 

2.) Message sent from eucatools to the Cloud Controller (CLC) 

3.) Message processed by EC2 API components in CLC, parses the message 

4.) CLC creates its own metadata about the volume to be created and gives it a name: _vol-abc0123_ 

5.) CLC sets status of volume to 'creating' and sends a message to Storage Controller in the appropriate zone/cluster/partition to actually create the volume. 

6.) SC checks that permissions are okay, sets up some metadata of its own, and starts the asynchronous volume create operation. Then, immediately replies back to the CLC, which has been waiting for the reply. 

7.) SC creates the volume itself using these steps below:

Assuming /dev/loop0 is open and there are no other volumes, so target-id 1 is unused as well.

7.1. Create a file: $EUCALYPTUS/var/lib/eucalyptus/volumes/vol-abc0123 of size 1GB + lvm header size (~4MB): `dd -if /dev/zero -of $EUCALYPTUS/var/lib/eucalyptus/volumes/vol-X count=1 bs=1M`

7.2. Find unused loopback: `losetup -f`

7.3. Attach file to a loopback using losetup: `losetup /dev/loop0 $EUCALYPTUS/var/lib/eucalyptus/volumes`

7.4. Create physical volume from loopback: `pvcreate /dev/loop0`.

7.5. Create volume group: `vgcreate /dev/loop0 vg-xyza` (the volume group name is a 4 char random hash).

7.6. Create logical volume: `lvcreate -n lv-jklm -l 100%FREE vg-xyza`

**Note the change here from 3.1.x, in 3.2.x the volume is not exported via iSCSI until an 'attach' call is made by the user**

8.) Disables the logical volume: `lvchange -an vg-xyza/lv-jklm`

9.) Frees the loopback device: `losetup -d /dev/loop0`

9.) Concurrently, the CLC is periodically checking the state of all volumes on every SC, so now that the volume is ready, the CLC sees it and sets the volume state to 'available' as viewable by the user via euca-describe-volumes commands.

***

**Attach volume**

1.) User runs euca-attach-volume -d /dev/sdf -i i-123abc vol-abc0123

2.) CLC gets message to attach the volume.

3.) CLC does some checks to make sure this is a valid operation (i.e. volume is not already attached)

4.) CLC finds the appropriate zone/cluster/partition where the volume is located.

5.) CLC sends message to SC telling it to prep the volume for attach and to get the connection information to send to the Node Controller.

6.) SC does attach stuff (see below) and returns the connection information necessary for clients to attach

6.1. Create new iSCSI target: `tgtadm --lld iscsi --op new --mode target --tid 1 -T <some_prefix><SC_NAME>:store0`

6.2. Create new lun in the target: `tgtadm --lld iscsi --op new --mode logicalunit --tid 1 --lun 1 -b /dev/vg-xyza/lv-jklm`

6.3. Add eucalyptus CHAP user to target: `tgtadm --lld iscsi --op bind --mode account --tid 1 --user eucalyptus`

6.4. Bind the target to all interfaces: `tgtadm --lld iscsi --op bind --mode target --tid 1 -I ALL`

7.) CLC gets response and sends request to Cluster Controller (CC) in proper zone/cluster/partition to do the attach of the volume to the instance.

8.) CC looks up the Node Controller (NC) hosting the instance requested

9.) CC sends the doAttach message to the proper NC including the connection info.

10.) NC uses the connection info to connect its local iSCSI initiator to the iscsi target on the SC

11.) NC has connection to the iSCSI target and has a local hypervisor block-device (i.e. /dev/sdd)

12.) NC exposes that block device to the instance via libvirt and does proper mapping of device to user-desired name (i.e. NC's /dev/sdd becomes the instance's /dev/sdf, as per the original user request).
