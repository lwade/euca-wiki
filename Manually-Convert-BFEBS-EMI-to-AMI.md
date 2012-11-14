You can easily convert EBS-backed images from Eucalyptus to Amazon and back again with usually only minor changes to the root filesystem. This document describes how.

# EMI to AMI (Eucalyptus to Amazon)

## Requirements

* A snapshot of an EBS-backed instance in your Eucalyptus cloud.
* An AWS account and ec2-api-tools installed and configured.

## Steps

1. The first step in the EBS-backed EMI to AMI conversion is to transfer the contents of the EBS volume in Eucalyptus to a file on disk that can be imported using ec2-import-volume. Do this by first launching a "scratch" instance in Eucalyptus, creating a volume from the EBS snapshot, attaching the volume, and using "dd" to copy the contents of the volume out.

 _(Note: if you already have the disk image available outside of a Eucalyptus volume, you can skip this step.)_

    ```
    $ euca-run-instances -k admin emi-xxxxxxxx
    $ euca-create-volume --snapshot snap-xxxxxxxx -z cluster01
    $ euca-attach-volume -v vol-xxxxxxxx i-xxxxxxxx -d /dev/sdc

    (Log into the instance, verify volume device with fdisk -l)

    $ ssh -i admin.key root@10.110.1.86 "dd if=/dev/sdc bs=1M" | dd of=ebs-disk.raw bs=1M
    $ euca-detach-volume vol-xxxxxxxx
    ```

2. Make sure that the kernel specified as the default in /boot/grub/menu.lst is a Xen-compatible kernel. You may want to edit this file by mounting the .raw image with -o loop and replacing it with a simple grub stanza:

 ```
 default 0
 timeout 0
 
 title Xen 2.6.32.24-0.2
   root (hd0,0)
   kernel /boot/vmlinuz-2.6.32.46-0.3-xen root=LABEL=rootdisk console=ttyS0
   initrd /boot/initrd-2.6.32.46-0.3-xen
 ```
 
 Depending on your guest OS version and configuration, you may need to specify a different kernel than the one you generally use in Eucalyptus on KVM.

3. Now that you've copied the volume contents out of Eucalyptus, you need to upload it to EC2. There is an EC2 command line tool to do this in one step. Run: 

 ```
 $ ec2-import-volume ebs-disk.raw -f raw -z us-east-1a -b bfebs-emi-import-test \
   -o $AWS_ACCESS_KEY -w $AWS_SECRET_KEY
 ```

 This step will take some time. Watch the progress by using "ec2-describe-conversion-tasks". When it's  finished, that command will show you the vol-xxxxxxxx volume ID.

4. Snapshot and register the new volume into an EBS-backed AMI:

 ```
 $ ec2-create-snapshot vol-xxxxxxxx
 ```

 Now register the volume as an AMI. Note the final option here: --kernel aki-b4aa75dd -- this instructs AWS to     use the PV-GRUB kernel to launch this image, which will use the built-in grub configuration in your own image.   Please see http://docs.amazonwebservices.com/AWSEC2/latest/UserGuide/UserProvidedkernels.html for more information. You may need to change this to another AKI ID depending on the AWS region in which you are operating.

 ```
 $ ec2-register -n "Eucalyptus EMI Imported Image" --root-device-name /dev/sda -b /dev/sda=snap-xxxxxxxx -a   x86_64 --kernel aki-b4aa75dd  
 ```

5. You now have an AMI that can be launched.

# AMI to EMI (Amazon to Eucalyptus)

## Requirements

* Access to a snap-xxxxxx or vol-xxxxxx on Amazon that holds the root filesystem volume you want to import.
* A Eucalyptus cloud.

## Steps

1. Copy the volume contents to a local machine:

 ```
 $ ec2-attach-volume vol-xxxxxx -i i-xxxxxx -d /dev/sdc
 $ ssh -i aws-key.priv root@aws-instance "dd if=/dev/sdc bs=1M" | dd of=imported-ami.raw bs=1M
 $ ec2-detact-volume vol-xxxxxx
 ```

2. Now you have a copy of the volume on disk as imported-ami.raw. Now you need to transfer it to a volume in your Eucalyptus cloud, using exactly the same procedure, except the other way. In a KVM Eucalyptus cloud, the volume names are "vdX" instead of "sdX" as they are in Amazon. Assuming you have a Eucalyptus "scratch" instance running:

 ```
 $ euca-attach-vol vol-yyyyyy -i i-yyyyyy -d /dev/vdc
 $ dd if=imported-ami.raw bs=1M | ssh -i euca-key.priv root@scratch-instance "dd of=/dev/vdc bs=1M"
 $ euca-detach-volume vol-yyyyyy
 ```

3. Then snapshot and register the new volume as an EMI:

 ```
 $ euca-snapshot vol-yyyyyy
 $ euca-register -n ami-import-test --root-device-name /dev/vda -b /dev/vda=snap-xxxxxx 
 ```

You will probably need to modify the initrd within the instance. This is left as an exercise for the reader and we will document it here later.

*****

[[category.docs]]
[[category.images]]