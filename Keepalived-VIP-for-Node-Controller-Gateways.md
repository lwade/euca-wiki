Background
----------
In a Eucalyptus HA configuration there are two Cluster Controllers's (CC) which
are in an active-passive state. One is in "ENABLED" mode and one is "DISABLED" mode.
If a failure occurs, the active CC services moves to the secondary CC system.

If you combine this with Eucalyptus MANAGED or MANAGED-NOVLAN networking
configuration with a private back-end network your Node Controllers (NC) will require a
default gateway for access to external networks and to the Walrus service to download
Eucalyptus Machine Images.

This gateway is not yet part of Eucalyptus but may be included in a later release.
This article describes how to setup a virtual IP that is shared between the two CC
systems to use as the default gateway on all NC.

Further details can be found in the following bug:

[https://eucalyptus.atlassian.net/browse/EUCA-2412](https://eucalyptus.atlassian.net/browse/EUCA-2412)

Keepalived
----------

As a workaround, you can use a tool that creates a virtual IP (VIP) and maintains
membership of servers through healthchecks. There are a number of tools to do this
under Linux, however we'll focus on Keepalived.

[Keepalived](http://www.keepalived.org/) is available in any modern Linux distribution
such as RHEL/CentOS (located in the EPEL repo) and Ubuntu and provides a vast number
of features, too many to cover here.

We will use it to create a VIP between our two Cluster Controllers and add a status
check script to make sure it only runs on the system that is in ENABLED/active mode.

Installation
------------

### 1. Install the package (EPEL required for CentOS/RHEL)

``` bash centos
$ yum -y install keepalived
```

``` bash ubuntu
$ sudo apt-get install -y keepalived
```

### 2. Setup the keepalived configuration file 

Add the following file to both CC systems ensuring you update the fields below for each one.

See *'man keepalived'* or the /usr/share/doc/keepalived examples for details of all the options.

* *SERVER_HOSTNAME* - Hostname of the system e.g. cc1.cloud.mycompany.com
* *VIP_ADDRESS*/32 - Virtual IP Address to use as a gateway address e.g. 192.168.1.50/32
* *PRIV_DEV* - This is usually VNET_PUBINTERFACE from eucalyptus.conf on your CC. The private network used to connect the CC to NC systems e.g. bond1

``` plain keepalived.conf
global_defs {
    notification_email {
        root@localhost
    }
    notification_email_from keepalived@localhost

    smtp_server 127.0.0.1
    smtp_connect_timeout 10

    lvs_id <SERVER_HOSTNAME>
}

vrrp_script cc-enabled-check {
    script "/usr/local/bin/CCclient-enabled-check.sh"
    interval 5
    weight 2
    fall 1
    rise 1
}

vrrp_instance cloud_cluster1_gateway {
    state EQUAL
    interface <PRIV_DEV>
    virtual_router_id 45
    priority 100
    advert_int 1
    smtp_alert
    authentication {
        auth_type PASS
        auth_pass <RANDOM-PASSWORD>
    }

    virtual_ipaddress {
        <VIP_ADDRESS> dev <PRIV_DEV>
    }

    track_script {
        cc-enabled-check
    }
}
```

### 3. Compile and Install CCclient_full

[CCclient_full](https://github.com/eucalyptus/eucalyptus/wiki/Debugging-Eucalyptus-C-language-components)
is a tool used for debugging Eucalyptus. It allows you to query the CC on the internal API and importantly,
poll state information. See localState in the output below.

``` bash Using CCclient_full
$ /usr/local/bin/CCclient_full 127.0.0.1:8774 describeServices
LocalState=ENABLED localEpoch=0 details=ERRORS=0
type=cluster name=cc1_c1
uri=http://192.168.1.50:8774/axis2/services/EucalyptusCC
```

This state information shows if the component is ENABLED. That's exactly what we need to feed to keepalived
so that the VIP runs on the ENABLED CC component.

CCClient_full is part of the Eucalyptus source code but not distributed within any Eucalyptus distribution
packages, so you will need to compile it yourself following the standard Eucalytpus [compilation instructions](https://github.com/eucalyptus/eucalyptus/blob/master/INSTALL) on Github.

Alternatively, if you are running on CentOS 6 x86_64 you can download this pre-compiled binary:

``` bash
wget -c https://github.com/tomellis/puppet-modules/raw/master/keepalived/scripts/CCclient_full -O /usr/local/bin/CCclient_full
chmod 750 /usr/local/bin/CCclient_full
```

### 4. Add check script

This script is used as a heuristic to determine if the CC component is in an ENABLED or DISABLED state by running
the CCclient_full utility and checking to see if the local state is ENABLED. It returns a 0 if enabled, or a
1 if disabled.

Keepalived uses this script to determine which host it should run the VIP on, when the script returns enabled it adds
additional weight of 2 to the priority of the system and removes two from the priority if it is disabled. This means
the system that is running the enabled CC service becomes the system which runs the VIP.

Add this script to both systems along with the CCclient_full binary above, /usr/local/bin is a good location for this.

``` bash /usr/local/bin/CCclient-enabled-check.sh
#!/bin/bash
#
# Check to see if a CC service is in ENABLED or DISABLED state using CCclient_full
# Author: Tom Ellis <tom.ellis@eucalyptus.com>
#
# Returns 1 if DISABLED or not installed
# Return 0 if ENABLED

export AXIS2C_HOME=/usr/lib64/axis2c
export LD_LIBRARY_PATH=$AXIS2C_HOME/lib:$AXIS2C_HOME/modules/rampart/

if [ -f /usr/local/bin/CCclient_full ]; then
    RETVAL=1
    /usr/local/bin/CCclient_full 127.0.0.1:8774 describeServices | grep -q ENABLED
    RETVAL=$?
    if [ $RETVAL = "0" ]; then
       echo "Local Eucalyptus services in ENABLED state"
       exit 0
    else
       echo "Local Eucalyptus services in DISABLED state"
       exit 1
    fi
else
    echo "CCclient_full not present"
    exit 1
fi
```

You can also grab this directly:
``` bash
wget -c https://raw.github.com/tomellis/puppet-modules/master/keepalived/scripts/CCclient-enabled-check.sh -O /usr/local/bin/Cclient-enabled-check.sh
chmod 750 /usr/local/bin/Cclient-enabled-check.sh
```

Test that the script works on your system, it should report the CC service state.

### 5. Enable keepalived and start it

``` bash
chkconfig keepalived on
service keepalived start
```
You should see the logs from keepalived in your syslog as it comes up and creates the shared VIP on the ENABLED Cluster Controller.


Further Information
-------------------

### Puppet

I like to automate configuration. So, I've created a modified version of a keepalived puppet module to be used with the scripts above:

[https://github.com/tomellis/puppet-modules/tree/master/keepalived](https://github.com/tomellis/puppet-modules/tree/master/keepalived)

### Links

Eucalyptus Bug: [https://eucalyptus.atlassian.net/browse/EUCA-2412](https://eucalyptus.atlassian.net/browse/EUCA-2412)

CCClient_full: [https://github.com/eucalyptus/eucalyptus/wiki/Debugging-Eucalyptus-C-language-components](https://github.com/eucalyptus/eucalyptus/wiki/Debugging-Eucalyptus-C-language-components)

Keepalived: [http://www.keepalived.org/](http://www.keepalived.org/)
