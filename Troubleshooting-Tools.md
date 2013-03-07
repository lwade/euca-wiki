This is a survey of all tools we are currently using to troubleshoot issues with Eucalyptus installations.  This should include both pre-install infrastructure checks and post-install configuration verification, health checking, and gathering of support information for further escalation.

## Already existing projects that incoporate Troubleshootiness
- Chris Grzegorczyk's [DevEUtils](https://github.com/eucalyptus/deveutils): 
- Tom Ellis' SOSreports hosted at the [Dr. Euca](https://github.com/eucalyptus/doctor-euca) project
- Vic's EuTester script to gather system info: [debug.py](https://github.com/eucalyptus/eutester/blob/testing/testcases/cloud_admin/get_debug.py)

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

[Troubleshooting and Pre-install Checks for Walrus](Walrus-Troubleshooting)

[Pre-Install Checks for Storage Controller](Presinstall_SC)

[Troubleshooting EBS/Volumes](Troubleshoot_EBS)