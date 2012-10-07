# Create a single kernel to boot all Linux based EMIs 
https://code.launchpad.net/~smoser/+junk/kexec-loader

This ramdisk will boot the kernel configured as part of your grub. It should works with grub1 and grub2. It reads grub.cfg or menu.lst and boot the configured kernel using kexec.

## Requirements
1. Ubuntu instance ( can be an EMI running on eucalyptus )
2. Check out kexec-loader from launchpad
<pre><code>
bzr branch lp:~smoser/+junk/kexec-loader
</code></pre>

## How to 
1. Copy all files from conf directory to /etc/initramfs-tools/
<pre><code>
cp -ax kexec-loader/conf/* /etc/initramfs-tools
</code></pre>
2. Update initramfs
<pre><code>
update-initramfs -c -k `uname -r`
</code></pre>

3. Bundle / upload / register - kernel / initramdisk pair

NB: Make sure to have kexec installed ( kexec-tools )