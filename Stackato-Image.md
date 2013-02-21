## Stackato Status

### Image registration

Image for Stackato can be uploaded to Eucalyptus by using the following documentation:

***NOTE: don't use uec-publish-image. Use documentation for bundling, uploading and registering kernel, ramdisk, image found here - http://www.eucalyptus.com/docs/3.2/ug/add_existing.html#add_existing***

http://docs.stackato.com/server/openstack-legacy.html?highlight=openstack

*Make sure and use vm.type that has at least 2 gigs of RAM.  Stackato will have odd behavior if this requirement is not met.*

### Pre-requisites

- Allocate an elastic IP (this will be used for DNS configuration regarding wildcard DNS entry)
- Configure DNS as documented here - http://docs.stackato.com/server/configuration.html#dns

*Since I was using a Eucalyptus DNS enabled cloud, I had to use an external DNS server and create DNS entries that pointed to the public DNS name of the elastic IP I reserved. Example is below:*

<pre>
# nsupdate
> server 10.1.1.254
> zone dev.eucalyptus-systems.com
> update add stackato.dev.eucalyptus-systems.com 3600 IN A 10.1.1.103
> send
> update delete stackato.dev.eucalyptus-systems.com A 
> send
> update add stackato.dev.eucalyptus-systems.com 600 cname euca-192-168-55-103.wu-tang.euca-hasp.eucalyptus-systems.com
> send
> update add *.stackato.dev.eucalyptus-systems.com 600 cname stackato.dev.eucalyptus-systems.com 
> send
> quit

** test lookup of CNAME stackato.dev.eucalyptus-systems.com **
$ nslookup stackato.dev.eucalyptus-systems.com
Server:		10.1.1.254
Address:	10.1.1.254#53

stackato.dev.eucalyptus-systems.com	canonical name = euca-192-168-55-103.wu-tang.euca-hasp.eucalyptus-systems.com.
Name:	euca-192-168-55-103.wu-tang.euca-hasp.eucalyptus-systems.com
Address: 192.168.55.103

** test wildcard DNS name lookup against stackato.dev.eucalyptus-systems.com **
$ nslookup host.stackato.dev.eucalyptus-systems.com
Server:		10.1.1.254
Address:	10.1.1.254#53

host.stackato.dev.eucalyptus-systems.com	canonical name = stackato.dev.eucalyptus-systems.com.
stackato.dev.eucalyptus-systems.com	canonical name = euca-192-168-55-103.wu-tang.euca-hasp.eucalyptus-systems.com.
Name:	euca-192-168-55-103.wu-tang.euca-hasp.eucalyptus-systems.com
Address: 192.168.55.103
</pre>

### Steps to Configure Stackato

Once you launch the instance, ssh into the instance using the following credentials:

username:  stackato
password:  stackato

Once logged into the instance, run the following commands (in this order):

- kato op static_ip (use the ip information of the instance by checking out ifconfig and route -n; for DNS server entries, I just used what was in the /etc/resolv.conf of the instance)
- kato rename <public DNS name> (this is the DNS name you set up in the external DNS - e.g. stackato.dev.eucalyptus-systems.com)
- kato disable mdns
- sudo reboot (reboot instance)

Log back into instance after it has finished rebooting and run the following commands:

- kato setup core api.<public DNS name> (same as above, the DNS name set in the external DNS - e.g. stackato.dev.eucalyptus-systems.com)
- kato enable --all-but mdns

Thats it.  Should be good to go.  Open up a browser and go to http:<public DNS name> e.g. http://stackato.dev.eucalyptus-systems.com.  You should see the [Admin Management Console](http://docs.stackato.com/quick-start/index.html#accessing-stackato-via-management-console) and then go from there.

### Next Steps

* Need to test Stackato 2.6.7 image.
* Need to get the non cloud-init image on emis.eucalyptus.com
* Work on getting the cloud-init Stackato image working.
* Work on setting up a micro-cloud cluster in Eucalyptus.


### References

* http://docs.stackato.com/quick-start/index.html#stackato-micro-cloud
* http://docs.stackato.com/server/index.html#accessing-server-via-the-command-line
* http://docs.stackato.com/server/configuration.html#nat-vs-bridged-networking
* http://docs.stackato.com/quick-start/index.html#accessing-stackato-via-management-console
* http://docs.stackato.com/server/configuration.html#dns
* http://docs.stackato.com/cluster/cloud-init.html
* http://docs.stackato.com/server/ec2.html#vm-ec2
* http://docs.stackato.com/cluster/index.html

*Note:  This was tested with Stackato v2.6.6*