# Inject ephemeral disk into root disk

Normally the ephemeral disk is being provided as a second partition to the image and are mounted on a separate mount point. You can change this behaviour by modifying your initramfs.

## Centos 6 - x64

Those steps have been tested on a CentOS6 x64 instance using KVM ( I guess, it will work with other virtualisation but I have not tested it )

Create a script to delete / create partitions. 

<pre>
cat > 05-extend-rootdisk.sh << EOF
#!/bin/sh
# -*- mode: shell-script; indent-tabs-mode: nil; sh-basic-offset: 4; -*-
# ex: ts=8 sw=4 sts=4 et filetype=sh 
PATH=/usr/sbin:/usr/bin:/sbin:/bin
# Get rootdisk
ROOTDISK=`ls /dev/[xvsh]*da`
# Get the begining of partition 1
BEGINING_PARTITION_1=`parted -m ${ROOTDISK} unit B print | grep ^1: | awk -F: '{print $2}'`
# Get the end of partition 2
END_PARTITION_2=`parted -m ${ROOTDISK} unit B print | grep ^2: | awk -F: '{print $3}'`
# Delete partition  1 & 2
parted ${ROOTDISK} rm 1
parted ${ROOTDISK} rm 2
# Create partition 1 
parted -s  ${ROOTDISK} unit B mkpart primary ${BEGINING_PARTITION_1} ${END_PARTITION_2}

parted -l ${ROOTDISK}
# Resize FS on partition 1
e2fsck -fy ${ROOTDISK}1
resize2fs ${ROOTDISK}1
EOF
chmod +x 05-extend-rootdisk.sh
</pre>

CentOS6 initramfs directory structure differ from Fedora structure. We will add our script as part of the mount directory.
emi-D8533CD7
<pre>
dracut --include 05-extend-rootdisk.sh /mount --install 'resize2fs e2fsck parted awk' initramfs.img
</pre>

Ok you are ready to bundle / upload your new ramdisk !

## Ubuntu 

For all version above, 10.04, you should be able to use growroot. The following steps have been tested successfully with 10.04 / 12.04.

You can find some doc on how to do it @ [[http://manpages.ubuntu.com/manpages/lucid/man8/initramfs-tools.8.html]]

First we need to add some extra binary to initramfs, we can do it using hooks. Create a file inemi-D8533CD7 /etc/initramfs-tools/hooks/
<pre>
cat > /etc/initramfs-tools/hooks/autoextendrootfs << EOF
#!/bin/sh
PREREQ=""
prereqs()
{
	echo "$PREREQ"
}

case $1 in
prereqs)
	prereqs
	exit 0
	;;
esac

. /usr/share/initramfs-tools/hook-functions

# Copy binary in place to be used by script later
copy_exec /sbin/parted /sbin
copy_exec /sbin/e2fsck /sbin
copy_exec /sbin/resize2fs /sbin
copy_exec /usr/bin/awk /usr/bin
copy_exec /usr/bin/touch /usr/bin
copy_exec /bin/grep /bin
EOF
</pre>

During the boot process, you can run a script at different stages of the initramfs. We are going to run a script before the rootdisk get mounted ( local-premount ) to modify the partitions and resize the FS.

<pre>
cat > /etc/initramfs-tools/scripts/local-premount/autoextendrootfs << EOF
#!/bin/sh
PREREQ=""
prereqs()
{
	echo "$PREREQ"
}

case $1 in
prereqs)
	prereqs
	exit 0
	;;
