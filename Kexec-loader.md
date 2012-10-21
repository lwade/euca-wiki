# Create a single kernel to boot all Linux based EMIs 
https://code.launchpad.net/~smoser/+junk/kexec-loader

Normally on Eucalyptus, when you want to run a new image you need three components ( kernel / ramdisk / disk image ). Each components need to be bundled / uploaded / registered. They gave you respectively an eki / eri / emi. You then register your emi with the correct eki / eri. This process is time consuming and can be error prone. 

The following kernel / ramdisk is able to boot any images which has got a grub1 or grub2 correctly configured in either grub.conf / menu.lst / grub.cfg. It initially boots the image with this kernel and then reads grub configuration file to discover kernel configured within the image and [kexec](http://en.wikipedia.org/wiki/Kexec) into this kernel. 

## How to use it. 
### Download it
where has it been uploaded ?

### Register kernel / ramdisk 
Before being available, you will need to register them on your eucalyptus cloud. 
<pre><code>
euca-bundle-image --kernel true -i <kernel filename>
euca-upload-bundle -m <manifest> -b <bucket>
euca-register <bucket>/<manifest>
euca-bundle-image --ramdisk true -i <ramdisk filename>
euca-upload-bundle -m <manifest> -b <bucket>
euca-register <bucket>/<manifest>
</code></pre>

### Set these as your default kernel and ramdisk:
You aren't required to do the step of setting the eki / eri as the
default, but if you don't, you still have to associate every emi you
register with this eki/eri pair, and who wants to do that?

<pre><code>
euca-modify-property cloud.images.defaultkernelid=<eki>
euca-modify-property cloud.images.defaultramdiskid=<eri>
</code></pre>

### Enjoy

Yes, now profit by not having to specify kernel and ramdisk for every
image you register.   If you use an image created by one of the
popular image creation tools, which puts a kernel, a useful ramdisk &
a grub config inside the filesystem, they should Just Work.


## How to create your own kexec ramdisk 
### Requirements
1. Ubuntu instance (can be an EMI running on eucalyptus)
2. Check out kexec-loader from launchpad
<pre><code>
bzr branch lp:~smoser/+junk/kexec-loader
</code></pre>

### How to 
1. Copy all files from conf directory to /etc/initramfs-tools/
<pre><code>
cp -ax kexec-loader/conf/* /etc/initramfs-tools
</code></pre>
2. Update initramfs
<pre><code>
update-initramfs -c -k `uname -r`
</code></pre>

3. Bundle / upload / register - kernel / initramdisk pair

NB: Make sure to have kexec installed (kexec-tools)