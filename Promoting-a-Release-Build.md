Purpose
-------

This page is intended to be used by the Eucalyptus Release Engineering team. It provides documentation for the internal process of promoting a build for release to the public.

Selecting a build to promote
----------------------------

In order to promote a release of Eucalyptus, one must first SSH to the internal staging server. This server mirrors the contents of the public-facing [Downloads Server](http://downloads.eucalyptus.com). Once logged into the server, change to the directory where promotion scripts are located:

    $ cd /opt/nightly/build

In this directory there are a number of different shell scripts that are used to do promotions of nightly, milestone, and release builds. In order to do the promotion of a release build you will need to run the _promote-release.sh_ script. You'll need to supply two arguments: the _commit hash_ and the _version number_.

> Please note that the _commit hash_ is actually any reference that the package API understands. It can be a hash, tag or branch.

For example, if your commit hash was _1234567890_ and the version you are releasing is _3.3_, you would run the following command:

    $ sudo ./promote-release.sh 1234567890 3.3

This command will query the package API to determine if the commit hash is valid for the _internal_ Eucalyptus repository. If it is, it will create a new repository for the proprietary packages if one does not exist, or merge the packages into the existing repository. Then the script will check which version of the _eucalyptus(abi)_ the proprietary packages require. This is the version of the Eucalyptus open source packages that will be promoted. Again, a new repository will be created or the packages will be merged into the existing repository.

Please note that doing a release promotion **will not** promote the packages to the public server. It will only select the packages to promote and merge them into repositories on the internal staging server. This is because typically SA and GA do not occur on the same day. At a later time an rsync will need to be run to promote these packages to the public server.

Promotion best practices
------------------------

### Always do a backup

Before running the promotion scripts, it's best to make a backup of the repositories that will be updated. For instance, if are promoting a release of version _3.2_, then first backup the 3.2 directory. If for any reason the promotion does not turn out like you expect you can reverse your changes.

### Checking for packages prior to promotion

You can also check to see if a particular build exists before running the promotion script by using the [Arado tools](https://github.com/mspaulding06/arado) directly. Since the tools need to use a _virtualenv_ you'll first need to put the correct Python into your path, as well as the path to the tools.

    $ export PATH=/opt/nightly/aradopy/bin:/opt/nightly/arado:$PATH

Now you can run any of the Arado tools that you would like. If you want to check if a build exists and what the package contents of that build are, you can run a command like this:

    mspaulding@release-repo:/opt/nightly/arado$ arado-describe-build -p eucalyptus -c 3.3-m4
    axis2c-1.6.0-0.5.el6.x86_64.rpm
    axis2c-debuginfo-1.6.0-0.5.el6.x86_64.rpm
    dhcp41-4.1.1-24.P1.el6.x86_64.rpm
    dhcp41-common-4.1.1-24.P1.el6.x86_64.rpm
    eucalyptus-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-admin-tools-3.3.0-0.0.638.20130315git255b1f45.el6.noarch.rpm
    eucalyptus-cc-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-cloud-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-common-java-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-common-java-libs-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-console-3.3.0-0.0.638.20130315git255b1f45.el6.noarch.rpm
    eucalyptus-debuginfo-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-gl-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-nc-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-sc-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    eucalyptus-walrus-3.3.0-0.0.638.20130315git255b1f45.el6.x86_64.rpm
    python-eucadmin-3.3.0-0.0.638.20130315git255b1f45.el6.noarch.rpm
    rampartc-1.3.0-0.5.el6.x86_64.rpm
    rampartc-debuginfo-1.3.0-0.5.el6.x86_64.rpm
    vblade-14-7.el6.x86_64.rpm

In order to use the _arado-describe-build_ tool, you will need to specify a _project_ and _commit_ as was done for the example above. A commit can be any type of commit reference that Git understands, like a commit hash, tag or branch. After the running the command and verifying that the packages you want to promote are available and the correct ones, you can run the promotion process.
