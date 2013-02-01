## Stackato Status

### Image registration

Image for Stackato can be uploaded to Eucalyptus by using the following documentation:

*** NOTE: don't use uec-publish-image. Use documentation for bundling, uploading and registering kernel, ramdisk, image found here - http://www.eucalyptus.com/docs/3.2/ug/add_existing.html#add_existing ***

http://docs.stackato.com/server/openstack-legacy.html?highlight=openstack

### Blockers

* updated test stackato image cloud-init to use metadata source of EC2 (i.e. dpkg-reconfigure cloud-init).  This seems to have caused an issue in regards to how the instance initially runs.
* using instance without cloud-init - instance launches, but there seems to be an issue using a private IP and public IP configuration with Stackato.  Ran the following configuration for using public and private IP:

- kato setup core api.euca-192-168-55-101.wu-tang.euca-hasp.eucalyptus-systems.com (public IP)
- kato enable --all-but mdns
- kato op static_ip (private IP information)
- added 127.0.0.1 to /etc/resolv.conf

### Next Steps

* Get image (non cloud-init) working.  Once we get the right network configuration, the cloud-init Stackato image *should* work fine.

### References

* http://docs.stackato.com/server/index.html#accessing-server-via-the-command-line
* http://docs.stackato.com/server/configuration.html#nat-vs-bridged-networking
* http://docs.stackato.com/quick-start/index.html#accessing-stackato-via-management-console
* http://docs.stackato.com/cluster/cloud-init.html
* http://docs.stackato.com/server/ec2.html#vm-ec2
* http://docs.stackato.com/cluster/index.html