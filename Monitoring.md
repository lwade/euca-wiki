This page contains a list of resources that a monitoring agent could check for on a production Eucalyptus IaaS cloud.

**Standard Operating System Checks**
* CPU Load - **Done**
* System Load - **Done**
* Memory usage - **Done**
* Swap usage - **Done**
* Disk space - **Done**
* Network Traffic
* Total Processes - **Done**
* I/O (metric tbd) 

**Cloud**
* Running images
* Running instance type
* Available resources
* Available cores - **Done**

**Cloud Controller**
* PostgreSQL connections / CLC logs indicate proxool errors / CLC/postgres (can connect)
* Number of Public IP Addresses allocated **Done**
* Number of available instances per type (warn when availability runs low)
* CLC logs indicate errors in VmInstance$RestoreAllocation
* CLC,Walrus,SC,VB/jvm (heap usage, full gc stop time threshold)
* Check TCP port 8773 is listening **Done**

**Walrus**
* Cache size
* Bukkits disk usage
* DRBD sync & role (status) 
* Walrus logs indicate "Peer is primary and I am supposed to be
master! Unable to proceed!"
* Check TCP port 8773 is listening **Done**

**Cluster Controller**
* Do the NODES in the a HA CC setup match for paired CCs
* CC/NC rampart package versions do not match (more Eucalyptus Bug specific, not daily monitoring?)
* CC logs indicate errors communicating with an NC
* Network tomography (partition detection; routing/switching check)
* Check TCP port 8774 is listening **Done**

**Storage Controller**
* Local cache
* Loopback devices in use - **Done**
* Specifically disk space in /var/lib/eucalyptus/volumes
* Failed snapshots? (check for vol and snap ID's by filename length?)
* tgtd status
* NetApp/Equallogic logs have  faulures because an "iSCSI session from
another initiator already exists"
* I/O Wait
* Check TCP port 8773 is listening **Done**

**Node Controller**
* NC/kernel (load average threshold, dmesg call stacks with io_sched in them)
* NC/libvirtd (is it alive)
* Loopback devices in use - **Done**
* iscsid - To rethink, as if iscsid is stops, euca will restart it auto on mounting EBS vol.
* iscsiadm (can establish sessions)
* libvirtd - **Done**
* Specifically disk space in /var/lib/eucalyptus/instances/[cache,work]
* NC has not received a describeResources request in the last
WARN_TIMEOUT minutes: Warning
* NC has not received a describeResources request in the last
ERROR_TIMEOUT minutes: Error
* iscsiadm on NC is not responding to control operations or has
errors about loading kernel module
* Check TCP port 80 is listening **Done**