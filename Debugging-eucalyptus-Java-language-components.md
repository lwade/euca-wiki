The following are suggestions for debugging Eucalyptus components running in a JVM: CLC, Walrus, SC.

## Load the code into an IDE

If your installation is remote from your development machine, you'll first want to check out from Eucalyptus git repository the exact version of the code that is running. 

* If the code was built from source on the remote host, by using `git log` you can find out what git revision to check out on your development machine. 
* If, however, Eucalyptus was installed from packages, look for the git commit hash among package metadata:

```
# yum info eucalyptus | grep Release
Release     : 0.0.798.20130422gite9dc4a97.el6
```

The commit hash follows `git` and ends before the trailing `.el6`. In this example, the revision is `e9dc4a97`.

## Restart the CLC

To enable connections from a debugger, restart the Eucalyptus JVM with `--debug` flag on the command line. Command-line flags can be changed by editing the line that invokes the JVM in the /etc/init.d/eucalyptus-cloud init script:

```
$EUCALYPTUS/usr/sbin/eucalyptus-cloud --debug $CLOUD_OPTS -h $EUCALYPTUS -u $EUCA_USER ...
```

If the problem you are trying to debug takes place early in the lifetime of the VM, you can additionally supply the `--debug-suspend` flag to have the VM pause during startup indefinitely, waiting for a connection from a debugger.

## Connect from debugger in Eclipse

* open `clc:eucalyptus` project in Eclipse
* select **Run > Debug Configurations**
* select **Remote Java Application** in the left panel
* add a new configuration with a 'plus' icon in top left
  * give it a name
  * project should be set by default
  * connection type is default (socket attach)
  * host: is your CLC
  * port: `5005`

*****

[[category.debugging]]