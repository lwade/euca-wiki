You can directly import an HVM AMI from Amazon into Eucalyptus with a few modifications.

## Requirements

* An HVM AMI running in Amazon
* A scratch instance running in Amazon
* A scratch instance running in Eucalyptus

## Steps

### In EC2:

1. Run a cc2.8xlarge instance of the HVM AMI. This example uses ami-6e649707, a SLES11 SP1 image.

 ```
 $ ec2-run-instances -k keypair ami-6e649707 -z us-east-1c -t cc2.8xlarge2
 ```

3. Wait for it to go to "running" state, and then immediately stop the instance:

 ```
 $ ec2-stop-instances i-87f05bf8
 ```

4. Take a snapshot of the instance's backing volume:

 ```
 $ ec2-create-snapshot vol-4f4fce33
 ```

5. Create a volume from the snapshot: 
 
 ```
 $ ec2-create-volume --snapshot snap-c8d4c681 -z us-east-1c
 ```

6. Attach the volume to your "scratch" instance:

 ```
 $ ec2-attach-volume -i i-39f85346 vol-1f61e063 -d /dev/sdf
 ```

 Log into the instance and run fdisk -l to determine correct attachment device. In this example it was /dev/xvdf, not /dev/sdf--the device name depends on the Linux kernel.

7. Download the volume contents to local disk:

 ```
 $ ssh -i keypair.priv root@aws-scratch-instance "gzip - < /dev/xvdf" | zcat > ami-hvm-10gb-suse.img
 ```

### In Eucalyptus:

1. Create a 10GB volume to hold the AMI contents:

 ```
 # euca-create-volume -s 10 -z cluster01
 ```

9. Attach it to your scratch instance:

 ```
 # euca-attach-volume -i i-scratch vol-xxxxxx -d /dev/vdc
 ```

 Log into the scratch instance and run fdisk -l to determine the correct device to which the volume was attached to.

10. Copy the volume contents to the instance:

 ```
 # gzip - < ami-hvm-10gb-suse.img | ssh -i tgerla-euca.priv root@scratch-instance "zcat > /dev/vdc"
 ```

11. Log into the scratch instance and run these commands (assuming volume device is /dev/vdc):

 ```
 # yum install parted.x86_64
 # partprobe /dev/vdc
 # mount /dev/vdc1 /mnt
 # mount -o bind /dev /mnt/dev
 # mount -o bind /proc /mnt/proc
 # chroot /mnt
 # passwd <-- (set a password for 'root' here)
 # mkinitrd
 # exit
 # e2label /dev/vdc1 rootdisk
 ```

 NOTE: These commands will be different for other Linux distributions. In particular, regeneration of the initial ramdisk (mkinitrd) will be different.

12. Edit /mnt/boot/grub/menu.lst and ensure that the selected kernel line says: "root=LABEL=rootdisk" not "root=/dev/sdc1" or similar. This is important so that grub can find the right root device.

13. Unmount the partition:

 ```
 # umount /mnt/dev
 # umount /mnt/proc
 # umount /mnt
 ```

14. Detach the volume:

 ```
 # euca-detach-volume vol-xxxxxx
 ```

15. Snapshot and register the new EMI:

 ```
 # euca-create-snapshot vol-XXXXXX
 # euca-register -n "Imported HVM image" --root-device-name /dev/vda -b /dev/vda=snap-XXXXXX
 ```