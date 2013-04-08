## Introduction

This article explains how to setup openshift origin on a, Fedora 18 instance running on a Eucalyptus private cloud. In order to get Fedora 18 up and running as an instance on Eucalyptus one should follow this article , [Fedora 18 EMI](https://github.com/eucalyptus/eucalyptus/wiki/Fedora-18-Image)

## Setup

The setup is very simple (POC based), it consists of 1 instance running Fedora 18, this instance would act as both broker as well as node in the openshift setup.

The instance type chosen is m1.xlarge that provides 2CPU/2048MB RAM/20GB of disk, the EMI is bfEBS hence providing persistent disk for root filesystem.

## Installation and Configuration

In order to setup Openshift origin we rely on the [openshift ansible playbook](https://github.com/maxamillion/ansible-openshift_origin)

### Install git and ansible 

On the local machine get ansible and git installed:

```
yum -y install git ansible
```

### Configure ansible hosts

Edit the ansible hosts file (/etc/ansible/hosts) on to add broker and node information

```
[brokers]
10.104.3.10

[nodes]
10.104.3.10
```

Note that 10.104.3.10 is public IP address of the instance, replace with correct settings.

### Get the ansible playbook

```
git clone https://github.com/maxamillion/ansible-openshift_origin.git
```

Currently there is **1 problem**:

* The playbook tries to install gcc that tries to update glibc on the instance, that in turns fails complaining for audit package been old version.

```
Transaction Check Error:
file /usr/lib64/audit from install of glibc-2.16-30.fc18.x86_64 conflicts with file from package audit-2.2.1-2.fc18.x86_64
```

It is hence advice to modify the broker.yml to include audit in the 'develpkgs' task

```
- name: Devel Packages Install
  hosts: brokers
  user: root
  vars_files:
    - varfiles/origin_vars.yml
  tasks:
    ### Dev tools needed by some gem installs for native modules/extensions
    - name: Install dev-tool deps for gem installs
      yum: pkg=$item state=latest
      with_items:
        - ruby-devel
        - audit
        - mysql-devel
        - mongodb-devel
        - gcc
        - make
  tags:
    - develpkgs
```

### Run the ansible playbook

The default login to the instance is as ec2-user , who has sudo privileges, the playbook has user set to root, we would need to modify that in both broker.yml and node.yml

```
TODO: one liner sed for this job?
```

Next we need to run the following command after changing directory to the playbook directory, replace the private key with your environment specific configuration:

```
ansible-playbook --private-key=/home/jeevanullas/sshlogin --user=ec2-user --sudo broker.yml
```

That should do it , and setup the broker.

An output from a sample run in lab is available [here](https://gist.github.com/jeevanullas/5335541#file-openshift-ansible-playbook-broker-output-txt)

Next would be the node, replace the private key with your environment specific configuration:

```
ansible-playbook --private-key=/home/jeevanullas/sshlogin --user=ec2-user --sudo node.yml
```

An output from a sample run in lab is available [here](https://gist.github.com/jeevanullas/5336280#file-openshift-ansible-playbook-node-output-txt)

### Finalizing the install 

**TODO**

## Start using Openshift

This section would walk through verifying the install and then provide example on how to use the openshift origin PaaS

### Verifying the broker

In order to quickly check if broker API endpoint is working, we do the following inside the instance

```
$ curl -Ik https://localhost/broker/rest/api
```

The output should look something like below

```
HTTP/1.1 200 
Date: Mon, 08 Apr 2013 13:49:28 GMT
Server: Apache/2.4.4 (Fedora)
X-Powered-By: Phusion Passenger (mod_rails/mod_rack) 3.0.17
X-UA-Compatible: IE=Edge,chrome=1
Cache-Control: max-age=0, private, must-revalidate
X-Request-Id: 828a632f548672f76150cf51e726fb9e
X-Runtime: 0.009659
X-Rack-Cache: miss
ETag: "850315cef2c28482d633170c5877d148"
Status: 200
Content-Type: application/json; charset=utf-8
```

We can open the console from a web browser, after making sure DNS client points to the DNS server running inside the openshift instance, URL would be:

```
https://broker.example.com/console
```