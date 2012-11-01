Being able to translate AMIs directly to EMIs automatically is a key goal for effective hybrid image use.  We strongly suspect that for a large number of extant AMIs, such translation should "just work", and for another larger set of images, such translation could work with some straightforward tweaks.

The best targets for translation are those images that are using pvgrub to boot.  pvgrub is a mechanism that allows AWS to boot images without using Amzon-specific AKIs and ARIs, which are highly Amazon-specific; in the boot process, pvgrub swaps out the running kernel and ramdisk with the kernel and ramdisk from the image itself.  Eucalytpus uses a similar mechanism based on [[kexec-loader]].  Therefore, theoretically, there should be a large number of images that "just work" on Amazon using pvgrub that should similarly "just work" on Eucalyptus using kexec-loader.

Our goal here is to build a set of tools that does as much as can possibly be done automatically, so that we get a clear picture of which images "just work" -- and which ones don't, and why.

**Step 1: Identifying AMIs that use the pvgrub AKI**

First, we get a list of the pvgrub AKIs in a particular region (thanks to these guys: http://www.dowdandassociates.com/content/howto-amazon-ec2-kernel-images-pv-grub).  We'll choose us-east-1 because it's one of the busiest.

`euca-describe-images \
--owner amazon \
--filter image-type=kernel \
--filter manifest-location=*pv-grub* \
--region us-east-1`

The list of AKIs this turns up on 10/30: aki-407d9529, aki-427d952b, aki-4c7d9525, aki-4e7d9527, aki-525ea73b, aki-805ea7e9, aki-825ea7eb, aki-88aa75e1, aki-8e5ea7e7, aki-b2aa75db, aki-b4aa75dd, aki-b6aa75df

So now we can pull a full list of all available images into a file:

`euca-describe-images --all > /tmp/all-images`

And then grep for each of these:

`grep aki-xxxxxxxx /tmp/all-images | wc -l`

The counts:
aki-407d9529 1433
aki-427d952b 1183
aki-4c7d9525 38
aki-4e7d9527 74
aki-525ea73b 7
aki-805ea7e9 1298
aki-825ea7eb 1709
aki-88aa75e1 233
aki-8e5ea7e7 29
aki-b2aa75db 9
aki-b4aa75dd 11
aki-b6aa75df 77

Total pvgrub-based AMIs in us-east-1: 6101
Total AMIs in us-east-1: 20369 

This gives us a list of 6000+ images that we might be able to test to see if they "just work" on Euca using kexec-loader.

A quick and dirty perl hack example to get all AMI IDs that use a particular AKI:

`grep aki-825ea7eb /tmp/all-images | perl -ne 'if (/(ami-.{8}?)/) {print "$1\n";}'`

Ultimately, there should be a script that does this properly on a regular basis -- but it's easy to get a dataset to get us started.

**Step 2: Running the AMIs, checking them for Euca compatibility, and bundling** 

(NOTE: these are working notes in progress. --[[Greg DeKoenigsberg]], 1 Nov 2012)

Having a list of AMIs, we can build a script that performs the following steps:

* Picks an AMI id from the top of the stack;
* Launches an Amazon instance based on that AMI;
* Looks at ramdisk parameters to see if it's likely to run in Eucalyptus using kexec-loader;
* If so, bundles the running instance into a Eucalytpus image and uploads it to the Euca cloud;
* Tests the resultant Euca image to see if ssh responds.

Once we've done this, it should be easy to provide users with a tool and a set of AMIs that can be
translated into EMIs with a high degree of confidence.

First let's start with a typical AMI in US-EAST-1 with a pvgrub AKI:

`IMAGE   ami-002bf169    bitnami-cloud/wordpress/bitnami-wordpress-3.3.2-0-multisite-linux-ubuntu-12.04-x86_64-s3.manifest.xml   979382823631    available       public          x86_64  machine aki-825ea7eb`
