Quick setup for testing new validator scripts with the latest 3.3 code:

1. yum install -y PyYAML python-argparse python-paramiko
2. Create a validator scripts directory -- for example, /etc/eucadmin/validator-scripts
3. Add your script in your scripts directory (we'll call it /etc/eucadmin/validator-scripts/hello):
```
#!/bin/sh

echo '{ "failed": 0, "info": "Hello world!" }'
```
4. Make the script executable
5. Create a config file specifying when the script should run (we'll call it /etc/eucadmin/validator.yaml):
```
preinstall:
  CLC:
    - hello
```
6. Create /etc/eucadmin/eucadmin.conf
```
validator_config_path = /etc/eucadmin/validator.yaml:/usr/lib/eucadmin/validator.yaml
validator_script_path = /etc/eucadmin/validator-scripts:/usr/lib/eucadmin/validator-scripts
```
7. If you Eucalyptus install is in /opt/eucalyptus, you may need a wrapper script at /usr/sbin/euca-validator to set up some environment variables:
```
#!/bin/sh
export EUCALYPTUS=/opt/eucalyptus
export PYTHONPATH=$EUCALYPTUS/usr/lib/python2.6/site-packages
export PATH=$PATH:$EUCALYPTUS/usr/sbin
$EUCALYPTUS/usr/sbin/euca-validator $@
```

That's it.  Now you should be able to see the script run:

```
# euca-validator -C CLC preinstall --log-level DEBUG -j
DEBUG:eucadmin.validator:Reading configuration files: /etc/eucadmin/validator.yaml:/usr/lib/eucadmin/validator.yaml
DEBUG:eucadmin.validator:Script search path is /etc/eucadmin/validator-scripts:/usr/lib/eucadmin/validator-scripts
DEBUG:eucadmin.validator:Running script: /etc/eucadmin/validator-scripts/hello
DEBUG:eucadmin.validator:Running script: /usr/lib/eucadmin/validator-scripts/selinux
{
    "selinux": {
        "current": "Disabled", 
        "failed": "0", 
        "boot": "1", 
        "sysconfig": "disabled"
    }, 
    "hello": {
        "info": "success!", 
        "failed": 0
    }
}
```