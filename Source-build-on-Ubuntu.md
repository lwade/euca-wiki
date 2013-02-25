# DRAFT: Building eucalyptus from source on Ubuntu

## Ubuntu 12.10

1. Installed dependencies (I'm not sure if this list is really correct, but it was enough to make the build work and get the CLC started):
```
sudo apt-get install cdbs debhelper libaxis2c-dev librampart-dev default-jdk libvirt-dev \
  libfuse-dev libfuse2 libcurl4-openssl-dev libssl-dev ant-optional zlib1g-dev pkg-config \
  swig python python-setuptools rsync wget open-iscsi libxslt1-dev gengetopt ant groovy \
  postgresql-server-dev-9.1 iputils-arping make libapache2-mod-axis2c postgresql postgresql-client \
  libbackport-util-concurrent-java libwsdl4j-java libjaxen-java libcommons-cli-java libwsdl2c-java \
  unzip libxalan2-java python-pygresql
```
1. Stop the default postgres service (should probably also permanently disable it):
```
sudo service postgresql stop
```
1. sudo vim /usr/lib/axis2/bin/tools/wsdl2c/WSDL2C.sh
```
java -classpath /usr/share/java/wsdl2c.jar:/usr/share/java/wsdl4j.jar:/usr/share/java/commons-logging.jar:/usr/share/java/backport-util-concurrent.jar:/usr/share/java/xalan2.jar org.apache.axis2.wsdl.WSDL2C $*
```
1. Apply this patch: http://pkgs.fedoraproject.org/cgit/eucalyptus.git/plain/eucalyptus-jdk7.patch
```
wget -nd http://pkgs.fedoraproject.org/cgit/eucalyptus.git/plain/eucalyptus-jdk7.patch
patch -p1 < eucalyptus-jdk7.patch
```
1. Configure:
```
./configure --with-axis2c=/usr/lib/axis2 --with-axis2c-services=/usr/lib/axis2/services \
            --prefix=/opt/euca-3.3.0 --with-apache2-module-dir=/usr/lib/apache2/modules \
            --with-db-home=/usr/lib/postgresql/9.1/
```
1. `make`
1. `sudo make install`
1. Finish setup as root:
```
sudo bash
export EUCALYPTUS=/opt/euca-3.3.0/
export PATH=$PATH:/opt/euca-3.3.0/usr/sbin
vim /opt/euca-3.3.0/etc/eucalyptus/eucalyptus.conf  # set EUCALYPTUS=/opt/euca-3.3.0, HYPERVISOR=kvm
useradd eucalyptus
euca_conf --setup
euca_conf --initialize
/opt/euca-3.3.0/etc/init.d/eucalyptus-cloud start
```
1. Install other runtime dependencies:
```
apt-get install python-m2crypto python-boto
```