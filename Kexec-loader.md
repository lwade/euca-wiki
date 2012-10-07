# Create a single kernel to boot all Linux based EMIs 
https://code.launchpad.net/~smoser/+junk/kexec-loader

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
