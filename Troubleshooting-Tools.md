This is a survey of all tools we are currently using to troubleshoot issues with Eucalyptus installations.

## Pre-install Checklist
**Author: Jason Eden**

Network
- Physical configuration - Network cables plugged into correct switches
- IP address assignment
- Public IP pool range assigned
- Hostnames in DNS
- Multicast working
- Private Network - VLAN range assigned

Firewall
- ports open to front-end (8443, 8773, 8888)

Storage
- dedicated user/pass for eucalyptus
- storage pools configured

OS Config
- SElinux Disabled
    “getenforce” -> disabled
- IPtables disabled
- Bonding configured (optional)
- sysctl setting (shmmax/semmni/etc.)
- NTP (identical settings across servers?)
- Ensure bind or dnsmasq are not running

Hypervisor Config
- configuring br0 (for every mode except MANAGED)

Dependencies