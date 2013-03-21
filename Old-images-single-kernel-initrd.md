_**(Note: this content is from the old projects.eucalyptus.com site and may be outdated.)**_

h1. Modifying Initrd

As mentioned previously, one way to deal with the root device been different depending on the hypervisor and the hypervisor configuration, is to modify the initrd to allow for this variety. This is what we did to modify the initrd:

* get the initrd.gz to be modified: it should be a compressed file (gzip);
* uncompress it 
<pre>
zcat initrd.gz > /tmp/initrd
</pre>
* the end result should be a cpio archive: using the command _file_ note the typo of cpio archive for example
<pre>
initrd: ASCII cpio archive (SVR4 with no CRC)
</pre>
* extract the archive: 
<pre>
mkdir /tmp/test
cd /tmp/test
cat /tmp/initrd |cpio -id
</pre>
* modify it! In our case we modify the local scrips in _scripts/local_. Using an initrd from Debian, we modified the function _pre_mountroot()_ we added the following:
<pre>
                 # nasty hack for instances: let's force known roots
                 for z in $(/bin/ls /dev/vd[a-z]1 2> /dev/null) ; do
                         ROOT="${z}"
                         if [ -e "${ROOT}" ]; then
                                 return
                         fi
                 done
                 for z in $(/bin/ls /dev/xvd[a-z]1 2> /dev/null) ; do
                         ROOT="${z}"
                         if [ -e "${ROOT}" ]; then
                                 return
                         fi
                 done
                 for z in $(/bin/ls /dev/hd[a-z]1 2> /dev/null) ; do
                         ROOT="${z}"
                         if [ -e "${ROOT}" ]; then
                                 return
                         fi
                 done
 </pre>
and we added just before _giving up_:
<pre>
        # We've given up, but we'll let the user fix matters if they can
        while [ ! -e "${ROOT}" ]; do
</pre>
The modification simply look for the known root devices (sda, xvda, vda etc ...) and try to blindly mount it 
* repackage the archive  using 
<pre>
find ./ | cpio -oH <format> > /tmp/initrd.new
</pre>
using the format you discovered previously. In our case it is newc;
* compress the archive 
<pre>
gzip initrd.new
</pre>