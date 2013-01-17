For Fedora 18, [downloadable cloud images](http://mattdm.fedorapeople.org/cloud-images/) are being provided.  These are whole-disk images, though, which require some conversion to be loaded into eucalyptus as instance-store images.  Below is a shell script which takes care of this process.  It takes two arguments: the qcow filename and the destination bucket.
It assumes that you have euca2ools and qemu-img installed on your system, and due to its use of loopback devices, it must be run as root.

```
#!/bin/bash -x
QCOWFILE=$1
RAWFILE=$( echo $1 | sed 's/qcow2/raw/' )
EXTFILE=$( echo $RAWFILE | sed 's/raw/ext4/' )
qemu-img convert -O raw $QCOWFILE $RAWFILE
LODEV=$( losetup -f --show $RAWFILE )
kpartx -a $LODEV
cp --sparse=always <(su -c "dd if=/dev/mapper/"$( basename $LODEV )"p2 bs=8M") $EXTFILE
mount -o loop $EXTFILE /mnt
KERNEL=$( basename /mnt/boot/vmlinuz-* )
euca-bundle-image --kernel true -i /mnt/boot/$KERNEL
euca-upload-bundle -b $2 -m /tmp/$KERNEL.manifest.xml
EKI=$( euca-register $2/$KERNEL.manifest.xml | awk '{ print $2 }' )
INITRD=$( basename /mnt/boot/initr* )
euca-bundle-image --ramdisk true -i /mnt/boot/$INITRD
euca-upload-bundle -b $2 -m /tmp/$INITRD.manifest.xml
ERI=$( euca-register $2/$INITRD.manifest.xml | awk '{ print $2 }' )
umount /mnt
kpartx -d $LODEV
losetup -d $LODEV
euca-bundle-image --kernel $EKI --ramdisk $ERI $EXTFILE
euca-upload-bundle -b $2 -m /tmp/$( basename $EXTFILE ).manifest.xml
euca-register $2/$( basename $EXTFILE ).manifest.xml
```