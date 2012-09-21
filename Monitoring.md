This page contains a list of resources that a monitoring agent could check for on a production Eucalyptus IaaS cloud.

**Standard Operating System Checks**
* CPU Load
* Memory usage
* Disk space
* Network Traffic
* I/O (metric tbd)

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
* Specifically disk space in /var/lib/eucalyptus/volumes
* Failed snapshots? (check for vol and snap ID's by filename length?)
* tgtd status

**Node Controller**
* Loopback devices in use
* iscsid
* libvirtd
* Specifically disk space in /var/lib/eucalyptus/instances/[cache,work]

**Cloud**
* Running images
* Running instance type
* Available ressource

