First, set up the build dep yum repository:

```
cat <<EOF >/etc/yum.repos.d/builddeps.repo
[builddeps]
name=builddeps
baseurl=http://downloads.eucalyptus.com/software/eucalyptus/build-deps/3.1/centos/6/x86_64/
gpgcheck=0
enabled=1
EOF
```

Download the source RPM:

```
wget -nd http://downloads.eucalyptus.com/software/eucalyptus/3.1/centos/6/source/eucalyptus-3.1.2-0.210.el6.src.rpm
```

Install rpm-build and the deps it assumes are present:

```
yum install -y rpm-build gcc make
```

Install the build requirements for the RPM using yum:

```
yum-builddep -y eucalyptus-3.1.2-0.210.el6.src.rpm
```

Install the source RPM:

```
rpm -ivh eucalyptus-3.1.2-0.210.el6.src.rpm
```

Run the rpm build process through the "compile" step:

```
cd ~/rpmbuild/SPECS
rpmbuild -bc eucalyptus.spec
```

Now you have a build in ~/rpmbuild/BUILD/eucalyptus-3.1.2 ... from here you can modify and recompile bits as need.  I often test java changes by modifying source, running "make" in the clc directory, and then copying a single jar from clc/target into /usr/share/eucalyptus and restarting eucalyptus-cloud.
