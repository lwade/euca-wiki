_**(NOTE: this content is from the images project from the old projects.eucalyptus.com site and might be out of date.)**_

h1. Image-info

CentOS 5.8
This image originated as a 5.3 images in the euca 1.6 days. source of kernels unknown.
Image was updated to be 5.8 and has latest patches as of date of creation (in filename).
/etc/rc.local is custom for ssh key handling. /etc/image-id was added. I hear there is an old version of euca2ools on there.

Debian 6
This image (like the one above) started as an older image (Debian 5, I think). It was updated to contain the latest patches from Debian
same kernels as above image.
/etc/rc.local is custom for ssh key handling. /etc/image-id was added. I think there is an old version of euca2ools on there.

Ubuntu 10.04
This image was downloaded from canonical's cloud images page. It is the lucid-server-cloudimg-amd64.tar.gz
/etc/image-id was added. The kernel is one Graziano got from an ubuntu release (exact source uncertain). It supports both hypervisors (single kernel)
Known issues:
  - none

Ubuntu 10.12
This image was downloaded from canonical's cloud images page. It is the precise-server-cloudimg-amd64.tar.gz
/etc/image-id was added. The kernel is one Graziano got from an ubuntu release (exact source uncertain). It supports both hypervisors (single kernel)
Known issues:
  - This image is ext4 as downloaded. This down not run on CentOS 5 host NCs because that OS doesn't handle ext4.

CentOS 6.2
This image was built by Andy Grimm using BoxGrinder. Details here: http://192.168.7.65/ami-creator/centos-6/bugfix/
/etc/image-id was added. udev rules modified on ramdisk to support xen and kvm device name mapping (not working on kvm at the moment)
Kernel goes with this distro (comment: this should be the goal for all images)
Known issues:
  - udev mapping of /dev/vdXN (to /dev/sdXN) does not work, though mounted ebs volumes work fine on the regular device names.
