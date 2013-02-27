The following are example configurations for the Cluster Controller and Node Controller for the various network modes.  For all examples, the CC's _private_ interface must be on the same subnet as the NC's _public_ interface.

## CC Example Configurations

| VNET_MODE     | # of NICS | DISABLE_ TUNNELING | CC Phys Pub | CC Phys Priv | VNET_ PUBINTERFACE  | VNET_ PRIVINTERFACE | Other required fields |
|:-------------:|:---------:|:-:|:-----------:|:------------:|:------------------:|:------------------:| ----- |
|MANAGED        | 1         | Y | eth0        | eth0         | eth0               | eth0               | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED        | 2         | Y | eth0        | eth1         | eth0               | eth1               | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED        | 1         | N | eth0        | eth0         | br0 (eth0 slave)   | eth0 (br0?)        | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED        | 2         | N | eth0        | eth1         | br0 (eth0 slave)   | eth1               | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED-NOVLAN | 1         | Y | eth0        | eth0         | eth0               | eth0               | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED-NOVLAN | 2         | Y | eth0        | eth1         | eth0               | eth1               | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED-NOVLAN | 1         | N | eth0        | eth0         | br0 (eth0 slave)   | eth0 (br0?)        | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|MANAGED-NOVLAN | 2         | N | eth0        | eth1         | br0 (eth0 slave)   | eth1               | VNET_PUBLICIPS VNET_SUBNET VNET_NETMASK VNET_DNS VNET_ADDRSPERNET |
|STATIC         | 1         | N/A |eth0        | eth0         | N/A                | eth0               | VNET_SUBNET VNET_NETMASK VNET_BROADCAST VNET_ROUTER VNET_DNS VNET_MACMAP |
|SYSTEM         | 1         | N/A |eth0        | eth0         | N/A                | N/A                | |

## NC Example Configurations

| VNET_MODE     | NC Phys Pub | VNET_PUBINTERFACE | VNET_BRIDGE |
|:-------------:|:-----------:|:-----------------:|:-----------:|
|MANAGED        | eth0        | eth0              | N/A |
|MANAGED-NOVLAN | eth0        | N/A               | br0 (eth0 slave) |
|STATIC         | eth0        | N/A               | br0 (eth0 slave) |
|SYSTEM         | eth0        | N/A               | br0 (eth0 slave) |