_**(Note: this content is from the old projects.eucalyptus.com site and may be outdated.)**_

h1. Single Kernel

Unlike a public cloud where the hardware is controlled and so the environment can be made uniform, a Eucalyptus private cloud installation needs to adapt to whatever environment is already deployed. Virtualisation aspires to abstract away the hardware details, thus offering a very uniform view to the application and OS layer running into an instance. This allow the image to be _portable_ so that an image created on Eucalyptus cloud A can run on Eucalyptus cloud B. Ideally the images should be portable across all different clouds.

The virtualisation abstraction works usually pretty well, but we are seen some obvious difference that needed to be accounted for. One relates to the boot process of the instance. Breaking this abstraction means that now the user needs to understand which kernel to use for the different cluster and/or cloud and perhaps having to modify the image to account for that changes. For example, our starter images used to ship with 2 kernel: one for xen and for kvm. The goal of this project is to use a single kernel/initrd for both kvm and xen, thus to simplify the image management.

h2. The Kernel

The kernel used to boot the image, will need to be compatible with the underlying virtualisation. For example. to run in xen, one would need to have the kernel compiled with the rights drivers to be able to cooperate with the xen hypervisor. Hardware virtualisation may help here, since now the kernel do not need to be aware of the hypervisor, yet if it is, the instance may have access to extra features, or run faster or better. Eucalyptus runs on Xen and Kvm, and so we need to ensure that the kernel would boot both.

We are trying to use the Debian kernel, since Debian currently support both hypervisors.

h2. The Root File System problem

Depending on the hypervisor and the driver used by the guest and the hypervisor to access the disk, the disk as showed to the guest OS running in the image, can be hda, sda, vda or xvda. The meaning of the naming is:
* hda: IDE hard drive. This is just legacy, we have not seen an installation using hda in a long time;
* sda: SCSI hard drive. This is the default and what is seen in Amazon's EC2;
* vda: VIRTIO hard drive. Used by kvm it requires the kernel to know it is running on top of kvm. It should be faster than the SCSI or IDE alternatives;
* xvda: paravirtualized hard drive: Used by xen, it requires the kernel to have the right drivers. It should be faster than the SCSI or IDE alternatives.

Notice that the same system could run at the same time a subset of the above combination. For example, we could have an instance running on xvda and another instance running on sda. 

When an instance is booted, a kernel parameter is passed to indicate the ROOT file system. And typically this is what is passed *@root=/dev/sda1,ro@*
obviously the device after _root=_ needs to be changed depending on the real device used to boot. But at this time I am not aware of a way to determine which combination of kernel/hypervisor will use which device.

So the workaround is to make the image aware of this. For example, root could be a UUID (for example my laptop has root=UUID=94072b6a-7668-462d-a8f0-9e0053f5d035) or labels then using something like root=/root (label is a property of the file system). 

Another workaround we have been working on is to modify the initrd in order to discover which hard drive we have the root file system on and mount it: more on this idea on the [[Initrd|modifying Initrd]].