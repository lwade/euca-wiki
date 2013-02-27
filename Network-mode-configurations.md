The following are example configurations for the Cluster Controller and Node Controller for the various network modes.  For all examples, the CC's _private_ interface must be on the same subnet as the NC's _public_ interface.

## CC Example Configurations

| VNET_MODE     | # of NICS | CC Phys Pub | CC Phys Priv | VNET_PUBINTERFACE  | VNET_PRIVINTERFACE | Other required fields |
|:-------------:|:---------:|:-----------:|:------------:|:------------------:|:------------------:| ----- |
|MANAGED        | 1         | eth0        | eth0         | eth0 (why not br0?)| br0 (eth0 slave)   | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED        | 2         | eth0        | eth1         | eth0               | br0 (eth1 slave)   | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED-NOVLAN | 1         | eth0        | eth0         | eth0 (why not br0?)| br0 (eth0 slave)   | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED-NOVLAN | 2         | eth0        | eth1         | eth0               | br0 (eth1 slave)   | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|STATIC         | 1         | eth0        | eth0         | N/A                | eth0               |VNET_SUBNET VNET_NETMASK VNET_BROADCAST VNET_ROUTER VNET_DNS VNET_MACMAP |
|SYSTEM         | 1         | eth0        | eth0         | N/A                | N/A                | |

*NOTE*: If you enable tunneling (with DISABLE_TUNNELING="N"), then VNET_PUBINTERFACE must be a bridge.

## NC Example Configurations

| VNET_MODE     | NC Phys Pub | VNET_PUBINTERFACE | VNET_BRIDGE |
|:-------------:|:-----------:|:-----------------:|:-----------:|
|MANAGED        | eth0        | br0               | N/A (eth0 according to QA code? why?) |
|MANAGED-NOVLAN | eth0        | N/A               | br0 (eth0 slave) |
|STATIC         | eth0        | N/A               | br0 (eth0 slave) |
|SYSTEM         | eth0        | N/A               | br0 (eth0 slave) |