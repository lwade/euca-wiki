# Pre-install Checks/Tips for Walrus
For more info on how Walrus works and to get background on what is required see the [Walrus design and Admin info](Walrus) wiki page.

## Regular Walrus (non-HA)
Non-HA Walrus is fairly simple. It just requires a filesystem at $EUCALYPTUS_HOME/var/lib/eucalyptus/bukkits.
It is recommended to provide Walrus with its own disk(s) and filesystem if possible to help isolate Walrus work from the IO requirements of the rest of the host. This is particularly important if you are installing Walrus on the same host as the CLC. Walrus does IO-heavy and CPU intensive operations when serving images to the NCs as part of instance-run operations. Cacheing is done on the NC so that a single image need only be processed once, but depending on the size of the system and the amount of image turnover this may require significant resources on the Walrus host.

## HA Walrus/DRBD
It is highly recommended to read and understand [DRBD](http://www.drbd.org/users-guide-8.3/) prior to installing and configuring Walrus. Walrus in HA-mode utilizes DRBD to provide block-level replication of data between the two Walrus nodes.

### A few things to check *prior* to starting Walrus:
1. /etc/eucalyptus/drbd.conf exists and defines a resource on both nodes as using protocol-C for sync.
2. /etc/drbd/drbd.conf includes a 'include /etc/eucalyptus/drbd.conf' line.
3. Check the connection state of the drbd using 'drbdadm cstate r0' (assuming the resource you've configured is r0). Should show 'Connected'
4. Check the data state of the drbd resource using 'drbdadm dstate r0'. Should show 'UpToDate/UpToDate'
5. Make sure both Walrus nodes are in 'Secondary' roles. 'drbd-overview' will give an overview of the whole resource state and should show 'Secondary/Secondary'. Walrus will automatically select one node to become primary and will handle the transition itself.
6. Ensure that each node can be rebooted and DRBD state automatically restored properly (only reboot one node at a time to check).
7. Ensure that there is a valid filesystem on the drbd resource. Once the the resource is in Secondary/Secondary and UpToDate/UpToDate, make a node the primary and create the filesystem on the device (i.e. mkfs.ext4 /dev/drbd1).
8. Ensure that the eucalyptus user can mount the DRBD device at $EUCALYPTUS_HOME/var/lib/eucalyptus/bukkits (you can check by first making the node assume the 'Primary' role with 'drbdadm primary r0' then, 'sudo -u eucalyptus mount /dev/drbd1') and that the proper entry is in /etc/fstab to specify the mount. **Be sure to unmount it after checking and reset the role to 'Secondary' with 'drbdadm secondary r0'**.

### Things to check *after* starting Walrus:
1.The resource and device properties: walrus.blockdevice and walrus.resource should correspond with to the configured DRBD resource (e.g. 'r0') and drbd device (e.g. /dev/drbd1).

## Using iSCSI LUNs with Walrus

Things to consider if backing Walrus with one or more iSCSI LUNs:

1. If using LVM on-top of one or more iSCSI LUNS, ensure LVM LV (if using LVM on-top of iSCSI) is properly re-enabled after reboot. This may require a 'vgchange -ay' or similar in /etc/rc.local since iSCSI is started after LVM on reboot and therefore the VG will be disabled since it cannot find the backing LUN. Explicitly enabling it after the LUN is attached with iSCSI should resolve the issue.

2. In HA configurations if the above is not done DRBD will go to 'Diskless' mode if it cannot find the backing disk.

3. Be aware that if *not* using LVM on top of iSCSI that you must configure DRBD to reference the proper device name that won't change on reboot. Using /dev/sd* is not a good idea because it may change on reboot. It is better to use /dev/disk/by-id/scsi-* that corresponds to the LUN(s) being used. Put that path in the /etc/eucalyptus/drbd.conf file rather than /dev/sd* and it will use the udev id (based on the Serial Number given to the LUN by the SAN).
