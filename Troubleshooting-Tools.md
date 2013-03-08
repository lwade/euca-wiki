This is a survey of all tools we are currently using to troubleshoot issues with Eucalyptus installations.  This should include both pre-install infrastructure checks and post-install configuration verification, health checking, and gathering of support information for further escalation.

## Already existing projects that incoporate Troubleshootiness
- Chris Grzegorczyk's [DevEUtils](https://github.com/eucalyptus/deveutils): 
- Tom Ellis' SOSreports hosted at the [Dr. Euca](https://github.com/eucalyptus/doctor-euca) project
- Vic's EuTester script to gather system info: [debug.py](https://github.com/eucalyptus/eutester/blob/testing/testcases/cloud_admin/get_debug.py)

## Pre-install Checklist
**Authors: Jason Eden, Tom Ellis, and Pavan Yara**

Network
- Physical configuration - Network cables plugged into correct switches
- IP address assignment
- Public IP pool range assigned
- Hostnames in DNS (or host lists maintained in /etc/hosts)
- Multicast working between CLC, Walrus, and SC
- Private Network - VLAN range assigned
- No DHCP server running on the networks to be used by Euca (unless we are doing a SYSTEM install)

Firewall
- ports open to front-end (8443, 8773, 8888, 53, 8080)

Storage
- dedicated user/pass for eucalyptus
- storage pools configured

OS Config
- SElinux Disabled
    “getenforce” -> disabled or permissive
- IPtables disabled
- Bonding configured (optional)
- sysctl setting (shmmax/semmni/etc.)
      sysctl -> nofile, file-max?
- NTP (identical settings across servers?)
- Ensure bind or dnsmasq are not running
- NetworkManager disabled (preferably system-wide, but at least per interface for those to be used for Euca)
- Preferably have their subscription package installed on all Euca nodes


Hypervisor Config
- configuring br0 (for every mode except MANAGED)

Dependencies
- SAN client package installed from SAN vendor, if applicable

[Troubleshooting and Pre-install Checks for Walrus](Walrus-Troubleshooting)

[Troubleshooting and Pre-install Checks for Storage Controller](SC-Troubleshooting)

[Troubleshooting EBS/Volumes End-to-End](EBS-Troubleshooting)

[Pre-Installation Checklist (authored by Lee Rich)](https://docs.google.com/document/d/1FBZuLf_pr2TbEHHWlEeObQXKO-oZ7bq1IF_I2mRVpwU/edit?usp=sharing)