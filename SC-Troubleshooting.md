# Troubleshooting and Pre-install Checks for the Storage Controller

## Overlay (FS-based) Storage Controller
* service tgtd status
* tgtadm --lld iscsi -m target -o show
* losetup -a

## Direct Attached (non-FS) Storage Controller
* service tgtd status
* tgtadm --lld iscsi -m target -o show
* lvdisplay
* pvdisplay
* vgdisplay

## SAN-backed Storage Controller
### Netapp
Aggregate available

### Equallogic

### EMC VNX
* Storage Pool
* SC Paths
* NC Paths
* Licences for Snapview Clone, VNX-Snapshot
* Navisphere Secure CLI installed and runnable (typically at /opt/Navisphere/bin/naviseccli)
* If issues are encountered installing the CLI, using yum rather than rpm to install will properly resolve the 32-bit glibc dependency that can sometimes be problematic.

From the SC, run: 
`/opt/Navisphere/bin/naviseccli -User <user> -Password <password> -Address <sanhost> -Scope [0|1] snap -feature -info`
to confirm VNX-Snapshot capability

From the SC, run:
`/opt/Navisphere/bin/naviseccli -User <user> -Password <password> -Address <sanhost> -Scope [0|1] clone -listclonefeature`


