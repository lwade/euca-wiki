This page contains a list of resources that a monitoring agent could check for on a production Eucalyptus IaaS cloud.

**Standard Operating System Checks**
* CPU Load
* Memory usage
* Disk space
* Network Traffic

**Cloud Controller**
* PostgreSQL connections
* Number of Public IP Addresses allocated
* Number of available instances per type (warn when availability runs low)

**Walrus**
* Cache size
* Bukkits disk usage
* DRBD sync status 

**Cluster Controller**

**Storage Controller**
* Local cache
* Loopback devices in use

**Node Controller**
* Loopback devices in use
* iscsid
* libvirtd

**Cloud**
* Running images
* Running instance type
* Available ressource

