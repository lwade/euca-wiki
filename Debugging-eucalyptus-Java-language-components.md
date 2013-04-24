The following are suggestions for debugging Eucalyptus components running in a JVM: CLC, Walrus, SC.

## Load the code into an IDE

If your installation is remote from your development machine, you'll first want to load the exact version of the code that is running. 

* If the code was built from source on the remote host, you can find out what `git` revision to check out with `git log`. 
* If, however, Eucalyptus was installed from packages, look for the git commit hash among package metadata:

```
# yum info eucalyptus | grep Release
Release     : 0.0.798.20130422gite9dc4a97.el6
```

The commit hash follows `git` and ends before the trailing `.el6`.

## Restart CLC

* Add `--debug` and, optionally, `--debug-suspend` 

## Connect from Eclipse as follows

* open clc:eucalyptus project in Eclipse
* select Run > Debug Configurations
* select Remote Java Application in the left panel
* add a new configuration with a 'plus' icon in top left
** give it a name
** project should be set by default
** connection type is default (socket attach)
** host: is your CLC
** port: 5005

*****

[[category.debugging]]