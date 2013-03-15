# Netapp SAN Support in the Eucalyptus Storage Controller

## Some Terminology
_EBS Volume_ - The logical volume that the SC manages as part of the EBS service. euca-describe-volumes shows these

_EBS Snapshot_ - The logical snapshot entity that the SC manages for the EBS service. euca-describe-snapshot shows these

_FlexVolume_ - A Netapp container entity in Netapp ONTAP that allocates space and hosts LUNs

_LUN_ - The actual data container on the Netapp that Eucalyptus uses to provide both EBS Volumes and EBS Snapshots

_IGroup_ - A Netapp construct that is a logical grouping of iSCSI Initiators (basically, hosts) that is a mechanism to provide host access to a lun.

## Requirements of the Netapp for Eucalyptus
Eucalyptus requires:
* Netapp SAN with ONTAP 7.0+. Supports ONTAP 7 and ONTAP 8+ (Eucalyptus 3.2 does not support cluster-mode/vServer on ONTAP 8.0+, but Eucalyptus 3.3 will add that support) 
* Administrative login credentials to the Netapp head-filer (For 7-mode Netapp SANs). Must be able to create/delete volumes using these credentials. With these credentials you should be able to ssh to the Netapp's management interface and perform operations like listing volumes.  Note: these are *NOT* CHAP credentials.
* An available Aggregate with some free space. Eucalyptus does not require exclusive access to the aggregate, it will only operate on FlexVolumes/volumes that it has created, but as a best practice it is recommended to give each Eucalyptus cluster its own aggregate.
* FlexClone license on the Netapp. Eucalyptus uses FlexVolumes to contain EBS volumes and uses FlexClone to create EBS snapshots and volumes.
* iSCSI License for ONTAP. The default for Netapp is NFS, so iSCSI requires a specific license to work. Eucalyptus supports _only_ iSCSI with Netapp.

## Configuring Eucalyptus to use Netapp
See the Eucalyptus documentation for this here: [Configuring Eucalyptus SC for Netapp](http://www.eucalyptus.com/docs/3.2/ig/configure_storage_controller.html#configure_storage_controller)

## Basic Overview of EBS with Eucalyptus and Netapp SANs

### Create
1. User -> euca-create-volume -s 1 -z PARTITIONX -> CLC
2. CLC -> createVolume() -> PARTITIONX's SC
3. SC -> create flexvol -> netapp
4. SC -> create lun in flexvol -> netapp

### Attach vol-x to instance-y
1. User -> euca-attach-volume vol-X -d /dev/sdf -i i-YYYYY -> CLC
2. CLC -> attachVolume(vol-X, host-iqn) -> SC
3. SC -> check for existing igroup for iqn -> netapp
4. SC -> add iqn to existing igroup or create new one -> netapp
5. SC -> map lun for vol-X to igroup -> netapp
6. SC -> add auth to igroup -> netapp
vol-X is now ready for iSCSI connections from the NC host where instance-y is running.

NOTE: The SC periodically sets the default iSCSI initiator authentication to "none" meaning that no host can connect a Netapp LUN even if the host is present in an IGroup thats mapped to the LUN unless there is an authentication rule overriding the default for that host. The overriding rule is added as a part of attach process (step 6), so this ensures that there are never any extra permissions left open except those explicitly allowed by Eucalyptus.

### Snapshot vol-X to snap-Y
1. User -> euca-create-snapshot vol-X -> CLC
2. CLC -> create snapshot -> SC
3. SC -> clone flexvol vol-X to new flexvol snap-Y -> netapp
4. SC -> split clone -> netapp
5. SC -> check split status -> netapp (do this over and over until split is done)
6. Now the SC does an "attachVolume" for the SC itself so it can copy the snapshot to walrus.

