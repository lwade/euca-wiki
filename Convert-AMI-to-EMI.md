_This document is in draft form--improvements and suggestions welcome._

Here are two ways to convert an AMI (Amazon Machine Image) living in Amazon into an EMI (Eucalyptus Machine Image):

## First Approach - Download from S3

If you own the AMI in question, you can download the bundled image directly from S3, unbundle, decrypt, and upload to Eucalyptus.

Things you'll need:

* A running Eucalyptus instance in your cloud.
* Access to the Amazon S3 bucket containing the AMI you need.

Steps:

1. Download and decrypt the AMI from Amazon by running:

    ```
    ec2-download-bundle -b <bucketname> -d ami-conversion
    ec2-unbundle-image -m ami-conversion/manifest.xml
    ```

2. Now that you have the root filesystem image, you need to add the kernel modules that you are using on  Eucalyptus. To do this, run a Eucalyptus instance and copy the kernel modules in to your AMI, and ensure that the /etc/fstab file is correct.

    ```
    mount -o loop ami.img /mnt
    rsync -i euca-key.private 192.168.1.100:/lib/modules/ /mnt/lib/modules/
    echo "devpts           /dev/pts      devpts   gid=5,mode=620   0 0" >> /etc/fstab
    umount /mnt
    ```

3. Now, bundle the modified AMI into Eucalyptus:

    ```
    euca-bundle-image -i ami.img --ramdisk eri-xxxxxx --kernel eki-xxxxxx
    euca-upload-bundle -b myami -m /mnt/manifest.xml
    euca-register myami/manifest.xml
    ```

    (Substitute eri-xxxxxx and eki-xxxxxx with the appropriate EKI and ERI ids based on the running Eucalyptus instance you copied the kernel modules from.)

4. Test the instance.

## Second Approach: Bundle a Running AMI

If you don't own the S3 buckets and can't download the image directly, you can launch an AMI, install euca2ools, and use euca-bundle-vol to import your image.

1. In EC2, launch an instance of the image you want to import to Eucalyptus. 
2. Install euca2ools on that instance: http://www.eucalyptus.com/download/euca2ools
2. Copy your admin-credentials.zip file from Eucalyptus to this instance.
3. Run euca-bundle-vol based on the instructions here: [[Bundling Images]]
4. Copy the files (.xml and .part files) to your Eucalyptus system.
5. Run euca-upload-bundle and euca-register to upload and register the new system.

*****

[[category:docs]]
[[category:images]]