_**(Note: this content is from the old project.eucalyptus.com site and may be outdated.)**_

h1. Partnerimages

h2. Purpose

In an effort to provide value to our customers, we provide some “base” linux images to our customers. We've been working to make it easier for customers to get these images installed on their cloud (eustore tools). Base images are a fair starting point, but they really don't have anything more than the base OS.

We have many partners who create images with their software installed. We could also try offering those to our customers so that the same convenience mechanisms could be leveraged to install them on the customer clouds.


h2. Responsibilities

Eucalyptus is currently hosting base images and those will likely be called starter images as we roll out upgraded ones. We could also host partner images in the same place. We would designate them differently than our own base images and indicate that they were supplied by a partner.

It would be up to the partner to validate the images. They would need to supply them in the correct packaging format (see below) as well as test on the EPC to ensure they function properly on Eucalyptus. Testing an image should include the following checks.

* Test proper run, reboot and terminate operation
* Test ability to connect via ssh
* Test attaching/mounting an EBS volume, then unmounting/detaching
* Test access to instance metadata and user data (via special URL: http://169.254.269.254/latest/meta-data/)

Run these tests on both the ECC and the EPC, which ensures that both Open Source and Enterprise Eucalyptus will support the image.


h2. Technical Details

Primarily, it will be important to provide the image with kernel/ramdisk. While a unified kernel would be preferred, the other option is to provide separate kernel/ramdisk for xen and kvm. This is roughly the directory structure that should be with the compressed tar file (.tgz);

./thisimage.img
./xen-kernel/xen-ramdisk
./xen-kernel/xen-kernel
./kvm-kernel/kvm-ramdisk
./kvm-kernel/kvm-kernel
Here is an example from an actual image file.

./centos.5-5.x86-64.img
./kvm-kernel/initrd.img-2.6.28-11-generic
./kvm-kernel/vmlinuz-2.6.28-11-generic
./xen-kernel/initrd-2.6.27.21-0.1-xen
./xen-kernel/vmlinuz-2.6.27.21-0.1-xen