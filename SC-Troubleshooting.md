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
can you ssh to the IP you specified with sanhost property from the SC? Eucalyptus does not use SSH internally for the Netapp-backed SC, but be sure that the endpoint is reachable from the SC.


### Equallogic
The Equallogic-backed SC uses a persistent SSH connection to the SAN to issue commands. Verify that from the SC you can ssh into the Equallogic using the sanhost, sanuser, and sanpassword property values you set.
Once the SC is configured and running, you can check that the chapuser was correctly created by ssh'ing to Equallogic and running `chapuser show`. You should see a 'eucalyptus' user in the output.
### EMC VNX
* Storage Pool
* SC Paths
* NC Paths
* Licences for Snapview Clone, VNX-Snapshot
* Clone-Private-LUN on each VNX SP. Each CPL need only be 1GB.
* Navisphere Secure CLI installed and runnable (typically at /opt/Navisphere/bin/naviseccli)
* If issues are encountered installing the CLI, using yum rather than rpm to install will properly resolve the 32-bit glibc dependency that can sometimes be problematic.

To confirm VNX-Snapshot capability, from the SC, run: 
`/opt/Navisphere/bin/naviseccli -User <user> -Password <password> -Address <sanhost> -Scope [0|1] snap -feature -info`

To confirm Snapview Clone capability, from the SC, run:
`/opt/Navisphere/bin/naviseccli -User <user> -Password <password> -Address <sanhost> -Scope [0|1] clone -listclonefeature`