esac
ROOTDISK=`ls /dev/[xvsh]*da`
# Begin real processing below this line
# Get the begining of partition 1
BEGINING_PARTITION_1=`parted -m ${ROOTDISK} unit B print | grep ^1: | awk -F: '{print $2}'`
# Get the end of partition 2
END_PARTITION_2=`parted -m ${ROOTDISK} unit B print | grep ^2: | awk -F: '{print $3}'`
# Delete partition  1 & 2
parted ${ROOTDISK} rm 1
parted ${ROOTDISK} rm 2
# Create partition 1 
parted -s ${ROOTDISK} unit B mkpart primary ${BEGINING_PARTITION_1} ${END_PARTITION_2}
parted -l ${ROOTDISK}
# Create a blank mtab as otherwise resize2fs refuse to run
touch /etc/mtab
# Resize FS on partition 1
resize2fs -f ${ROOTDISK}1
EOF
</pre>

You will need to make sure that both files are executable ( chmod +x ) and you can now update your initramfs.
<pre>update-initramfs -v -u</pre>

You can now bundle / upload your image.

( I need to revisit the Fedora part as I have just discover that passing the --install <commands> forces dracut to grab the commands as well as the required libraries )


## Fedora 16

Disclaimer, I have tested the following steps on a F16 image with Eucalyptus 3.0. The NC are using KVM

Grab the current initramfs and explode it into a directory
<pre>
cp /boot/initramfs* /tmp/initramfs.gz
gunzip /tmp/initramfs.gz -c > /tmp/initramfs.img
mkdir /tmp/custom-initramfs
cd /tmp/custom-initramfs
cat /tmp/initramfs.img | cpio -idv
</pre>

To modify the partitions, we are going to use the parted command. It is not available as part of the default initramfs, so we will need to add it as well as the necessary library. To find out which library are required, you can run strace
<pre>
strace -e open parted
open("/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libparted.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libreadline.so.6", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libtinfo.so.5", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libuuid.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libdl.so.2", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libdevmapper.so.1.02", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libsepol.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libblkid.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libudev.so.0", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/librt.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libgcc_s.so.1", O_RDONLY|O_CLOEXEC) = 3
open("/lib64/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3
</pre>

Now we need to make sure that all required libraries are available as part of the initramfs, if they are not we need to copy them across. I was missing :
 - libparted
 - libreadline
 - libtinfo

You will need to copy the missing library from your host and create the soft link
<pre>
cp /lib64/libparted.so.1.0.0 lib64/
ln -s /lib64/libparted.so.1.0.0 lib64/libparted.so.1
cp /lib64/libreadline.so.6.2 lib64/
ln -s /lib64/libreadline.so.6.2 lib64/libreadline.so.6 
cp /lib64/libtinfo.so.5.9 lib64/
ln -s /lib64/libtinfo.so.5.9 lib64/libtinfo.so.5   
cp /sbin/parted sbin/
</pre>

We need to do the same with resize2fs. I was not missing any library, so I just add to copy the resize2fs binary 
<pre>
cp /sbin/resize2fs sbin/
</pre>

Now we just need to add a script in the dracut hooks
<pre>
cat > lib/dracut/hooks/pre-mount/10-extend-rootdisk.sh << EOF
#!/bin/sh
# -*- mode: shell-script; indent-tabs-mode: nil; sh-basic-offset: 4; -*-
# ex: ts=8 sw=4 sts=4 et filetype=sh 
PATH=/usr/sbin:/usr/bin:/sbin:/bin
# Get the begining of partition 1
beg_part1=`parted -m /dev/vda unit B print | grep ^1: | awk -F: '{print $2}'`
# Get the end of partition 2
end_part2=`parted -m /dev/vda unit B print | grep ^2: | awk -F: '{print $3}'`
# Delete partition  1 & 2
parted /dev/vda rm 1
parted /dev/vda rm 2
# Create partition 1 
parted -s  /dev/vda unit B mkpart primary  ${beg_part1} ${end_part2}
# Resize FS on partition 1
e2fsck -fy /dev/vda1
resize2fs /dev/vda1
EOF
</pre>

Now, we need to recreate the initramfs
<pre>
find . -print0 | cpio --null -ov --format=newc | gzip -9 > ../initramfs-custom.img
</pre>

That's it, you can now bundle, upload, register initramfs-custom.img