1. kickstart fedora 17
1. switch selinux to permissive mode
1. reboot
1. /sbin/fixfiles -F restore (should use autorelabel, but reboots were failing)
1. yum update yum
1. edit /etc/yum.repos.d/fedora-updates-testing.repo
   * set enabled=1
   * set includepkgs=*wso2*,mule*
1. rpm --import https://fedoraproject.org/static/DE7F38BD.txt
1. yum clean all
1. yum --releasever=18 --disableplugin=presto distro-sync
1. Review http://fedoraproject.org/wiki/Upgrading_Fedora_using_yum#Fedora_17_-.3E_Fedora_18 to ensure that there aren't other known issues to be worked around.
1. Configure the bridge (so that system mode will work).  Note that this may vary by machine!
```
cat <<EOF >/etc/sysconfig/network-scripts/ifcfg-br0
DEVICE=br0
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=dhcp
TYPE=Bridge
DELAY=0
PERSISTENT_DHCLIENT=yes
EOF

cat <<EOF >/etc/sysconfig/network-scripts/ifcfg-em1
DEVICE=em1
ONBOOT=yes
NM_CONTROLLED=no
# BOOTPROTO=dhcp
TYPE=Ethernet
BRIDGE=br0
EOF
```
1. Fix SELinux context: chcon -u system_u /etc/sysconfig/network-scripts/ifcfg-*
1. systemctl enable network.service
1. yum erase NetworkManager NetworkManager-glib
1. reboot
1. grab eucalyptus rpms from (koji build, etc.).  Current build is 3.1.0-19
1. yum install *.rpm (eucalyptus-cloud eucalyptus-sc eucalyptus-walrus eucalyptus-cc eucalyptus-nc)
1. Broken deps:  yum install mod_wso2-axis2 wso2-axis2-devel wso2-axis2-modules (need -14 or later)
1. kernel params
```
cat <<EOF >/etc/sysctl.d/eucalyptus-cloud
kernel.shmmax = 536870912
kernel.sem = "250 32000 32 32768"
EOF
```
1. sysctl -p /etc/sysctl.d/eucalyptus-cloud
1. su eucalyptus -c "euca_conf --initialize"
1. touch /etc/sysconfig/eucalyptus (required by systemd scripts to set environment variables)
1. for x in /usr/share/java/mx4j/mx4j{,-impl,-jmx,-remote,-tools}.jar; do ln -s $x /usr/share/eucalyptus/; done
1. systemctl start eucalyptus-cloud
1. WAIT ....
1. euca_conf --get-credentials admin.zip
1. unzip admin.zip
1. source ./eucarc
1. euca-describe-services
1. euca_conf --register-walrus -C walrus -P walrus -H 192.168.51.79
1. euca_conf --register-sc -C SC00 -P PARTI00 -H 192.168.51.79
1. euca_conf --register-cluster -C CC00 -P PARTI00 -H 192.168.51.79
1. systemctl start eucalyptus-cc
1. euca_conf --register-nodes 192.168.51.79
1. ln -s libmod_rampart.so /usr/lib64/wso2-axis2/modules/rampart/libmod_rampart.so.0
1. comment to euca_test_nc from /usr/libexec/eucalyptus/eucalyptus-nc.init
1. ln -s grub2-install /sbin/grub-install
1. Add polkit rule
```
cat <<EOF >/etc/polkit-1/rules.d/91-euca.rules
polkit.addRule(function(action, subject) {
   if (action.id == "org.libvirt.unix.manage" &&
       subject.user == "eucalyptus") {
       return polkit.Result.YES;
   }
   return polkit.Result.NOT_HANDLED;
});
EOF
```
1. systemctl restart polkit
1. chown root:kvm /dev/kvm
1. rmmod kvm_intel; rmmod kvm
1. modprobe kvm; modprobe kvm_intel
1. systemctl start eucalyptus-nc
1. wget -nd http://s3.amazonaws.com/fedora17-201209050306/initramfs-3.5.3-1.fc17.x86_64.img
1. wget -nd http://s3.amazonaws.com/fedora17-201209050306/ks-fedora17-201209050306.img.gz
1. wget -nd http://s3.amazonaws.com/fedora17-201209050306/vmlinuz-3.5.3-1.fc17.x86_64
1. euca-bundle-image  --kernel true -i vmlinuz-3.5.3-1.fc17.x86_64
1. euca-upload-bundle -m /tmp/vmlinuz-3.5.3-1.fc17.x86_64.manifest.xml -b fedora17
1. euca-register fedora17/vmlinuz-3.5.3-1.fc17.x86_64.manifest.xml
1. euca-bundle-image --ramdisk true -i initramfs-3.5.3-1.fc17.x86_64.img 
1. euca-upload-bundle -m /tmp/initramfs-3.5.3-1.fc17.x86_64.img.manifest.xml -b fedora17
1. euca-register fedora17/initramfs-3.5.3-1.fc17.x86_64.img.manifest.xml
1. gunzip ks-fedora17-201209050306.img.gz; 
1. euca-bundle-image --kernel eki-4BAD3438 --ramdisk eri-5BB43B0C -i ks-fedora17-201209050306.img
1. euca-upload-bundle -m /tmp/ks-fedora17-201209050306.img.manifest.xml -b fedora17
1. euca-register fedora17/ks-fedora17-201209050306.img.manifest.xml
1. euca-add-keypair foobar > foobar.priv
1. chmod 600 foobar.priv
1. euca-run-instances -k foobar -t m1.large emi-BC81369A

Note: The instance boot time is _really_ slow, partly due to SELinux file relabelling.  Also, since I'm testing system mode here, cloud-init fails, and you have to log in as root.  It is possible to set up metadata service access for system mode, but I haven't done the iptables rules for that yet.
